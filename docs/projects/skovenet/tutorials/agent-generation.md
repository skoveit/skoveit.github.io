# Agent Generation

**sgen** is the primary agent generator and encryption key manager for the framework. It embeds a full Go toolchain and the agent source code, enabling you to generate implants on the fly.

### Cross-Compilation Example
```bash
# Generate a Windows Agent
./sgen generate --os windows --arch amd64 --output finance_audit.exe
```
By default, this command generates a new public/private key pair. If you prefer to manage keys manually, use the keygen command or specify a key during generation:

```bash 
./sgen keygen

./sgen generate --key base64pubkey.... 
```

## Configuration Flags

| Flag | Description |
| :--- | :--- |
| `--os` | Target operating system (linux, windows, darwin) |
| `--arch` | Target architecture (amd64, arm64) |
| `--key` | Embed a specific public key for authentication |
| `--output` | Custom filename for the generated binary |

To see what platforms and architectures are currently supported by your `sgen` binary, use the `list` command. This ensures you know exactly what options are available for your current engagement.

```bash
./sgen list

  Supported Platforms
  ───────────────────────────────
  OS           ARCH      
  ───────────────────────────────
  linux        amd64     
  linux        arm64     
  linux        386       
  linux        arm       
  darwin       amd64     
  darwin       arm64     
  windows      amd64     
  ───────────────────────────────
  Total: 7 combinations
```


Once your identity is created and your agent is deployed, proceed to [Mission Execution](mission-execution.md).
