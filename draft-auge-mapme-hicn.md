---
title: "MAP-Me/hICN : Managing Anchorless Mobility in Hybrid ICN (hICN)"
abbrev: Managing Anchorless Mobility in hICN
docname: draft-irtf-icnrg-mapme-hicn
date: 2018-03
category: info

ipr:
area: general
workgroup: icnrg
keyword: Internet-Draft
keyword: Information-Centric Networking
keyword: Hybrid ICN

stand_alone: yes
pi: [toc, sortrefs, symrefs]

author:
-
    ins: J. Augé
    name: Jordan Augé
    role: editor
    org: Cisco Systems Inc.
    email: augjorda@cisco.com
-
    ins: G. Carofiglio
    name: Giovanna Carofiglio
    role: editor
    org: Cisco Systems Inc.
    email: gcarofig@cisco.com
-
    ins: L. Muscariello
    name: Luca Muscariello
    role: editor
    org: Cisco Systems Inc.
    email: lumuscar@cisco.com
-
    ins: M. Papalini
    name: Michele Papalini
    role: editor
    org: Cisco Systems Inc.
    email: micpapal@cisco.com

normative:
  RFC0792: icmp
  RFC3971: send
  RFC4389: ndp
  RFC4861: icmpv6
  RFC5909: send-proxy
  RFC6496: send-ndp

informative:
  draft-irtf-icnrg-mapme:
    title: "MAP-Me : Managing Anchorless Mobility in Content Centric Networking"
    author:
    -
        ins: J. Augé
        name: Jordan Augé
        role: editor
        org: Cisco Systems Inc.
        email: augjorda@cisco.com
    -
        ins: G. Carofiglio
        name: Giovanna Carofiglio
        role: editor
        org: Cisco Systems Inc.
        email: gcarofig@cisco.com
    -
        ins: L. Muscariello
        name: Luca Muscariello
        role: editor
        org: Cisco Systems Inc.
        email: lumuscar@cisco.com
    -
        ins: M. Papalini
        name: Michele Papalini
        role: editor
        org: Cisco Systems Inc.
        email: micpapal@cisco.com
    date: 2018
  compagno2017secure:
    title: Secure Producer Mobility in Information-Centric Network
    author:
     -
       ins: A. Compagno et al.
    date: 2017
    series:
      name: ACM SIGCOMM ICN



--- abstract
This documents describes a realization of MAP-Me, an anchorless producer
mobility management solution, within the Hybrid ICN (hICN) context.

--- middle

# Introduction

Information Centric Networking (ICN) appears as a promising paradigm for solving
issues faced by IP networks in mobility management, IoT, content distribution to
name a few. Consumer mobility is natively supported, while low-overhead and
anchor-less schemes have been developed to support producer mobility. The main
idea is to send a message between the new and the old position of the producer,
flipping forwarding entries of all encountered routers on the way so as to
rebuld a new valid forwarding graph rooted at the new producer location.

Hybrid Information Centric Networking (hICN) has been proposed as a
viable candidate for ICN insertion into existing IP networks, showing
interesting feasibility and scalability properties. A major feature of hICN is
its ability to transparently traverse regular IP equipments as hICN packets are
encoded directly into RFC compliant IP packets, just with a different semantic
for routers in the know.

This document discusses the introduction of such a producer mobility solution
into hICN, which is non trivial as it means affecting the forwarding entries of
regular IP routers on the way.


# Implementation in an hICN network

In this section, we describe the changes to a regular hICN architecture required
to implemented MAP-Me and detail the above described algorithms. This requires
specifying the packet format carrying special Interest messages, additional
temporary information associated to the FIB entry and additional operations to
update such entry.

In the spirit of hICN, we use RFC compliant ICMPv4 and ICMPv6 messages which
allows the protocol to work with regular IP routers that might not have been
updated with hICN functionalities. ICMP message have the interesting capability
of being forwarding plane messages, some of which have the ability to affect the
configuration of routers themselves. This is the case of the special ICMP
redirect messages, available both in ICMPv4 and ICMPv6 (as part of the Neighbour
Discovery process). Because those messages are limited to a subnet, we
additionally rely on proxy functionalities also available in off-the-shelf.


# Reusing ICMP redirects and proxy functionalities

The first proposal consists in reusing ICMP redirect messages (available in IPv4
{{RFC0792}} and IPv6 {{RFC4861}} through the Neighbour Discovery process) which
allow routerss to inform an end-host about a more appropriate gateway for future
packets towards a given IP or prefix. Those messages are typically sent after
receiving a message for which the router knows a better next hop on the current
subnet.

