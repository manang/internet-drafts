---
title: "MAP-Me : Managing Anchorless Mobility in Content Centric Networking"
abbrev: Managing Anchorless Mobility in CCN
docname: draft-irtf-icnrg-mapme
date: 2018-03
category: info

ipr:
area: general
workgroup: icnrg
keyword: Internet-Draft
keyword: Information-Centric Networking
keyword: Content-Centric Networking
keyword: Named-Data Networking

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
  RFC6301:

informative:
  MAPME:
    title: "MAP-Me: Managing Anchor-less Producer Mobility in Content-Centric Networks"
    author:
      -
        ins: J. Augé
        name: Jordan Augé
        org: Cisco Systems
      -
        ins: G. Carofiglio
        name: Giovanna Carofiglio
        org: Cisco Systems
      -
        ins: G. Grassi
        name: Giulio Geassi
        org: UPMC / UCLA
      -
        ins: L. Muscariello
        name: Luca Muscariello
        org: Cisco Systems
      -
        ins: G. Pau
        name: Giovanni Pau
        org: UPMC / UCLA
      -
        ins: X. Zeng
        name: Xuan Zeng
        org: UPMC / SystemX
    date: 2018
    series:
      name: IEEE TNSM
      value: vol. PP, no. 99, pp 1-1

  ahlgren2012survey:
    title: A survey of information-centric networking
    author:
      -
        ins: B. Ahlgren, C. Dannewitz, C. Imbrenda, D. Kutscher and B. Ohlman
    date: 2012
    series:
      name: IEEE Communications Magazine
      value: vol. 50, no. 7, pp. 26-36

  carofiglio2016mwldr:
    title: Leveraging ICN In-network Control for Loss Detection and Recovery in Wireless Mobile Networks
    author:
      -
        ins: G. Carofiglio et al.
    date: 2016
    series:
      name: ACM SIGCOMM ICN

  compagno2017secure:
    title: Secure Producer Mobility in Information-Centric Network
    author:
     -
       ins: A. Compagno et al.
    date: 2017
    series:
      name: ACM SIGCOMM ICN

  feng2016mobility:
    title: Mobility support in Named Data Networking
    author:
     -
       ins: B. Feng et al.
    date: 2016
    series:
      name: EURASIP Journal on Wireless Communications and Networking

  NDN-survey:
    title: Secure Producer Mobility in Information-Centric Network
    author:
      -
        ins: L. Zhang et al.
    date: 2016
    series:
      name: "Workshop on Name-Oriented Mobility: Architecture, Algorithms and Applications"

  NLSR:
    title: Named-data link state routing protocol
    author:
      -
        ins: L. Zhang et al.
    date: 2013
    series:
      name: ACM SIGCOMM ICN

  tyson2013survey:
    title: "A Survey of Mobility in Information-centric Networks: Challenges and Research Directions"
    author:
      -
        ins: G. Tyson et al.
    date: 2013
    series:
      name: Communications of the ACM
      value: no. 56, pp. 90-98

  xylomenos2014survey:
    title: A Survey of Information-Centric Networking Research
    author:
      -
        ins: G. Xylomenos et al.
    date: 2014
    series:
      name: IEEE Communications Surveys & Tutorials
      value: vol. 16, no. 2, pp. 1024-1049

  zhang2014kite:
    title: "Kite: a mobility support scheme for NDN"
    author:
      - ins: Zhang, Yu and Zhang, Hongli and Zhang, Lixia
    date: 2014
    series:
      name:  1st ACM Conference on Information-Centric Networking
      value: pp. 179--180



--- abstract
Mobility has become a basic premise of network communications, thereby requiring
a native integration into 5G networks. Despite numerous efforts to propose and
standardize effective mobility-management models for IP, the result is a
complex, poorly flexible set of mechanisms. The natural support for mobility
offered by ICN (Information Centric Networking) makes it a good candidate to
define a radically new solution relieving limitations of the traditional
approaches. If consumer mobility is supported in ICN by design, in virtue of its
connectionless pull-based communication model, producer mobility is still an
open challenge. In this document, we focus on two prominent ICN architectures,
CCN (Content Centric Networking) and NDN (Named Data Networking) and
describe MAP-Me, an anchor-less solution to manage micro-mobility of
content producers via a name-based CCN/NDN data plane, with support for
latency-sensitive applications.

--- middle

# Introduction

