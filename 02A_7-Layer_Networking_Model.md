# OSI 7-Layer Networking Model

## OSI Model Introduction
Conceptual way of describing networking as 7 seperate layers from physical to application:
* Layer 7 - Application
* Layer 6 - Presentation
* Layer 5 - Session
* Layer 4 - Transport
* Layer 3 - Network
* Layer 2 - Data Link
* Layer 1 - Physical

Layers 1 - 3 are the **media layers**. This concerns how data is moved from point A to point B across networks. Layers 4-7 are the **host layers** and concern how data is represented, processed and understable to both the sender and receiver. As OSI model is conceptual, in implementation there is usually overlap between the layers.

## Layer 1 - Physical
* L1 specifications define how **raw bit streams** are transmitted and received on a shared physical medium. Properties include things like timing, voltage levels and rates.
* Physical media include copper (electrical signals), fibre optics (light signals) and wifi (RF signals).
* No device addressing - the layer only concerns how data is transmitted across a physical medium.
* If multiple devices sharing the same physical medium transmit information at the same time, there will be a collision and this will corrupt the information.
* L1 has no media access control and collision avoidance system, meaning that using layer 1 in isolation means there will be collisions. Also cannot detect when there are collisions.
* L1 does not scale well with increased device number due to increased chance of collisions and corruption.
* L1 needs L2 to stop data corruption and to be useful.

### L1 Device Example - Hubs
* Transmits signal from one port to every other port, acts like repeater. 
* No concept of addressing or routing.
* Will propogate collisions.

## Layer 2 - Data Link
* Requires function layer 1 - runs over L1.
* Ethernet is a L2 technology.
* Concerned with delivery of data in the form of **frames** and introduces **MAC addresses**.
* L2 introduces:
    * Identifiable devices.
    * Media access control (stops collisions).
    * Collision detects.
    * Unicast (1:1).
    * Multicast (1:n).

### Frames
A frame is the container of data for transmission in L2. It is comprised of:
* Preamble and start frame delimiter (SDF) - contains info on where the start of the frame is, and where to find the data.
* Source MAC address.
* Destination MAC address (or all - broadcast).
* EtherType (ET) - specifies which L3 protocol is used to encode the data (e.g. internet protocol - IP)
* Payload - data the frame is sending. Provided by the L3 protocol indicated by ET.
* Frame check sequence (FCS) - used for data error correction.

### Carrier-Sense Multiple Access (CSMA)
* CSMA is a type of media access control (MAC) that prevents multiple devices sharing the same physical medium from transmitting data at the same time, therefore preventing collisions.
* CSMA checks for the presence of a carrier signal on L1 and only transmits the frame is absent, otherwise it waits.
* In race condition where both transmit at same time, both wait for a specified time plus a random interval.

### L2 Device Example - Switch
* Smarter than hubs (L1 device) as it has due to presence of MAC addresses introduced by L2.
* Maintains MAC address table - list of which MAC address is connected to each port. Learns this the first time a device sents a frame, as it contains sender MAC address.
* Only sends frame to the port the destination device is connected to - doesn't repeat like hubs.
* If the destination MAC address isn't yet in the MAC address table, it will sent it to all ports.
* Won't forward collisions to other ports. 

## Layer 3 - Network
* Concerned with moving frames from one device spanning multiple L2 networks - i.e. routing.
* L2 enables devices within the same network to communicate as they use the same protocol, but different networks may use different L2 protocols - e.g. LANs usually use ethernet, but long range connections may use PPP, MPLS, ATM. Therefore another abstraction is required.
* L3 therefore spans multiple L2 networks.
* Internet protocol (IP) is an L3 protocol - adds IP addresses that enable routing across networks.
* Routers (L3 devices) move packets between networks by encapsulating **packets** in frames. The packet mostly doesn't change (some exceptions), but the frame that encapsulates it may change as it moves between different networks as it is stripped out and rebuilt.

