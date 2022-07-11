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
  RFC2736:
  RFC3550:
  RFC4566:
  RFC6363:
  RFC6716:
  RFC7230:
  RFC7540:
  RFC7826:
  RFC8095:
  RFC8216:
  RFC8298:
  RFC8312:
  RFC8836:
  RFC9000:
  RFC9002:
  RFC8698:

  I-D.draft-cardwell-iccrg-bbr-congestion-control:
  I-D.draft-engelbart-rtp-over-quic:
  I-D.draft-gruessing-moq-requirements:
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
  MOQ-BOF-113:
    target: https://www.ietf.org/mailman/listinfo/moq
    title: "Moq -- Media over QUIC"
  MOQ-BOF-request-114:
    target: https://datatracker.ietf.org/doc/bofreq-hardie-media-over-quic/
    title: "Media over QUIC -- IETF 114 BOF Request"
  MOQ-ml:
    target: https://www.ietf.org/mailman/listinfo/moq
    title: "Moq -- Media over QUIC"
  MOQ-uco-113:
    target: https://datatracker.ietf.org/meeting/113/materials/slides-113-moq-use-cases-overview-00.pdf
    title: "Moq -- Use Cases"

  MPEG-DASH:
    target: https://www.iso.org/standard/79329.html
    title: "ISO/IEC 23009-1:2019 Dynamic adaptive streaming over HTTP (DASH) - Part 1: Media presentation description and segment formats"
    date: 2019-12
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

This document describes considerations for protocol design within the "Media Over QUIC" umbrella.

--- middle

# Introduction {#intro}

This document describes considerations for protocol design within the "Media Over QUIC" umbrella.

At the time of writing, various proposals for "Media over QUIC" have been discussed on the MOQ mailing list ((MOQ-ml}}, and at a non-working-group-forming BOF at IETF 113 {{MOQ-BOF-113}}.

One outcome of the IETF 113 BOF was a request for a working-group-forming BOF at IETF 114, which has been approved {{MOQ-BOF-request-114}}.

The purpose of this document is to describe protocol considerations that, while important, aren't likely to be described in a charter.

## Relationship of This Document With {{I-D.draft-gruessing-moq-requirements}}

This document started life as a section in I-D.draft-gruessing-moq-requirements, which was moved into its own document to allow ease of discussion for both documents.

Careful readers of Sections 4 and 5 in {{I-D.draft-gruessing-moq-requirements}} will note that draft drew a fairly sharp line between interactive media use cases and live media use cases. As of this writing, both are in scope for the proposed MOQ Charter {{MOQ-BOF-request-114}}, which is reflected in the discussion of considerations for protocol design.

## For The Impatient Reader

- Our proposal is to focus on live media use cases, as described in "propscope", rather than on interactive media use cases or on-demand use cases.
- The reasoning behind this proposal can be found in "analy-interact".
- The considerations for protocol work to satisfy the proposed use cases can be found in {{considerations}}.

