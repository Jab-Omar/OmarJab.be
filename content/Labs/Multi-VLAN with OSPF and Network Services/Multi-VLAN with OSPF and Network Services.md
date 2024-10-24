# 🚀 Objective

In this lab, I took my networking skills to the next level by configuring **DHCP** across multiple **VLANs** within two separate LAN environments. The challenge didn't stop there—I also implemented advanced network services like **NTP**, **Syslog**, and ensured secure management with **SSH**. Why stick to the basics when you can go beyond?

![[Multi-VLAN with OSPF and Network Services.png|Logical Topology of the Multi-VLAN Network]]

---

### ⚙️ Key Configurations:

1. **DHCP for Multiple VLANs**  
   In this setup, I made sure each VLAN in two separate LANs had DHCP up and running. By configuring `ip helper-address`, devices in different VLANs could seamlessly receive their IP addresses without any manual input. Efficiency? Check!

2. **OSPF: Dynamic Routing, Simplified**  
   I chose **OSPF** as the dynamic routing protocol because why not use something fast and efficient? Each VLAN and LAN communicated smoothly, thanks to OSPF’s routing magic. For added security, I used passive interfaces where needed.

3. **NTP with MD5: Time’s on My Side**  
   Time synchronization is critical for network management, so I added an NTP server and went the extra mile by securing it with **MD5 authentication**. Now, all devices are on the same page—er, clock.

4. **Syslog: Logs in One Place**  
   Centralized logging? Yes, please. I set up a Syslog server to collect logs from all my Cisco devices, making it easier to monitor and troubleshoot from one spot. Let the log-tracking begin!

5. **SSH: Secure and Simple**  
   What’s a modern network without security? I enabled **SSH** on all Cisco devices so I could manage them securely. After testing SSH connections across the network, I could rest easy knowing everything was locked down.

---

## 🛠️ Challenges & How I Solved Them:

- **Addressing Chaos**  
   At first, my addressing table was a mess—let’s just say it wasn’t my finest hour. I had to step back, create a new table for the management VLAN, and rethink my strategy. Lesson learned: better planning upfront equals fewer headaches later.  
   
- **DHCP Frustrations**  
   Clients couldn’t get IPs, and I wasn’t happy about it. After tracing DHCP discover packets, I realized the problem: `ip helper-address` wasn’t on all subinterfaces. Once I corrected this oversight, the DHCP magic worked flawlessly.

- **SSH Problems? VLAN Was Down**  
   I couldn’t SSH into the switch. Turns out the management VLAN was down. A quick `no shutdown` on the interface brought it back to life, and SSH was up and running. Crisis averted!

- **DNS Confusion**  
   A quick reminder: Cisco devices need `ip name-server` to use DNS. After typing in the command, I could ping devices by hostname, making network management a breeze.

- **Packet Tracer Limits**  
   Some logs weren’t getting sent due to Packet Tracer’s limitations. To get the full picture, I’ll need to move to real hardware or a better simulator. Note to self: time to test this in the real world!

---

## Addressing Table

This addressing table was created based on an initial schema that divided the VLANs according to network size requirements:

![[Multi-VLAN with OSPF and Network Services schema.png]]

- **R1 LAN VLANs**:
    - **VLAN A**: Needed 80 IP addresses
    - **VLAN B**: Needed 30 IP addresses
    - **VLAN C**: Needed 16 IP addresses
- **R2 LAN VLANs**:
    - **VLAN D**: Needed 400 IP addresses
    - **VLAN E**: Needed 200 IP addresses

**Note:** Initially, VLAN F wasn’t planned, but I added it later to accommodate two servers (NTP, Syslog, DNS) and challenge myself.

