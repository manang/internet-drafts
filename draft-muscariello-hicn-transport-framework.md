---
title: Transport Framework for Hybrid Information-Centric Networking Architecture
abbrev: hICN-transport
docname: draft-muscariello-hicn-transport-latest
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
    org: Cisco Systems Inc.
    email: lumuscar@cisco.com
-
    ins: G. Carofiglio
    name: Giovanna Carofiglio
    org: Cisco Systems Inc.
    email: gcarofig@cisco.com

normative: 
    RFC0793:
    RFC1081:
    RFC1624:
    RFC2119:
    RFC3031:
    RFC3587:
    RFC3550:
    RFC4291:
    RFC4302:
    RFC6830:
    RFC8200:
informative:
    I-D.irtf-icnrg-ccnxsemantics:
    I-D.irtf-icnrg-ccnxmessages:
    I-D.irtf-icnrg-mapme:
    I-D.irtf-icnrg-terminology:
    I-D.muscariello-intarea-hicn:
    CCN: DOI.10.1145/1658939.1658941
    NDN: DOI.10.1145/2656877.2656887
    MAN: DOI.10.1109/INFCOMW.2012.6193505
    WLD: DOI.10.1145/2984356.2984361
    MIR: DOI.10.1109/TMC.2017.2734658
    RAQ: DOI.10.1109/ICNP.2013.6733576

--- abstract

Information-Centric Networking (ICN) architectures introduce a location-independent communication model based on request/reply exchanges between a client and one or more a-priori unknown sources (ref to RFCs). Request packets carry a data indentifier which is not bound to any specific host location, hence is not affected by network mobility. They are routed in a hop-by-hop fashion over one or multiple paths until they are matched by requested data packets. Data packets follow the reverse path of requests to be conveyed to one or more requesting clients. 

Mobility, Multi-path, Multi-source and Many-to-many communication (M4) characterize ICN transport making it a promising candidate for a various number of applications (e.g. live-streaming, IoT, mobile edge computing, realtime multimedia communications, etc.)
The requirements of native M4 transport are not met by traditional host-to-host transport protocols which assume the knowledge of both client and server end-points before packet transmission and do not leverage dynamic forwarding/caching/control decisions taken by in-path nodes.

There have been various attempts in the literature to either adapt existing transport protocols to ICN or to define novel approaches, in all cases targeting a specific application (refs to papers)
A few attemtps have been made at addressing the novel challenges brought by ICN in terms of flow definition, path labelling, etc., but to the best of our knowledge no unified ICN transport framework has been proposed so far.
Without the ambition to design a specific ICN transport protocol, this document discusses a general M4 transport framework for Hybrid ICN, an IP-integrated open-source ICN architecture (ref to FD.io hICN) and  illustrates its applicability to a few relevant use cases.

--- middle

# Introduction


## Problem and Scope

Information-Centric Networking (ICN) identifies a networking paradigm  centering 
communication around named data, in contrast to host location. ICN offers a simplified 
and more efficient user-to-content communication that resolves the mismatch 
between content-centric usage and underlying host-to-host foundations of the Internet.

Despite important architectural differences in the proposed designs (REFS), a shared
idea characterizes ICN, that of abstracting data delivery from underlying point
to point connectivity by means of an information-aware network layer operating
directly on secured named data regardless of their location. 
It results a native support for mobility, storage and security as network features are integrated
by design, rather than as an afterthought.





## Related work
Rel work : a summary

## Reference ICN architecture: hICN

We focus on hICN (refs) and on the open-sourced implementation in FD.io hICN project (ref)

Two sentences of recap

## Terminology

## Notational Conventions

The words "MUST", "MUST NOT", "SHOULD", and "MAY" are used in this document.
It's not shouting; when these words are capitalized, they have a special
meaning as defined in {{RFC2119}}.


# Transport framework architecture

## Application APIs
Application requirements to tailor underlying transport
## Consumer and Producer Sockets
Packet semantics
## Flow identification
...
## Rate and congestion control loop
...
## Loss detection and control 
...

# Coexistance with non-ICN transport

# Examples of application
## Live-streaming video distribution
## Realtime Communications

# Security considerations
## Consumer
## Producer





