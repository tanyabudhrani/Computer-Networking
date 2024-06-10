# lecture 6

## elements of a wireless network

| Component | Description |
| --- | --- |
| Wireless Hosts | Laptop, smartphone. Run applications. May be stationary or mobile. - Wireless does not always mean mobility. |
| Base Station | Typically connected to wired network. Relay: responsible for sending packets between wired network and wireless host(s) in its "area". - e.g., cell towers, 802.11 access points. |
| Wireless Link | Typically used to connect mobile(s) to base station. Also used as backbone link. Multiple access protocol coordinates link access. Various data rates, transmission distance. |

# wireless network classification

- coverage area
    - short range wireless networks

- WPAN (Wireless personal area network)

- WLAN (Wireless local area network)
- long range wireless networks
    - WWAN (Wireless wide area network, cellular network)
        - WMAN (Wireless metropolitan area network)
    - satellite network
- infrastructure
    - infrastructure based wireless network
    - ad hoc wireless network

## short range wireless network

- short range wireless networks are confined to a limited area
    
    
    | personal area network | connect various devices within a close area |
    | --- | --- |
    | local area network | cover corporate building, school campuses, manufacturing plants, homes |
- operate on an unlicensed spectrum reserved for industrial, scientific, medical (ISM) usage
    - no need to obtain a license, and free of charge
    - global availability
    - the most common frequency band is at 2.4 GHz
    - other bands at 5GHz, 6GHz, and 60GHz are also often used

## long range wireless networks

- long range wireless networks span large geographical areas
    - goal: to provide wide-area coverage, or even global coverage
        - wireless wide area network can cover a city, a state, or even entire country
        - satellite network can provide connections across the world
- operated by different companies
    - wireless connectivity provided as a service product

### infrastructure based wireless network

- base station connects to wired network
- base station provides wireless connectivity for mobile hosts within the coverage
    - e.g., cellular network, WLAN

### adhoc wireless network

- no base stations
- nodes organize themselves into a network
- nodes can only transmit to other nodes within link coverage
- single hop vs multiple hop

# wireless link characteristics

| decreased signal strength | radio signal attenuates as it propagates through medium (path loss) |
| --- | --- |
| interference from other sources | standardized wireless network frequencies (e.g., 2.4 GHz) are shared by other devices (e.g., phone); devices (e.g., microwave oven) interfere as well |
| multi-path propagation | radio signal reflects off objects ground, arriving at destination at slightly different times |
- multiple wireless senders and receivers create additional problems (beyond multiple access):

- hidden terminal problem
    - B, A hear each other
    - B, C hear each other
    - A, C cannot hear each other so A,C are unaware of their interference at B

- signal attenuation
    - B, A hear each other
    - B, C hear each other
    - A, C cannot hear each other
        - cause interference at B

## code division multiple access (CDMA)

- unique “code” assigned to each user; i.e., code set partitioning
    - all users share same frequency, but each user has own “chipping” sequence (i.e., code) to encode data
    - allows multiple users to “coexist” and transmit simultaneously with minimal interference (if codes are “orthogonal”)

- encoded signal = (original data) X (chipping sequence)

- decoding: inner-product of encoded signal and chipping sequence

# IEEE 802.11 WLAN

| Standard | Description |
| --- | --- |
| 802.11a | 5-6 GHz range. Up to 54 Mbps. |
| 802.11b | 2.4-5 GHz unlicensed spectrum. Up to 11 Mbps. Direct Sequence Spread Spectrum (DSSS) in physical layer. - All hosts use same chipping code. |
| 802.11g | 2.4-5 GHz range. Up to 54 Mbps. |
| 802.11n | Multiple antennae. 2.4-5 GHz range. Up to 200 Mbps. |

- Use CSMA/CA for multiple access
- Have infrastructure and ad hoc modes
## 802.11 WLAN architecture

- wireless host (station) communicates with base station (access point (AP))

- basic service set (BSS) in infrastructure mode contains:
    - stations
    - AP

- independent basic service set (IBSS) in ad hoc mode: stations only

## 802.11: channels and association

- 802.11b: 2.4GHz-2.485GHz spectrum divided into 11 channels at different frequencies
    - AP chooses frequency for all stations
    - interference possible among APs: channel can be same as that chosen by neighboring APs
- station: must *associate* with an AP
    - scans channels, listening for beacon frames containing AP’s name (SSID) and MAC address
    - selects AP to associate with perform authentication
    - will typically run DHCP to get IP address in AP’s subnet

### passive scanning

1. beacon frames: from APs to HI
2. association Request frame: from H1 to selected AP 2
3. association Response frame: from selected AP 2 to H1

### active scanning

1. probe request frame: broadcast from H1 to APs
2. probe Response frames: from APs to HI
3. association Request frame: from H1 to selected AP 2
4. association Response frame: from selected AP 2 to H1

## 802.11: multiple access

- collisions: multiple nodes transmit at same time
- CSMA does not work well in wireless channel

- carrier sense before transmitting
    - cannot sense ongoing transmissions at other node due to signal attenuations

- no collision detection
    - cannot detect collisions occurred at the receiving node: hidden terminal
- 802.11 uses CSMA/CA(collision avoidance) to avoid collisions

## 802.11 mac protocol: CMSA/CA

### 802.11 sender

1. if sense channel idle for **DIFS** then
    - transmit entire frame (no CD)

2. if sense channel busy then

- start random backoff timer
- timer counts down while channel idle
- transmit when timer expires
- if no ACK, increase random backoff interval, repeat 2

### 802.11 receiver

1. if frame received OK
    - return ACK after **SIFS** (ACK needed due to hidden terminal problem)

## 802.11 mac protocol: MACA

- collision avoidance: avoid collisions of long data frames
    - idea - allow sender to “reserve” channel rather than randomly access channel for long data frames
- multiple access with collision avoidance (MACA)
    - sender first broadcasts *small* request-to-send (RTS) packets to receiver using CSMA
        - RTS heard by all neighboring nodes
        - RTSs may still collide with each other (but they’re short and rare)
    - receiver broadcasts small clear-to-send (CTS) in response to RTS
        - CTS heard by all neighboring nodes
    - sender transmits data frame; others defer transmissions
    - receiver transmits ACK packet to acknowledge data frame reception

> avoid data frame collisions completely by using small control packets

## 802.11: mobility within same subnet

- H1 remains in same IP subnet: IP address can remain same
- switch: which AP is associated with H1?
    - self-learning: switch will receive frame from H1 and “remember” which switch port can be used to reach H1
    
## 802.15: wireless personal area network

- less than 10 m diameter
- replacement for cables (mouse, keyboard, headphones)
- **ad hoc**: no infrastructure
- master/slaves:
    - slaves request permission to send (to master)
    - master grants requests
- 802.15: evolved from Bluetooth specification
- 2.4-2.5 GHz radio band

Certainly! Here is the content converted into a properly formatted Markdown table:

# Cellular Network Architecture

| component | description |
| --- | --- |
| Cell | Covers geographical region. Base Station (BS) analogous to 802.11 AP. Mobile Stations (MS) attach to network through BS. Air-interface: physical and link layer protocol between MS and BS. |
| MSC | Connects cells to wired telephone network. Manages call setup. Handles mobility. |

## first hop

- two techniques for sharing MS-to-BS radio spectrum

- combined FDMA/TDMA: divide spectrum in frequency channels, divide each channel into time slots

- CDMA: code division multiple access

## 4G LTE network architecture

- all IP core: IP packets tunneled (through core IP network) from base station to gateway
- no separation between voice and data – all traffic carried over IP core to gateway
