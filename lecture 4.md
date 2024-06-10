# lecture 4

# network layer

- transport segments from sending to receiving host

- **sending side:** encapsulates segments into datagrams

- **receiving side**: delivers segments to transport layer
- network layer protocols run in every host, router— **router** examines header fields in all IP datagrams passing through it

### forwarding

- move packets from router’s input to appropriate router’s output

### routing

- determine the route taken by packets from source to destination

## data plane

- local, per-router **forwarding function** that determines how datagram arriving on router input port is forwarded to output port

## control plane

- network-wide logic that determines how datagram is routed among routers along end-end path from source host to destination host

- **traditional routing algorithms**:
    - implemented in routers

- **software-defined networking (SDN)**:
    - implemented in remote servers

> the control plane determines how packets should be forwarded | the data plane actually forwards the packets

### per-router control plane

- individual routing algorithm components in each and every router interact in the control plane

### logically centralized control plane

- a distinct (typically remote) controller interacts with local control agents (CAs)

> **per router**: both forwarding and routing function are constrained within each router | **logically centralized**: data plane and control plane are separate devices

# router architecture

- a **router** is a networking device that forwards data packets between computer networks

- the router determines a packet’s future path by examining the destination IP address of the header and comparing it to the routing database

# input ports

| physical layer | bit-level reception |
| --- | --- |
| data link layer | ethernet |
| decentralized switching | goal: complete input port processing at ‘line speed’

according to header field values, lookup output port using forwarding table in input port memory (“match plus action”)

queuing: if datagrams arrive faster than forwarding rate into switch fabric |

- **destination-based forwarding**:
    - forward based only on destination IP address (traditional)

- **generalized forwarding**:
    - forward based on any set of header field values

### destination-based forwarding

- routers forward datagrams based on the specified destination IP address, without considering the source (focussing only on endpoint)

## longest prefix matching

- when looking for forwarding table entry for given destination address, use longest address prefix that matches destination address
    - longest prefix matching refers to an algorithm used by routers in IP networking to **select an entry from a routing table**

- DA: 11001000 00010111 00010**110 10100001**

> all computers on one network have the same ip prefix — routers basically look at the destination address’ IP prefix, search the forwarding table for a match, then forward the packet to the corresponding hop 

to handle multiple IP addresses, we use the **longest prefix matching rule**— find an entry in a table that has the longest prefix matching with the incoming packet’s destination IP

## switching fabric
- transfer packet from input buffer to appropriate output buffer
- **switching rate**: rate at which packets can be transferred from inputs to outputs
    - often measured as multiple of input/output line rate

- switching fabric is a network topology in which network nodes interconnected via one or more network switches

## switching via memory

- first generation routers:
    - traditional computers with switching under direct control of CPU
    - **packet copied to system’s memory**
    - speed limited by memory bandwidth (**2 bus crossings per datagram**)

## switching via bus

- datagram from input port memory to output port memory via a **shared bus**
- **bus contention**: switching speed limited by bus bandwidth
    - **cisco 5600**: 32 gbps bus - sufficient speed for access and enterprise routers

## switching via interconnection network

- overcome bus bandwidth limitations
- banyan networks, crossbar, other interconnection nets initially developed to connect processors in multiprocessor
- **advanced design**: fragmenting datagram into fixed length cells, switch cells through the fabric
    - cisco 12000: switches 60 gbps through the interconnection network
    
## input port queueing

- fabric slower than input ports combined -> **queueing may occur at input queues**
    - queueing delay and loss due to input buffer overflow!
- **head-of-the-line** **(HOL) blocking**: queued datagram at front of queue prevents others in queue from moving forward
    - HOL occurs if there is a single queue of data packets waiting to be transmitted, and the packet at the head of the queue cannot move forward due to congestions

# output ports

- buffering required when datagrams arrive from switch fabric is faster than the transmission rate
- **scheduling discipline** chooses the datagram among queued datagrams for transmission

## output port queueing

- buffering when arrival rate via switch exceeds output line speed
- queueing (delay) and loss due to **output port buffer overflow**!

# IPv4

