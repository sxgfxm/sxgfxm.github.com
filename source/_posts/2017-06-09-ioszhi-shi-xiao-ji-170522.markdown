---
layout: post
title: "iOS知识小集-170522"
date: 2017-06-09 09:57:35 +0800
comments: true
categories: iOS
keywords: sxgfxm, Networking Concepts, Bonjour Overview, NSNetServices, DNS Service Discovery
description: Networking Concepts, Bonjour Overview, NSNetServices, DNS Service Discovery
---

## Networking Concepts
[Networking Concepts](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/NetworkingConcepts/Introduction/Introduction.html#//apple_ref/doc/uid/TP40012487)

<!-- more -->

### Networking Terminology
**host**: a host is any device that is connected to a network and provides an endpoint for networked communication.It is called a host because it hosts the applications and daemons that run on it.  
**nfrastructure device**: an infrastructure device is any piece of equipment that is responsible for making the network function.
When one host sends data across a network, it divides the data into small pieces called packets.
A packet generally contains three basic parts: a **header** that tells where the packet should be sent, a **payload** that contains the actual data, and a **trailer** that contains checksum information to ensure that the packet was received correctly.  
**encapsulation**: When one packet contains another packet (generally of a different type), this is called encapsulation.
### Networking Layers
**Link Layer**: The bottommost layer is the link layer, or **physical layer**.This layer of the networking stack involves the actual hardware used to communicate with nearby physically connected hosts.A network interface is a piece of hardware that provides a link-layer interconnect.  
**IP Layer**: Sitting on top of the link layer is the IP layer. The IP layer provides packet transport from one host to another in such a way that the packets can pass across multiple physical networks. The path your packets take is called a route, and each link that the packets follow from one router to another along the route is called a hop.To hide this difference, the IP layer splits packets into multiple pieces—a process known as fragmentation—and reassembles them at the other end.  
**Transport Layer**: On top of the IP layer, you’ll find several transport layers. The two most common protocols at this layer are the transmission control protocol (TCP) and the user datagram protocol (UDP). Both TCP and UDP provide basic data transport from one host to another, much like IP, but add the notion of port numbers.  
**UDP**  
  No guarantee ---- Like the layers below it, UDP provides no guarantee that the data will ever reach its destination.  
  Low latency ---- UDP may be a good choice for situations where low latency is required.  
  Broadcast messages in IPv4 ---- packets sent to a broadcast address are received by every host within its broadcast domain.  
  Multicast messages ---- UDP packets sent to a multicast address are sent out to any host that subscribes to them.  
  Preservation of record (packet) boundaries ---- With UDP, the receiver sees each message individually instead of as a continuous stream of bytes.  
**TCP **   
  Delivery guarantees ---- Data transmitted using TCP is guaranteed to be received in the order in which it was sent and (connection failures notwithstanding) in its entirety.  
  Congestion control ---- Sending hosts back off the speed of transmission (and retransmission) if data is getting dropped along the way due to an over-utilized link.  
  Flow control ---- When busy, receiving hosts tell sending hosts to wait until they are ready to handle more data.  
  Stream-based data flow ---- Your software sees the data as a series of bytes instead of as a series of discrete records (messages, in UDP parlance)  
  Path MTU discovery ---- TCP chooses the largest packet size that avoids fragmentation en route.  
**Application Layer**: The application layer sits at the top of the protocol stack. This layer includes such protocols as hypertext transfer protocol (HTTP) and file transfer protocol (FTP).

### Understanding Latency
Latency refers to the round-trip time for a request. Every network has latency.  
The minimum latency between two points on the earth can be calculated by dividing the distance by the speed at which light or electricity moves in a particular medium.  
Routing delays.  
Retransmission and exponential backoff.  
Signal propagation delays within hardware that receives, transmits, forwards, or repeats packets.

### Addressing Schemes and Domain Names
At every level of networking, each host is assigned one or more numeric identifiers that uniquely represent it within a particular network.  
**Link-Layer Addressing**: At the link layer (physical layer), each network interface is usually identified by a globally unique hardware ID: Ethernet—MAC address etc.The hardware ID is used to determine whether a particular device should listen to a packet or ignore it.  
**IP-Layer Addressing**: At the IP layer and above, hosts are identified by an IP address. An IP address can be in one of two forms: IPv4 or IPv6.  
  **IPv4**: An IPv4 address consists of four bytes, and is usually represented to the user as a series of four numbers separated by decimal points.  
  **IPv6**: An IPv6 address is a 128-bit value, and is usually written as eight groups of 16-bit hexadecimal numbers separated by colons. Leading zeros in each group can be omitted as long as there is at least one digit in each group.  
**Domain Name System (DNS)**:
A domain name is a human-readable name that describes a particular host. Each domain name is made up of a series of parts separated by periods.  
Features:  
  Minimize service disruption when an IP address changes.  
  Allow a host to be accessed by more than one address.  
  Allow multiple physical hosts to pretend to be a single host.  
  Can adapt to changes in the underlying technology.  
  Being easier to remember.  

### Packet Routing and Delivery
Each packet contains a the link-layer address of its intended recipient.

## Networking Overview
[Networking Overview](https://developer.apple.com/library/content/documentation/NetworkingInternetWeb/Conceptual/NetworkingOverview/Introduction/Introduction.html#//apple_ref/doc/uid/TP40010220)

## Bonjour Overview
[Bonjour Overview](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/NetServices/Introduction.html#//apple_ref/doc/uid/10000119i)  

The Bonjour zero-configuration networking architecture provides support for publishing and discovering TCP/IP-based services on a local area or wide area network.  
Bonjour is Apple’s implementation of a suite of zero-configuration networking protocols. Bonjour is designed to make network configuration easier for users.

## NSNetServices and CFNetworkServices Programming Guide
[NSNetServices and CFNetworkServices Programming Guide](https://developer.apple.com/library/content/documentation/Networking/Conceptual/NSNetServiceProgGuide/Introduction.html#//apple_ref/doc/uid/TP40002736)  

## DNS Service Discovery Programming Guide
[DNS Service Discovery Programming Guide](https://developer.apple.com/library/content/documentation/Networking/Conceptual/dns_discovery_api/Introduction.html#//apple_ref/doc/uid/TP30000964)  
