# Address Resolution Protocol (ARP) | Technical Documentation

## Overview
The **Address Resolution Protocol (ARP)** is a fundamental protocol used in local networks to map an **IP address (Layer 3)** to a **MAC address (Layer 2)**.

In an Ethernet network, devices identify each other logically using IP addresses. However, the actual delivery of data happens using Ethernet frames, and those frames require the MAC address of the destination network interface card (NIC).

Because of that, when a device wants to send data to another device inside the same network, it needs a way to discover:
> *"Which MAC address corresponds to this IP address?"*

That discovery process is handled by ARP. In simple terms, ARP acts like a translation mechanism between IP addresses and physical hardware addresses inside a LAN.

---

## Operational Workflow (How ARP Works)
ARP uses a simple request–reply process to resolve addresses. The process normally follows four steps:

###  ARP Cache Lookup
Before sending any request to the network, the host checks its **ARP cache**. This is a temporary table stored by the operating system that keeps recently discovered mappings. If the mapping exists, the host sends the frame immediately without a new request.

###  ARP Request (Broadcast)
If the MAC address is not found in the cache, the host sends an **ARP Request**. This request is broadcast to the entire network using the Ethernet broadcast address:
`FF:FF:FF:FF:FF:FF`

**The message essentially asks:**
* *"Who has IP 192.168.1.5? Tell 192.168.1.2"*

Because this is a broadcast frame, every device in the same local network receives the request.

###  ARP Reply (Unicast)
The device that owns the requested IP recognizes the request and sends an **ARP Reply** directly back to the requester (**unicast communication**).
* *Example:* "I am 192.168.1.5, and my MAC address is AA:BB:CC:DD:EE:FF"

###  Cache Update
After receiving the reply, the host stores the mapping inside its ARP cache. Once the mapping exists, the host encapsulates the IP packet into an Ethernet frame and sends it to the correct destination.

---

## Network Boundaries
ARP operates **only** within a local network (LAN). Broadcast traffic does not pass through routers.
* **External Communication:** If a host needs to talk to a device outside its network, it does not perform ARP for the remote IP. Instead, it resolves the MAC address of the **Default Gateway (router)**.
* **The Process:** The host sends the frame to the router, which then forwards the packet toward the final destination network.

---

## Security & SOC Perspective
From a security perspective, ARP has a critical limitation: **it lacks authentication**. Most systems accept ARP replies without verification, making the network vulnerable to Layer 2 attacks:

* **ARP Spoofing / Poisoning:** An attacker sends fake ARP responses associating their own MAC address with a legitimate IP (often the gateway).
* **Man-in-the-Middle (MitM):** By poisoning the cache, an attacker can intercept, monitor, or manipulate traffic between two hosts without being noticed.

### Mitigation Techniques
* **Dynamic ARP Inspection (DAI):** Switches validate ARP packets by comparing them with trusted DHCP information.
* **Static ARP Entries:** For critical infrastructure, administrators manually configure fixed IP–MAC mappings.

---

## Hands-on Commands

### Checking the ARP Table
To view the ARP cache on most systems:
```bash
arp -a