| **Device** | **Interface** | **IP Address** | **Subnet Mask** | **Gateway**  | **VLAN**    |
| ---------- | ------------- | -------------- | --------------- | ------------ | ----------- |
| **R1**     | G0/0          |                |                 |              |             |
|            | G0/0.10       | 172.16.4.1     | 255.255.255.128 |              | 10 (A)      |
|            | G0/0.20       | 172.16.4.129   | 255.255.255.224 |              | 20 (B)      |
|            | G0/0.30       | 172.16.4.161   | 255.255.255.224 |              | 30 (C)      |
|            | G0/0.100      | 172.16.4.193   | 255.255.255.248 |              | 100 (Mgmt)  |
|            | G0/1          | 172.16.5.1     | 255.255.255.248 |              |             |
|            | lo1           | 1.1.1.1        | 255.255.255.255 |              |             |
| **R2**     | G0/0          |                |                 |              |             |
|            | G0/0.40       | 172.16.0.1     | 255.255.254.0   |              | 40 (D)      |
|            | G0/0.50       | 172.16.2.1     | 255.255.255.0   |              | 50 (E)      |
|            | G0/0.60       | 172.16.3.6     | 255.255.255.248 |              | 60 (F)      |
|            | G0/0.100      | 172.16.3.9     | 255.255.255.248 |              | 100 (Mgmt)  |
|            | G0/1          | 172.16.5.2     | 255.255.255.248 |              |             |
|            | lo1           | 2.2.2.2        | 255.255.255.255 |              |             |
| **R3**     | G0/0          | DHCP           | DHCP            |              |             |
|            | G0/1          | 172.16.5.3     | 255.255.255.248 |              |             |
|            | lo1           | 3.3.3.3        | 255.255.255.255 |              |             |
| **PC-A**   | NIC           | DHCP           | 255.255.255.128 | 172.16.4.1   | 10 (A)      |
| **PC-B**   | NIC           | DHCP           | 255.255.255.224 | 172.16.4.129 | 20 (B)      |
| **PC-C**   | NIC           | DHCP           | 255.255.255.224 | 172.16.4.161 | 30 (C)      |
| **PC-D**   | NIC           | DHCP           | 255.255.255.0   | 172.16.2.1   | 50 (E)      |
| **PC-E**   | NIC           | DHCP           | 255.255.254.0   | 172.16.0.1   | 40 (D)      |
| **Syslog** | NIC           | 172.16.3.1     | 255.255.255.248 | 172.16.3.6   | 60 (F)      |
| **DNS**    | NIC           | 172.16.3.2     | 255.255.255.248 | 172.16.3.6   | 60 (F)      |
| **ISP**    | G0/0          | 209.165.0.1    | 255.255.255.252 |              |             |
| **Google** | /             | 8.8.8.8        |                 |              |             |
| **S1**     | VLAN 100      | 172.16.4.194   | 255.255.255.248 | 172.16.4.193 | 100 (Mgmt)  |
| **S2**     | VLAN 100      | 172.16.3.10    | 255.255.255.248 | 172.16.3.9   | 100 (Mgmt)  |
| **S3**     | VLAN 1        | 172.16.5.4     | 255.255.255.248 | 172.16.5.3   | 1 (Dafault) |


---

## Configuration Guide for Devices (R1, R2, R3, S1, S2, S3)

#### 1. **Basic Configuration**
- This section applies to all devices and includes initial settings such as device names, banners, and name-server configurations.

```bash
# Basic Configuration (applies to R1, R2, R3, S1, S2, S3)

en
conf t
hostname <Device-Name>   # Sets the hostname (R1, R2, R3, etc.)
banner motd ###only authorized personnel can access this device###   # Security banner
ip name-server 172.16.3.2   # Sets the DNS server for all devices (Syslog/DNS server)
```

#### 2. **SSH Configuration**
- SSH is used for secure remote access to the devices. The following commands configure SSH access and a user login.

```bash
# SSH Configuration (applies to R1, R2, R3, S1, S2, S3)

ip domain-name omar.be   # Set the domain name for SSH
username omar secret cisco   # Creates a user 'omar' with the password 'cisco'
crypto key generate rsa modulus 1024   # Generate RSA keys for SSH
line vty 0 15
 transport input ssh   # Allow SSH connections
 login local   # Use local database for login
 logging synchronous   # Synchronize log messages with user input
```

For switches (S1, S2, S3), the VLANs are configured:

```bash
# Switch (S1, S2, S3) Configuration for SSH

int vlan <VLAN-ID>
ip address <Switch-IP> <Subnet-Mask>   # Assign an IP to the VLAN interface
no shutdown   # Bring up the interface
```

#### 3. **NTP (Network Time Protocol) Configuration**
- This section ensures that all devices synchronize their clocks using an NTP server, providing time accuracy across the network.

```bash
# NTP Configuration (applies to R1, R2, R3, S1, S2, S3)

ntp authenticate   # Enables NTP authentication
ntp authentication-key 1 md5 cisco123   # Sets the authentication key
ntp trusted-key 1   # Marks the key as trusted
ntp server 172.16.3.2 key 1   # Specifies the NTP server (172.16.3.2) with key 1
ntp update-calendar   # Updates the device’s clock
```

