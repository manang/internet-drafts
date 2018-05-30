---
title: Hybrid Information-Centric Networking
abbrev: hICN
docname: draft-muscariello-intarea-hicn
date:
category: info

ipr:
area:
workgroup:
keyword: Internet-Draft

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:

-
    ins: L. Muscariello
    name: Luca Muscariello
    role: editor
    org: Cisco Systems Inc.
    email: lumuscar@cisco.com
-
    ins: G. Carofiglio
    name: Giovanna Carofiglio
    role: editor
    org: Cisco Systems Inc.
    email: gcarofig@cisco.com
-
    ins: J. Augé
    name: Jordan Augé
    role: editor
    org: Cisco Systems Inc.
    email: augjorda@cisco.com
-
    ins: M. Papalini
    name: Michele Papalini
    role: editor
    org: Cisco Systems Inc.
    email: mpapal@cisco.com
-
    ins: M. Sardara
    name: Mauro Sardara
    role: editor
    org: Cisco Systems Inc.
    email: msardara@cisco.com
-
    ins: A. Compagno
    name: Alberto Compagno
    role: editor
    org: Cisco Systems Inc.
    email: acompagn@cisco.com

normative: 
    RFC0793:
    RFC1081:
    RFC1624:
    RFC3587:
    RFC3550:
    RFC4291:
    RFC4302:
    RFC8200:
informative:
    I-D.irtf-icnrg-ccnxsemantics:
    I-D.irtf-icnrg-ccnxmessages:
    I-D.irtf-icnrg-mapme:
    I-D.irtf-icnrg-terminology:
    CCN: DOI.10.1145/1658939.1658941
    NDN: DOI.10.1145/2656877.2656887
    MAN: DOI.10.1109/INFCOMW.2012.6193505
    WLD: DOI.10.1145/2984356.2984361


--- abstract

This documents describes

--- middle

# Introduction
Information-Centric Networking (ICN) identifies a networking paradigm
centering communication around data, in contrast to host location.
ICN offers simplified and more efficient user-to-content communications
that resolves the mismatch between content-centric usage and underlying
host-to-host foundations of the Internet.

The  objective of this document is to describe hybrid ICN, a network protocol 
that fully integrates ICN in IPv6, at a minimum cost in terms of required modifications 
in end-points and routers and in a way to guarantee transparent interconnection 
with IP without using overlays.

The ICN reference design used in this document is CCNx as  described in
{{I-D.irtf-icnrg-ccnxsemantics}} and {{I-D.irtf-icnrg-ccnxmessages}}. 
IPv6 is used unchanged as described in {{RFC8200}}.

There are some basic principles behind the hICN architecture: (i) 
the network can transport any kind of application, i.e. hICN can serve
content-distribution or real-time applications just to cite to examples
of applications with very different requirements. (ii) hICN offers connection-less
and location independent communications by identifing data with unambiguous
names instead of just naming network interfaces. (iii) An hICN capable
node accepts a data packet from an ingress interface if and only if at least 
one matching request packet is stored in the local cache of the node otherwise
the data packet is dropped. (iv) In hICN basic security services are offered
by the archictecture: a cryptographic signature across a security envelop 
is computed by the producer (using its own private key) and must be verified by the consumer 
(using the producer public key).
The security envelop cab be as fine grained as a single data packet or 
can also be made on groups of packets using the technique of the transport manifest.
The security service provides data integrity and producer authenticity.

# Architecture
The communication model described in this document covers the transport
and the network layer.

The network layer components include the forwarding plane only and do not 
consider the routing plane. The assumption here is that existing routing 
protocols working for IPv6 should be reused as much as possible as they are. 
Improvements to existing routing protocols are out of scope and might be developped in
other documents  to better exploit novel features made available by the hICN
forwarding plane.
 
The hICN network service can run on top of any link-layer protocol and  requires that
interfaces have a valid IPv6 address. This can be local or global depending on
the deployment. hICN data names are globally routable names which are visible
to the transport layer. Conversly, the transport layer has no visibility of addresses of 
network interfaces (known as locators).
The network layer forwards two kind of protocol data units: the request and the
reply, called interest and data packets.

