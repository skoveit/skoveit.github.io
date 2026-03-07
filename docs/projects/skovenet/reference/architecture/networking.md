# Networking Architecture

SkoveNet is built on the **libp2p** stack, providing a modular and resilient peer-to-peer networking layer.

## Transport Layer

The system uses multiple transport protocols to ensure connectivity across different network environments:

*   **TCP**: The primary reliable transport for node-to-node communication.
*   **WebSockets**: Used as a fallback to bypass restrictive firewalls that only permit HTTP/S traffic.
*   **QUIC / WebRTC** (Planned): Currently being evaluated for improved NAT traversal and browser-to-node connectivity.

## Security & Encryption

All network traffic is encrypted and authenticated using the **Noise Protocol Framework**.

*   **Handshake**: Nodes perform a cryptographic handshake to establish secure, encrypted sessions.
*   **Identity (Peer IDs)**: Peer identities are cryptographically derived from **Ed25519** public keys, ensuring that node IDs cannot be spoofed.
*   **Command Signing**: Command messages from the operator are signed using an Ed25519 private key. Implants verify these signatures before execution to prevent unauthorized control.

## Routing: GossipSub Mesh

SkoveNet has migrated from basic flood routing to **GossipSub**, a more efficient and scalable pubsub routing mechanism.

*   **Mesh Maintenance**: Nodes maintain a local "mesh" of connected peers (target degree of 6) to balance bandwidth usage and delivery reliability.
*   **Message Propagation**: Messages are propagated through the mesh using an intelligent gossip mechanism, reducing redundant traffic while ensuring high delivery rates.
*   **Deduplication**: Every message is tracked by ID to prevent loops and redundant processing.

## NAT Traversal

To maintain connectivity behind NATs and firewalls, SkoveNet employs several automated strategies:

1.  **UPnP & NAT-PMP**: Attempts to automatically configure port forwarding on compatible routers.
2.  **AutoNAT**: Nodes utilize peers to determine their own public reachability and IP address.
3.  **Hole Punching**: Integrated libp2p hole punching (DCUtR) attempts to establish direct connections between nodes behind NATs without requiring a relay.
