# Execution

To interact with the network, you need to launch the **Controller** on your operator machine. It will connect to the local agent that has joined the network, so ensure you have an active agent running as a member of the network.

```bash
./bin/linux/controller
```

### 1. Authentication
All commands must be signed. Use your private key to authenticate your session:
```bash
> sign <your_private_key_base64>
```

### 2. Situational Awareness
List all discovered nodes in the network:
```bash
> peers
```

### 3. Targeting
Select a specific agent to interact with:
```bash
> use <peerID>
```

### 4. Direct Interaction
Once "attached" to an agent, you can run built-in commands or shell commands:
```bash
[peerID]> info      # Get system information
[peerID]> ls        # List directory
[peerID]> whoami    # Run shell command
[peerID]> shell     # Start interactive shell
```
### 5. Discovery Commands
```bash
[peerID]> peers       # List all currently connected peers
[peerID]> radar       # Discover all nodes within the network
[peerID]> graph on    # Launch the web-based network graph viewer
[peerID]> graph off
```

### 6. Transitioning
To return to the global view or target another agent:
```bash
[peerID]> background
```

## Resilience
The mesh is self-healing. If you lose connection to your entry-point node, the controller will attempt to route commands through other available neighbors if they exist in your local peer list.
