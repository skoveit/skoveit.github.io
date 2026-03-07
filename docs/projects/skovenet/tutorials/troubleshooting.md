# Troubleshooting

This guide covers common issues encountered while deploying and operating SkoveNet.

## Controller Connection Issues

### "No running agent found"
*   **Meaning**: The Controller cannot find a local agent to talk to.
*   **Cause**: The agent is not running on the current machine, or the IPC socket is inaccessible.
*   **Fix**: 
    1.  Start the agent first: `./agent`
    2.  Ensure you have permissions to access `/tmp/skovenet-agent.sock` (Unix) or the named pipe (Windows).
    3.  If the agent crashed, a stale socket file might remain. Restart the agent to recreate it.

---

## Network & Connectivity

### Agents don't see each other (No peers)
*   **Firewalls**: SkoveNet listens on a **random high TCP port** by default. Ensure your firewall allows inbound/outbound traffic for the agent binary.
*   **Local Discovery**: Automatic discovery uses **mDNS (Multicast DNS)**. This only works on the same local network (LAN) and requires multicast traffic to be allowed.
*   **NAT Issues**: While SkoveNet has built-in NAT traversal (UPnP/NAT-PMP), aggressive symmetric NATs may still block direct P2P connections.
*   **Fixes**:
    1.  Check if ports are open: `netstat -tulpn | grep agent`
    2.  Check for `[+] Peer connected` logs in the agent output.
    3.  Ensure nodes are on the same subnet if relying on mDNS.

### "Command timed out"
*   **Meaning**: The command was sent, but a response wasn't received within 10 seconds.
*   **Observation**: The controller will say: `"Command sent. Output will appear asynchronously."`
*   **Cause**: The target node is under high load, or the network path is unstable.
*   **Fix**: Use `radar` to check the latency to the target node. High latency (>500ms) often leads to timeouts.

---

## Authentication & Operator Errors

### "Cannot send command: not signed in"
*   **Fix**: You must use the `sign <private_key>` command in the controller before you can issue commands to agents. The private key must match the public key embedded in the agent during generation.

### "Rejected unsigned/invalid command"
*   **Cause**: The agent receiving the command verified the signature and it failed. 
*   **Fix**: Ensure you are using the correct private key that corresponds to the `sgen --key` used at build time.

---

## Build & Environment

### "undefined reference" or "missing dependencies"
*   **Fix**: Ensure you are using **Go 1.25.4+**. 
*   **Fix**: Run `make vendor` or `go mod download` before building.

---

## Deep Debugging

If you're still stuck, run the agent with the flag `--debug` and watch the stdout for:
*   `Host created via tcp ...` - Confirms the node started.
*   `Radar ping from ...` - Confirms mesh broadcast is working.
*   `Signature verified ...` - Confirms your operator keys are working.