With the phenomenal spread of portable user devices, mobility
has become a basic requirement for almost any communication network as well as a
compelling feature to integrate in the next generation networks (5G). The need for a
mobility-management paradigm to apply within IP networks has striven a lot of
efforts in research and standardization bodies (IETF, 3GPP among others), all
resulting in a complex access-dependent set of mechanisms implemented via a
dedicated control infrastructure. The complexity and lack of flexibility of such
approaches (e.g. Mobile IP) calls for a radically new solution dismantling
traditional assumptions like tunneling and anchoring of all mobile
communications into the network core. \changes{This is particularly important with the
increase in rates and mobile nodes (IoT), a vast amount of which never moves.}

The Information Centric Network (ICN) paradigm brings native support for
mobility, security, and storage within the network architecture, hence emerging
as a promising 5G technology candidate. Specifically on mobility management, ICN
has the potential to relieve  limitations of the existing approaches by leveraging
its primary feature, the redefinition of  packet forwarding based on
"names" rather than "network addresses". We believe that removing the dependence
on location identifiers is a first step in the direction of removing the need
for any anchoring of communications into fixed network nodes, which may
considerably simplify and improve mobility management. Within the ICN paradigm,
several architectures have been proposed, as reported in
{{xylomenos2014survey}} and {{ahlgren2012survey}}.

As a direct result of CCN/NDN design principles, consumer mobility
is natively supported}: a change in  physical location for the consumer
does not translate into a change in the data plane like for IP. The
retransmission of requests for data not yet received by the consumers
takes place without involving any signaling to the network. Producer
mobility and realtime group communications present more challenges,
depending on the frequency of movements, latency requirements, and
content lifetime. The topology does not reflect the naming structure,
and we have to preserve key functionalities such as multipath, caching,
etc. In all cases, beyond providing connectivity guarantees, additional
transport-level mechanisms might be required to protect the flow
performance (see {{carofiglio2016mwldr}} for instance).

MAP-Me aims at tackling such problems by exploiting key CCN/NDN characteristics.
Previous attempts have been made in CCN/NDN (and ICN in general)
literature to go beyond the traditional IP approaches, by using the
existing CCN/NDN request/data packet structures to trace producer
movements and to dynamically build a reverse-forwarding path (see
{{NDN-survey}} for a survey). They still rely on a stable home address to
inform about producer movements or on buffering of incoming requests at the
producer's previous point of attachment -- PoA --, which prevents support for
latency-sensitive streaming applications. We focus on this class of applications
(e.g. live streaming or videoconferencing) as they have the most stringent
performance requirements: negligible per-packet loss-rate and delays. In
addition, they typically originate from a single producer and don't allow for
the use of caching.

MAP-Me defines a name-based mechanism operating in the forwarding plane
and completely removing any anchoring, while aiming at latency
minimization. It has the following characteristics:

- MAP-Me addresses micro (e.g. intra Autonomous Systems) producer mobility.
  Addressing macro-mobility is a non-goal of the proposal. We are focusing here
  on complementary mechanisms able to provide a fast and lightweight handover,
  preserving the performance of flows in progress.

- MAP-Me does not rely on global routing updates, which would be too slow and
  too costly, but rather works at a faster timescale propagating forwarding
  updates and leveraging real-time notifications left as breadcrumbs by the
  producer to enable live tracking of its position. For simplicity, we use the
  word 'producer' in place of the more correct expression producer name
  prefixes. The objective being the support of high-speed mobility and
  real-time group applications

- MAP-Me leverages core CCN/NDN features like stateful forwarding, dynamic and
  distributed Interest load balancing to update the forwarding state at routers,
  and relaying former and current producer locations.

- MAP-Me is designed to be access-agnostic, to cope with highly heterogeneous
  wireless access and multi-homed/mobile users.

- Finally, low overhead in terms of signaling, additional state at routers, and
  computational complexity are also targeted in the design to provide a solution
  able to scale to large and dynamic mobile networks.

MAP-Me performance has been thoroughly analyzed and provides guarantees of
correctness, stability and bounded stretch {{MAPME}}.

# State-of-the-art and benefits of anchorless mobility solutions

Many efforts have been made to define mobility-management models for IP
networks in the last two decades, resulting in a variety of complex,
often not implemented, proposals. A good survey of these approaches is
{{RFC6301}}. Likewise, within the ICN family, different
approaches to mobility management have been presented {{tyson2013survey}}.

When facing high-frequency mobility, those so-called Resolution-Based
(RB) approaches present a similar trade-off: for every packet the
consumer has to resolve the producer's location or use stale information
and run the risk to reach an old position, incurring in timeout, or
Nack, etc.

