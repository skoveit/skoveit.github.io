# Node Discovery

Discovery is the process by which SkoveNet nodes locate and connect to peers to form the mesh network.

## Local Discovery: mDNS

By default, SkoveNet uses **mDNS (Multicast DNS)** for automated discovery on local area networks.

*   **Service Tag**: `mesh-c2`
*   **Mechanism**: Nodes broadcast their identity and listen for peer announcements. When a new peer is found, the node caches the address info and attempts a connection.
*   **Jitter**: Connection attempts include a random jitter (500ms - 2000ms) to reduce network noise and coordination spikes.

## Mesh propagation (Peer Exchange)

Nodes also discover new peers through the GossipSub mesh. As nodes connect to each other, they share information about their known peers, allowing the network to expand dynamically beyond the local subnet.

## Network Limits

The system maintains a healthy network topology through strict connection management:
*   **HighWater Mark**: A maximum of 8 concurrent peer connections.
*   **LowWater Mark**: A minimum of 4 peer connections.
*   **NAT Service**: Nodes provide and utilize NAT traversal services (AutoNAT and UPnP) to ensure connectivity between peers in different environments.

---

### Roadmap

*   **DHT (Kademlia)**: Planned for global peer discovery across the public internet without the need for central registry or bootstrap nodes.
*   **Bootstrap Nodes**: Configuration for static entry points into the network.
