# Commands & Execution

The SkoveNet Controller provides a powerful CLI for interacting with the mesh.

## Interactive Mode

When you start the binary (as a controller), you enter an interactive shell.

```text
Connected to agent
Type 'help' for commands, TAB for completion

[0 peers]> 
```

## Command Reference

### Global Commands (Unattached Mode)

These commands are available when you are not currently "attached" to a peer.

| Command | Description | Usage |
| :--- | :--- | :--- |
| `help` | Show global commands. | `help` |
| `peers` | List all currently connected peers. | `peers` |
| `id` | Show the local node's Peer ID. | `id` |
| `sign` | Authenticate as operator. | `sign <key>` |
| `use` | Select a specific peer to interact with. | `use <peer-id>` (Use TAB for completion) |
| `radar` | Scan the network and measure latency. | `radar` |
| `graph` | Start/Stop the topology visualizer. | `graph on` / `graph off` |
| `cls` / `clear` | Clear the terminal screen. | `cls` |
| `quit` / `exit` | Shutdown the node and exit. | `quit` |

### Peer Commands (Attached Mode)

Once you have selected a peer via `use`, these commands are sent directly to the agent.

| Command | Description | Usage |
| :--- | :--- | :--- |
| `run` | executed as a shell command (sh, cmd) | `run <command> <args>` |
| `ls` | List directory contents. | `ls <path>` |
| `cd` | Change current working directory. | `cd <path>` |
| `pwd` | Print current working directory. | `pwd` |
| `ps` | List running processes. | `ps` |
| `info` | Show system information of the agent. | `info` |
| `upload` | Upload a file to the remote agent. | `upload <local> <remote>` |
| `download` | Download a file from the remote agent. | `download <remote> <local>` |
| `background` | Detach from the peer and return to global mode. | `background` (or `back`) |
| `help` | Show peer-specific commands. | `help` |


## Example: Remote Interaction

1.  **List Peers** to find a target.
    ```bash
    [2 peers]> peers
    Connected peers:
      1. 12D3KooWABC...
      2. 12D3KooWXYZ...
    ```

2.  **Select a Peer** (Attach session).
    ```bash
    [2 peers]> use 12D3KooWABC...
    Selected peer: 12D3KooWABC...
    [12D3KooW...]> 
    ```

3.  **Run Built-in Commands**.
    ```bash
    [12D3KooW...]> pwd
    /home/user/workspace
    
    [12D3KooW...]> ls
    MODE        SIZE      NAME
    drwxr-xr-x  -         src/
    -rw-r--r--  1024      notes.txt
    ```

4.  **Run Shell Commands Directly**.
    ```bash
    [12D3KooW...]> whoami
    operator
    ```

5.  **File Transfer**.
    ```bash
    [12D3KooW...]> upload script.sh /tmp/script.sh
    File uploaded successfully.
    ```

6.  **Background the Session**.
    ```bash
    [12D3KooW...]> background
    Deselected peer
    [2 peers]> 
    ```