We propose to forge existing control packets standardized and commonly found in
network deployments so as to perform reverse path repair as requires by our
mobility protocol. We have so far identified a non-exhaustive set of
implementation possibilities for which we detail the behaviour and packet
formats, and the relative benefits/possible issues.

In order to extend this scheme network-wide, we can build on the proxy
functionalities that have been introduced such as Proxy ARP (to confirm) and
Proxy ND, and that are in particular used in (Proxy) Mobile IP solutions to
allow the home agent to attract traffic addressed to the mobile agent.

We assume that an operator willing to deploy hICN in its network would be able
to modify the configuration of its IP equipments to allow for ICMP redirects, as
well as the ARP and Neighbour Proxy functionalities. In hICN-enabled routers,
full-protocol support would be implemented, while falling back to regular ICMP
redirect processing otherwise.

By reusing existing protocols, this solution allows a straightforward
introduction in existing networks, without the need to implementing and
standardizing a new protocol that would affect forwarding decisions, for
instance a routing protocol with a higher administrative distance. We will see
later that this also allows to reuse existing security solutions, although some
functionalities such as sequence number checking will have to be extended to
account for the presence of regular IP nodes.


# MAP-Me messages

## IPv6 flavour

### Interest Update

MAP-Me introduces two new special packets, denoted Interest Update, and Interest
Notification, with their respective acknowledgements. We consider first the IPv6
flavour of hICN, where Interest Update packet are encoded as ICMPv6 redirect
packets, as illustrated in Figure {{fig-v6-iu}}.

Those packets should be recognized as legitimate redirect traffic in order to
impact the routing cache.


The type is set to 5 as recommended by {{RFC4861}}, and
the code to 0 (effect ?). As in the original proposal, those packets have the
form of Interests, as their source address is the locator of the originating
hICN router, and and their destination a content prefix. They thus follow the
regular interest path in hICN forwarders. Following hICN design, the source
address will be rewritten upon traversing an hICN router (XXX or IP with NDP),
while the destination address will be unchanged.

The "target address"...

The "destination address" of the ICMP header...

Like in the original proposal, those packets carry a sequence number encoded in
the ICMPv6 payload.

In addition to the original proposal, they also transport a prefix length (in
bits) as all the names in hICN have a fixed size.

The security tokens related to the mechanism are discussion in Section
{{sec:security}}.

