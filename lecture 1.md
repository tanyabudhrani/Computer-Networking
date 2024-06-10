# lecture 1

# the internet

## nuts and bolts view
- billions of connected computing devices
    - hosts = end systems
    - running network apps
- communication links are made with fiber, copper, radio, satellite with a transmission rate known as ‘bandwidth’
- **packet switches** are forward packets (chunks of data) made of routers and switches

## service view
- infrastructure that provides services to applications
    - e.g. web, volP, email, games, e-commerce
- provides programming interface to app programs that allow sending and receiving app programs to connect to internet

| internet: network of networks | interconnected ISPs |
| --- | --- |
| protocols: control sending and receiving of messages | TCP, IP, HTTP, skype, 802.11 |
| internet standards | RFC: request for comments
IETF: internet engineering task force |

## protocols
- protocols define format, order of messages sent and received among network entities, and actions taken on message transmission
- **network protocols**:
    - entities — machines rather than humans
    - all communication activities in the internet are governed by protocols

# network structure
- **network edge**:
    - hosts are clients and servers (data centers)
- access networks and physical media using wired or wireless communication links
- the core of a network are the interconnected routers and the network of networks

> how to connect end systems to edge router? 
- residential access nets
- institutional access nets 
- mobile access nets

## access network: digital subscriber line (DSL)
- use existing telephone line to central office DSLAM
    - data over DSL phone line goes to internet
    - voice over DSL phone line goes to telephone net
    - DSL has dedicated access to central office

- < 2.5 MBPS upstream transmission rate

- < 24 MBPS downstream transmission rate

> DSLAM stands for **digital subscriber line access multiplexer**, and it is a network device located in the telephone company’s central office — these telephone lines are used to connect individual homes to the central office

DSL or digital subscriber line technology allows data to be transmitted over existing telephone lines at higher speeds than traditional voice calls — simultaneously, voice signals can also be transmitted over the same DSL phone line

## access network: cable network
- **HFC**: hybrid fiber coax — a broadband network architecture that combines both fiber-optic cables and coaxial cables
    - **asymmetric**: refers to the fact that the downstream (data from the internet to the user) and upstream (data from user to the internet) transmission rates are not the same
    - up to 30 MBPS downstream transmission rate, 2 MBPS upstream transmission rate
    
- the network involves a combination of **fiber-optic** (used for the backbone of the network to provide high speed) and **coaxial** (used for the last-mile connection from local distribution point to individuals) cables
- **cable headend**: a facility where multiple cable systems come together
- **frequency division multiplexing**: different channels transmitted in different frequency bands

## enterprise access networks (ethernet)
- ethernet is a family of wired networking technologies used to define the standards of how data packets are placed on the network medium, transmitted between devices, and received
    - 10 MBPS - 10 GBPS transmission rates
      
> ethernet can operate over various physical media, including twisted pair, fiber-optic, and coaxial cables — today, end systems typically connect into ethernet switch

## wireless access network
- connects **end system to router** via base station (access point)

- **wireless LANs:**
    - within short range (100 meters)

- **wide-area wireless access:**
    - provided by telco (cellular) operator

## physical media

| signal  | propagates between transmitter and receiver pairs  |
| --- | --- |
| physical link | lies between transmitter and receiver  |
| guided media | signals propagate in solid media by copper, coax, and fiber  |
| unguided media | signals propagate freely (e.g. radio)  |
| twisted pair | two insulated copper wires (category 5 is 100 MBPS, 1 GPS, while category 6 is 10 GBPS)  |

### coaxial cable
- two concentric copper conductors
- **bidirectional**
- broadband
    - multiple channels on cable
    - HFC

### fiber optical cable
- glass fibers carrying light pulses
- **high speed operation:**
    - high speed point to point transmission
- **low error rate:**
    - repeaters spaced far apart are immune to electromagnetic noise

### radios
- signals carried in electromagnetic spectrum using no physical “wire”
- **bidirectional**
- propagation environment effects are reflection, obtrusion by objects, interference

| terrestrial microwave | up to 45 MBPS channels |
| --- | --- |
| LAN | wifi 6 — 1.2 GBPS |
| wide-area | 4G ~ 10 MBPS
5G ~ 1 GBPS  |
| satellite | KBPS to 45 MBPS channels for multiple smaller channels with a 270 msec end-end delay 

geosynchronous vs. low altitude orbit |

## hosts: sends/receives packets of data
- takes application message, breaks into smaller chunks known as packets of length L bits, transmit packet into access network at transmission rate R
    - link transmission rate aka **link bandwidth** aka link capacity
- **packet transmission delay** = time needed to transmit L-bit packet into link = L(bits)/R(bits/sec)

# network core
- mesh of interconnected routers
- **packet-switching**:
    - hosts break application-layer messages into packets
    - forward packets from one router to the next, across links on path from source to destination
    - each packet transmitted at full link capacity

## packet-switching: store and forward
- entire packet must arrive and be stored at router before it can be transmitted on next link
- takes **L/R seconds** to transmit L-bit packet into link at R bps
- end-to-end delay = **2L/R** (assuming 0 propagation delay)