- **IP address**: 32-bit identifier for host, router interface
- **interface**: connection between host/router and physical link
    - router typically has multiple interfaces
    - host typically has one or two interfaces (e.g., wired ethernet, wireless 802.11)
- **IP addresses are associated with each interface**

## ip classful addressing

- an **address space** is the total number of addresses that can be used
- in **classful addressing**, the address space is divided into five classes: A, B, C, D, and E
    - each of these classes have a valid range of IP address

- the class of IP addresses is used to determine the number of bits used for network ID and host ID and the number of total networks and hosts possible in that particular class

## special addresses

- some parts of the address space in class A, B, C reserved for special addresses

| special address  | netid | hostid | source or destination |
| --- | --- | --- | --- |
| network address | specific  | all 0s | none |
| direct broadcast address | specific  | all 1s | destination |
| limited broadcast address | all 1s | all 1s | destination  |
| this host on this network  | all 0s | all 0s | source |
| specific host on this network   | all 0s | specific  | destination  |
| loopback address | 127 | any | destination  |

## mask

- a **mask** is a 32-bit binary number
    - it can bitwise AND with an IP address to get the **network address**

| class | mask in binary | mask in dotted decimal  |
| --- | --- | --- |
| A | 11111111 00000000 00000000 00000000 | 255.0.0.0 |
| B | 11111111 11111111 00000000 00000000 | 255.255.0.0 |
| C | 11111111 11111111 11111111 00000000 | 255.255.255.0 |

## subnets

- device interfaces with same netid part of IP address that can **physically reach each other without intervening router**
    - computers that belong to the same subnet are addressed with an identical group of its most significant bits of their IP address
- **to determine subnets**: detach each interface from its host or router, creating islands of **isolated networks** — each isolated network is a subnet

# fragmentation

- network links have MTU (**max. transfer size**): largest possible link-level frame
    - different link types have different MTUs
- large IP datagram is divided (“fragmented”) within net where **one datagram becomes several datagrams that are “reassembled” only at final destination**
- **IP header bits** are used to identify the order of related fragments

> in: one large datagram
out: 3 smaller datagrams

## how to get an ip address?

- allocated manually and hard-coded by system administrators in a file
- **dynamic host configuration protocol** **(DHCP)**: dynamically get address from server “plug-and-play”

# DHCP

- goal is to allow host to **dynamically obtain its IP address** from network server when it joins network
- DHCP has a pool of available addresses
    - when a **request arrives**, DHCP pulls out the next available address and assigns it to the client for a time period
    - when a **request comes in from a client**, DHCP server first consults the static table
- DHCP is great when devices and IP addresses change
    - can renew its lease on address in use
    - allow reuse of addresses (only hold address while connected/“on”)
    - support for mobile users who join network at ad hoc
- DHCP overview
    
    
    | host broadcasts “DHCP discover” msg [optional] |
    | --- |
    | DHCP server responds with “DHCP offer” msg [optional] |
    | host requests IP address: “DHCP request” msg |
    | DHCP server sends address: “DHCP ack” msg |

- DHCP can return more than just allocated IP address on subnet:
    - address of first-hop router for client
    - name and IP address of DNS server
    - network mask (indicating network versus host portion of address)

> **dynamic host configuration protocol** is a client/server protocol that automatically provides an IP host with its IP address and other related configuration information such as the subnet mask and default gateway

### example

- connecting laptop needs its IP address, address of first-hop router, address of DNS server: use DHCP
- DHCP request encapsulated in UDP, encapsulated in IP, encapsulated in 802.3 ethernet
    - ethernet frame broadcast (dest: FFFFFFFFFFFF) on LAN, received at router running DHCP server
- ethernet decapsulated to IP decapsulated to UDP decapsulated to DHCP

- DHCP server formulates DHCP ACK containing client’s IP address, IP address of first-hop router, name & IP address of DNS server
- encapsulation of DHCP ACK, frame is forwarded to client and decapsulated up to DHCP at client
- client now knows its IP address, name and IP address of DNS server, IP address of its first-hop router

> how can an ISP get a block of addresses?

**ICANN**: internet corporation for assigned names and numbers
- allocates addresses
- manages DNS
- assigns domain names, resolves disputes
