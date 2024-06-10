# lecture 2

Created: January 16, 2024 8:55 AM

# creating an internet app

- need to write programs that run on different hosts and communicate over a network (e.g. browser software that communicates with web server software)
- no need to write software for network-core devices since they do not run user applications
    - writing applications on end systems allow for rapid app development

## client-server architecture

- **server**:
    - always on host
    - permanent IP address
- **clients**:
    - communicates with server and may be intermittently connected but do not communicate with each other
    - may have dynamic IP addresses

![Screenshot 2024-01-16 at 9.06.25 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_9.06.25_AM.png)

> an **IP address** is a 32-bit identifier for hosts or routers, represented in dot-decimal notation (192.0.2.1), consisting of four decimal numbers, each ranging from 0 to 255
> 

## P2P architecture

![Screenshot 2024-01-16 at 9.06.40 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_9.06.40_AM.png)

- no permanent always on server
- arbitrary end systems directly communicate
- peers request service from other peers, and provides service in return to other peers
    - **self scalability**: new peers bringing new service capacity, as well as new service demands
- peers are intermittently connected and need complex management

> in **client-server architecture**, clients and servers are differentiated wherein servers respond with the services requested by the client | in **p2p architecture**, clients and servers are not differentiated, and every node can both request and respond for the services
> 

### inter-process communication

- **process**: a program running in a host
    - within same host, two processes can communicate using inter-process communication
    - processes in different hosts can communicate by exchanging messages

- **client process**: process that initiates communication

- **server process**: process that waits to be contacted
- applications with P2P architecture have both client processes and server processes

## sockets

- processes send/receive messages to/from their sockets — essentially the **endpoint of a two way communication link**
- socket analogous to door
    - sending process pushes message out door
    - sending process relies on transport service out of the door to deliver message to door at receiving process

> sockets are generally employed in client server applications — the server creates a socket, attached it to a network port address then waits for the client to contact it; the client then creates a socket and then attempts to connect to the server socket
> 

![Screenshot 2024-01-23 at 9.25.10 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-23_at_9.25.10_AM.png)

## addressing processes

- how to address a process over the internet?
    - to receive messages, processes must have an identifier
    - host device has a unique 32-bit IP address
    - IP address of a host cannot identify a process which runs on the host since many processes can be running on the same host
    - a **port number** is associated with a process on host
- **process identifier** includes both IP address and port number associated with process on host
    - e.g. HTTP server: 80, mail server: 25

### what transport service does an app need?

| application | data loss | bandwidth | delay sensitive |
| --- | --- | --- | --- |
| file transfer | no loss | elastic | no |
| email | no loss | elastic | no  |
| web documents | no loss | elastic | no  |
| real-time audio/video | loss-tolerant | audio: 5kbps - 1mbps 
video: 10kbps - 5mbps | yes, 100’s ms |
| stored audio/video | loss-tolerant | audio: 5kbps - 1mbps 
video: 10kbps - 5mbps | yes, few secs |
| interactive games | loss-tolerant | audio: 5kbps - 1mbps 
video: 10kbps - 5mbps | yes, 100’s ms |
| text messaging | no loss | elastic | yes and no |

- **data loss**
    - some apps (e.g. file transfer) require 100% reliable data transfer
    - some other apps (e.g. audio) can tolerate some loss

- **delay sensitive**
    - some apps (e.g. interactive games) require low delay to be usable
    - some other apps (e.g. email) don’t care

- **bandwidth**
    - some apps (e.g. video) require minimum bandwidth to be usable
    - some other apps (e.g. web) make use of whatever bandwidth they get

## internet transport layer services

### TCP

| connection-oriented | setup is required between client and server processes  |
| --- | --- |
| reliable data transfer | between sending and receiving processes  |
| flow control | sender won’t overwhelm receiver  |
| congestion control | throttle sender when network overloaded  |
| does not provide | timing, minimum bandwidth guarantee |

### UDP

- unreliable data transfer between sending and receiving processes
- does not provide:
    - reliability,
    - flow control,
    - congestion control,
    - timing,
    - bandwidth guarantee,
    - connection set

| application  | layer protocol | transport layer protocol |
| --- | --- | --- |
| email | SMTP [RFC 2821] | TCP |
| remote terminal access | Telnet [RFC 854] | TCP |
| web | HTTP [RFC 2616] | TCP |
| file transfer | FTP [RFC 959] | TCP |
| streaming multimedia | HTTP (e.g., youTube), RTP [RFC 1889] | TCP or UDP |
| internet telephony | SIP, RTP, proprietary (e.g., Skype) | TCP or UDP |