The network layer offers a communication service to the transport layer by
means of a local unidirectional channel that we call local or application face.
This channel is used by the transport layer to send requests and receive
replies or to send replies upon receptions of requests.

A transport end-point is always a unidirectional channel that is used to
either send data or receive data. The former end-point is called
data producer while the latter data consumer. The producer end-point
produces data under a location independent name, which is a globally routable 
IPv6 prefix. A consumer end-point fetches data by using the non ambiguous
name as provided by the producer.  The producer end-point is responsible 
for managing the usage of the prefix in terms of provisioning, association to
applications and revocation.

The transport end-point offers two kind of services to applications: a producer
and a consumer service. The service is instantiated in the application by
opening communication sockets with an API to perform basic transport service
operations: allocation, initialization, configuration, data transmission and reception.

The producer and consumer sockets can implement different types of services
such as stream or datagram, reliable or unreliable.
In all cases all transport services are connection-less, meaning that
a producer transport service produces named data in a socket memory that is
accessible by any valid request coming from one or multiple consumers.
The consumer, on the other hand, retrieve named data using  location independent
names which are not tied to any interface identifier (also called locator).
This transport model allows to implement reliable consumer mobility without
any special mobility management protocol. hICN supports communication of multi-homed
end-hosts without any special treatment in the transport layer.
The hICN network layer can also implement more robust usage of multi-path
forwarding in IPv6 networks as balanced request/reply flows self-stabilize
network congestion. The typical IP load balancers stickiness is not
a requirement in hICN as the consumer end-point is robust to out of order 
data reception by design.

A data packet is an IPv6 packet with a transport layer header
carrying data from an application that produces data.
An interest packet is an IPv6 packet with a transport layer header
and is used to unambiguously fetch a data packet from a producer end
point.

## End-points
In hICN we have two novel endpoints: the producer and the consumer.
We identify two kind of communication sockets each with a specific API: the
producer and consumer sockets. These socket types are designed to exchange data
in a multi-point to multi-point manner. The producer-consumer model is a well
known design concept for multi-process synchronization where a shared memory is
used to let multiple consumers to retrieve the data that is made available by
producer processes into the same memory.
In (h)ICN we have the same concept that is applied to a network where memories
are distributed across the communication path. The first memory in the path is
the production buffer of the producer end-point that forges Data Packets and
copies them into a shared memory isolated into a namespace. Consumer sockets can
retrieve data from such memory by using the (h)ICN network layer.
The model just described is an inter-process communication example (IPC) that
requires data to cross a communication network by using a transport protocol.

The way consumers and producers synchronize depends on application requirements
and the transport layer exposes a variety of services: stream/datagram,
reliable/unreliable, with or without latency budgets etc. Independently of the
specif requirements of the applications, producer sockets always perform data
segmentation from the upper layer into Data Packets, as well as compute 
digital signatures on the packet security envelop. This envelop can
also be computed across a group of packets, by including a cryptographic hash of
each packet into the transport manifest, and eventually signing only such
manifest. This is a socket option that can bring significant performance
improvement. The manifest encoding we use in this paper is reported in
Fig.\ref{fig:manifest} and has to fit into an MTU.

The consumer socket, on the other end, always performs reassembly of Data
Packets, hash integrity verification and signature verification. This is common
to all architectures in (h)ICN. The usual assumption is that the producer socket
uses an authentic-able identity while using namespaces that it has been
assigned. The end-point must be able to manage the mapping of her identity and
the allocated namespace by issuing digital certificates about the mapping. The
consumer end-point must retrieve the associated certificate to perform the basic
operations. It is out of scope for this paper how to design and implement a
scalable system to perform such certificate operations.


## Naming
In hICN, two name components are defined: the name prefix and the name suffix.
The name prefix is used to identify an application object, a service or 
in general an application level source of data in the network. 
This is incarnated by a listening socket that binds to the name prefix.
The name suffix is used to index segmented data within the scope of the 
name prefix  used by the application. 

For instance an RTP {{RFC3550}} source with a given SSRC can be mapped 
into a name prefix.  Single RTP sequence numbers can be mapped into namex suffixes.
For example an HTTP server can listen to a name prefix to serve HTTP requests.
An HTTP reply with large payload with require the transport layer to segment 
the application data unit according to an MTU. Name suffixes are used to
index each segment in the socket stream.

