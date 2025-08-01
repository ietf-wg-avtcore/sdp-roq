---
title: SDP Offer/Answer for RTP over QUIC (RoQ)
abbrev: SDP O/A for RoQ
category: exp

docname: draft-dawkins-avtcore-sdp-roq-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
# area: "Web and Internet Transport"
# workgroup: "Audio/Video Transport Core Maintenance"
keyword:
 - RTP over QUIC
 - RoQ
 - SDP
venue:
#  group: "Audio/Video Transport Core Maintenance"
#  type: "Working Group"
#  mail: "avt@ietf.org"
#  arch: "https://mailarchive.ietf.org/arch/browse/avt/"
  github: "ietf-wg-avtcore/sdp-roq"
  latest: "https://ietf-wg-avtcore.github.io/sdp-roq/draft-dawkins-avtcore-sdp-roq.html"

author:
 -
    fullname: "Spencer Dawkins"
    organization: Wonder Hamster Internetworking LLC
    country: United States of America
    email: spencerdawkins.ietf@gmail.com
 -
    fullname: Victor Pascual
    organization: Nokia
    city: Barcelona
    country: Spain
    email: victor.pascual_avila@nokia.com

normative:
  SDP-protos:
    target: https://www.iana.org/assignments/sdp-parameters/sdp-parameters.xhtml#sdp-parameters-2
    title: "SDP Parameters - Proto"
    date: September 2021
  SDP-attribute-name:
    target: https://www.iana.org/assignments/sdp-parameters/sdp-parameters.xhtml#sdp-att-field
    title: "SDP Parameters - attribute-name"
    date: September 2021

informative:

--- abstract

This document is intended to allow the use of QUIC as an underlying transport protocol for RTP applications that commonly use SDP as a session signaling protocol to set up RTP connections, such as SIP and WebRTC. The document describes several new SDP "proto" and "attribute-name" attribute values in the "Session Description Protocol (SDP) Parameters" IANA registry that can be used to describe QUIC transport for RTP and RTCP packets, and describes how SDP Offer/Answer can be used to set up an RTP connection using QUIC.

This document also contains non-normative guidance for implementers.

--- middle

# Introduction {#intro}

This document is intended to allow the use of QUIC as an underlying transport protocol for RTP applications that commonly use SDP as a session signaling protocol to set up RTP connections, such as SIP ({{?RFC3261}}) and WebRTC ({{?RFC8825}}). The document describes several new SDP "proto" and "attribute-name" attribute values in the "Session Description Protocol (SDP) Parameters" IANA registry ({{SDP-protos}} and {{SDP-attribute-name}}) that can be used to describe QUIC transport for RTP and RTCP packets (hereafter abbreviated as "RoQ"), and describes how SDP Offer/Answer ({{!RFC3264}}) can be used to set up an RTP ({{!RFC3550}}) connection using QUIC ({{!RFC9000}} and related specifications), as defined in {{!I-D.ietf-avtcore-rtp-over-quic}}.

The normative descriptions and requirements for RoQ SDP appear in {{idents-atts}}, {{new-attrs}}, and {{special-cons}}.

Non-normative guidance for implementers appears in {{impl-topics}}.

A sample SDP offer appears in {{offer-example}}.

## Notes for Readers {#readernotes}

(Note to RFC Editor - if this document ever reaches you, please remove this section)

This document has not yet been adopted by any IETF working group, so does not carry any special status within the IETF.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

Because the use of SDP to describe RTP over QUIC transport relies heavily on terminology introduced in {{!I-D.ietf-avtcore-rtp-over-quic}}, the definitions in that document are prerequisite for understanding this document, and those terms are included here by reference.

# New SDP Protocol identifiers {#idents-atts}

This document reuses AVP profiles from {{SDP-protos}}, in order to allow existing SIP and RTCWEB RTP applications to migrate more easily to RTP over QUIC.

## The QUIC proto {#quic}

The 'QUIC' protocol identifier is similar to the 'UDP' and 'TCP' protocol identifiers in that it only describes the transport protocol, and not the upper-layer protocol.

An 'm' line that specifies 'QUIC' MUST further qualify the application-layer protocol using an fmt identifier, such as "QUIC/RTP/AVPF".

Media described using an 'm' line containing the 'QUIC' protocol identifier are carried using QUIC streams, as defined in {{!RFC9000}}, or in QUIC DATAGRAMs, as defined in {{!RFC9221}}.