> TCP is used best for direct communication in which a reliable connection is needed (e.g. text messaging, email, file transfers) | UDP is used best for live and real-time data when speed is more important than reliability (e.g. gaming, live streaming)
> 

# web service

- web pages consist of base HTML-files which embed several referenced objects
    - objects can be HTML files, jpeg images, java applet, audio files, etc
    - each object is addressable by a URL (uniform resource locator)

- **client**: browser requests web page, and receives, and displays web objects

- **server**: web server sends objects in response to client’s requests

# HTTP connections

- **HTTP**: hypertext transfer protocol
    - web’s application layer protocol designed to transfer information between networked devices and runs on top of other layers of the network protocol stack
    - two types of messages: **requests, response**
1. client initiates TCP connection (creates socket) to server, **port 80** 
2. server accepts TCP connection from client 
3. HTTP messages (application-layer protocol messages) exchanged between browser (**HTTP client**) and web server (**HTTP server**)
4. TCP connection closed
- **HTTP is stateless** — server maintains no information about past clients

- **non-persistent HTTP**:
    - at most one object sent over TCP connection, then TCP connection is closed
    - downloading multiple objects requires multiple connections

- **persistent HTTP**:
    - multiple objects can be sent over single TCP connection between client and server

## non-persistent http

- **RTT (round-trip time)**: time for a small packet to travel from client to server and back
- **HTTP response time**: one RTT to initiate TCP connection + one RTT for HTTP request + first few bytes of HTTP response to return
    - file transmission time

![Screenshot 2024-01-16 at 9.32.52 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_9.32.52_AM.png)

> non-persistent HTTP response time = **2RTT + file transmission time**
> 

### non-persistent HTTP example

