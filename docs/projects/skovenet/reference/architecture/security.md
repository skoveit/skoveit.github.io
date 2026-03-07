# Security Architecture

SkoveNet implements a multi-layered security model to ensure the integrity, confidentiality, and authenticity of the mesh network.

## Cryptographic Identity

Every node in the network is uniquely identified by an **Ed25519** public key.
*   **Key Generation**: Nodes generate a persistent Ed25519 keypair upon initial creation.
*   **Peer ID**: The Peer ID is a cryptographic hash of the public key, ensuring that node identities are immutable and cannot be spoofed without the corresponding private key.

## Transport Security

All peer-to-peer communication is secured using the **Noise Protocol Framework**.
*   **Encryption**: Traffic is encrypted end-to-end between nodes.
*   **Handshake**: Nodes utilize a standard Noise handshake to establish authenticated and encrypted sessions before any application data is exchanged.
*   **Integrity**: Every packet is protected against tampering and replay attacks.

## Command Authorization

To maintain control over the mesh, SkoveNet employs a strict signature verification model for command execution.

*   **Operator Sovereignty**: Commands must be signed by an authorized operator's private key before being accepted by a node.
*   **Signature Format**: Payloads are signed using **Ed25519**, covering the command type, source, target, and timestamp to prevent modification or re-routing.
*   **Verification**: The `Handler` verifies the signature of incoming `MsgTypeCommand` messages. If the signature is missing or invalid, the command is immediately discarded.

## Threat Mitigations

| Threat | Mitigation |
| :--- | :--- |
| **Eavesdropping** | End-to-end encryption via Noise Protocol. |
| **Impersonation** | Peer IDs tied to Ed25519 public keys. |
| **Unauthorized Commands** | Explicit signature verification for all command payloads. |
| **Message Tampering** | Authenticated encryption ensures payload integrity. |
| **Network Loops** | TTL and visitation tracking prevent message infinite loops. |