L3 provides:
* Device-to-device communication over many L2 networks
* Cross-networking addressing - e.g. via IPv4, IPv6
* ARP - translate IP to MAC address
* Routing of encapsulated L2 packets via route tables
 
L3 doesn't provide:
* Multiple channels of communication (e.g. multiple applications) between source and destination. Packets are only sent over one channel.
* Guarantee that packets are delivered in the order.

### Packets (IPv4)
Content of IPv4 packet:
* Source IP address.
* Destination IP address.
* Protocol - specifies which L4 protocol was used to generate the L3 data (e.g. TCP, UDP, ICMP).
* Time to live (TTL) - maximum number of hops before being discarded. Stops packet from constantly being looped.
* Data.

### Packets (IPv6)
Content of IPv6 packet:
* Source IP address (larger size - more possible addresses).
* Destination IP address (larger size - more possible addresses).
* Hop limit - same as TTL.
* Data.

### IP Addressing (IPv4)
* IP addresses are represented in decimal-dotted notation - 4 blocks of number from 0-255. In memory, this is represented in binary as 4 sets of 8 bits (32 bits total). Each block is known as an octet (8 bits).
* Two parts of IP address:
    * Network - first two octets (16 bits).
    * Host - last two octets (16 bits).
* If network part of two IP addresses are the same, they are on the same IP network and therefore local.
* IP addresses are assigned either statically or dynamically (DHCP).
* IP addresses must be unique globally.

### Subnet Masks
* Allows a host to determine if an IP address it wants to communicate with is on the local network or not. If local, then the host can communicate directly. If not local, the the device must communicate with a **gateway** and therefore undergo IP routing through different intermediate networks.
* Works by determining which part of the IP address represents the network (e.g. first two octets).

#### Example (225.225.0.0)
* Decimal dotted notation: 255.255.0.0 
* Binary: 11111111 11111111 00000000 00000000
* Prefix: /16 (means the left 16 bits are 1)

1s indicate which part is the network part of the addressand 0s indicate the host part. Therefore, 225.225.0.0 means the first two octets are the network part.

### Route Tables & Routes
* Route tables match a destination IP address to the most optimal next hop machine. 
* Route tables are found on routers and devices and enable packets to hop through different networks in the optimal route until the destination is reached.
* Route table entries with the highest specifity (higher prefix numbers) are preferred.

## Address Resolution Protocol (ARP)
* ARP translates an IP address of a machine into a MAC address.
* When a packet reaches the destination network, ARP is used to find the MAC address for destination IP which is then sent through L2 to the destination MAC.
* ARP works in a broadcasting manner - the enquiring devices broadcasts to the network "who has this MAC address?" and the corresponding machine responds back with their IP address.

## Problems with L3
Each packet is treated independantly, therefore:
* L3 doesn't guarantee delivery of packets in order - different packets may take different routs and arrive out of order.
* Packets can go missing due to intermitant network conditions causing packets to be stuck in routing loops, then discarded due to TTL. This is quite common!
* No seperate communication channels (i.e. for different applications) - packets are sent only via a single channel.
* No flow control - if source transmits packets faster than destination can receive, packets may be lost.

## Layer 4 (Transport) and Layer 5 (Session)
* Usually some overlap between L4 and L5 - notes below cover both together.
* TCP and UDP are examples of L4 protocols that run on top of L3. Both run on top of IP (L3).

### Transmission Control Protocol (TCP)
* Used when reliability, error correction and ordering of data is desired. 
* Used for most important use cases - e.g. in HTTP, HTTPS, SSH.
* Connection-orientated protocol - sets up bi-directional channel between two devices.

#### TCP Segments
Segments are the fundamental data transfer unit for TCP. They are encapsulated in L3 packets and do not contain IP addresses (these are L3 concepts), but instead are routed via ports which are individual channels for transmission.

Segments contain:
* Source port number
* Destination port number
* Sequence number - i

### User Datagram Protocol (UDP)
Faster than TCP, but less reliable as it doesn't have overheads in guaranteeing reliable data delivery. Used when performance is more important than reliability.