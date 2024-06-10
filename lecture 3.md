# lecture 3

# transport layer

- provide logical **end-to-end connection** between app processes running on different hosts
- transport layer protocols run in end systems
    - **send side**: breaks app messages into segments, passes to **network layer**
    - **rcv side**: reassembles segments into messages, passes to **app layer**
- more than one transport layer protocol available to apps
    - **internet**: TCP and UDP

## transport layer

- logical connection **between hosts**

## network layer

- logical connection **between processes**

### household analogy

- a child in A’s house sends a letter to a child in B’s house
    
    
    | host | house |
    | --- | --- |
    | process | child |
    | app message | letter |
    | transport protocol | A and B |
    | network protocol | postal service |

## internet transport layer protocols

- **TCP**: reliable, in-order delivery that provides congestion control, flow control, and connection setup

- **UDP**: unreliable, unordered delivery
    - extension of “best-effort” IP
- no services for delay guarantees or bandwidth guarantees

# multiplexing/demultiplexing

- **multiplexing at sender**: gathering of data from multiple application processes of the sender, enveloping that data with a header, and sending them as a whole to the intended receiver

- **demultiplexing at receiver**: delivering received segments at the receiver side to correct app layer processes

## how demultiplexing works

- host receives IP datagrams — each datagram has **source IP address** and **destination IP address**, and each carries **one transport-layer segment** that has source, destination port number
- host uses IP addresses & port numbers to direct segment to appropriate socket

## connectionless demultiplexing

- when host receives **UDP segment**:
    - checks destination port numbers
    - directs UDP segment to socket with that port numbers
- IP datagrams with **same destination port number**, but different source IP addresses and/or source port numbers will be directed to same socket at destination

## connection-oriented demux

- **TCP socket** identified by 4-tuples:
    - source IP address
    - source port number
    - destination IP address
    - destination port number
- **demux**: receiver uses all four values to direct segment to appropriate socket
- server host may support many simultaneous TCP sockets, where each socket identified by its own 4-tuple
- **web servers** have different sockets for each connecting client
    - **non-persistent HTTP** will have different socket for each request

- three segments, all destined to server with IP address B, port 80, are demuxed to different sockets

> **TCP** **is a connection-oriented protocol** that allows communication between two or more computer devices by establishing connections in the same or different networks | **UDP is a connectionless protocol** that allows communication between two or more computer devices without establishing any connection
> 

# UDP: user data protocol

- provide “best effort” service — UDP segments may be lost or delivered out-of-order to application
- connectionless— meaning there no handshaking between UDP sender & receiver
    - each UDP segment is handled independently of others
- UDP used in streaming multimedia apps (loss tolerant, rate sensitive), DNS, SNMP
- **reliable transfer over UDP**: add reliability at application layer (application-specific error recovery)

## UDP checksum

- detect “errors” in transmitted segment

- **sender**:
    - treat segment contents, including header fields, as sequence of 16-bit integers
    - **checksum**: addition (one’s complement sum) of segment contents
    - sender puts checksum value into **UDP checksum field**

- **receiver**:
    - compute checksum of received segment
    - check if computed checksum equals checksum field value:
        - NO - error detected
        - YES - no error detected

# principles of reliable data transfer

- important in application, transport, link layers
    
- characteristics of unreliable channel will determine complexity of **reliable data transfer (rdt) protocol**

## rdt 1.0: reliable transfer over a reliable channel

- underlying channel is perfectly reliable — no bit errors and no loss of packets

- **sender**:
    - sender sends data into underlying channel

- **receiver**:
    - receiver reads data from underlying channel

## rdt 2.0: channel with bit errors

- underlying channel may flip bits in packet
- new mechanisms in rdt2.0 (beyond rdt1.0)
    
    
    | error detection | use checksum to detect bit errors |
    | --- | --- |
    | feedback | send control msgs (ACK,NAK) from receiver to sender |
    | no error scenario | acknowledgements (ACKs): receiver explicitly tells sender that packet received is OK |
    | error scenario | negative acknowledgements (NAKs): receiver explicitly tells sender that packet received has errors
    
    sender retransmits packet upon receipt of NAK |
- what happens if ACK/NAK corrupted?
    - sender doesn’t know what happened at receiver— it can’t just retransmit packet so it may possibly duplicate packet
- **handling duplicates**:
    - sender retransmits current packet if ACK/NAK corrupted
    - sender adds sequence number to each packet
    - receiver discards (doesn’t deliver up) duplicate packet

## rdt 2.1: discussion

- **sender**:
    - **sequence number** added to packet
    - two sequence numbers (0,1) will suffice because we must check if received ACK/NAK corrupted
    - sender must “remember” whether “expected” packet should have sequence numbers of 0 or 1

- **receiver**:
    - store the last packet with sequence number
    - must check if received packet is duplicate
        - check whether 0 or 1 is expected packet sequence number
        

> note: receiver can not know if its last ACK/NAK is received OK at sender

## rdt 2.2: a NAK-free protocol