Most of the rest of this document provides background for these sections.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Terminology {#term}

# Considerations for Protocol Work {#considerations}

 This document is intended to "up-level" the conversation beyond specific protocols, so that we can more likely agree on what is important for protocol design work.

## Here Be Dragons

This focument is Spencer's understanding of discussions that have taken place on the MOQ mailing list {{MOQ-ml}}, at the MOQ BOF at IETF 113 {{MOQ-BOF-113}}, and in side meetings and conversations. Spencer could have misunderstood any part of this, and would love to be corrected if he is confused.

## Media Transport Protocols and Transport Protocols {#mtp}

Within this document, the term "Media Transport Protocol" is used to describe to describe the protocol that carries media metadata and media in its payload, and the term "Transport Protocol" describes a protocol that carries a Media Transport Protocol, or another Transport Protocol, in its payload. This is easier to understand if the reader assumes a protocol stack that looks something like this:

~~~~
               Media
    ---------------------------
           Media Format
    ---------------------------
      Media Transport Protocol
    ---------------------------
       Transport Protocol(s)
~~~~

where

* "Media" would be something like the output of a codec, or other media sources such as closed-captioning,
* "Media Format" would be something like an RTP payload format {{RFC2736}} or an ISOBMFF {{ISOBMFF}} profile,
* "Media Transport Protocol" would be something like RTP or DASH {{MPEG-DASH}}, and
* "Transport Protocol" would be a protocol that provides appropriate transport services, as described in Section 5 of {{RFC8095}}.

Not all possible streaming media applications follow this model, but for the ones that do, it seems useful to have names for the protocol layers between Media Format and Transport Layer

It is worth noting explicitly that the Transport Protocol layer might include more than one protocol. For example, a specific Media Transport Protocol might run over HTTP, or over WebTransport, which in turn runs over HTTP.

It is worth noting explicitly that more complex network protocol stacks are certainly possible - for instance, packets with this protocol stack may be carried in a tunnel, or in a VPN. That's something to keep in mind, but shouldn't affect the MOQ protocol work.

## Considerations for Media and Media Format {#sec-mmf}

### Codec Agility

When initiating a media session, both the sender and receiver will need to agree on the codecs, bitrates, resolution, and other media details based on
capabilities and preferences. This agreement needs to take place before commencing
media transmission, but might also take place during media transmission, perhaps as a result of changes to device output or network
conditions (such as reduction in available network bandwidth).

It may be prefered to use existing ecosystem for such purposes, e.g. SDP {{RFC4566}}.

I think the question(s) whether "MOQ protocol" output will be more like SIP/SHIP, more like SDP, or both(?) - and that's ignoring the architecture work mentioned later

### Authentication

In order to allow hosts to authenticate one another, capabilities beyond what QUIC provides may be necessary. This should be kept simple but robust in nature to
prevent attacks like credential brute-forcing.

TODO: More details are required here

## Considerations for Media Transport Protocols (#sec-mtp}

### Flow Directionality

Media should be able to flow in either direction from client to server or
vice-versa, either individually or concurrently but should only be negotiated at
the start of the session.

## Considerations for Transport Protocols Used For Media (@sec-transport}

### Appropriate Congestion Control {#acc}

The proposed MOQ charter {{MOQ-BOF-request-114}} is silent on the question of appropriate congestion control mechanisms for MOQ. If new congestion control mechanisms are required, they are unlikely to be in scope for a MOQ working group. 

A question that should be in scope is how a QUIC endpoint knows what congestion control is appropropriate for a QUIC connection carrying MOQ media. A variety of potential mechanisms are possible - QUIC signaling, port numbers, APLN, etc. - but need further investigation within MOQ. 

### Support Lossless and Lossy Media Transport

TODO: confirm scope of MOQ to describe lossless media transport, lossy media transport, or both lossless and lossy transport.

This MAY be a way to get at "interactive" and "live" without arguing with Cullen endlessly about whether Meetecho is interactive (insert emoticon)

### WebTransport

I think this ship has sailed (the answer is "yes"), which gives us new and exciting questions about supported protocol stacks.

- WebTransport supports HTTP/2, are we going to explicitly exclude it?
- Also, WebTransport {{I-D.draft-ietf-webtrans-overview}} has normative language
around congestion control, which may be at odds with the considerations described in {{acc}}.

### Migration of Sessions and Multipath

Handling of migration of a session between hosts, either of sender or receiver
should be supported. This may either happen because the sender is undergoing
maintenence or a rebalancing of resource, because the either is experiencing a
change in network connectivity (such as a device moving from WiFi to cellular
connectivity) or other reasons.

This may depend on QUIC capabilities such as {{I-D.draft-ietf-quic-multipath}}
but support for full QUIC operation over multiple paths between senders and receivers is by no means essential.If we're doing QUIC, we OUGHT to be getting these, relatively, for free.

### NAT Traversal

This one's probably still relevant, depending on the answer to {{codec-agility}} and a bunch of other answers.

### Multicast

I don't see this making the cut in the first approved MOQ charter, do you?

Even if multicast and other network broadcasting capabilities are often used in delivering media in our use cases, QUIC doesn't yet support multicast, and a QUIC protocol extension would be needed to do so. In addition, the inclusion of multicast would introduce more complexity in both the specification and
client implimentations.
On the other hand, UDP multicast may be considered as the last mile delivery transport outside of QUIC transport, thus
it would be beneficial for a protocol to provide such an opportunity (e.g. RTP/QUIC -> RTP/UDP).

## Other Considerations

### Architecture and Workflows

The proposed MOQ BOF request {{MOQ-BOF-request-114}} makes a reference to "The MOQ architecture". Is there a starting point for this? {{MOQ-uco-113}}, and other presentations at the IETF 113 BOF used a three-level workflow (from ingest, to syndication, to distribution). The 

We talked about complete workflows - does it look like the proposed charter et. al just assumes you can use the same mechanism for syndication and for streaming? Do we agree with that?

Need to talk about "number of media sources", and especially the difference between 1:m and n:m topologies.

### Support an Appropriate Range of Latencies

We said we were going to name numbers, rather than labels, right?

I think where MOQ is headed, is that they are going to TRY to support everything, and if you can't support an appropriate latency for your use case, that's Simply A Matter Of Network Engineering.

Should "on demand" be a thing?

Key point here isn't just one-way-delay, it's also return-path latency.

# IANA Considerations

This document makes no requests of IANA.

# Security Considerations

As this document is intended to guide discussion and consensus, it introduces
no security considerations of its own.

Protocol Specifications that reflect those discussions would contain their own relevant security considerations.

--- back

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

(Your nme could be here ...)
