# TCP vs UDP 

## What TCP and UDP Actually Are?

TCP (Transmission Control Protocol)

TCP is a stateful, connection-oriented transport protocol.
Its primary goal is reliable and ordered data delivery, even if that introduces additional latency and overhead.

TCP explicitly tracks the state of a connection between two endpoints, which means both sides must agree on when communication starts, how data is acknowledged, and when the connection ends.

Core Characteristics
- Connection setup (3-way handshake):
Establishes a session before data is exchanged.

- Sequence numbers and acknowledgments (ACKs):
Ensure data arrives in the correct order and detect missing segments.

- Retransmission on loss:
Lost packets are resent until acknowledged.

- Flow control (window size):
Prevents a sender from overwhelming a receiver.

- Congestion control:
Dynamically adapts sending rate based on network conditions.

## Security Implication

Because TCP maintains per-connection state, each active or half-open connection consumes memory and processing resources.

This makes TCP vulnerable to state exhaustion attacks, where an attacker forces a system to allocate resources for connections that never fully complete or never terminate properly (e.g., SYN flood scenarios).

# UDP (User Datagram Protocol)

UDP is a stateless, connectionless transport protocol.
It provides a minimal service: sending independent packets without guarantees.

UDP does not track sessions, does not verify delivery, and does not attempt to recover from loss.

## Core Characteristics

-No handshake:
Data can be sent immediately without prior negotiation.

-No delivery guarantee:
Packets may be lost without detection.

-No ordering:
Packets may arrive out of sequence.

-No retransmission:
Lost data is not resent by the protocol.

## Security Implication

Because UDP does not maintain connection state, it is extremely lightweight for both sender and receiver.

This makes UDP cheap to abuse at scale, especially in high-volume attacks such as amplification and reflection attacks, where small requests can trigger disproportionately large responses toward a victim.

## Key Takeaway 

TCP trades performance for control and reliability → creates state-related attack surfaces

UDP trades reliability for speed and simplicity → enables high-volume abuse with minimal cost

Both protocols are fundamental to modern networks, and both introduce distinct and predictable security risks based on how they were designed.

## Visibility & Control (Why This Matters in SOC / Blue Team)

In defensive operations, visibility equals control.
How much a protocol exposes about a conversation directly affects investigation, detection accuracy, and attribution.

| Aspect | TCP | UDP |
|------|-----|-----|
| Session tracking | Easy | Hard |
| Firewall inspection | Deep | Limited |
| IDS/IPS accuracy | High | Lower |
| Attribution | Better | Worse |

## Why These Differences Exist

## Session Tracking

TCP explicitly defines a session lifecycle (start, data exchange, termination).
Security tools can correlate packets into a single conversation using sequence numbers, acknowledgments, and connection states.


UDP has no concept of a session. Each packet is independent, forcing analysts and tools to infer relationships between packets based on timing, IPs, and ports—often unreliably.

## Firewall Inspection

Stateful firewalls can:

- Track TCP connection states (SYN, ESTABLISHED, FIN)

- Allow or block traffic based on connection behavior, not just ports

With UDP, firewalls usually fall back to:

- Basic rules (IP, port, rate)

- Time-based pseudo-state (best effort, not protocol-enforced)

This reduces precision and increases false positives or blind spots.

## IDS / IPS Accuracy

TCP’s structure allows detection systems to:

- Reassemble streams

- Detect anomalies in sequencing or retransmissions

- Match signatures reliably across sessions

UDP traffic:

- Lacks ordering and retransmission

- Often arrives fragmented or incomplete

- Makes payload reconstruction inconsistent

As a result, detection confidence is generally lower.