Specifically for the CCN/NDN solutions, several surveys of mobility-management
approaches can be found {{NDN-survey}}, {{feng2016mobility}}. In
{{NDN-survey}} for instance, the authors distinguish three categories of
solutions -- routing, mapping, and tracing-based -- depending on the type of
indirection point (also called Rendez-Vous, RV). We build on such classification
and extend it to distinguish a fifth class of approaches not relying upon the
existence of any anchor point as the RV (Anchor-less approaches):

* ''Routing-based (RT)'' solutions rely on intra-domain routing, and require
  updating all routing in the AS after a mobile's movement. Scalability of these
  solutions is widely recognized as a concern which explains why they are
  usually ruled out, in particular for CCN/NDN where the name space is even
  larger than IP.

* ''Resolution-based (RB)'' solutions rely on dedicated RV nodes (similar to
  DNS) which map content names into routable location identifiers. To maintain
  this mapping updated, the producer signals every movement to the RV node. Once
  the resolution is performed, packets can be correctly routed from the consumer
  along the shortest path, with unitary path stretch (defined as the ratio
  between the realized path length over the shortest path one). Requiring
  explicit resolution, together with a strict separation of names and locators,
  RB solutions involve a scalable CCN/NDN routing infrastructure able to
  leverage forwarding hints; however, scalability is achieved at the cost of a
  large hand-off delay as evaluated e.g. in due to RV update and name
  resolution. To summarize, RB solutions show good scalability properties and
  low stretch in terms of consumer to producer routing path, but result to be
  unsuitable for frequent mobility and for reactive rerouting of
  latency-sensitive traffic, which are key objective of MAP-Me.

* Anchor-based (AB) proposals are inspired by Mobile IP, and maintain a mapping
  at network-layer by using a stable home address advertised by a RV node, or
  anchor. This acts as a relay, forwarding through tunneling both interests to
  the producer, and data packets coming back. Advantages of this approach are
  that the consumer does not need to be aware of producer mobility and that it
  has low signaling overhead because only the anchor has to be updated. It
  however inherits the drawbacks of Mobile IP -- e.g. triangular routing and
  single point of failure -- and others more specific to the CCN/NDN context:
  potential degradation of caching efficiency, bad integrity verification due to
  the renaming of content during movement. It also hinders multipath
  capabilities and limits the robustness to failure and congestion initially
  offered by the architecture. In contrast, MAP-Me maintains names intact and
  avoid single point-of-passage of the traffic.

* Tracing-based (TB) solutions allow the mobile node to create a hop-by-hop
  forwarding reverse path from its RV back to itself by propagating and keeping
  alive traces stored by all involved routers. Forwarding to the new location is
  enabled without tunneling. Like AB though, this approach assumes that the data
  is published under a stable RV prefix.

* Anchor-less (AL) approaches allow the mobile nodes to advertise their mobility
  to the network without requiring any specific node to act as a RV. They are
  less common and introduced in CCN/NDN to enhance the reactivity with respect
  to AB solutions by leveraging {{zhang2014kite}} CCN/NDN name-based routing. The PoA
  starts buffering incoming Interests for the mobile producer until a forwarding
  update is completed and a new route is built to reach the current location of
  the producer. Enhancement of such solutions considers handover prediction.
  Besides the potentially improved delay performance w.r.t. other categories of
  approaches, some drawbacks can be recognized: buffering of Interests may lead
  to timeouts for latency-sensitive applications and handover prediction is hard
  to perform in many cases. In contrast MAP-Me reacts after the handoff, without
  requiring handover prediction, and avoids Interests buffering but introduces
  network notification and discovery mechanism to reduce the handoff latency.

# Design principles

MAP-Me is an anchor-less, name-based, layer-2 agnostic approach operating at
forwarding plane designed according to the following design principles:

- Transparent: MAP-Me does not involve any name nor modifications to
  basic request/reply operations to be compatible with standard CCN/NDN
  design and to avoid issues caused by name modifications like
  triangular routing, caching degradation, or security vulnerabilities.

- Distributed: MAP-Me is designed to be fully distributed, to enhance
  robustness w.r.t. centralized mobility management proposals subject to
  single point-of-passage problem.

- Localised: MAP-Me updates affect the minimum number of routers at the
  edge of the network to restore connectivity. The goal is to realize
  effective traffic off-load close to the end-users.

- Lightweight: MAP-Me mobility updates are issued at prefix granularity,
  rather than content or chunk/packet granularity, to minimize signaling
  overhead and temporary state kept by in-network nodes;

