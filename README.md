# cisco-packet-tracer-2-router-dhcp-static-routing
Cisco Packet Tracer Lab: Two-Router Network with DHCP and Static Routing
# Cisco Packet Tracer Lab: Interconnecting Two Routers with DHCP and Static Routing

## Objective

The objective of this lab is to configure two Cisco 4331 routers, connect multiple LAN networks through switches, provide automatic IP addressing using DHCP, and establish communication between end devices located on different routers.

---

## Network Topology

### Devices Used

* 2 × Cisco 4331 Routers
* 4 × Switches (2 connected to each router)
* 12 × End Devices (3 connected to each switch)
* NIM-2T Modules for serial communication between routers

### Topology Overview

Router R1:

* Switch 1 → 3 End Devices
* Switch 2 → 3 End Devices

Router R2:

* Switch 3 → 3 End Devices
* Switch 4 → 3 End Devices

The two routers are interconnected through serial interfaces using NIM-2T modules.

---

## Router R1 Configuration

### Basic Configuration

Changed the router hostname:

```bash
Router(config)# hostname R1
```

Configured privileged EXEC password:

```bash
R1(config)# enable password abc
```

### LAN Interface Configuration

Configured GigabitEthernet interfaces:

```bash
R1(config)# interface g0/0/0
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown

R1(config-if)# exit
R1(config)# interface g0/0/1
R1(config-if)# ip address 192.168.2.1 255.255.255.0
R1(config-if)# no shutdown
```

### DHCP Configuration

DHCP pools were created for both LAN networks. This allowed end devices to automatically obtain:

* IP Address
* Subnet Mask
* Default Gateway

No static IP configuration was required on the PCs.

```bash
R1(config-if)# exit
R1(config)# ip dhcp pool LAN1
R1(dhcp-config)# network 192.168.1.0 255.255.255.0
R1(dhcp-config)# default-router 192.168.1.1

R1(dhcp-config)# exit
R1(config)# ip dhcp pool LAN2
R1(dhcp-config)# network 192.168.2.0 255.255.255.0
R1(dhcp-config)# default-router 192.168.2.1
```

---

## Router R2 Configuration

### Basic Configuration

Changed the router hostname:

```bash
Router(config)# hostname R2
```

### LAN Interface Configuration

Configured GigabitEthernet interfaces:

```bash
R2(config)# interface g0/0/0
R2(config-if)# ip address 192.168.4.1 255.255.255.0
R2(config-if)# no shutdown

R2(config-if)# exit
R2(config)# interface g0/0/1
R2(config-if)# ip address 192.168.3.1 255.255.255.0
R2(config-if)# no shutdown
```

### DHCP Configuration

DHCP pools were configured for both networks.

As a result, all end devices connected to R2 automatically received IP addresses without manual configuration.

```bash
R2(config-if)# exit
R2(config)# ip dhcp pool LAN1
R2(dhcp-config)# network 192.168.4.0 255.255.255.0
R2(dhcp-config)# default-router 192.168.4.1

R2(dhcp-config)# exit
R2(config)# ip dhcp pool LAN2
R2(dhcp-config)# network 192.168.3.0 255.255.255.0
R2(dhcp-config)# default-router 192.168.3.1
```

---

## Router-to-Router Connection

### Initial Attempt

Initially, the routers were connected using interface:

```bash
g0/0/2
```

However, communication could not be established successfully.

To resolve this issue, NIM-2T modules were installed on both Cisco 4331 routers and a serial connection was used instead.

### Serial Interface Configuration

Router R1:

```bash
R1(dhcp-config)# exit
R1(config)# interface s0/1/0
R1(config-if)# ip address 130.0.0.1 255.255.0.0
R1(config-if)# no shutdown
```

Router R2:

```bash
R2(dhcp-config)# exit
R2(config)# interface s0/2/0
R2(config-if)# ip address 130.0.0.2 255.255.0.0
R2(config-if)# no shutdown
```

---

## DHCP Experiment on Serial Interfaces

An attempt was made to configure Router R2 to obtain an IP address dynamically from Router R1 using:

```bash
R2(config-if)# ip address dhcp
```

However, Cisco Packet Tracer returned an error.

After investigation, it was determined that serial interfaces do not support DHCP client functionality in Packet Tracer. Therefore, static IP addresses were configured manually on both serial interfaces.

---

## Connectivity Testing

### Successful Tests

The following tests were successful:

* End devices within the same LAN network
* End devices in different LANs under the same router
* Router R1 to Router R2 ping tests
* DHCP address assignment to all end devices

### Failed Tests

Communication between:

* End devices connected to R1
* End devices connected to R2

initially failed.

The routers could communicate with each other, but the LAN networks behind each router were unknown to the opposite router.

---

## Static Routing Solution

To allow traffic to reach unknown networks, default static routes were configured.

Router R1:

```bash
R1(config-if)# exit
R1(config)# ip route 0.0.0.0 0.0.0.0 s0/1/0
```

Router R2:

```bash
R2(config-if)# exit
R2(config)# ip route 0.0.0.0 0.0.0.0 s0/2/0
```

### Purpose of Default Route

A default route acts as a gateway of last resort.

If the router does not know the destination network, it forwards the packet through the specified serial interface.

This approach is useful when:

* Destination networks are unknown
* Network topology is simple
* A quick routing solution is required

However, in larger networks, specific static routes or dynamic routing protocols such as RIP, OSPF, or EIGRP are preferred for better performance and scalability.

---

## Final Verification

After configuring the default routes:

* Router-to-router communication remained successful.
* Initial packet transmission between end devices failed.
* Subsequent transmission attempts were successful.
* End devices connected to Router R1 were able to communicate with end devices connected to Router R2.

```bash
R1(config)# end
R1#

R2(config)# end
R2#
```

---

## Conclusion

This lab demonstrated:

* Basic Cisco router configuration
* DHCP server implementation on routers
* Automatic IP assignment to end devices
* Installation and use of NIM-2T serial modules
* Serial interface configuration
* Limitations of DHCP on serial interfaces
* Static default routing between routers
* End-to-end communication across multiple networks

The lab successfully established communication between all networks connected to both routers using DHCP and static routing.