### one-hop numerical example
- L = 7.5 Mbits
- R = 1.5 MBPS
- one-hop transmission delay = 5 seconds

## packet-switching: queuing delay, loss
- if arrival rate (in bits) exceeds transmission rate of link for a period of time, packets will queue at router and wait to be  transmitted on link, although packets can be dropped if memory buffer fills up

## two key network-core functions
- **routing**: determines source-destination route taken by packets
- **forwarding**: move packets from router’s input to appropriate router’s output

## circuit switching
- method of communication where a dedicated communication circuit is established between two devices for the duration of their conversation
    - in the diagram, each link has four circuits, call gets second circuit in top link and first circuit in right link
- **dedicated resources**: no sharing — meaning that, circuit segment is idle if not used by call
- commonly used in traditional telephone networks

### packet switching vs. circuit switching
- packet switching allows more users to use network
- **example:** 1 MBPS link — each user uses 100 KBMS when “active” and they’re active 10% of the time
    - **circuit switching**: 10 users
    - **packet switching**: with 35 users, probability > 10 active users at the same time is less than 0.0004
    - $P = ∑(^3(^5(i))=1_1 C(35, i) 0.1^ix0.9(^3(^5(^-(^i)))$
- packet switching is great for bursty data since it uses resource sharing, is simpler, and has no call setup
    - excessive congestion possible for packet delay and loss, but requires protocols for reliable data transfer and congestion control

> in circuit switching, we use a dedicated path established for the entire communication, where resources are reserved for the duration of the circuit | in packet switching, data is split into packets and sent independently using dynamic resource allocation
> 

## internet structure: network of network

- end systems connect to internet vis access ISPs (internet service providers)
    - residential, company, and university ISPs
- access ISPs in turn must be interconnected so that any two hosts can send **packets** to each other
- resulting network of networks is very complex

### example

- given millions of access ISPs, how do we connect them together?
    - one option is to connect each ISP to every other access ISP so that customer ISPs and provider ISPs have an economic agreement
- but if one global ISP is viable business, there will be competitors which must be interconnected
- and regional networks may arise to connect access nets to ISPs while content provider networks may run their own networks to bring services and content close to end users

- at the center, there is a small number of well-connected large networks
    - **tier-I**: commercial ISPs, national and international coverage
    - **content provider network**: private network that connects its data centers to internet, often bypassing tier-I, regional ISPs

# delay and loss
- packets queue in router buffers
    - packet arrival rate to router (temporarily) exceeds output link capacity which results in packets being queued, waiting for transmission

## four sources of packet delay

| d[proc]: nodal processing | check bit errors, determine output link, typically < msec |
| --- | --- |
| d[queue]: queuing delay | time waiting at output link for transmission which depends on congestion level of router  |
| d[trans]: transmission delay | L: packet length (bits), r: link bandwidth (bps)-- d[trans] = L/R |
| d[prop]: propagation delay | d: length of physical link, s: propagation speed ($3x10^8$)-- d[prop] = d/s |

### queueing delay (revisited)

- R: link bandwidth
- L: packet length
- a: average packet arrival rate

- **La/R ~ 0**: average queueing delay small

- **La/R = 1**: average queueing delay large

- **La/R > 1**: move “work” arriving than can be serviced

## packet loss
- router buffer has finite capacity
- packets arriving to full queue will be dropped however, lost packets may be retransmitted by previous node, by source end system, or not at all

## throughput
- rate at which bits are transferred between the receiver or sender
    - **instantaneous**: rate at given point in time
    - **average**: rate over longer period of time

- **bottleneck link**: link on end-end path that constrains end-end throughput

### internet scenario
- per-connection end-end throughput (min $R_c$, $R_S$, R/10) — in practice, $R_c$ or $R_S$ is often the bottleneck

# protocol layers
- networks are complex with many pieces
    - host
    - routers
    - links of various media
    - applications
    - protocols
    - hardware, software

### example
- a series of steps of air travel
    
    | ticket (purchase) | ticket (complain) |
    | --- | --- |
    | baggage (check) | baggage (claim) |
    | gates (load) | gates (unload) |
    | runway takeoff | runway land  |
    | airplane routing | airplane routing  |

- **layers**: each layer implements a service (function)
    - via its own internal-layer actions
    - relying on services provided by layer below
    - providing services to layer above

## why layering?
- helps deal with complex systems — explicit structure allows us to clearly identify the relationship of complex system’s pieces
    - **modularization** eases maintenance and updates so that changes to implementation of one layer’s service is transparent to the rest of the system

## internet protocol stack

| application | supporting network applications | FTP, SMTP, HTTP |
| --- | --- | --- |
| transport | process-process data transfer | TCP, UDP |
| network | routing of datagrams from source to destination | IP, routing protocols |
| link | data transfer between neighboring network elements | ethernet, 802.11, wifi, PPP |
| physical | bits on the wire |  |

## ISO/OSI reference model

| application |
| --- |
| presentation — allows applications to interpret meaning of data (e.g. encryption, compression, machine-specific conventions) |
| session — synchronization, checkpointing, recovery of data exchange |
| transport |
| network |
| link |
| physical |