#### 4. **Syslog Configuration**
- Syslog is configured to send logs to the Syslog server (172.16.3.1), which is important for network monitoring and troubleshooting.

```bash
# Syslog Configuration (applies to R1, R2, R3, S1, S2, S3)

logging host 172.16.3.1   # Send log messages to the syslog server
logging trap debugging   # Send debug-level messages
logging synchronous   # Prevent log messages from disrupting user input
```

#### 5. **Interface and VLAN Configuration**
- Routers: Each subinterface is configured for different VLANs with proper encapsulation and IP addresses.
- Switches: VLANs are assigned, and access or trunk mode is set for each interface.

**Router (R1, R2, R3)**

```bash
# Interface Configuration on Routers (R1, R2, R3)

interface G0/0.<VLAN-ID>
 description <Description of Interface>
 encapsulation dot1Q <VLAN-ID>   # Configures 802.1Q encapsulation for the VLAN
 ip address <IP-Address> <Subnet-Mask>   # Assign an IP address to the subinterface
 ip helper-address 172.16.5.3   # DHCP relay for VLANs (DHCP server: R3)
 no shutdown   # Enable the interface

interface G0/1
 description External Interface
 ip address 172.16.5.<X> 255.255.255.248   # External connection between routers
 no shutdown
```

**Switches (S1, S2, S3)**

```bash
# VLAN Configuration on Switches (S1, S2, S3)

vlan <VLAN-ID>
 name <VLAN-Name>   # Assign a name to the VLAN

interface f0/1
 switchport mode access   # Set the interface in access mode
 switchport access vlan <VLAN-ID>   # Assign the VLAN to the interface
 switchport nonegotiate   # Disable DTP
```

#### 6. **DHCP Configuration**
- DHCP pools are created on the routers to automatically assign IP addresses to devices within specific VLANs.

```bash
# DHCP Pool Configuration on Routers

ip dhcp excluded-address <First-IP> <Last-IP>   # Exclude IPs for static assignments (gateways, servers, etc.)

ip dhcp pool <VLAN-Name>
 network <Network-Address> <Subnet-Mask>   # Define the network range for the pool
 default-router <Gateway-IP>   # Default gateway for devices in the pool
 dns-server 172.16.3.2   # Set the DNS server
```

#### 7. **Security Enhancements**
- Secure configuration measures include shutting down unused interfaces, disabling Dynamic Trunking Protocol (DTP), and disabling CDP for added security.

```bash
# Security Enhancements (applies to R1, R2, R3, S1, S2, S3)

interface range f0/1-24, g0/1-2
 shutdown   # Disable unused interfaces

no cdp enable   # Disable Cisco Discovery Protocol
```

#### 8. **OSPF (Open Shortest Path First) Configuration**
- OSPF is used for dynamic routing between the routers. Each router advertises its networks into the OSPF process.

```bash
# OSPF Configuration (applies to R1, R2, R3)

router ospf 1
 network 172.16.<X>.0 <Wildcard> area 0   # Advertise connected networks
 passive-interface g0/0   # Mark interfaces as passive for OSPF if needed
```

---

### 🏁 Conclusion:  

This lab was more than just practice—it was a deep dive into configuring and optimizing a complex network. From **multi-VLAN DHCP** to **OSPF** routing, **NTP**, and **Syslog**, I got hands-on with core services while troubleshooting like a pro.

### Next steps?  
- **Implement security features** like Port Security, DHCP Snooping, and Dynamic ARP Inspection (DAI) to protect the network from threats.
- Try the setup on **real hardware** or a more powerful simulator for a complete, realistic experience.
- Extend the configuration with **access control lists (ACLs)** for traffic filtering and **SNMP** for network monitoring.

---

### 📎 Resources:
- [Download the Packet Tracer File](https://drive.google.com/file/d/1Srv8G9mBs39VRTwowHS1CT5aW1EaNwiw/view?usp=sharing)  
- Download my [Excel sheet](https://docs.google.com/spreadsheets/d/1qelj3OIkT3ZCXuVmiqebR6FF_rsx-2_r/edit?usp=sharing&ouid=107882186599568955026&rtpof=true&sd=true) with all the commands used throughout the lab.
- [[index|Return to Main Page]]

---