More details about how to use hICN to transport HTTP or RTP will be given
in a different document. 

### Name prefix
The format of an hICN name prefix is the following:

~~~~~~~~~~
|            64 bits             |        64 bits                |
+--------------------------------+-------------------------------+
|           routable prefix      |       data identifier         |
+----------------------------------------------------------------+
      
~~~~~~~~~~

It is composed of a routable IPv6 /64 prefix as per {{RFC3587}} which
SHOULD be globally routable. The data identifier is encoded in 64 bits.
An application can use several identifiers if needed.

From the description given above, the name prefix is a location independent 
name encoded in an IPv6 address. 

### Name Suffix
The name suffix is used by the transport layer protocol to index segments.
The segment MUST be indexed in the end-points and in the network with the same
suffix. This implies that there is one transport segment per IP packet and that
IP fragmentation is not allowed.


~~~~~~~~~~
            |            32 bits         | 
            +-----------------------------
            |           name suffix      |      
            +-----------------------------
      
~~~~~~~~~~


## Packet Format
Two protocol data units are defined below: the interest (request)
and the data (reply).

They are composed of a network and transport header. The transport header is
the same for both packet types while the network header is slightly different.


### Interest Packet 

~~~~~~~~~~

0                   1                   2                   3
0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version| Traffic Class |             Flow Label                |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Payload Length        |  Next Header  |   Hop Limit   |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+                                                               +
|                                                               |
+                         Source Address                        +
|                                                               |
+                                                               +
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+                                                               +
|                                                               |
+                          Name Prefix                          +
|                                                               |
+                                                               +
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
                  Interest Packet Header Format
~~~~~~~~~~


    Source Address:       128-bit address of the originator of the packet
                          (possibly not the end-host but a previous hICN node).

    Name Prefix:          128-bit name prefix of the intended service.


### Data Packet

~~~~


0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version| Traffic Class |             Flow Label                |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Payload Length        |  Next Header  |   Hop Limit   |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+                                                               +
|                                                               |
+                         Name Prefix                           +
|                                                               |
+                                                               +
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+                                                               +
|                                                               |
+                          Source Address                       +
|                                                               |
+                                                               +
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
                  Interest Packet Header Format
~~~~

    Name Prefix:          128-bit name prefix of the intended service.
    
    Source Address:       128-bit address of the destination of the packet
                          (possibly not the end-host but the next hICN node).

~~~~

0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                          Name Suffix                          |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                           Path Label                          |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Data |   Time    |M|A|S|R|S|F|        Loss Detection         |
| Offset|   Scale   |A|C|I|S|Y|I|         and Recovery          |
|       |           |N|K|G|T|N|N|                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Checksum            |             Lifetime          |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~

    Name Suffix:       32-bit name-suffix of the packet  
                       (possibly not the end-host but a previous hICN node).

    Path Label:        32-bit label used to carry an encoding of the path
                       between the consumer and data responder, be it an
                       intermediate  node or the producer end-point. 

    Time Scale:        6-bit natural number in the range 1-64 used as a scaling 
                       factor for time calculations. If not null it is used
                       to scale lifetime. 

    Manifest:          flag to indicate the packet carries a transport manifest
                       in the payload.

    Signature:         flag to indicate the packet carries an authentication
                       header with a signature. Interest packet do not carry
                       signatures.

    Loss Detection:    16-bit natural number used to implement data 
    and Recovery       sequencing on per adjacency basis to detect an 
                       recover losses using the mechanism WLDR described 
                       in {{WLD}}.

    Lifetime:          16-bit unsigned integer to carry the packet lifetime in
                       milliseconds.  
                       
    Checksum:          Updated using RFC 1624.




## Packet cache
The packet cache is a router local memory used to remporarily store requests and reply.
The simplest incarnation of the packet cache MUST index packets by full name, i.e.
the concatenation of the name prefix and suffix.
Insertion and deletion of packets in the cache is described below.

## Forwarding
The forwarding path in hICN is composed of two components: the interest and 
data path. Requests and replies are processed at the hICN node 
in a different way. Both forwarding paths, to be implemented, require
a packet cache to be incorporated into the router. The  cache 
is used to temporarily store requests and replies for a relatively short
amount of time.  

