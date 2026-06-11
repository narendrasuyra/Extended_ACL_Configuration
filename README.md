# 🔐 Extended ACL Configuration – Cisco Packet Tracer

A hands-on network security project demonstrating **Extended Access Control List (ACL)** configuration on a Cisco 1841 router using Cisco Packet Tracer. The project enforces granular, service-level traffic policies between two branch offices and a central headquarters server farm.

---

## 📋 Project Overview

This project simulates a real-world enterprise network with three sites — Branch 1, Headquarters, and Branch 2 — connected through WAN links. A single numbered Extended ACL (ACL 101) is configured on the Headquarters router to control exactly which servers each branch can reach and which protocols they may use.

---

## 🗺️ Network Topology

```
Branch 1                  Headquarters                Branch 2
192.168.1.0/24            192.168.2.0/24              192.168.3.0/24

 PC0  PC1               Server-1 HTTPS (2.1)          PC2  PC3
  |    |                Server-2 HTTP  (2.2)            |    |
Switch0                  DNS Server    (2.3)          Switch2
  |                           |                          |
Router0 ──── 12.12.12.0/24 ── Router1 ── 24.24.24.0/24 ── Router2
                           (ACL HERE)
```

---

## 🎯 ACL Policy

| Source | Destination | Protocol / Port | Action |
|---|---|---|---|
| Branch 1 (192.168.1.0/24) | Server-1 HTTPS (192.168.2.1) | TCP / 443 | ✅ Permit |
| Branch 1 (192.168.1.0/24) | Server-2 HTTP (192.168.2.2) | TCP / 80 | ❌ Deny |
| Branch 1 (192.168.1.0/24) | DNS Server (192.168.2.3) | IP | ❌ Deny |
| Branch 2 (192.168.3.0/24) | Server-2 HTTP (192.168.2.2) | TCP / 80 | ✅ Permit |
| Branch 2 (192.168.3.0/24) | DNS Server (192.168.2.3) | IP | ✅ Permit |
| Branch 2 (192.168.3.0/24) | Server-1 HTTPS (192.168.2.1) | IP | ❌ Deny |
| Any | Any | IP | ✅ Permit |

---

## ⚙️ Configuration

### ACL 101 Rules
```
access-list 101 permit tcp 192.168.1.0 0.0.0.255 host 192.168.2.1 eq 443
access-list 101 deny   tcp 192.168.1.0 0.0.0.255 host 192.168.2.2 eq 80
access-list 101 deny   ip  192.168.1.0 0.0.0.255 host 192.168.2.3
access-list 101 deny   ip  192.168.1.0 0.0.0.255 host 192.168.2.2

access-list 101 permit tcp 192.168.3.0 0.0.0.255 host 192.168.2.2 eq 80
access-list 101 deny   tcp 192.168.3.0 0.0.0.255 host 192.168.2.2 eq 443
access-list 101 permit ip  192.168.3.0 0.0.0.255 host 192.168.2.3
access-list 101 deny   ip  192.168.3.0 0.0.0.255 host 192.168.2.1

access-list 101 permit ip any any
```

### Applying to Interfaces
```
interface fa0/0
 ip access-group 101 in

interface se0/0/0
 ip access-group 101 in
```

---

## ✅ Verification Results

| Source | Test | Result |
|---|---|---|
| PC0 / PC1 (Branch 1) | `https://192.168.2.1` | ✔ Accessible |
| PC0 / PC1 (Branch 1) | `http://192.168.2.2` | ✘ Blocked |
| PC0 / PC1 (Branch 1) | DNS Server (192.168.2.3) | ✘ Blocked |
| PC2 / PC3 (Branch 2) | `http://192.168.2.2` | ✔ Accessible |
| PC2 / PC3 (Branch 2) | `https://192.168.2.2` | ✘ Blocked |
| PC2 / PC3 (Branch 2) | DNS Server (192.168.2.3) | ✔ Accessible |
| PC2 / PC3 (Branch 2) | `https://192.168.2.1` | ✘ Blocked |

---

## 📁 Repository Structure

```
📦 Extended-ACL/
├── Main File.pkt          # Cisco Packet Tracer project file
├── README.md              # This file
└── docs/
    ├── topology.png       # Network topology diagram
    └── Extended_ACL_Project_Documentation.docx
```

---

## 🧠 Concepts Covered

- **Extended ACLs** — filter by source IP, destination IP, protocol, and port
- **Wildcard masks** — inverse subnet masks used in ACL matching
- **ACL rule ordering** — first-match logic; specific rules before general
- **ACL placement** — applied inbound on HQ router interfaces facing each branch
- **Implicit deny** — all ACLs end with an invisible `deny any`; explicit `permit ip any any` added for other traffic

---

## 🛠️ Tools Used

- [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer) — Network simulation
- Cisco IOS CLI — Router configuration

---

## 📄 License

This project is for educational purposes. Feel free to use or adapt it for learning and practice.