- same functionality as rdt2.1, using ACKs only
- instead of NAK, **receiver sends ACK for last packet received OK** — receiver must explicitly include sequential number of packet being ACKed
- duplicate ACK at sender results in same action as NAK — **retransmit current packet**

## rdt 3.0: channels with errors and loss

- **new scenario**: underlying channel can also lose packets (data, ACKs)
    - checksum, sequential number, ACKs, retransmissions will have some help but not enough
    

- **approach**: sender waits “reasonable” amount of time for ACK
    - requires countdown timer
- sender retransmits if the expected ACK is not received during this time — **receiver must specify sequence numbers** of packet being ACKed
- if packet (or ACK) is just delayed (not lost), then retransmitted packet will be duplicate → using **sequential number** already handles this

### stop and wait operation

- rdt3.0 is correct, but performance is poor
    - e.g. 1 Gbps link capacity, 15 msec prop. delay, 8Kbit length packet:
        
    - as RTT=30 msec, 8K bits transmitted every 30 msec → 267 kbps throughput over 1 Gbps link bandwidth

## pipelined protocols

- **pipelining**: sender allows multiple, “in-flight”, yet-to-be-acknowledged packets to be transmitted sequentially
    - range of sequence numbers must be increased (**[0,1] => [0,N-1]**)

- **go-back-N**:
    - sender can have up to N unacked packets in pipeline
    - receiver only sends **cumulative ACK**
        - doesn’t ACK packet if there’s a gap in sequential numbers
    - sender has timer for **oldest unacked packet**
        - when timer expires, **retransmit all unacked packets**

- **selective repeat**:
    - sender can have up to N unacked packets in pipeline
    - receiver sends **individual ACK** for each packet
- sender maintains timer for **each unacked packet**
    - when timer expires, **retransmit only that unacked packet**

## go-back-N: sender

- k-bit sequential number in packet header
- “window” of up to N, consecutive unacked packets allowed

- **cumulative ACK**: ACKs all packets up to sequence number N (including sequence number n)
    - sender may receive duplicate ACKs
- sender sets timer for oldest in-flight packet
- **timeout(n**): sender retransmits packet with sequential number and all packets with higher sequential number in window

## go-back-N: receiver

- **ack-only**: receiver always sends ACK for correctly- received packet with highest in-order sequential number
- may generate duplicate ACKs but we need only remember expected sequential number
- out-of-order packets are discarded to prevent buffer
    - send ACK for correctly-received packet **with highest in-order sequential number**

## selective repeat

- **receiver** individually acknowledges all correctly received packets
    - buffers packets, as needed, for eventual in-order delivery to upper layer

- **sender** only resends those packets whose ACKs do not received
    - sender maintains timer for each unacked packet
    
- **sender window**: N consecutive sequential numbers that includes sequence number of sent (both acked and unacked) packets and to be sent packets

### sender

- if next available sequential number is in window, send packet
- **timeout(n)**: resend packet n and restart timer ACK(n) in `[sendbase,sendbase+N-1]`
    - mark packet n as received
    - if n is **smallest unacked packet**, advance window base to next unacked sequential number

### receiver

- packet n is in `[rcvbase, rcvbase+N-1]`
    - send ACK(n)
    - **out-of-order**: buffer
    - **in-order**: deliver (also deliver buffered, in-order packets), advance window to next not-yet-received packet
- packet n is in `[rcvbase-N,rcvbase-1]`
    - send ACK(n)

> as go back N acknowledges the packet cumulatively, it rejects out-of-order packets | as selective repeat supports receiving out-of-order packets (it sorts the window after receiving the packets), it uses independent acknowledgements to acknowledge the packets
> 

### dilemna

- **example**:
    - sequence numbers: 0, 1, 2, 3 | window size = 3

- receiver sees no difference in two scenarios — duplicate data accepted as new

# TCP segment structure

| point-to-point | one sender, one receiver |
| --- | --- |
| connection oriented | handshaking  |
| reliable, in-order byte stream | messages are transmitted in pipeline  |
| full duplex data  | bidirectional data flow in same connection |
| flow control | sender will not overwhelm receiver |
| window size | used for TCP congestion control and flow control  |

- **sequence number**: byte “number” of first byte in segment’s data stream
    - sequence number of next byte expected from other side forms the cumulative ACK


## TCP round trip time, timeout

- how to set TCP timeout value?
    - longer than RTT but RTT varies

- **too short**: premature timeout, unnecessary retransmissions

- **too long**: slow reaction to segment loss

### how to estimate RTT?

- **sampleRTT** is measured time from **segment transmission until ACK receipt**
    - ignore retransmissions

- sampleRTT will vary, want estimated RTT “smoother”
    - average several recent measurements, not just current sampleRTT

- **timeout interval**: estimatedRTT plus “safety margin” wherein a large variation in estimatedRTT = larger safety margin
- estimate sampleRTT deviation from estimatedRTT:

# TCP reliable data transfer