~~~~~~~~~~
   0                   1                   2                   3
   0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-- IPv6
   |Version| Traffic Class |           Flow Label                  |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |         Payload Length        |  Next Header  |   Hop Limit   |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+h-+-+-+-+
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
   +                      Destination Address                      +
   |                                                               |
   +                                                               +
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-- ICMPv6
   |     Type=5    |     Code=1    |          Checksum             |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                           Reserved                            |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                               |
   +                                                               +
   |                                                               |
   +                       Target Address                          +
   |                                                               |
   +                                                               +
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                                                               |
   +                                                               +
   |                                                               |
   +                     Destination Address                       +
   |                                                               |
   +                                                               +
   |                                                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |   Options (EMPTY)                                             |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-- payload
   |                             seq                               |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |     len       |                   PADDING                     |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~
{: #fig-v6-iu title="IU propagation example"}

Version
Traffic Class
Flow Label
Payload Length
Next Header
: Unchanged

Hop limit:
: Unchanged for Interest Updates. Set to 1 for Interest Notification.

Source Address
:

Destination Address
:

Type
: ICMP redirect packets are identified by a type field set to 5.

Code
: ICMP redirect codes have the following meaning:
- 0 = Redirect datagrams for the Network.
- 1 = Redirect datagrams for the Host.
- 2 = Redirect datagrams for the Type of Service and Network.
- 3 = Redirect datagrams for the Type of Service and Host.
In MAP-Me, we use codes equal to 0.


XXX we should not need prefix len thanks to code 0 ????

Checksum
Reserved
: Unchanged
    The checksum is the 16-bit ones's complement of the one's
    complement sum of the ICMP message starting with the ICMP Type.
    For computing the checksum , the checksum field should be zero.
    This checksum may be replaced in the future.
    (RFC 792)

Target Address
:

Destination Address
:

Options:
: Unchanged (unset)

Payload
: Sequence Number and Name Length

    A sequence number : used to handle concurrent updates and prevent
    forwarding loops during signaling, and to control discovery interests
    during consumer interest propagation;


    Gateway Internet Address : Address of the gateway to which traffic for the
    network specified in the internet destination network field of the
    original datagram's data should be sent.
    (RFC 792)

### Interest Update Acknowledgement

ICMP redirect packets are typically not acknowledged. We use a regular ICMP
error which according to {{RFC0792}} cannot be discarded by intermediate routers.

- need to reach previous hICN hop
- as we won't have nat function, we need a way to unambiguously identify it !

### Interest Notification and Interest Notification Acknowledgement

Interest Notification packets are similar to Interest Update at the difference
that their time-to-live, the *hop limit* field in the IPv6 header, is set to 1.
Those packet are recognized by an hICN router, processed, and as expected not
forwarded. The ICMP TTL expired might be used as an acknowledgement.

## IPv4 flavour

~~~~~~~~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-- IPv4
   |Version|  IHL  |Type of Service|          Total Length         |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |         Identification        |Flags|      Fragment Offset    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |  Time to Live |    Protocol   |         Header Checksum       |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                       Source Address                          |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                    Destination Address                        |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-- ICMP
   |                    Options                    |    Padding    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |     Type=5    |     Code=0    |          Checksum             |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-- Redirect
   |         Gateway Internet Address = Content prefix             | FAUX
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-- Payload
   |             seq               |                               | Orig.: Internet header
   +-------------------------------+            PADDING            | + 64 bits or Original
   |                                                               | Data Datagram
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~
{: #fig-v4-iu title="Interest Update packet format, as ICMPv4 redirect"}

~~~~~~~~~~
    0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-- IPv4
   |Version|  IHL  |Type of Service|          Total Length         |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |         Identification        |Flags|      Fragment Offset    |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |  Time to Live |    Protocol   |         Header Checksum       |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                       Source Address                          |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                    Destination Address                        |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-- ICMP
   |     Type=42    |     Code=0    |          Checksum             |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~
{: #fig-v4-iuack title="Interest Update Acknowledgement, as ICMPv4 packet"}



# MAP-Me additional Network Information

In hICN routers, the FIB/TFIB data structure should be maintained as described
in {{draft-irtf-icnrg-mapme}}.

In IP routers, we expect both ICMP redirect and ND proxy functionalities to be
enabled. Updated forwarding information use to be stored in the route cache,
which was removed from Linux kernel v3.6.

TODO

Alternatively, similar functionalities can be enabled through a local daemon.

TODO

Or rely on additional mechanisms such as SRv6.

TODO


# IU/IN transmission at producer

IU/IN transmission at producer is dictated by an update in the adjacency graph,
which is assumed to be monitored by a Face Manager component.

In hICN, the Face Manager monitors the layer 3 IP adjacencies that is discovered
through the Neighbour Discovery protocol, and create hICN connections
appropriately.

As in the original draft, upon discovery of a new adjacency, and for each
locally served prefix, the sequence number maintained by the producer is
incremented and a corresponding Special Interest is sent on the newly created
connection.


# Algorithm description

The algorithms remain the same, although in an hybrid deployment with both hICN
and IP nodes, the sequence number check might be delayed up until the next hICN
router. In that case, the normal correcting mechanism proceeding by sending the
updated IU backwards should fix potential issues during concurrent updates.

ICMP redirect and LPM ???????
Quick if prefix not contained in FIB, as we should not create FIB entries ?

Assuming the operator can activate this function within its network, the sending
of an ICMP redirect message from the producer's new location towards its own IP
address should traverse intermediate nodes configured as proxy, and recursively
update the routing cache tables with the desired next hop gateway. Upon
completion, the process would have built a prefered path for reaching the
producer's IP address through the reverse path between is former and newer
location.


# Security considerations {#sec:security}

All mobility management protocols share the same critical need for securing
their control messages which have a direct impact on the forwarding of users'
traffic. {{compagno2017secure}} reviews standard approaches from the literature
before developing a fast, lightweight and distributed approach based on hash
chaining that can be applied to MAP-Me and fits its design principles.

All mobility management protocols share the same critical need for securing
their control messages which have a direct impact on the forwarding of users'
traffic. {{compagno2017secure}} reviews standard approaches from the
literature before developing a fast, lightweight and distributed approach that
can be applied to \name and fits its design principles.

Finally, security of the scheme can be ensured via existing the existing SEND
mechanism {{RFC3971}} (and its proxy extensions {{RFC6496}}, {{RFC5909}}) for
backwards compatibility.


# Acknowledgements


# Contributors


# IANA Considerations {#iana}

This memo includes no request to IANA.