- Reactive: MAP-Me works at forwarding layer to enable updates in FIBs at
  network latency, i.e. round-trip time scale. Specific mechanisms are
  defined, referred to as  network notifications and discovery, to
  maximise reactivity in mobility management in case of real-time producer
  tracking and of latency-sensitive communications;

- Robust to network conditions (e.g. routing failure, wireless or
  congestion losses, and delays), by leveraging hop-by-hop retransmissions
  of mobility updates.

# MAP-Me description

As a data plane protocol, MAP-Me handles producer mobility events by means of
dynamic FIB updates with the objective of minimizing unreachability of the
producer. It relies on the existence of a routing protocol responsible for
creating/updating the FIB of all routers, possibly with multipath routes, and
for managing network failures {{NLSR}}.

MAP-Me is composed of:

- an Update protocol (MAP-ME-IU) (Section {{sec-mapme-iu}}), which is the
  central component of our proposal;

- a Notification/Discovery protocol (Section {{sec-mapme-in}}), to be coupled
  with the Update protocol (the full approach is referred to as MAP-Me) to
  enhance reactivity in mobility management for realtime/latency-sensitive
  application.

# Update protocol {#sec-mapme-iu}

## Rationale

The rationale behind MAP-Me is that the producer announces its movements to the
network by sending a special Interest Packet, named Interest Update (IU) to
"itself" after it reattaches to the network. Such a message looks like a regular
Interest packet named with the prefix advertised by the producer. As such, it is
forwarded according to the information stored in the FIBs of traversed routers
towards previous locations of the producer known by router FIBs. A special flag
carried in the header of the IU enables all routers on the path to identify the
Interest as a mobility update and to process it accordingly to update their FIBs
(a detailed description of the IU processing is provided in Sec. {{sec-algos}}.

The key aspect of the proposal is that it removes the need for a stable home
address (present in Tracing-Based approaches for instance) by directly
leveraging name-based forwarding state created by CCN/NDN routing protocols or
left by previous mobility updates. FIB updates are triggered by the reception of
mobility updates in a fully distributed way and allow a modification on-the-fly
to point to the latest known location of the producer.

## Update propagation

MAP-ME-IU aims at quickly restoring global reachability of mobile prefixes with
low signaling overhead, while introducing a bounded maximum path stretch (i.e.
ratio between the selected and the shortest path in terms of hops).

Let us illustrate its behavior through the example in Figure
{{fig-proposal-iu}}, where a single producer serving prefix /p moves from
position P0 to P1 and so on. Figure {{fig-proposal-iu}} (a) shows the tree
formed by the forwarding paths to the name prefix /p where IU initiated by the
producer propagates.

~~~~~~~~~~

                         +---+                        +---+
                         | 0 | P0                      | 0 | P0
                         +---+                         +---+
                          ^ ^                           ^ ^
                         /   \                         /   \
                     +---+    +---+                +---+    +---+
                     | 0 |    | 0 |                | 0 |    | 0 |
                     +---+    +---+                +---+    +---+
                      ^ ^                           ^ ^
                     /   \                         /   \
                 +---+    +---+                +---+    +---+
                 | 0 |    | 0 |                | 0 |    | 0 |
                 +---+    +---+             A  +---+    +---+
                  ^ ^                  IU1 /    ^ ^
                 /   \                    /    /   \
             +---+    +---+          .... .+---+.   +---+
             | 0 |    | 0 |         .   P1 | 1 | .  | 0 |
             +---+    +---+        .       +---+ .  +---+
              ^ ^                  .        ^ ^    .
             /   \                 .       /   \     .
         +---+    +---+            .   +---+    +---+  .
         | 0 |    | 0 |            .   | 0 |    | 0 |  .
         +---+    +---+            .   +---+    +---+ .
                                    ..................

                  (a)                       (b)

                                                .................
                         +---+               ...       +---+     ..
                         | 0 | P0           .          | 1 | P0    .
                         +---+             .        A  +---+       .
                          ^ ^             .  IU(1) /    / ^        .
                         /   \            .       /    V   \       .
                     +---+    +---+      .         +---+    +---+  .
                     | 0 |    | 0 |     .          | 1 |    | 0 |  .
                     +---+    +---+    .        A  +---+    +---+  .
                      ^ ^              . IU(1) /    / ^           .
                     /   \            .       /    V   \         .
         ........+---+.   +---+      .         +---+    +---+   .
        .        | 1 | .  | 0 |     .          | 1 |    | 0 |   .
       .  FIB    +---+ .  +---+    .        A  +---+    +---+   .
      . updated   / ^   .          . IU(1) /    / ^            .
      .          V   \   ....      .      /    V   \          .
      .      +---+    +---+  .     .       +---+    +---+   .
      .  P1  | 1 |    | 0 |  .     .    P1 | 1 |    | 0 |   .
      .      +---+    +---+  .     .       +---+    +---+   .
      .       ^ ^            .     .        ^ ^            .
      .      /   \          .       .      /   \         .
      .  +---+    +---+    .        .  +---+    +---+  .
      .  | 0 |    | 0 |   .         .  | 0 |    | 0 |  .
      .  +---+    +---+  .          .  +---+    +---+  .
       ..................            ..................

                  (c)                       (d)

                         +---+
                         | 1 | P1
                         +---+
                        ^  A  ^
                      /    |    \
                 +---+   +---+   +---+
                 | 0 |   | 0 |   | 1 |
                 +---+   +---+   +---+
                                  ^ ^
                                 /   \
                             +---+    +---+
                             | 0 |    | 1 |
                             +---+    +---+
                                       ^ ^
                                      /   \
                                  +---+    +---+
                              P0  | 1 |    | 0 |
                                  +---+    +---+
                                   ^
                                  /
                              +---+
                              | 0 |
                              +---+

                           (e)
~~~~~~~~~~
{: #fig-proposal-iu title="IU propagation example"}

Network FIBs are assumed to be populated with routes toward P0 by a name-based
routing protocol. After the relocation of the producer from P0 to P1, once the
layer-2 attachment is completed, the producer issues an IU carrying the prefix
/p and this is forwarded by the network toward P0 (in general, toward one of its
previous locations according to the FIB state of the traversed routers).

Figure {{fig-proposal-iu}} (b) shows the propagation of the IU. As
the IU progresses, FIBs at intermediate hops are updated with the ingress face
of the IU (Figure {{fig-proposal-iu}} (c) and (d)). IU propagation
stops when the IU reaches P0 and there is no next hop to forward it. The result
is that the original tree rooted in P0 becomes re-rooted in P1 (Figure
{{fig-proposal-iu}} (e)). Looking at the different connected regions
(represented with dotted lines), we see that IU propagation and consequent FIB
updates have the effect of extending the newly connected subtree (represented as
a red cloud): at every step, an additional router and its predecessors are
included in the connected subtree. The properties of the update propagation
process in terms of bounded length and stretch are studied in {{MAPME}}.

# Concurrent updates

Frequent mobility of the producer may lead to the propagation of concurrent
updates. To prevent inconsistencies in FIB updates, MAP-Me-IU maintains a
sequence number at the producer end that increases at each handover and
identifies every IU packet. Network routers also keep track of such sequence
number in FIB to verify IU freshness. Without detailing the specific operations
in MAP-Me to guarantee update consistency (whose description is provided in
Section {{sec-algos}}, we can say that modification of FIB entries
is only triggered when the received IU carries a higher sequence number than the
one locally stored, while the reception of a less recent update determines a
propagation of a more recent update through the not-yet-updated path.

An example of reconciliation of concurrent updates is illustrated in Figure
{{fig-proposal-iu-c}} (f), when the producer has moved successively
to P1 and then to P2 before the first update is completed.

~~~~~~~~~~
                         +---+                         +---+
                         | 0 | P0                      | 2 | P0
                         +---+                      A  +---+
                          ^ ^                IU(2) /    / ^
                         /   \                    /    V   \
                     +---+    +---+                +---+    +---+
                     | 0 |    | 0 |           <!>  | 2 |    | 0 |
                     +---+ A  +---+             A  +---+ A  +---+
                      ^ ^   \            IU(1) /    ^ \   \
                     /   \   \  IU(2)         /    /   V   \ IU(2)
                 +---+    +---+                +---+    +---+
                 | 0 |    | 2 | P2             | 1 |    | 2 |
              A  +---+    +---+             A  +---+    +---+
       IU(1) /    ^ ^                  IU1 /    / ^
            /    /   \                    /    V   \
             +---+    +---+                +---+    +---+
          P1 | 1 |    | 0 |             P1 | 1 |    | 0 |
             +---+    +---+                +---+    +---+
              ^ ^                           ^ ^
             /   \                         /   \
         +---+    +---+                +---+    +---+
         | 0 |    | 0 |                | 0 |    | 0 |
         +---+    +---+                +---+    +---+

                  (f)                       (g)

                         +---+                      +---+
                         | 2 | P0                   | 2 | P2
                         +---+                      +---+
                          / ^                         ^
                         V   \                        |
                     +---+    +---+                 +---+
                 <!> | 2 |    | 2 |                 | 2 |
          IU(2)  /   +---+    +---+                 +---+
                /     ^ \                           ^   ^
               V     /   V                         /     \
                 +---+    +---+                +---+     +---+
                 | 2 |    | 2 | P2         P0  | 2 |     | 2 |
       IU(2)  /  +---+    +---+                +---+     +---+
             /    / ^                           ^         ^ ^
            V    V   \                         /    P1   /   \
             +---+    +---+                 +---+    +---+   +---+
          P1 | 1 |    | 0 |                 | 0 |    | 2 |   | 0 |
             +---+    +---+                 +---+    +---+   +---+
              ^ ^                                     ^ ^
             /   \                                   /   \
         +---+    +---+                          +---+    +---+
         | 0 |    | 0 |                          | 0 |    | 0 |
         +---+    +---+                          +---+    +---+

                  (h)                                 (i)
~~~~~~~~~~
{: #fig-proposal-iu-c}

Both updates propagate concurrently until the update with sequence number 1
(IU(1)) crosses a router that has been updated with fresher information -- that
has received IU with higher sequence number (IU(2)) as in Figure
{{fig-proposal-iu-c}} (g). In this case, the router stops the propagation
of IU(1) and sends back along its path a new IU with an updated sequence number
({{fig-proposal-iu-c}} (h)). The update proceeds until ultimately the
whole network has converged towards P2 ({{fig-proposal-iu-c}} (i)).

MAP-Me-IU  protocol reacts at a faster timescale than routing -- allowing more
frequent and numerous mobility events -- and over a localized portion of the
network edge between current and previous producer locations. We thus expect
MAP-Me-IU respectively to minimize disconnectivity time and to reduce the link
load, which are the main factors affecting user flow performance, as show in
{{MAPME}} evaluations.

# MAP-Me Notification/Discovery protocol {#sec-mapme-in}

IU propagation in the data plane accelerates forwarding state re-convergence
w.r.t. global routing (GR) or resolution-based (RB) approaches operating at
control plane, and w.r.t. anchor-based (AB) approaches requiring traffic
tunneling through the anchor. Still, network latency makes IU completion not
instantaneous and before an update completes, it may happen that a portion of
the traffic is forwarded to the previous PoA and dropped because of the absence
of a valid output face leading to the producer.

Previous work in the Anchor-Less category has suggested the buffering of
Interests at previous producer location to prevent such losses by increasing
network reactivity. However, such a solution is not suitable for applications
with stringent latency requirements (e.g. real-time) and may be incompatible
with IU completion times. Moreover, the negative effects on latency performance
might be further exacerbated by IU losses and consequent retransmissions in case
of wireless medium. To alleviate such issues, we introduce two separate
enhancements to MAP-Me-IU protocol, namely (i) an "Interest Notification"
mechanism for frequent, yet lightweight, signaling of producer movements to the
network and (ii) a scoped "Producer Discovery" mechanism for consumer requests
to proactively search for the producer's recently visited locations.

## Interest Notification

An Interest Notification (IN) is a breadcrumb left by producers at every
encountered PoA. It looks like a normal Interest packet carrying a special
identification flag and a sequence number, like IUs.  Both IU and IN share the
same sequence number (producers indistinctly increase it for every sent message)
and follow the same FIB lookup and update processes. However, unlike IU
packets, the trace left by INs at the first hop router does not propagate
further. It is rather used by the discovery process to route consumer requests
to the producer even before an update process is completed.

It is worth observing that updates and notifications serve the same purpose of
informing the network of a producer movement. \changes{The IU process restores
connectivity and as such has higher latency/signaling cost than the IN process,
due to message propagation. The IN process provides information to track
producer movements before update completion when coupled with a scoped
discovery. The combination of both IU and IN allows to control the trade-off
between protocol reactivity and stability of forwarding re-convergence.

## Discovery

The extension of MAP-Me with notifications relies on a local discovery phase:
when a consumer Interest reaches a PoA with no valid output face in the
corresponding entry, the Interest is tagged with a "discovery" flag and labeled
with the latest sequence number stored in FIB (to avoid loops). From that point
on, it is broadcasted with hop limit equal to one to all neighbors and discarded
unless it finds the breadcrumbs left by the producer to track him
(notifications). The notifications can either allow to forward consumer
Interests directly to the producer or give rise to a repeated broadcast in case
of no valid output face. The latter is the case of a breadcrumb left by the
producer with no associated forwarding information because the producer has
already left that PoA as well.  A detailed description of the process is
reported in Sec. {{sec-algos}}.

The notification/discovery mechanism proves important to preserve the
performance of flows in progress, especially when latency-sensitive.

## Full approach

The full approach is a combined update and notification/discovery approach
consisting of sending a IN immediately after an attachment and a IU at most
every Tu seconds, referred to as MAP-Me, to reduce signaling overhead especially
in case of high mobility. The update-only proposal, denoted as MAP-Me-IU, is
equally interesting on its own and might be a fit depending on the use case.

# Implementation

In this section we describe the changes to a regular CCN/NDN architecture
required to implement MAP-ME and detail the above-described algorithms. This
requires to specify a special Interest message, additional temporary information
associated to the FIB entry and additional operations to update such entry.

## MAP-Me messages

Two new optional fields are introduced in a CCN/NDN Interest header:

- a special "Interest Type" (T) to specify four types of messages: Interest
  Updates (IU), Interest Notifications (IN), as well as their associated
  acknowledgment (Ack) messages (IU_Ack and IN_Ack). Those flags are recognized
  by the forwarding pipeline to trigger special treatment;

- a "sequence number" to handle concurrent updates and prevent forwarding loops
    during signaling, and to control discovery interests' propagation;

## MAP-Me additional Network Information

FIB entries are enriched with a sequence number, initialized to 0, say, by
routing protocol and updated by MAP-Me upon reception of IU/IN messages. The
Data about not-yet-acknowledged messages are temporarily stored in what we
denote as "Temporary FIB buffer", or "TFIB", to ensure reliability of the
process, and removed upon reception of the corresponding acknowledgement.

As sketched in Figure {{fig-tfib}}, each TFIB entry is composed of an
associative array (F -> T) mapping a face F on which IU has been sent with the
associated retransmission timer T (possibly Null). Note that the update
mechanism is a constant delay operation at each router and is performed at line
rate.

~~~~~~~~~~
         IU (IN) input face(s)            IU (IN) output face

   +-----------+-------------------+.......+.......................+
   |  /prefix  |  { next hop(s) }  |  seq  |  { face : rx_timer }  |
   +-----------+-------------------+.......+.......................+
    \_____________ _______________/ \______________ ______________/
                  V                                V
             original FIB                     TFIB section
~~~~~~~~~~
{: #fig-tfib title="MAP-ME FIB/TFIB description"}

# Algorithm description {#sec-algos}

## IU/IN transmission at producer

MAP-Me operations are triggered by producer mobility/handover events. At the
producer end, a mobility event is followed by a layer-2 attachment and, at
network layer, a change in the FIB. More precisely, a new face is created and
activated upon attachment to a new PoA. This signal triggers the increase of
MAP-Me sequence number and the transmission of an IU or IN for every served
prefix carrying the updated sequence number.

To ensure reliable delivery of IUs, a timer is setup in the temporary section of
the FIB entry (TFIB). If an acknowledgement of the IU/IN reception is not
received within t seconds since the packet transmission, IU is retransmitted.

We define the

```
SendReliably(F, type, E)
```

function fpr sending Special Interests of a given type on faces F based
on FIB entry E. It schedules their retransmission through a timer T
stored in TFIB:

```
E.TFIB = E.TFIB U (F -> T)
```

and removed on Ack.

## IU/IN transmission at network routers

At the reception of IU/IN packets, each router performs a name-based Longest
Prefix Match lookup in FIB to compare sequence number from IU/IN and from FIB}.
According to that comparison:

- if the IU/IN packet carries a higher sequence number, the existing
  next hops associated to the lower sequence number in FIB are used to
  forward further the IU (INs are not propagated) and temporarily copied
  into TFIB to avoid loss of such information before completion of the
  IU/IN acknowledgement process (in case of IN, such entries in TFIB are
  set with a $\bot$ timer to maintain a trace of the producer recent
  attachment). Also, the originating face of the IU/IN is added to FIB
  to route consumer requests to the latest known location of the
  producer.

- If the IU/IN packet carries the same sequence number as in the FIB,
  the originating face of the IU/IN is added to the existing ones in FIB
  without additional packet processing or propagation. This may occur in
  presence of multiple forwarding paths.

- If the IU/IN packet carries a lower sequence number than the one in
  the FIB, FIB entry is not updated as it already stores 'fresher
  information'.  To advertise the latest update through the path
  followed by the IU/IN packet, this one is re-sent through the
  originating face after having updated its sequence number with the
  value stored in FIB.

## Hop-by-hop IU/IN acknowledgement

The operations in the forwarding pipeline for IU/IN processing are reported in
Alg. {{alg-update}}.

~~~~~~~~~~
   | Algorithm 1 : ForwardSpecialInterest(SpecialInterest SI, Ingress face F)
   |
   |  CheckValidity()
   |  // Retrieve the FIB entry associated to the prefix
   |  e, T <- FIB.LongestPrefixMatch(SI.name)
   |  if SI.seq >= e.seq then
   |  .   // Acknowledge reception
   |  .   s <- e.seq
   |  .   e.seq <- SI.seq
   |  .   SendReliably(F, SI.type + Ack, e)
   |  .   //Process special interest
   |  .   if F in e.TFIB then
   |  .   .   // Remove outdated TFIB entry (eventually cancelling timer)
   |  .   .   e.TFIB = e.TFIB \ F
   |  .   if SI.seq > s then
   |  .   .   if SI.type == IU then
   |  .   .   .   // Forward the IU following the FIB entry
   |  .   .   .   SendReliably(e.NextHops, SI.type, e
   |  .   .   else
   |  .   .   .   // Create breadcrumb and preserve forwarding structure
   |  .   .   .   e.TFIB = e.TFIB U {( f -> NULL) : for all f in e.NextHops}
   |  .   .   e.NextHops = {}
   |  .   e.NextHops = e.NextHops U { F }
   |  else
   |  .   // Send updated IU backwards
   |  .   SI.seq = e.seq
   |  .   SendReliably(F, SI.type, e)
~~~~~~~~~~
{: #alg-update}

## Face removal at producer/network nodes

Upon producer departures from a PoA, the corresponding face is destroyed. If
this leads to the removal of the last next hop, then faces in TFIB with Null
timer (entries generated by notifications) are restored in FIB to preserve the
original forwarding tree and thus global connectivity.

## Consumer request forwarding in case of producer discovery

The forwarding of regular Interests is mostly unaffected in MAP-Me, except in
the case of discovery Interests that we detail in Alg. {{alg-forward}}.
The function SendToNeighbors(I) is responsible for broadcasting the Interest I
to all neighboring PoAs.

~~~~~~~~~~
   | Algorithm 2:  InterestForward(Interest I, Origin face F)
   |
   |  // Regular PIT and CS lookup
   |  e <- FIB.LongestPrefixMatch(I.name)
   |  if e = 0 then
   |  .   return
   |  if I.seq = 0 then
   |  .   // Regular interest
   |  .   if hasValidFace(e.NextHops) or DiscoveryDisabled then
   |  .   .   ForwardingStrategy.process(I, e)
   |  .   else
   |  .   .   // Enter discovery mode
   |  .   .   I.seq <- e.seq
   |  .   .   SendToNeighbors(I)
   |  else
   |  .   // Discovery interest: forward if producer is connnected
   |  .   if hasProducerFace(e.NextHops) then
   |  .   .   ForwardingStrategy.process(I, e)
   |  .   // Otherwise iterate iif higher seq and breadcrumb
   |  .   else if e.seq >= I.seq and EXISTS f | (f -> NULL) in e.TFIB then
   |  .   .   I.seq <- e.seq
   |  .   .   SendToNeighbors(I)
~~~~~~~~~~
{: #alg-forward}

When an Interest arrives to a PoA which has no valid next hop for it (because
the producer left and the face got destroyed), it enters a discovery phase where
the Interest is flagged as a Discovery Interest and with the local sequence
number, then broadcasted to neighboring PoAs.

Upon reception of a Discovery Interest, the PoA forwards it direcly to the
producer if still attached, otherwise it repeats the one-hop brodcast discovery
to neighboring PoAs if it stores a recent notification of the producer presence,
i.e. an entry in TFIB having higher sequence number than the one in the
Discovery Interest. Otherwise, the Discovery Interest is discarded.

It is worth observing that the discovery process is initiated only in the case
of no valid next hop, and not every time a notification is found in a router.
This is important to guarantee that the notification/discovery process does not
affect IU propagation and completion.

# Security considerations

All mobility management protocols share the same critical need for securing
their control messages which have a direct impact on the forwarding of users'
traffic. {{compagno2017secure}} reviews standard approaches from the literature
before developing a fast, lightweight and distributed approach based on hash
chaining that can be applied to MAP-Me and fits its design principles.

# Acknowledgements

# Contributors

- Giulio Grassi (UPMC/UCLA)
- Giovanni Pau (UPMC/UCLA)
- Xuan Zeng (UPMC/SystemX)

# IANA Considerations {#iana}

This memo includes no request to IANA.