- TCP creates rdt service on top of IP’s unreliable service with the use of pipelined segments, cumulative ACKs, and a single retransmission timer
- **retransmissions** are triggered by timeout events and duplicate ACKs

## TCP sender events

- data received from apps
    - create segment with sequence number
    - sequence number is **byte-stream number of first data byte in segment**
    - start timer if not already running
        - timer for oldest unacked segment
    - **expiration interval**: TimeOutInterval

- timeout
    - retransmit segment that caused timeout and restart timer
- ACK received if ACK acknowledges previously unacked segments
    - update sequential number for segments known to be acked, and **start timer if there still have unacked segments**

## TCP fast retransmit

- the time-out period is often relatively long which causes a long delay before resending lost packet
- **detect lost segments via duplicate ACKs** — sender often sends many segments back-to-back
- if segment is lost, there will likely be many duplicate ACKs

> **triple duplicate ACKs**: if sender receives 3 duplicate ACKs for same data, it resends unacked segment with **smallest sequential number** — since it is likely that unacked segment is lost, sender doesn’t wait for timeout

# TCP flow control

- receiver side of TCP connection has a **receiver buffer**— receiver controls sender, so sender won’t overflow receiver buffer by transmitting too much or too fast
- receiver “advertises” free buffer space by including rwnd value in TCP header of receiver-to-sender segments
    - receiver buffer size is set via socket options (typical default is 4096 bytes)
    
- sender limits unacked (“in-flight”) data to receiver’s rwnd value, and guarantees receive buffer will not overflow

# congestion control

- **congestion**: too many sources sending too much data too fast for network to handle
- manifestations:
    - **lost packets** (buffer overflow at routers)
    - **long delays** (queueing in router buffers)

## cause of congestion: scenario 1

- two senders, two receivers
- one router, infinite buffers
- output link capacity: R
- no retransmission

## cause of congestion: scenario 2

- one router, finite buffers
- sender retransmission of timed-out packet
    - **application-layer input = application-layer output** (λin = λout)
    - transport-layer input includes retransmissions: **λ’in ≥ λin**

- **costs of congestion**:
    - more work (retrans) for given goodput
    - unneeded retransmissions since link carries multiple copies of the packet

## cause of congestion: scenario 3

- four senders
- multihop paths
- timeout/retransmit
- as red λ’in increases, all arriving blue packets at upper queue are dropped, blue throughput → 0

- **another cost of congestion**:
    - when packet was dropped, any transmission capacity used for that packet was wasted

# TCP congestion control

- **additive increase multiplicative decrease (AIMD) approach**: sender increases transmission rate (**window size**), probing for usable bandwidth, until loss occurs
    - **additive increase**: increase congestion window size (cwnd) by 1 MSS (maximum segment size) every RTT until loss detected
    - **multiplicative decrease**: cut cwnd in half after loss
  
- **TCP sending rate**: roughly send cwnd bytes, wait RTT for ACKs, then send more bytes
    - **rate ~ cwnd/RTT bytes/sec**

> the congestion window (cwnd) is increased by one segment per RTT

## TCP slow start fast recovery

- when connection begins, increase rate exponentially until first lost event
    - initially cwnd = 1 MSS
    - double cwnd every RTT
    - done by **incrementing congestion window for every ACK received**
- initial rate is slow but ramps up exponentially fast

## TCP detecting, reacting to loss

- loss indicated by timeout
    - congestion window is reset to 1 MSS; window then **grows exponentially** to threshold, then **grows linearly**
- **loss indicated by 3 duplicate ACKs**
    - duplicate ACKs indicate network capable of delivering some segments, but expected packet is lost, and cwnd is cut in half but then grows linearly (TCP reno)
- TCP tahoe always sets cwnd to 1 (timeout or 3 duplicate ACKs)

## TCP switching from slow start to CA

- exponential increase should switch to linear increase when **congestion window gets to 1/2 of its value** before timeout
- implementation:
    - on loss event, variable `ssthresh` is set to 1/2 of cwnd

## TCP throughput

- what is the average TCP throughput as function of window size, RTT?
    - ignoring the slow start, we assume sender always has data to send
- **W**: window size (measured in bytes) where loss occurs
    - **average window size** (number of in-flight bytes) is 3/4 W
    - **average throughput** is 3/4W per RTT
- **average TCP throughput = 3/4 W/RTT bytes/sec**

## TCP fairness

- if K TCP sessions share same bottleneck link of bandwidth R, each should have an **average rate of R/K**
- two competing sessions:
    - additive increase gives slope of 1, as throughout increases
    - multiplicative decrease decreases throughput proportionally

- **fairness and UPD**
    - multimedia apps often do not use TCP and do not want rate throttled by congestion control
    - instead use UDP to send audio/video at constant rate, tolerate packet loss

- **fairness, parallel TCP connection**
    - applications can open multiple parallel connections between two hosts
    - e.g. 9 existing connections on a link with rate R
        - new app asks for 1 TCP, gets rate R/(9+1) = R/10
        - new app asks for 10 TCPs, gets 10R/(10+10) = R/2
