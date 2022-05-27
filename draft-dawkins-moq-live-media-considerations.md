---
title: "Media Over QUIC: Live Media Considerations"
abbrev: "MOQ: Live Media Considerations"
category: info

docname: draft-dawkins-moq-live-media-considerations-latest
submissiontype: IETF  # also: "independent", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Applications and Real-Time"
workgroup: "Media Over QUIC"
keyword:
 - Media

venue:
  group: "Media Over QUIC"
  type: "Working Group"
  mail: "moq@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/moq/"
  github: "SpencerDawkins/moq-live-media-considerations"
  latest: "https://SpencerDawkins.github.io/moq-live-media-considerations/draft-dawkins-moq-live-media-considerations.html"

author:
  -
    ins: S. Dawkins
    name: Spencer Dawkins
    org: Tencent America LLC
    country: United States of America
    email: spencerdawkins.ietf@gmail.com

informative:
  RFC3550:
  RFC4566:
  RFC6363:
  RFC6716:
  RFC7230:
  RFC7540:
  RFC7826:
  RFC8216:
  RFC8298:
  RFC8312:
  RFC8836:
  RFC9000:
  RFC9002:
  RFC8698:

  I-D.draft-cardwell-iccrg-bbr-congestion-control:
  I-D.draft-engelbart-rtp-over-quic:
  I-D.draft-hurst-quic-rtp-tunnelling:
  I-D.draft-kpugin-rush:
  I-D.draft-lcurley-warp:
  I-D.draft-rtpfolks-quic-rtp-over-quic:
  I-D.draft-sharabayko-srt:
  I-D.draft-sharabayko-srt-over-quic:
  I-D.draft-jennings-moq-quicr-arch:
  I-D.draft-jennings-moq-quicr-proto:

  I-D.draft-ietf-mops-streaming-opcons:
  I-D.draft-ietf-quic-datagram:
  I-D.draft-ietf-quic-http:
  I-D.draft-ietf-quic-multipath:
  I-D.draft-ietf-webtrans-overview:

  AVTCORE-2022-02:
    target: https://datatracker.ietf.org/meeting/interim-2022-avtcore-01/session/avtcore
    title: "AVTCORE 2022-02 interim meeting materials"
    date: February 2022
  MOQ-ml:
    target: https://www.ietf.org/mailman/listinfo/moq
    title: "Moq -- Media over QUIC"
  DASH:
    target: https://www.iso.org/standard/79329.html
    title: "ISO/IEC 23009-1:2019: Dynamic adaptive streaming over HTTP (DASH) -- Part 1: Media presentation description and segment formats (2nd edition)"
  ISOBMFF:
    target: https://www.iso.org/standard/83102.html
    title: "ISO/IEC 14496-12:2022 Information technology — Coding of audio-visual objects — Part 12: ISO base media file format"
    date:  January 2022
  WebRTC:
    target: https://www.w3.org/groups/wg/webrtc
    title: "Web Real-Time Communications Working Group"
  QUIC-goals:
    target: https://datatracker.ietf.org/doc/charter-ietf-quic/01/
    title: "Initial Charter for QUIC Working Group"
    date: October 4, 2016


--- abstract

This document describes use cases that have been discussed in the IETF community under the banner of "Media Over QUIC", provides analysis about those use cases, recommends a subset of use cases that cover live media ingest, syndication, and streaming for further exploration, and describes considerations that should guide the design of protocols to satisfy these use cases.


--- middle

# Introduction {#intro}

This document describes use cases that have been discussed in the IETF community under the banner of "Media Over QUIC", provides analysis about those use cases, recommends a subset of use cases that cover live media ingest, syndication, and streaming for further exploration, and describes considerations that should guide the design of protocols to satisfy these use cases.


# Conventions and Definitions

{::boilerplate bcp14-tagged}



## For The Impatient Reader

- Our proposal is to focus on live media use cases, as described in "propscope", rather than on interactive media use cases or on-demand use cases.
- The reasoning behind this proposal can be found in "analy-interact".
- The considerations for protocol work to satisfy the proposed use cases can be found in {{considerations}}.

Most of the rest of this document provides background for these sections.



# Terminology {#term}

# Considerations for Protocol Work {#considerations}

 This sction is intended to "up-level" the conversation beyond specific protocols, so that we can more likely agree on what is important for protocol design work.

Please note that the considerations in this section are focused especially on the use cases described in PREVIOUS DRAFT, although other use cases are mentioned for comparison and contrast.

## Here Be Dragons

The discussion in {{considerations}} is less mature than in most other sections of this document. The good news is that this section is fertile ground for people who would like to contribute to future revisions of this document. Comments are even more welcome for this section than for the rest of the document, for which they are welcome. The authors suggest that high-level comments are most appropriate at this time.