By caching a request in an hICN node, the reply can be transmitted back 
to the right nodes as the source address field in the interest 
contains the interface identifier of the hICN node 
having transmitted the request.  Replies are optionally cached if needed.

This means that the interest forwarding path is based on lookups in the IP FIB
just like any other IP packet, with the additional processing due to a cache lookup
to check if the actual reply is already present in the local cache for expedited
reply.

On the other hand,  data packet forwarding  is similar to label switching, being the packet 
name identifier (prefix plus suffix) the forwarding label. The next hop for the reply in transit
is indeed selected by using information in a cached matching request.

The name prefix in the packet header is never modified along the path for both
requests and replies, while the locator, i.e. the interface identifier 
written in the source or destination address field, for
interest or data packets respectively, is modified at the egress of the router
as reported below.
  
### Interest Path
At the router ingress the incoming interest packet I is parsed to obtain the name prefix and the name suffix.
An exact match look up is made in the packet cache using the full packet name as key.
Based on the outcome of the lookup the following options are possible:

 1. at least one match is found.
 
     1.1. If one match is a data packet D, other matches are ignored, and D is prepared for transmission by setting D's  
     destination address with I's source address. D is passed to the egress to further processing before transmission.
     For instance the next-hop MAY be selected  by using the router IPv6 FIB (longest prefix match). 
     The IPv6 FIB lookup MIGHT be saved
     in case the next-hop can be derived directly from information previously derived by processing the incoming 
     interest packet I. I is eventually dropped.
     
     1.2. There is one or multiple matches and all are interest packets.
     
       - One matching interest has the same source address and I is classified as duplicate and further processed as 
         duplicate.
       - Matching interest packets have different source addresses and I is classified as filtered and stored in 
         the cache.
 2. a match is not found and I passed to the egress for further processing to determine the next-hop by using 
 the router IP FIB.
    
 Notice that the destination address field in the interest packet is polymorphic as it has two different types
 based on the data structured it is looked-up against. It has the type of a location independent name while used to find
 a match in the packet cache and it has an address prefix type to find the next-hop in the IPv6 FIB.
Polymormism is transparent for the forwarding plane while it has several implications in the control plane.

~~~~
           Packet Cache
 RX       +------------+
 Interest |            | Translation Operation
+---------> Data Hit   | IPv6Hdr(Data).DstAddr:= IPv6Hdr(Interest).SrcAddr 
 TX       |      +     |
 Data     |      |     |
<---------+ <----+     |  
          +------------+
~~~~


~~~~

          +----------------+    +--------+
 Interest |                |    |        | Egress NIC
  +-------> Data Miss      +--->+ IP FIB |+----->
          |                |    |        | Translation Operation
          | Interest Miss  |    |        | IPv6Hdr(Interest).SrcAddr:= NIC.Addr
          |                |    |        |
          +----------------+    +--------+
          
~~~~


~~~~
                                 Same src addr
         Packet Cache            +-----------+
         +--------------+        | Duplicate |
Interest |              |        +-----^-----+
 +-------> Data Miss    |              |
         | Interet Hit+-------------->-+
         |              |              |
         +--------------+              |
                                 +-----v-----+
                                 |Filtered   |
                                 +-----------+
                                 Different src addr
          
~~~~


### Data Path
At the router ingress the incoming data packet D is parsed to obtain the name prefix and the name suffix.
An exact match look up is made in the packet cache using the full packet name as key.
Based on the outcome of the lookup the following options are possible:

1. one or multiple matching interest packets are found
    1.1. The data packet D is cloned to have as many copies as the number of matching interests including D. 
    The destination address field of each copy of D is set with the source address field of each interest packet.
    All copies are passed to the egress to further processing before transmission in order to find each data packet's 
    next-hop.
2. No matching is an interest packet and the D is dropped.

### Additional packet processing





~~~~
    RX
    Data   +-----------+
