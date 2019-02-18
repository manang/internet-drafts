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

ICN/hICN architectures introduce a location-independent communication model based on request/reply exchanges between a client and one or more a-priori unknown sources (ref to RFCs). Requests carry a data indentifier which is not bound to any specific
host location, hence unaffected by network mobility. They are dynamically routed in a hop-by-hop fashion over one or multiple paths until they are matched by the requested data. Data packets follow the reverse path of requests to be conveyed to one or more requesting clients. 

Mobility, Multi-path, Multi-source and Many-to-many communication characterize ICN/hICN transport making it a promising candidate for a various number of applications (e.g. live-streaming, IoT, mobile edge computing, realtime multimedia communications, etc.)
Clearly, the traditional host-to-host transport protocols is not directly applicable as it requires the knowledge of both client and server end-points and does not involve dynamic forwarding / caching / control decisions taken by in-path nodes.

Many attempts to define ICN transport protocols for targeted applications (refs to papers)
A few attemtps to redefine flow in ICN context, but no unified ICN transport framework so far.
Without the ambition to design a specific ICN transport protocol, we specify in this document a M4 transport framework suitable for ICN/hICN architectures and illustrate its applicability over relevant use cases.

--- middle

# Introduction

## Problem and Scope
ICN communication model: a summary





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