## RoQ RTP Protos {#rtp-protos}

As much as possible, attributes used in this section are reused from other specifications, with references to the original definitions.

### The QUIC/RTP/AVP proto {#avp}

The QUIC/RTP/AVP transport describes RTP media with minimal RTCP-based feedback ("RTP Profile for Audio and Video Conferences with Minimal Control"), as defined in {{!RFC3551}}.

The QUIC/RTP/AVP transport is realized using the framing method described in {{!I-D.ietf-avtcore-rtp-over-quic}}.

### The QUIC/RTP/AVPF proto {#avpf}

The QUIC/RTP/AVPF transport describes RTP media with extended RTCP-based feedback RTP/AVPF ("Extended RTP Profile for Real-time Transport Control Protocol (RTCP)-Based Feedback (RTP/AVPF)"), as defined in {{!RFC4585}}.

The QUIC/RTP/AVPF transport is realized using the framing method described in {{!I-D.ietf-avtcore-rtp-over-quic}}.

### The QUIC/RTP/SAVP proto {#savp}

The QUIC/RTP/SAVP transport describes RTP media with RTP/SAVP ("The Secure Real-time Transport Protocol (SRTP)"), as defined in {{!RFC3711}}.

The QUIC/RTP/SAVP transport is realized using the framing method described in {{!I-D.ietf-avtcore-rtp-over-quic}}.

### The QUIC/RTP/SAVPF proto {#savpf}

The QUIC/RTP/SAVPF transport describes RTP media with RTP/SAVPF ("Extended Secure RTP Profile for Real-time Transport Control Protocol (RTCP)-Based Feedback (RTP/SAVPF)"), as defined in {{!RFC5124}}.

The QUIC/RTP/SAVPF transport is realized using the framing method described in {{!I-D.ietf-avtcore-rtp-over-quic}}.

## AV Profile-related Security Considerations {#secure-avp-profiles}

This document currently defines the QUIC/RTP/SAVP and QUIC/RTP/SAVPF secure profiles, although this might seem unnecessary, because RoQ already uses QUIC security mechanisms. That choice is made for two reasons:

