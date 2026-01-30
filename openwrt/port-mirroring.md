# OpenWRT Traffic Mirroring to Suricata (TEE)

## Objective

Configure OpenWRT to mirror all network traffic to a **Suricata IDS** deployed on an **Ubuntu server** using a **private monitoring network**, without interrupting normal routing.

---

## Network Topology

| Interface | Role | IP Address |
|---------|------|------------|
| br-lan | LAN / Public network | 192.168.1.3/24 |
| eth1 | Private monitoring network | 10.0.3.15/24 |
| Ubuntu Server (Suricata) | IDS destination | 10.0.3.15 |

Traffic is mirrored **only** to the private network.

---

## Verify Interfaces and IP Addresses

### Show network interfaces
```bash
ip link show
```

### Show routing table
```bash
ip route
```
<img width="715" height="93" alt="image" src="https://github.com/user-attachments/assets/41962564-3a14-4d4f-8cd6-34111da56fdb" />


## Install Required Packages

### Update repositories:
```bash
opkg update
```

### Install required modules and tools:
```bash
opkg install kmod-ipt-tee iptables-mod-tee
opkg install tcpdump netcat
```

### Configure Traffic Mirroring (iptables TEE)
- Mirror outbound traffic
```bash
iptables -t mangle -A POSTROUTING -j TEE --gateway 10.0.3.15
```
- Mirror inbound traffic
```bash
iptables -t mangle -A PREROUTING -j TEE --gateway 10.0.3.15
```
These rules duplicate packets and send a copy to the Suricata server.
Original traffic continues normally.

### Verify iptables Rules
```bash
 iptables -t mangle -L -v -n
```

<img width="1912" height="457" alt="image" src="https://github.com/user-attachments/assets/7079e8de-a64b-4e00-9bbd-a6b4e2a81827" />


### Test Traffic Mirroring On OpenWRT
```bash
tcpdump -i eth1 -nn
```

### On Ubuntu (Suricata server)
```bash
tcpdump -i <monitoring-interface> -nn  #enp0s10 in my case
```
If traffic is visible, mirroring is working successfully.



