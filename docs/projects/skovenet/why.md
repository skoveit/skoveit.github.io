# I Built a C2 Framework Where the Network Itself Is the Infrastructure

_A deep dive into [SkoveNet](https://github.com/skoveit/skovenet), a fully decentralized command and control framework for red team operations._

---

Every C2 framework ever built shares one assumption: there is a server somewhere. A teamserver. A redirector. A domain. Something fixed that the operator controls and the agents call back to. Cobalt Strike, Sliver, Havoc, Metasploit, all of them. The entire ecosystem, commercial and open source alike, is built on the same centralized client-server model that has existed since the beginning.

I wanted to see what happens when you remove that assumption entirely.

SkoveNet has no teamserver. No fixed infrastructure. No domain to seize or IP to block. The agents form a self-healing P2P mesh, commands propagate through GossipSub broadcast, and the operator is simply whoever holds the cryptographic private key, connectable from anywhere in the network.

As far as I know, no C2 framework has been built this way before.

---

## When Does This Actually Make Sense?

Centralized C2 is a good architecture. It's predictable, mature, and the right tool for most engagements. SkoveNet isn't trying to replace it. It covers the cases where the centralized model creates operational problems that redirectors and domain fronting can't fully solve.

**Long-duration operations across distributed targets.** When agents are spread across multiple regions and the engagement spans weeks or months, a single teamserver becomes a fragile dependency. Network partitions happen, infrastructure gets disrupted. A mesh that self-heals removes that problem entirely.

**Accurate APT adversary emulation.** Advanced persistent threats are moving away from static C2 infrastructure. Emulating their TTPs accurately means emulating their architecture too, and that architecture is trending toward decentralized peer-to-peer patterns.

**Operations where infrastructure attribution is the primary risk.** When the concern isn't just agent detection but operator traceability, a model with no fixed server and no origin IP changes the threat model at a fundamental level. There's no infrastructure to seize because there is no infrastructure.

**As a force multiplier alongside your existing C2.** This is one I didn't anticipate when I started building it, but it might be interesting use case.

The scenario: you land on one machine using Sliver or Cobalt Strike. You do your lateral movement once, drop a SkoveNet agent on every machine you reach, and let the mesh form across the network. Now you stop thinking about lateral movement entirely.

Sliver controls one computer. SkoveNet controls the whole network.

From this point, You don't need to pivot again, you don't need to re-do lateral movement, you just operate. And because the mesh is self-healing and has no central server to burn, even if you lose your initial foothold completely, the mesh is still alive. You reconnect through any node in the network and you're back in.

---

## The Architecture

SkoveNet is written in Go on top of **libp2p** and has three components:

| Component      | Role                                                |
| -------------- | --------------------------------------------------- |
| **agent**      | P2P node that joins the mesh and executes commands  |
| **controller** | Operator CLI — connects to the local agent via IPC  |
| **sgen**       | Standalone agent generator — no Go toolchain needed |

The controller is just CLI that talks to a local agent over IPC. You join the mesh from any machine, hook the controller into that local agent, and broadcast signed commands to the entire network.

![](https://github.com/user-attachments/assets/5f2961b9-461d-4824-9ad4-2f22753b7614)

---

## Core Engineering Decisions

Designing a C2 without a central server means rethinking how trust and routing work from scratch.

### 1. The Rule of 5

Each node connects to a maximum of 5 peers. This is a deliberate tradeoff between mesh connectivity and stealth. Fewer connections means a smaller traffic footprint, lower detectability, and reduced bandwidth. When a node dies, the mesh organically self-heals by establishing new links around the dead zone.

### 2. Cryptographic Trust, Not Network Trust

The operator is **whoever holds the Ed25519 private key**. Commands are signed inside the controller. Agents only hold the public key, baked in at compile time. When a command arrives, the agent verifies the signature and that's it. No IP check, no domain check, no TLS cert.  Compromising one agent gives you nothing.

### 3. GossipSub for Message Propagation

Commands broadcast across the mesh using libp2p's **GossipSub**, the same protocol Ethereum and IPFS use. It combines eager push and lazy pull to handle network churn and partial connectivity without any custom flooding logic. By the time a command reaches its target it has passed through multiple nodes. There is no origin IP to trace.

### 4. Noise Protocol for Encryption

All peer-to-peer traffic is encrypted using the **Noise Protocol Framework**, the same protocol behind WireGuard and Signal. It's not bolted-on TLS. It's baked into the transport layer and gives you forward secrecy and mutual authentication on every connection automatically.

### 5. Zero-Config Local Discovery

Drop an agent on a new subnet and it will automatically discover and peer with other local nodes using mDNS (`_mesh-c2._tcp`), silently bridging disconnected network segments with no configuration needed.

---

## The Agent Generator: sgen

`sgen` is a standalone tool that generates cross-compiled agent binaries without requiring Go on the operator's machine.

```bash
# Generate a Linux agent (auto-creates a new keypair)
./sgen generate --os linux --arch amd64

# Generate a Windows agent with an existing key
./sgen generate --os windows --arch amd64 --key "base64pubkey..."

# Just generate a keypair
./sgen keygen
```

sgen embeds the full agent source and a portable Go toolchain inside itself. When you run it, it extracts the toolchain, patches your Ed25519 public key into the agent source, and cross-compiles the binary for the target platform. The result is a statically-linked, ready-to-deploy agent with your key baked in. No Go compiler needed anywhere.

---

## Operator Workflow

Here's what it actually looks like when you're operating:

```bash
# Join the mesh on your machine
./agent-linux-amd64

# Connect the controller
./controller

> sign <private_key>        # Authenticate as operator
> peers                     # List connected nodes
> use <peerID>              # Select a target

[peerID]> whoami
[peerID]> ls -la /etc
[peerID]> download /etc/shadow
[peerID]> upload payload.sh
[peerID]> background        # Back to global view

> radar                     # Scan the entire network
> graph on                  # Open real-time topology viewer
```

`radar` broadcasts a ping across the mesh and collects latency-measured responses from every reachable node, giving you a real-time map of the entire network.

`graph on` spins up a local web server and renders a live D3.js force-directed graph of the mesh topology by querying the peer connections of every node.

---

## Security Model

|Layer|Implementation|
|---|---|
|Transport Encryption|Noise Protocol on all P2P connections|
|Command Signing|Ed25519 signatures on all operator commands|
|Key Isolation|Private key stays in the controller; agents only hold the public key|
|Local Discovery|mDNS zero-config peer discovery (`_mesh-c2._tcp`)|
|NAT Traversal|UPnP/NAT-PMP port mapping, WebRTC hole punching, Circuit Relay v2|

---

## The Elephant in the Room: NAT Traversal

Operating natively peer-to-peer means wrestling with restrictive corporate firewalls and Symmetric NATs, and P2P hole-punching is notoriously hard in these environments.

Right now SkoveNet handles this through Circuit Relay v2 and manual bootstrap nodes using the `connect <multiaddr>` command. It works, but it still requires a reachable relay node which is a form of infrastructure.

The next step is integrating **STUN/TURN** and **DNS-over-HTTPS tunneling** to eliminate the need for static relays entirely and get to truly 100% infrastructure-less operation. That's the active research problem.

---

## What's Next

The roadmap is broken into phases:

**Phase 1 - WAN:** Identity persistence, bootstrap nodes, DHT discovery, AutoRelay.

**Phase 2 - C2 Capabilities:** Persistence installers, interactive shell, SOCKS proxy.

**Phase 3 - Stealth:** Pluggable transports (WebSocket, DNS, ICMP), jitter/sleep, memory-only operation.

**Phase 4 - Ecosystem:** REST/gRPC API, web dashboard, automated deployment.

---

## Tech Stack

|Layer|Technology|
|---|---|
|Language|Go|
|P2P Networking|libp2p|
|Messaging|GossipSub|
|Transport Encryption|Noise Protocol|
|NAT Traversal|WebRTC, UPnP, NAT-PMP|
|Discovery|mDNS, DHT (planned)|
|Command Auth|Ed25519 signing|
|IPC|Unix domain sockets|
|Cross-compilation|Embedded Go toolchain (sgen)|
|License|GPLv3|

---

## Final Thoughts

We spend so much time building complex redirector setups, domain fronting profiles, and CDN configurations just to protect a central server. By shifting to a decentralized mesh, you eliminate the server entirely. The network survives node loss. The operator identity relies on cryptography, not location.

It's a different way of thinking about post-exploitation infrastructure, and I think it's worth exploring.

The full source, design docs, and roadmap are on GitHub: **[github.com/skoveit/skovenet](https://github.com/skoveit/skovenet)**

If this resonates, a star helps a lot with visibility. Issues, contributions, and feedback are very welcome.

---

_SkoveNet is built for authorized security research and penetration testing only. Unauthorized use against systems you don't own is illegal._