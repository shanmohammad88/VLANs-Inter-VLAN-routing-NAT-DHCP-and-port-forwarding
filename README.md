
## **🌐 Lab Topology Diagram**

```
                [WAN PC / Internet]
                        |
                 [Router Fa0/0 - Public IP 200.1.1.1]
                        |
                   Router Fa0/1
                        |
                ---------------------
                |                   |
             Switch1              Switch2
        VLAN 10: HR PCs       VLAN 20: Sales PCs
        VLAN 30: IT PCs
```

### Devices:

* **Router**: 1 Cisco router
* **Switch**: 1 Layer 2 switch
* **PCs**: 6 PCs (2 per VLAN)
* **WAN PC**: External PC simulating the Internet
* **Internal Server**: 192.168.10.100 (HR Web Server)

---

## **1️⃣ VLAN Configuration**

### VLANs:

* HR → VLAN 10 → 192.168.10.0/24
* Sales → VLAN 20 → 192.168.20.0/24
* IT → VLAN 30 → 192.168.30.0/24

### Switch Commands:

```bash
enable
configure terminal
vlan 10
 name HR
vlan 20
 name Sales
vlan 30
 name IT

interface range fa0/1 - 2
 switchport mode access
 switchport access vlan 10
interface range fa0/3 - 4
 switchport mode access
 switchport access vlan 20
interface range fa0/5 - 6
 switchport mode access
 switchport access vlan 30

interface fa0/24
 switchport mode trunk
```

---

## **2️⃣ Inter-VLAN Routing (Router-on-a-Stick)**

### Router Sub-interfaces:

```bash
interface fa0/1.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
interface fa0/1.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
interface fa0/1.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
```

---

## **3️⃣ DHCP Configuration**

* Router serves DHCP for HR VLAN (VLAN 10)

```bash
ip dhcp pool HR
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
ip dhcp excluded-address 192.168.10.1 192.168.10.10
```

* Similarly, create pools for Sales and IT VLANs.

---

## **4️⃣ NAT and Port Forwarding**

* Router’s public interface → Fa0/0 (WAN)
* Internal server (HR Web Server) → 192.168.10.100

```bash
interface fa0/0
 ip address 200.1.1.1 255.255.255.0
 ip nat outside
interface fa0/1
 ip nat inside

ip nat inside source static tcp 192.168.10.100 80 200.1.1.1 80
```

* Test from WAN PC → `http://200.1.1.1` → should reach internal web server.

---

## **5️⃣ Verification Commands**

* `show vlan brief` → Check VLANs on switch
* `show ip interface brief` → Router interfaces
* `ping <PC_IP>` → Test inter-VLAN connectivity
* `show running-config` → Verify NAT, DHCP, VLANs



Do you want me to make that file for you?