+--------> | Interest  |
           | Hit       |
           |  +        |
           +-----------+
              |
              |
              |
              |       Translation Operation
              |       +------>
              |       | IPv6Hdr(Data[1]).DstAddr:=IPv6Hdr(Interest[1]).SrcAddr
              |       | TX Data[1]
              +-----> |
                      |  ...
                      |
                      |
                      |  IPv6Hdr(Data[N]).DstAddr:=IPv6Hdr(Interest[N]).SrcAddr
                      |  TX Data[N]
                      +------>



~~~~

~~~~

  RX             Packet Cache
  Data      +------------------+ Drop Data
+---------->+   Interest Miss  +------>
            |   OR Data hit    |
            +------------------+
~~~~


# Security
hICN inherits ICN data-centric security model:
integrity, data-origin authenticity and confidentiality are tied to
the content rather than to the channel.  
Integrity and data-origin authenticity are provided
through a digital signature computed by the producer and included in
each data packet. Integrity and data-origin authenticity qre provided in two ways
using two approaches: the first one based on IP Authenticated Header
{{RFC4302}} and the second one based on transport manifests.
When using IP AH, the signature is computed over (i)IP or extension
header fields either immutable in transit or that 
are predictable in value upon arrival at the consumer, (ii) the AH
header with the signature field set to zero. We recall that in hICN
the destinqtion header field is not immutable nor predictable and must be set
to zero for the signature computation. We also point out 
the AH in placed after the TCP header in order to prevent any kind of filtering from
network devices such as middleboxes.

~~~~~~~~~~
0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Next Header  |  Payload Len  |    ValidAlg   |               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                          Timestamp                            |
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
/                                                               /
/                            KeyID                              / 
/                                                               /
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
/                                                               /
/                           Signature                           /
/                                                               /
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~

    ValidAlg:          8-bit index to indicate which validation algorithm
                       must be used to verify the signature. 

    Timestamp:         64-bit time stamp that indicates the validity of the
                       signature.

    KeyID:             256-bit key identifier.

    Signature:         Variable length field that carries the cryotographic
                       signature of the security envelope.
                       It is 128 bytes for RSA-1024, 256 bytes for RSA-2048,
                       56 bytes for EDCSA 192, 72 bytes for ECDSA 256.

The transport manifest  is a L4 entity computed 
at the producer which contains the list of names of a group of data packets to convey 
to  the consumer. hICN cryptographic hashes of data packets are then computed instead 
of signatures. However, the manifest must be signed to guarantee a level of security 
equivalent to packet-wise signatures.

hICN is oblivious of the trust model adopted by consumers and works with 
any of the existing proposals. 
    

~~~~~~~~~~
0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version| MType |HashAlg|NextStr|     Flags     |NumberOfEntries|
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+                                                               +
|                                                               |
+                             Prefix                            +
|                                                               |
+                                                               +
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                           Name-suffix[1]                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                           Hash Value[1]                       |
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
                               . . . 
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                           Name-suffixv[NumberOfEntries]       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                           Hash Value [NumberOfEntries]        |
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+


~~~~~~~~~~
    Version:          8-bit index to indicate which validation algorithm
                      must be used to verify the signature. 

    MType:            64-bit time stamp that indicates the validity of the
                      signature.

    HashAlg:          256-bit key identifier.

    NextStr:          Encode an operator use to predict the name-suffix
                      sequence  

    Flags:            Flags.

    NumberOfEntries:  8-bit field that encodes the number of packets indexed
                      in the manifest.

    Name-prefix:      128-bit field carrying the name-prefix common to all
                      packets indexed in the manifest.
                      
    Name-suffix:      32-bit field carrying the name-suffix.

    Hash-value:       256-bit field carrying the SHA-256 hash of the packet
                      security envelop.  
     

# Transport
The transport layer in hICN provides segmentation and reassembly services to 
the applications, and sits on top of the hICN network layer which provides a 
request/reply service to the transport layer itself. End-points in TCP/IP can be 
described as acting as transmitters or receivers, or both at the same time, 
whereas hICN transport is oriented to the consumer/producer paradigm where a 
transmission is always triggered by a matching request. Moreover, a TCP connection 
is a bidirectional communication channel, while in hICN there are always two 
unidirectional sockets: the consumer and the producer. Transport protocols in 
hICN can be stream or datagram oriented, depending on the application (as TCP or UDP).


# IANA Considerations
There are no IANA considerations in this specification.