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


Receiver-Driven Connectionless Transport – In contrast
with the current sender-based TCP/IP model, ICN transport
is receiver-controlled, does not require connection instantiation
and accommodates retrieval from possibly multiple
dynamically discovered sources. It builds upon the flow
balance principle guaranteeing corresponding request-data
flows on a hop-by-hop basis [9]. A large body of work has
looked into ICN transport (surveyed in [78]), not only to
propose rate and congestion control mechanisms [81, 100] –
especially in the multipath case [30] – but also to highlight
the interaction with in-network caching [29], the coupling
with request routing [30, 50], and the new opportunities
provided by in-network hop-by-hop rate/loss/congestion
control [32, 93] for a more reactive low latency response of
involved network nodes.


- Rephrase around M4 transport capabilities




## Related work
Rel work : a summary
There have been various attempts in the literature to either adapt existing transport protocols to ICN or to define novel approaches, in all cases targeting a specific application (refs to papers)


Rel work on ICN transport protocols

....

A few attemtps have been made at addressing the novel challenges brought by ICN in terms of flow definition, path labelling, etc., but to the best of our knowledge no unified ICN transport framework has been proposed so far.

Rel work on work redifining general transport aspects of ICN

...

## Reference ICN architecture: hICN

Focus here is on defining a transport frameworkf for Hybrid ICN (REF to FD.io hICN)

We focus on hICN (refs) and on the open-sourced implementation in FD.io hICN project (ref)

Hybrid ICN is an architecture that brings ICN into IPv6 as described in [1]. By doing that, hicn allows to generalize IPv6 networking by using location-independent name-based networking. This is made either at the network layer and at the transport layer by also providing name-based sockets to applications.

hicn allows to reuse existing IPv6 protocol and architectures, to extend them and deploy hybrid solutions based on the use case and application needs. Moreover, hicn can exploit hardware and software implementations heavily based on IP, making much simpler insertion of the technology in current networks, current applications and design future applications reusing what the industry already provides in terms of on the shelf components. 


## Terminology

## Notational Conventions

The words "MUST", "MUST NOT", "SHOULD", and "MAY" are used in this document.
It's not shouting; when these words are capitalized, they have a special
meaning as defined in {{RFC2119}}.


# Transport framework architecture

M4 Transport framework provides segmentation and reassembly services to
the applications, and sits on top of hICN network layer which provides a 
request/reply service to the transport layer itself. End-points in TCP/IP can be 
described as acting as transmitters or receivers, or both at the same time, 
whereas hICN transport is oriented to the consumer/producer paradigm where a 
transmission is always triggered by a matching request. Moreover, a TCP connection 
is a bidirectional communication channel, while in hICN there are always two 
unidirectional sockets: the consumer and the producer. Transport protocols in 
hICN can be stream or datagram oriented, depending on the application (as TCP or UDP).
Currently, hICN prototype implements (i) the stream oriented ICN multi-path 
transport protocol with support for loss recovery, flow and congestion control 
introduced  in \cite{carofiglio2016optimal} and (ii) the in-network loss control 
mechanisms in \cite{Papalini-WLDR}. We plan to enrich in the future the set of 
supported transport protocols. As previously mentioned, a transport manifest is used 
by the producer to distribute meta-data useful for consumer to retrieve the actual 
data. It includes low level segmentation information (name suffixes) as well as 
integrity information, and it is signed with the producer's private key~\cite{Manifest}. 
Such approach is adopted as the default \hicn{} approach instead of per packet 
signature computation. The manifest also carries the expiration time of the associated data
which is an absolute quantity and requires reasonable synchronization to UTC.
In case the manifest is not used by the producer, the expiration time is carried
by the TCP timestamp option.


\subsection{Socket API}\label{subsec:socket-api}
We use substantially unaltered the INET6 socket API for \hicn{} with the twofold 
advantage to provide a known API for developers of new applications and simple 
integration of existing ones.
The system calls of \hicn{} socket API are based on the socket interface extensions 
for IPv6 \cite{rfc3493}. Both consumer and producer sockets bind to a socket address 
(\sa{}), which is initialized by specifying address family 
\af  and name prefix. The range of addresses stated by the name prefix 
tells the namespace in which a producer socket is allowed to publish data and a 
consumer socket is allowed to request data.

The \texttt{bind()} system call takes care of setting up a local face to the \hicn{} 
forwarder, which in the case of the producer also sets a FIB entry 
\texttt{(name\_prefix, socket\_id)}. The \texttt{recvmsg()} and the \texttt{recvfrom()} 
system calls are used by a consumer for retrieving data, while \texttt{sendmsg()} and 
\texttt{sendto()} are used by a producer for publishing a content and making it available 
for the consumers. 
After data is published under a given name,  subsequent requests for it can be 
satisfied by the producer socket without passing them to the application.

The consumer socket can use the \texttt{sendmsg()} for sending a single Interest 
with payload eventually triggering a \texttt{recvmsg()} on the remote producer 
to pass the payload to the application, e.g. to send and HTTP request in half RTT.
In general, push semantics are realized using reverse pull techniques, which require all end-points
to have a consumer and a producer socket open and bound to a valid name prefix.
An end-point can push data to one or several remote end-points by (i) calling a 
\texttt{sendto()} from the producer socket with actual data; (ii) passing the resulting 
transport manifest to a valid open consumer socket; (iii) calling a \texttt{sendmsg()} 
from the consumer to trigger an Interest packet carrying the data manifest as payload.
The remote producer (one or many) after reception of Interest can pass the manifest to
the local consumer socket to trigger the reverse pull procedure.
This technique can be used to send large HTTP requests to one or multiple servers, 
and has the advantage to enable multicast upload from one client to many servers.


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

We also envision for future work, the design of a secure transport layer that 
provides confidentiality to the communication by extending the widely diffused 
point-to-point approach (e.g., as in TLS and DTLS), by means of secure group 
communications~\cite{baugher2005multicast,perrig1999efficient,rafaeli2003survey,sherman2003key} 
to cope and exploit \hicn{} in-network caching and multicasting.

## Consumer
## Producer