* If an implementer wishes to adapt an existing RTP application to use RoQ, and that application uses a secure AVP profile (for example, SAVPF), providing support for legacy secure AVP profiles minimizes the changes required to the implementations at each end.
* While an RoQ RTP endpoint might wish to communicate with other RoQ RTP endpoints using an AVP profile that does not include media-level security (for example, AVPF) when communicating with a non-RoQ RTP endpoint, this communication must by definition use a Topo-PtP-Translator RTP middlebox (as described in {{Section 3.2.1 of !RFC7667}}, and the RoQ endpoint has no way to know whether the RTP middlebox has negotiated a secure AVP profile with the non-RoQ endpoint. In this situation, a RoQ implementation can use some approach like SFRAME, as described in {{!RFC9605}}, to achieve end-to-end media security, at the price of disallowing some types of translating middleboxes (for example, Topo-Media-Translator middleboxes, as described in {{Section 3.2.1.3 of !RFC7667}}).

>**NOTE:** Any PtP Translator middlebox that negotiates an RTP/AVP(F) AVP profile to both RTP endpoints, rather than an RTP/SAVP(F) profile, introduces a security risk. This is the case no matter which transport protocols are being translated, and the introduction of RoQ as an RTP transport protocol does nothing to change this risk.

# New SDP Attribute-Names for RoQ {#new-attrs}

This section describes new SDP attributes that are created for use with RoQ.

## RoQ Flow Identifiers {#rtp-quic-flow-id}

Section 5.1 of {{!I-D.ietf-avtcore-rtp-over-quic}} introduces a multiplexing identifier for RTP flows carried over a QUIC connection called "Flow Identifiers". This section defines a new SDP media-level attribute, "roq-flow-id". The attribute can be associated with an SDP media description ("m=" line) with any of the QUIC proto values defined in {{quic}}. In that case, the "m=" line port value indicates the port of the underlying QUIC transport UDP port, and the "roq-flow-id" value indicates the RoQ Flow Identifier.

No default value is defined for the SDP "roq-flow-id" attribute. Therefore, if the attribute is not present, the associated "m=" line MUST be considered invalid.

The definition of the SDP "roq-flow-id" attribute is:

Attribute name:  roq-flow-id

Type of attribute:  session or media

Mux category:  CAUTION

> **NOTE:** This specification sets the mux category (as discussed in Section 4 of {{?RFC8859}}) as CAUTION, as an RTP mixer which is multiplexing several incoming streams onto one connection needs to ensure that RoQ Flow Identifiers do not overlap, and might need to rewrite the Flow Identifiers in received streams when further multiplexing them.

Subject to charset:  No

Purpose:  This attribute indicates the RoQ Flow Idenfitier associated with the SDP media description.

Contact name:  Spencer Dawkins

Contact e-mail:  spencerdawkins.ietf@gmail.com

Reference:  {{!I-D.dawkins-avtcore-sdp-roq}} (This document)

Syntax:

~~~~~~
    roq-flow-id = 1*19(DIGIT) ; DIGIT defined in RFC 4566
~~~~~~

The RoQ flow identifier range is between 0 and 4611686018427387903 (2^62 - 1) (both included). Leading zeroes MUST NOT be used.

# Special Considerations for Selected SDP Attributes When Using RoQ Transport {#special-cons}

This section does not introduce new SDP attribute extensions, but describes how some existing SDP attribute extensions are reused to describe RoQ media flows.

We have two goals for this section:

* To describe how existing SDP attributes are used differently in order to support RoQ, and
* To be able to make the statement that other existing SDP attribute extensions can be reused with RoQ, with no special considerations.

This document assumes that an authenticated QUIC connection will be opened using a "roq" ALPN or some other ALPN, as described in Section 4.1 of {{!I-D.ietf-avtcore-rtp-over-quic}}.

## The SDP "setup" Attribute {#setup}

The SDP "setup" attribute, defined for media over TCP in {{!RFC4145}}, is reused to indicate which endpoint initiates a QUIC connection (whether the endpoint actively opens a QUIC connection, or accepts an incoming QUIC connection. This attribute MUST be present in SDP offers and answers for RoQ.

## The SDP "tls-id" Attribute {#tls-id}

The SDP "tls-id" attribute is reused as described in {{Section 5.1 of !RFC8842}} to allow either endpoint to decide whether to open a new QUIC connection, rather than reusing an existing QUIC connection. This attribute MUST be present in SDP offers and answers for RoQ.

## The SDP "fingerprint" Attribute {#fingerprint}

Because QUIC itself uses the TLS handshake as described in {{!RFC9001}}, the parties to a RoQ session MUST also provide authentication certificates as part of the TLS handshake procedure, as described in {{Section 5 of !RFC8122}}. When self-signed certificates are used, certificate fingerprint is represented in SDP using the fingerprint SDP attribute, as illustrated in {{Section 3.4 of !RFC8122}}, in order to allow mutual authentication, and provide assurance that two endpoints with no prior relationship are not being subjected to a person-in-the-middle attack, unless the signaling channel is also subjected to a person-in-the-middle attack.

## The SDP "rtcp-mux" Attribute {#rtcp-mux}

A RoQ application MUST include the "rtcp-mux" attribute defined in {{!RFC5761}} in its SDP signaling.

# Implementation Topics {#impl-topics}

**Note:** {{impl-topics}} contains no normative requirements.

{{idents-atts}}, {{new-attrs}}, and {{special-cons}} of this document provide normative requirements for RoQ endpoints that use SDP for signaling.

Beyond those normative requirements, there are topics that are worth considering as part of implementation work, because we have been asked, "but what about the grommet SDP extension?" These topics are not part of the normative "SDP for RoQ" specification, but are gathered here for now. These topics might better appear in an appendix, a separate "SDP for RoQ Implementation Guide", or even best included in the GitHub repository Wiki for this document, because that would allow us to maintain this material on an ongoing basis.

## Bundling Considerations {#bundle-cons}

{{?RFC8843}} describes a  Session Description Protocol (SDP) Grouping Framework extension called 'BUNDLE'. The extension can be used with the SDP offer/answer mechanism to negotiate the usage of a single transport (5-tuple) for sending and receiving media described by multiple SDP media descriptions ("m=" sections).

The authors believe that no special considerations apply when using BUNDLE with a single QUIC connection carrying RoQ.

If an application uses multiple 5-tuples in order to allow QUIC Connection Migration as described in {{Section 9 of !RFC9000}}, it is assumed that only one QUIC path will be active at any given time.

If an application uses multiple 5-tuples in order to make use of the Multipath Extension for QUIC as described in {{?I-D.draft-ietf-quic-multipath}}, this would allow multiple QUIC paths to be active simultaneously, and this assumption will need revisiting when {{?I-D.draft-ietf-quic-multipath}} is approved.

## Implications of Replacing RTCP Feedback with QUIC Feedback  {#quic-rtcp}

{{Section 10.4 of !I-D.ietf-avtcore-rtp-over-quic}} describes how some RTCP feedback can be replaced by equivalent statistics that are already collected by QUIC. The exact RTCP feedback that can be replaced depends on the QUIC statistics exposed by the underlying QUIC implementation, and these QUIC statistics might depend in turn on QUIC extensions supported in the underlying QUIC implementation. The set of possible relevant QUIC extensions is not fixed, but some discussion appears in {{Section 11 of !I-D.ietf-avtcore-rtp-over-quic}}. For these reasons, decisions about what RTCP feedback can be replaced will always be media-dependent and implementation-dependent.

It is assumed that an implementer will review the application requirements, the RTP proto in use, the available RTCP feedback for the media types being transferred, and available QUIC statistics, and will do the right thing.

More information about what RTCP feedback might be replaced by QUIC statistics, and what is possible, appears in {{Appendix B of !I-D.ietf-avtcore-rtp-over-quic}}.

## Implications of Congestion Control {#cong-ctrl}

A significant distinction between QUIC transport and UDP transport is that QUIC transport is always congestion-controlled at the QUIC layer. For RTP media, this ought to be a distinction without a difference. RoQ applications, like any other RTP applications, ought to perform flow control and congestion control using a control mechanism that is appropriate for the media being transferred.

Having said this, it is worth saying that RoQ applications can use any RTCP mechanisms such as Codec Control Messages {{?RFC5104}} that can affect variables such as the Maximum Media Stream Bit Rate, as long as the RTP application respects the relevant congestion control considerations (in the case of Codec Control Messages, these considerations appear in {{Section 5 of ?RFC5104}}).

RoQ applications can also use bandwidth modifiers ("b="), as described in {{Section 6 of ?RFC8859}}, to control bandwidth at the media level, as is the case with any other RTP applications.

RoQ applications can also use RTP Control Protocol (RTCP) Feedback for Congestion Control, as described in {{?RFC8888}}.

Because RoQ applications are always congestion controlled at the QUIC connection level, QUIC congestion control also acts as an RTP Circuit Breaker {{?RFC8083}}, with no special considerations for RoQ.

## Implications of using ICE with RoQ {#ice-impl}

The profiles defined in {{rtp-protos}} assume that if an application needs to perform NAT traversal, the endpoints will perform ICE procedures as described in {{!RFC8445}} to gather and prioritize candidate pairs, and will then select candidate pairs that can be included in SDP media lines, as described in {{rtp-protos}}.

**Editors' Note:** Other ways of performing NAT traversal for QUIC are possible, and this specification might be modified to support one or more of those methods in the future, given sufficient requirements.
The modifications would likely include additional protos being defined in {{rtp-protos}}.
The editors encourage feedback on this point.

Because a peer address is validated during QUIC connection establishment as described in {{Section 8.1 of !RFC9000}}, when a RoQ endpoint uses ICE {{!RFC8445}} to communicate with another RoQ endpoint, an ICE agent will have already performed ICE candidate pair connectivity checking before a QUIC connection can be opened for use with RoQ.

An implementer should be aware that it is possible for a RoQ connection to be subject to "ping"/liveness checks at several different levels:

* QUIC PING frames, as described in {{Section 10.1.2 of !RFC9000}}
* ICE keepalives, as described in {{Section 10 of ?RFC5245}} and in {{?RFC6263}}
* ICE consent freshness, as described in {{?RFC7675}}
* RTCP packets, as described in {{Section 6.2 of !RFC3550}}

The following considerations are worth reviewing for implementers.

* QUIC PING frames are entirely under the control of an implementation. If a QUIC connection carries RTP/RTCP traffic, the RTCP transmission interval is likely to suffice for RTP liveness detection, but a wise implementer will look at this in their environment and proceed accordingly.
* ICE consent freshness, as described in {{Section 4 of ?RFC7675}}, also serves the ICE keepalive function, so ICE keepalives are no longer necessary.
* At least some RTCP feedback might be unnecessary, as described in {{quic-rtcp}}, so a wise implementer will look at what RTCP feedback can be replaced with QUIC feedback.

# A QUIC/RTP/AVPF Offer Example {#offer-example}

**Editor's Note:** Spencer has been updating this example while working on the document, but we will need to review it carefully, before requesting Working Group Last Call.

**Note:** {{offer-example}} contains no normative requirements.

A complete example of an SDP offer using QUIC/RTP/AVPF might look like:

|---
| SDP line| Notes |
|---
|**Session Description** | |
|v=0 |Same as {{Section 5 of !RFC8866}}|
|o=jdoe 3724394400 3724394405 IN IP4 198.51.100.1 |Same as {{Section 5 of !RFC8866}}|
|s=Call to John Smith |Same as {{Section 5 of !RFC8866}}|
|i=SDP Offer #1 |Same as {{Section 5 of !RFC8866}}|
|u=http://www.jdoe.example.com/home.html |Same as {{Section 5 of !RFC8866}}|
|e=Jane Doe <jane@jdoe.example.com> |Same as {{Section 5 of !RFC8866}}|
|p=+1 617 555-6011 |Same as {{Section 5 of !RFC8866}}|
|c=IN IP4 198.51.100.1 |Same as {{Section 5 of !RFC8866}}|
|a=tls-id:abc3de65cddef001be82 | As defined in {{Section 4 of !RFC8842}}|
|a=setup:passive | Will wait for QUIC handshake (setup attribute from {{!RFC4145}} |
|t=0 0 |Same as {{Section 5 of !RFC8866}}|
|a=fingerprint:sha-1 47:5D:A9:48:E4:BA:44:D9:B5:BC:31:AB:4B:80:06:11:3F:D5:F5:38 | {{Section 5 of !RFC8122}} |
|---
|**Media Description** | |
|m=video 51372 QUIC/RTP/AVPF 99 | As defined in {{avpf}}|
|a=rtcp-mux | Will multiplex RTP and RTCP on the same port {{!RFC5761}}|
|a=roq-flow-id:4 | RoQ Flow Identifier shall be 4 for streams described by this SDP media description|
|c=IN IP6 2001:db8::2 |Same as {{Section 5 of !RFC8866}}|
|a=rtpmap:99 h266/90000 |H.266 VVC codec {{?I-D.ietf-avtcore-rtp-vvc}}|
|===


This example is largely based on an example appearing in {{!RFC8866}}, Section 5, but includes the necessary protos and attribute-names for RoQ SDP.

This SDP offer might be included in a SIP INVITE, for example.

# Security Considerations

The security considerations sections of the Normative References used in this document are incorporated by reference.

The reader is especially directed to the discussion of AV profile security considerations in {{secure-avp-profiles}}.

# IANA Considerations

This document defines new IANA values in the {{SDP-protos}} and {{SDP-attribute-name}} registries.

## QUIC and QUIC-related protos {#IANA-quic-protos}

This document defines these new SDP proto names.

|-------+----------------+--------------------------------------+
|Type | SDP Name | Reference |
|-------+----------------+--------------------------------------+
| proto | QUIC | {{quic}} of this specification |
| proto | QUIC/RTP/AVP | {{rtp-protos}} of this specification |
| proto | QUIC/RTP/AVPF | {{rtp-protos}} of this specification |
| proto | QUIC/RTP/SAVP | {{rtp-protos}} of this specification |
| proto | QUIC/RTP/SAVPF | {{rtp-protos}} of this specification |
|-------+--------+-------------+--------------+-----------------+

## roq-flow-id

This document defines a new SDP attribute, "roq-flow-id".

|-------+--------+-------------+--------------+-----------------+
|Type | SDP Name | Usage Level | Mux Category | Reference |
|-------+--------+-------------+--------------+-----------------+
| attribute | roq-flow-id | session, media | CAUTION | {{roq-flow-id}} of this specification |
|-------+--------+-------------+--------------+-----------------+

--- back

# Acknowledgments
{:numbered="false"}

The authors thank Sam Hurst for sharing his thoughts about the challenges of developing SDP for RoQ, and for providing specific comments and draft text.

The authors thank Mathis Engelbart for his feedback on this specification, and for helping to keep this this specification aligned with {{!I-D.ietf-avtcore-rtp-over-quic}}.

The authors thank Bernard Aboba and Mathis Westerlund for comments on various previous versions of this specification, under a variety of draft names.

The authors thank Jonathan Lennox and Harald Alvestrand for their feedback on this version of the specification.

The authors thank Roman Shpount for helping us get the specification of "connection" and "tls-id" correct in our specification.

A significant amount of work on this draft happened while Spencer was affiliated with Tencent America LLC. Spencer still appreciates that support.