## Codec Agility

When initiating a media session, both the sender and receiver will need to agree on the codecs, bitrates, resolution, and other media details based on
capabilities and preferences. This agreement needs to take place before commencing
media transmission, but might also take place during media transmission, perhaps as a result of changes to device output or network
conditions (such as reduction in available network bandwidth).

It may be prefered to use existing ecosystem for such purposes, e.g. SDP {{RFC4566}}.

## Support an Appropriate Range of Latencies

Support for a nominal latency appropriate for the use cases that are in scope should be
achievable, with consideration for the minimum buffer that a receiver playing content may
need to handle congestion, packet loss, and other degradation in network
quality.

## Migration of Sessions

Handling of migration of a session between hosts, either of sender or receiver
should be supported. This may either happen because the sender is undergoing
maintenence or a rebalancing of resource, because the either is experiencing a
change in network connectivity (such as a device moving from WiFi to cellular
connectivity) or other reasons.

This may depend on QUIC capabilities such as {{I-D.draft-ietf-quic-multipath}}
but support for full QUIC operation over multiple paths between senders and receivers is by no means essential.

## Appropriate Congestion Control {#acc}

An appropriate congestion control mechanism will depend upon the use cases under consideration.

It's worth remembering that we have more experience with QUIC carrying HTTP traffic than with any other type of application at this time, and consequently, we have more experience with congestion control mechanisms such as NewReno {{RFC9002}}, Cubic {{RFC8312}}, and BBR {{I-D.draft-cardwell-iccrg-bbr-congestion-control}} being used with QUIC than with any other congestion control mechanisms. These congestion control mechanisms may also be appropriate for the on-demand use cases described in "od-media".

Conversely, for the interactive use cases described in "interact", these congestion control mechanisms are very likely inappropriate, especially when QUIC is being used with a Media Transport Protocol such as RTP, which provides its own congestion control mechanism, and which does not seem to interact well with a second, QUIC-level congestion control mechanism. Congestion control mechanisms such as SCReAM {{RFC8298}} or NADA {{RFC8698}} may be more appropriate for media. "Congestion Control Requirements for Interactive Real-Time Media" {{RFC8836}} is a useful reference.

Awkwardly, the live media use cases described in PREVIOUS DRAFT live somewhere in the middle, and work will be needed to understand the characteristics of an appropriate congestion control mechanism for these use cases.

## Support Lossless and Lossy Media Transport

TODO: confirm scope of this draft to describe lossless media transport, lossy media transport, or both lossless and lossy transport.

## Flow Directionality

Media should be able to flow in either direction from client to server or
vice-versa, either individually or concurrently but should only be negotiated at
the start of the session.

## WebTransport

TODO: Unsure of the importance of this consideration for live media use cases. If this is critical, we have to consider two
things:

- WebTransport supports HTTP/2, are we going to explicitly exclude it?
- Also, WebTransport {{I-D.draft-ietf-webtrans-overview}} has normative language
around congestion control, which may be at odds with the considerations described in {{acc}}.

## Authentication

In order to allow hosts to authenticate one another, capabilities beyond what QUIC provides may be necessary. This should be kept simple but robust in nature to
prevent attacks like credential brute-forcing.

TODO: More details are required here

## Considerations Implying QUIC Extensions

Most of the discussion of protocol work in this document has avoided mentioning capabilities that may be useful for some use cases, but seem to imply the need for extensions to the QUIC protocol, beyond what is already being considered in the IETF QUIC working group. These are included in this section, for completeness' sake.

### NAT Traversal

From Section 8.2 of {{RFC9000}}:

> Path validation is not designed as a NAT traversal mechanism. Though the mechanism described here might be effective for the creation of NAT bindings that support NAT traversal, the expectation is that one endpoint is able to receive packets without first having sent a packet on that path. Effective NAT traversal needs additional synchronization mechanisms that are not provided here.

Although there are use cases that would benefit from a mechanism for NAT traversal, a QUIC protocol extention would be needed to support those use cases.

### Multicast

Even if multicast and other network broadcasting capabilities are often used in delivering media in our use cases, QUIC doesn't yet support multicast, and a QUIC protocol extension would be needed to do so. In addition, the inclusion of multicast would introduce more complexity in both the specification and
client implimentations.
On the other hand, UDP multicast may be considered as the last mile delivery transport outside of QUIC transport, thus
it would be beneficial for a protocol to provide such an opportunity (e.g. RTP/QUIC -> RTP/UDP).

# IANA Considerations

This document makes no requests of IANA.

# Security Considerations

As this document is intended to guide discussion and consensus, it introduces
no security considerations of its own.

--- back

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

(Your nme could be here ...)