- suppose user enters URL: **www.someSchool.edu/someDept/home.index**
    1. **HTTP client** initiates TCP connection to the HTTP server at [www.someSchool.edu](http://www.someSchool.edu) on port 80 
    2. **HTTP server** at host [www.someSchool.edu](http://www.someSchool.edu) at port 80 will accept the connection, and then notify the client it has accepted
    3. **HTTP client** sends HTTP request message (containing the URL) into TCP connection socket 
        - this message indicates that the client wants object someDept/home.index
    4. **HTTP server** receives the request message, forms a response message, containing requested object, and sends message into its socket 
    5. **HTTP server** closes TCP connection 
    6. **HTTP client** receives response message containing the html file, displays html— further parses html file to find referenced objects 
    

### persistent HTTP

- non-persistent HTTP requires 2 RTTs per object with an overhead for each TCP connection— browsers are often open parallel TCP connections to fetch referenced objects
- with persistent HTTP:
    - server leaves connection open after sending response
    - subsequent HTTP messages between same client/server send over open open connection
    - client sends requests as soon as it encounters a referenced object
    - as little as **one RTT** for all the referenced objects

> the main difference between persistent and non-persistent HTTP is that, in non-persistent, there can be at most one object that can be sent over a single TCP connection, while in persistent, multiple objects can be sent since the connection is left open
> 

## HTTP request message format

![Screenshot 2024-01-16 at 9.35.27 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_9.35.27_AM.png)

### example

![Screenshot 2024-01-16 at 9.35.56 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_9.35.56_AM.png)

- the request line contains the HTTP method and target (e.g. GET + a string that represents the object to be fetched)
- the header lines contains information such as the required host header and the message body
- the request ends with a blank line

## method types

| GET | requests a representation of the specified resource — retrieves data  |
| --- | --- |
| HEAD | asks for a response identical to a GET requests but without the response body (object) |
| POST | submits an entity to the specified resource, often causing a change in state  |
| PUT | uploads file in entity body to path specified in URL field  |
| DELETE | deletes file specified in the URL field  |
| CONNECT | establishes a tunnel to the server identified by the target resource  |
| OPTIONS | describes the communication option for the target resource  |
| TRACE | performs a message loop-back test along the path to the target resource  |
| PATCH | applies partial modifications to a resource |

## HTTP response status codes

- status codes appear in 1st line in server-to-client response message

| 200 | OK | request succeeded, requested object later in this message |
| --- | --- | --- |
| 301 | moved permanently  | requested object moved, new location specified later in location: |
| 400 | bad request | requested messaged not understood by server  |
| 404 | not found | requested document not found on this server  |
| 505 | http version not supported | requested HTTP protocol version not supported by server |

## cookies

- an HTTP cookie is a small piece of data that a server sends to a user’s web browser and is used to tell if two requests come from the same browser (remembers stateful information about the stateless HTTP protocol)

![Screenshot 2024-01-16 at 9.43.17 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_9.43.17_AM.png)

- **protocol endpoint entities**: maintain state at sender/receiver over multiple transactions — **cookies are http messages that carry state**

## web caching (proxy server)

- web caching is the activity of storing data for reuse by caching it the first time a user visits the page, so that the next time a user request the same page, the cache will serve the copy which prevents the origin server from being overloaded
- user sets browser (i.e. web accesses via web caching), and browser sends all HTTP requests to **proxy server**
    - if object found in proxy server, proxy server returns object, otherwise, proxy server requests object from origin server, then returns object to client
- proxy server acts as both client and server
    - **server** for original requesting client
    - **client** to origin server
- typically proxy server is deployed by ISP

### caching example

- one
    - assumptions:
        - average object size: 1M bits
        - average request rate from browsers to origin servers: 15/sec
        - average data rate to browsers: 1Mb*15/s=15Mbps
        - RTT from institutional router to any origin server: 2 secs
        - access link rate: 15 Mbps
    - consequences:
        - **LAN utilization**: average data rate in percentage = $15Mbps/100Mbps = 15%$%
        - **access link utilization** = access link rate in percentage = $15Mbps/15Mbps = 100%$% (which is a problem)
        - **total delay = internet delay + access delay + LAN delay** = 2 secs + minutes + msecs
- two
    - assumptions:
        - average object size: 1M bits
        - average request rate from browsers to origin servers: 15/sec
        - average data rate to browsers: 15 mbps
        - RTT from institutional router to any origin server: 2 secs
        - access link rate: 150 mbps
    - consequences:
        - **LAN utilization**: average data rate in percent = 15%
        - **access link utilization** = access link rate in percent = 150/15 = 10%
        - **total delay = internet delay + access delay + LAN delay** = 2 secs + msecs + msecs
    - **cost**: increased access link speed
- three
    - assumptions:
        - average object size: 1M bits
        - average request rate from browsers to origin servers: 15/sec
        - average data rate to browsers: 15 mbps
        - RTT from institutional router to any origin server: 2 secs
        - access link rate: 15 Mbps
    - consequences:
        - **LAN utilization**: average data rate in percent = 15%
        - **access link utilization** = access link rate in percent = 15%
        - **total delay = internet delay + access delay + LAN delay** = 2 secs + msecs + msecs
    - **cost**: web cache
- four
    - suppose **cache hit rate** (% of requests that are satisfied from the cache without having to go to origin server) is 0.4
        - 40% requests satisfied at cache
        - 60% requests satisfied at origin
    - **access link utilization** (% of requests that use the access link)
        - 60% of requests use access link
    - data rate to browsers over access link = **0.6*15 mbps = 9 mbps**
        - **utilization = 9/15 = 60%**
    - total delay
        - **0.6*(delay from origin servers)+0.4*(delay when satisfied at cache)** = 0.6(2.01) + 0.4(~msec) = 1.2 secs

## conditional GET

- the goal is not to send object if cache has up-to-date cached version
    - if we don’t transmit the object from the server to the client, then this eliminates the delay associated with spending the object over the network
    - since the object is not transmitted from the server, the downlink is used less frequently which leads to lower down link usage

- **cache**: specify date of cached copy in HTTP request
    
    ```nasm
    if-modified-since:<date>
    ```
    

- **server**: response contains no object if cached copy is up to date
    
    ```nasm
    HTTP/1.0 304 Not modified
    ```
    

![Screenshot 2024-01-16 at 10.15.31 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_10.15.31_AM.png)

# electronic mail

 

![Screenshot 2024-01-16 at 10.18.59 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_10.18.59_AM.png)

| user agent  | “mail reader”

composing, editing, reading mail messages

outlook, thunderbird, iphone mail client 

outgoing, incoming messages stored on server |
| --- | --- |
| mail servers | mailbox contains incoming messages for user 

message queue of outgoing mail messages |
| SMTP protocol | SMTP supports mail servers to send mail messages

client: sending mail server

server: receiving mail server |

## SMTP [RFC 2821]

- simple mail transfer protocol — a standard protocol used for the transmission of email messages from client to server that uses **TCP, port 25**
- **direct transfer**: sending server to receiving server
    - handshaking → transfer of messages → closure
- command/response interaction (HTTP)

- **commands**: ASCII text

- **response**: status code and phrase

### example

1. alice uses user agent to compose message to bob@someschool.edu 
2. alice’s user agent sends message to her mail server; message placed in message queue
3. **SMTP client** opens TCP connection with bob’s mail server
4. **SMTP client** sends alice’s message over the TCP connection 
5. bob’s mail server places the message in bob’s mailbox 
6. bob invokes his user agent to read message 

![Screenshot 2024-01-16 at 10.28.44 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_10.28.44_AM.png)

- SMTP uses persistent connections meaning that once a connection is established, it is kept open for the duration of the email until explicitly closed
- messages are required to be in 7-bit ASCII
    - uses `CRLF.CRLF` to determine end of message

### HTTP vs SMTP

- **HTTP**: pull
    - client initiates the communication by pulling the data from the server when it needs it

- **SMTP**: push
    - the server initiates the communication by pushing the email into the recipient’s server
- both have ASCII command and response interaction, as well as status codes

- **HTTP**: each object encapsulated in its own response message

- **SMTP**: multiple objects sent in multipart message

> HTTP (port 80) is focused on the transfer of hypertext and multimedia content for web browsing and interaction, while SMTP (port 25) is dedicated to the reliable transfer of electronic mail
> 

## mail message format

- **RFC 822**: standard for text message format
    - header lines (to, from, subject)
    - body (message) uses only ascii

![Screenshot 2024-01-16 at 10.35.46 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_10.35.46_AM.png)

- **mail access protocol**: retrieval from server
    - **POP**: post office protocol [RFC 1939] allows client to retrieve emails from a server using authorization and/or downloading of emails
    - **IMAP**: internet mail access protocol [RFC 1730] allows for the manipulation of stored messages on the server so that emails can be managed and organized
    - **HTTP**: involves accessing emails through a web browser (e.g. gmail, hotmail, yahoo)

> RFC 822 is a specification that defines the format of email messages
> 

## POP3 protocol

- used to retrieve emails from a mail server to a client device

- **authorization phase**
    - **client commands**: specifies the username and password for authentication
    - **server responses**: +OK and -ERR
    

- **transaction phase**
    - **list**: lists the message numbers and sizes currently on the server
    - **retr**: retrieves a specific email message from the server based on its message
    - **dele**: marks a specific email message for deletion
    - **quit**: ends the pop3 session

![Screenshot 2024-01-16 at 10.39.00 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_10.39.00_AM.png)

- previous example uses POP3 “download and delete” mode which downloads and keeps copies of messages on different clients — **POP3 is stateless** across sessions

## imap

- keeps all messages in one place — at server
- allows users to organize messages in folder
- keeps user state across sessions
    - names of folders and mappings between message IDs and folder names

# DNS: domain name system

- distributed database implemented in hierarchy of many name servers
- **application-layer protocol**: hosts, name servers communicate to resolve names (address/name translation)
    - core internet function, implemented as application-layer protocol
    - complexity at network edge

> essentially, a DNS is a hierarchical and decentralized naming system used to translate human-readable domain names into numerical IP addresses that are used by computers on the internet to identify each other
> 

### services

| hostname to IP address translation |
| --- |
| host aliasing  |
| mail server aliasing |
| load distribution (e.g. replicated web servers where many IP addresses correspond to one name) |
- we can’t centralize DNS because it is a single point of failure that requires a lot of maintenance, will acquire traffic volume, and doesn’t scale

![Screenshot 2024-01-16 at 10.45.28 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_10.45.28_AM.png)

## root name servers

- **13 logical root name servers worldwide**
    - each server is replicated many times
- root name servers are contacted by local name servers that cannot resolve name or contact authoritative name servers if name mapping is not known

## TLD, authoritative DNS servers

- **top level domain servers** are responsible for com, org, net, edu, aero, jobs, museums, and all top-level country domains

> when a DNS resolver receives a query for a specific domain, the TLD server provides information about the authoritative name servers for the next level (e.g. if someone queries “www.example.com”, the TLD server for .com would be queried and respond with authoritative name servers for “example.com”
> 

- **authoritative DNS servers** are the organization’s own DNS servers that provide authoritative hostnames to IP mappings

> when a TLD server directs a DNS resolver to the authoritative name servers for a second-level domain, the authoritative DNS servers for that domain provide the IP address (e.g. if the TLD server for .com directs the resolver to “example.com”, the authoritative servers for that server will find the IP address
> 

## local DNS server

- does not strictly belong to hierarchy
- each ISP has one — also called **default name server**
- when host makes DNS query, query is sent to its local DNS server
    - it may have local cache of recent name-to-address translation pairs (but may be out of date), if not, it acts as a proxy, forwards query into hierarchy

> local DNS is a server that is typically operated by an internet service provider, and functions to resolve domain names to IP address for the devices within that network
> 

### dns name resolution example

- host at [cis.poly.edu](http://cis.poly.edu) wants IP address for [gaia.cs.umass.edu](http://gaia.cs.umass.edu)

- **iterated query**: DNS server provides referrals if it doesn’t have the requested information
    - the **resolver** is responsible for sending subsequent queries based on referrals

- **recursive query**: DNS server either provides the answer or performs additional queries on behalf of the resolver
    - **resolver** relies on the DNS server to obtain information

## DNS caching, updating records

- once any name server learns the mapping of a domain name to an IP address, it caches this information
    - cache entries have a **timeout** that force them to disappear after some time
    - **top-level domain** servers are often cached in local name servers
- cached entries may be out of date (best effort name-to-address) translation
    - if name host changes IP address, it may not be known internet-wide until all TTLs of cached entries expire

> DNS caching is a mechanism employed by DNS resolvers (local DNS servers) to store previously resolved DNS query results for a specific time period — caching helps improve the efficiency and speed of the DNS resolution process by reducing the need to repeatedly query authoritative DNS servers for the same domain name
> 

## DNS records

- **DNS**: distributed database storing resource records (RR)
    - `RR format: (name, value, type, ttl)`

| type=A | name is hostname
value is IP address

used to associate a domain name with its corresponding IP address |
| --- | --- |
| type=NS | name is domain 
value is hostname of authoritative name server for this domain

used to specify the authoritative DNS servers for a domain |
| type=CNAME | name is alias name for some canonical name
value is canonical name

used to create an alias for a canonical domain name  |
| type=MX | value is name of mail server associated with name

used to specify mail servers responsible for receiving emails on behalf of the domain |
- query and reply messages, both with same message format
    
    ![Screenshot 2024-01-16 at 11.13.25 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_11.13.25_AM.png)
    

- **identification**: 16 bit number for query, reply to query uses same number

- **flags**:
    - query or reply
    - recursion desired
    - recursion available
    - reply is authoritative

# socket programming

- **socket**: a host based, application created, OS controlled interface into which application process can both send and receive messages to/from other application process
    - a way of connecting two nodes on a network to communicate with each other — one socket listens on a particular port at an IP, while the other socket reaches out to the other to form a connection
- socket api
    - introduced in BSD4.1 unix, 1981
    - explicitly created, used, released by apps
    - client/server paradigm

- **UPD**: unreliable datagram

- **TCP**: reliable, byte stream-oriented

> TCP is a connection-based protocol while UDP is connectionless, which is why TCP is more reliable and transfers data slowly
> 

### example

1. client reads a line of characters (data) from its keyboard and sends data to server
2. server receives the data and converts characters to uppercase
3. server sends modified data to client
4. client receives modified data and displays line on its screen

## socket programming with UDP

- **UDP**: no connection between client and server
    - no handshaking before sending data
    - sender explicitly attaches destination’s IP address and port number to each packet while receiver extracts sender’s IP address and port number from received packet
- **UDP**: transmitted data may be lost or received out-of-order

> UDP provides **unreliable transfer of groups of bytes** (”datagrams”) between client and server
> 

![Screenshot 2024-01-16 at 11.17.28 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_11.17.28_AM.png)

### example app

![Screenshot 2024-01-16 at 11.18.02 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_11.18.02_AM.png)

![Screenshot 2024-01-16 at 11.18.17 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_11.18.17_AM.png)

## socket programming with TCP

- client must contact server
    - server process must first be running and server must have **created socket** that welcomes client’s contact
- client contacts server by creating TCP socket, **specifying IP address and port number of server process**
    - when client creates socket, client TCP establishes connection to server TCP
- when contacted by client, server TCP creates new socket for server process to communicate with that particular client
    - allows server to talk with multiple clients
    - source port numbers used to distinguish clients

> TCP provides reliable, in-order, byte-stream transfer between client and server
> 

![Screenshot 2024-01-16 at 11.20.36 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_11.20.36_AM.png)

### example

![Screenshot 2024-01-16 at 11.21.34 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_11.21.34_AM.png)

![Screenshot 2024-01-16 at 11.21.19 AM.png](lecture%202%20c0842452895c47df99bc18afaae85f36/Screenshot_2024-01-16_at_11.21.19_AM.png)