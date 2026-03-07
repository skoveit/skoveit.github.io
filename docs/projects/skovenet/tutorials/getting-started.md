# Getting Started

The **best way** to install SkoveNet is to download the latest stable binaries from our [GitHub Releases](https://github.com/skoveit/skovenet/releases) page. or you can build it from the source code with `make`.

Download the `controller` and `sgen` binaries for your operator machine (Linux, Windows, or macOS).

---

### Step A: Generate the Agent
Use `sgen` to create a standalone agent for your target platform.
```bash
./sgen generate --os linux --arch amd64 --output agent
chmod +x ./agent
```

### Step B: Launch the Agent
Execute the agent on the target machine.
```bash
./agent
```

### Step C: Join the Network
Execute the agent on your machine.
```bash
./agent
```

### Step D: Launch the Controller
Start the operator interface (will connect to the local agent via IPC).
```bash
./controller
```

### Step E: Authenticate
Inside the controller, perform the following sequence:
```bash
> sign <your_private_key>    # Authenticate (given by `sgen generate`)
> peers                      # Discover the local agent
> use <peerID>               # Select the agent
[peerID]> ls                 # Execute the command!
```

---

## Next Steps

- **[Agent Generation](agent-generation.md)**: Explore `keygen` and cross-compilation.
- **[Mission Execution](mission-execution.md)**: Deep dive into red team operations.
