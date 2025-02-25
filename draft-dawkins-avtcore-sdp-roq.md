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

This document describes several new SDP "proto" and "attribute-name" attribute values in the "Session Description Protocol (SDP) Parameters" IANA registry that can be used to describe QUIC transport for RTP and RTCP packets, and describes how SDP Offer/Answer can be used to set up an RTP connection using QUIC as a transport protocol. These new values are necessary to allow the use of QUIC as an underlying transport protocol for RTP applications that commonly use SDP as a session signaling protocol to set up RTP connections, such as SIP and WebRTC.

This document also contains non-normative guidance for implementers.

--- middle

# Introduction {#intro}

This document describes several new SDP "proto" and "attribute-name" attribute values in the "Session Description Protocol (SDP) Parameters" IANA registry ({{SDP-protos}} and {{SDP-attribute-name}}) that can be used to describe QUIC transport for RTP and RTCP packets (hereafter abbreviated as "RoQ"), and describes how SDP Offer/Answer ({{!RFC3264}}) can be used to set up an ({{!RFC3550}}) connection using QUIC ({{!RFC9000}} and related specifications), as defined in {{!I-D.ietf-avtcore-rtp-over-quic}}. These new values are necessary to allow the use of QUIC as an underlying transport protocol for RTP applications that commonly use SDP as a session signaling protocol to set up RTP connections, such as SIP ({{?RFC3261}}) and WebRTC ({{?RFC8825}}).

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

This document reuses the following AVP profiles from {{SDP-protos}}, in order to allow existing SIP and RTCWEB RTP applications to migrate more easily to RTP over QUIC:

 * RTP/AVP ("RTP Profile for Audio and Video Conferences with Minimal Control"), as defined in {{!RFC3551}}.
 * RTP/AVPF ("Extended RTP Profile for Real-time Transport Control Protocol (RTCP)-Based Feedback (RTP/AVPF)"), as defined in {{!RFC4585}}.
 * RTP/SAVP ("The Secure Real-time Transport Protocol (SRTP)"), as defined in {{!RFC3711}}.
 * RTP/SAVPF ("Extended Secure RTP Profile for Real-time Transport Control Protocol (RTCP)-Based Feedback (RTP/SAVPF)"), as defined in {{!RFC5124}}.

## The QUIC proto {#quic}

The 'QUIC' protocol identifier is similar to the 'UDP' and 'TCP' protocol identifiers in that it only describes the transport protocol, and not the upper-layer protocol.

An 'm' line that specifies 'QUIC' MUST further qualify the application-layer protocol using an fmt identifier, such as "QUIC/RTP/AVPF".

Media described using an 'm' line containing the 'QUIC' protocol identifier are carried using QUIC streams, as defined in {{!RFC9000}}, or in QUIC DATAGRAMs, as defined in {{!RFC9221}}.

The following is an update to the ABNF for an 'm' line, as specified by {{!RFC8866}}, that defines a new value for the QUIC protocol.

~~~~~~
   media-field =         %s"m" "=" media SP port \["/" integer\]
                             SP proto 1*(SP fmt) CRLF

   m= line parameter        parameter value(s)
   ------------------------------------------------------------------
   <media>:                 (unchanged from RFC8866)
   <proto>:                 'QUIC'
   <port>:                  UDP port number
   <fmt>:                   (unchanged from RFC8866)
~~~~~~

## RoQ RTP Protos {#rtp-protos}

As much as possible, attributes used in this section are reused from other specifications, with references to the original definitions.

### The QUIC/RTP/AVP proto {#avp}

The following is an update to the ABNF for an 'm' line, as specified by {{!RFC8866}}, that defines a new value for the QUIC/RTP/AVP protocol.

~~~~~~
   media-field =         %s"m" "=" media SP port \["/" integer\]
                             SP proto 1*(SP fmt) CRLF

   m= line parameter        parameter value(s)
   ------------------------------------------------------------------
   <media>:                 (unchanged from RFC8866)
   <proto>:                 'QUIC/RTP/AVP'
   <port>:                  UDP port number
   <fmt>:                   (unchanged from RFC8866)
~~~~~~

### The QUIC/RTP/AVPF proto {#avpf}

The following is an update to the ABNF for an 'm' line, as specified by {{!RFC8866}}, that defines a new value for the QUIC/RTP/AVPF protocol.

~~~~~~
   media-field =         %s"m" "=" media SP port \["/" integer\]
                             SP proto 1*(SP fmt) CRLF

   m= line parameter        parameter value(s)
   ------------------------------------------------------------------
   <media>:                 (unchanged from RFC8866)
   <proto>:                 'QUIC/RTP/AVPF'
   <port>:                  UDP port number
   <fmt>:                   (unchanged from RFC8866)
~~~~~~

### The QUIC/RTP/SAVP proto {#savp}

The following is an update to the ABNF for an 'm' line, as specified by {{!RFC8866}}, that defines a new value for the QUIC/RTP/SAVP protocol.

~~~~~~
   media-field =         %s"m" "=" media SP port \["/" integer\]
                             SP proto 1*(SP fmt) CRLF

   m= line parameter        parameter value(s)
   ------------------------------------------------------------------
   <media>:                 (unchanged from RFC8866)
   <proto>:                 'QUIC/RTP/SAVP'
   <port>:                  UDP port number
   <fmt>:                   (unchanged from RFC8866)
~~~~~~

### The QUIC/RTP/SAVPF proto {#savpf}

The following is an update to the ABNF for an 'm' line, as specified by {{!RFC8866}}, that defines a new value for the QUIC/RTP/SAVPF protocol.

~~~~~~
   media-field =         %s"m" "=" media SP port \["/" integer\]
                             SP proto 1*(SP fmt) CRLF

   m= line parameter        parameter value(s)
   ------------------------------------------------------------------
   <media>:                 (unchanged from RFC8866)
   <proto>:                 'QUIC/RTP/SAVPF'
   <port>:                  UDP port number
   <fmt>:                   (unchanged from RFC8866)
~~~~~~

# New SDP Attribute-Names for RoQ {#new-attrs}

This section describes new SDP attributes that are created for use with RoQ.

## RoQ QUIC-DATAGRAMs Attribute {#quic-datagrams}

As noted in {{!I-D.ietf-avtcore-rtp-over-quic}}, the RoQ specification only assumes a baseline QUIC implementation as defined in {{!RFC8999}}, {{!RFC9000}}, {{!RFC9001}}, and {{!RFC9002}}, and this baseline does not provide unreliable datagrams, which are defined in {{!RFC9221}}.

It is very likely that RoQ implementers will wish to use QUIC DATAGRAMs, for a variety of reasons too large to list in this specification.

In order to support this capability, this section defines a new SDP media-level attribute, "quic-datagrams". The attribute can be associated with an SDP media description ("m=" line) with any of the QUIC proto values defined in {{quic}}.

Actual support for QUIC DATAGRAMs is negotiated between two QUIC endpoints, as described in Section 3 of {{!RFC9221}}, and nothing specified in SDP will cause a QUIC endpoint that does not advertise support for QUIC DATAGRAMs to suddenly begin to support them. However, it may be useful to tell a RoQ receiver that the RoQ sender plans to send QUIC DATAGRAMs, and to allow a RoQ receiver to tell the SDP sender that the RoQ receiver does not plan to support receiving QUIC DATAGRAMs for that media flow.

If the quic-datagrams attribute is present, the RoQ sender indicates its intention to use QUIC DATAGRAMs for the associated media flow, and the RoQ receiver indicates its willingness to accept QUIC DATAGRAMs for that media flow.

The quic-datagrams attribute is OPTIONAL for RoQ applications, even when the sender intends to use QUIC DATAGRAMs. Omitting the quic-datagrams attribute merely complicates the sender's decision whether to send specific media using QUIC DATAGRAMs.

If the attribute is not present in SDP, the sender sends its QUIC Initial packet with a non-zero max_datagram_frame_size QUIC transport parameter, and the receiver with a non-zero max_datagram_frame_size QUIC transport parameter, all will proceed normally. If the sender attempts to send DATAGRAMs before it receives a non-zero name=max_datagram_frame_size QUIC transport parameter in the initial handshake, this is a QUIC PROTOCOL_VIOLATION, as described in {{Section 3 of !RFC9221}}.

The definition of the SDP "quic-datagrams" attribute is:

Attribute name:  quic-datagrams

Type of attribute:  session or media

Mux category:  IDENTICAL

> **NOTE:** This specification sets the mux category (as discussed in Section 4 of {{?RFC8859}}) as IDENTICAL, as an RTP mixer which is multiplexing several incoming streams onto one connection needs to provide the same quidance to a RoQ receiver for all multiplexed media flows.

Subject to charset:  No

Purpose:  This attribute provides a hint as to whether the media associated with the SDP media description is likely to arrive via QUIC DATAGRAMs. It is a property attribute, which does not take a value.

Contact name:  Spencer Dawkins

Contact e-mail:  spencerdawkins.ietf@gmail.com

Reference:  {{!I-D.dawkins-avtcore-sdp-roq}} (This document)

Syntax:

~~~~~~
    quic-datagrams
~~~~~~

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

**Editors' Note:** What other considerations have we missed, that need to be mentioned here?

This document assumes that an authenticated QUIC connection will be opened using a "roq" ALPN or some other ALPN, as described in Section 4.1 of {{!I-D.ietf-avtcore-rtp-over-quic}}.

**Editor's Note:** Do we need to mention the SDP "tls-id" attribute, defined in {{?RFC8842}}? That spec is DTLS-specific, but whether it would also apply to TLS/QUIC connections changing five-tuples isn't clear to Spencer. This may require some conversations about QUIC connection migration (and, of course, Multipath QUIC, when {{?I-D.draft-ietf-quic-multipath}} leaves the QUIC working group).

## The SDP "setup" Attribute {#setup}

The SDP "setup" attribute, defined for media over TCP in {{!RFC4145}}, is reused to indicate which endpoint initiates a QUIC connection (whether the endpoint actively opens a QUIC connection, or accepts an incoming QUIC connection. This attribute MUST be present in SDP offers and answers for RoQ.

## The SDP "connection" Attribute {#connect}

The SDP "connection" attribute, defined for TCP in {{!RFC4145}}, is reused to indicate whether the endpoint will open a new QUIC connection, or reuse an existing QUIC connection. This attribute MUST be present in SDP offers and answers for RoQ.

## The SDP "fingerprint" Attribute {#fingerprint}

Because QUIC itself uses the TLS handshake as described in {{!RFC9001}}, the parties to a RoQ session MUST also provide authentication certificates as part of the TLS handshake procedure, as described in {{Section 5 of !RFC8122}}. When self-signed certificates are used, certificate fingerprint is represented in SDP using the fingerprint SDP attribute, as illustrated in {{Section 3.4 of !RFC8122}}, in order to provide assurance that two endpoints with no prior relationship are not being subjected to a man-in-the-middle attack.

## The SDP "rtcp-mux" Attribute {#rtcp-mux}

{{!I-D.ietf-avtcore-rtp-over-quic}} defines how RTP and RTCP can be multiplexed onto a single QUIC connection. An application that will perform this multiplexing MUST include the "rtcp-mux" attribute defined in {{!RFC5761}} in its SDP signaling.

# Implementation Topics {#impl-topics}

**Note:** {{impl-topics}} contains no normative requirements.

{{idents-atts}} and {{new-attrs}} of this document provide normative requirements for RoQ endpoints that use SDP for signaling.

Beyond those normative requirements, there are topics that are worth considering as part of implementation work, because we have been asked, "but what about the grommet SDP extension?" These topics are not part of the normative "SDP for RoQ" specification, but are gathered here for now. These topics might better appear in an appendix, a separate "SDP for RoQ Implementation Guide", or even best included in the GitHub repository Wiki for this document, because that would allow us to maintain this material on an ongoing basis.

**Editors' Question:** We've been asked about interaction with UDP-Connect to open pinholes in corporate proxies. What is there to say about that, in this specification?

## Bundling Considerations {#bundle-cons}

{{?RFC8843}} describes a  Session Description Protocol (SDP) Grouping Framework extension called 'BUNDLE'. The extension can be used with the SDP offer/answer mechanism to negotiate the usage of a single transport (5-tuple) for sending and receiving media described by multiple SDP media descriptions ("m=" sections).

The authors believe that no special considerations apply when using BUNDLE with a single QUIC connection carrying RoQ.

If an application uses multiple 5-tuples in order to allow QUIC Connection Migration as described in {{Section 9 of !RFC9000}}, it is assumed that only one QUIC path will be active at any given time.

If an application uses multiple 5-tuples in order to make use of the Multipath Extension for QUIC as described in {{?I-D.draft-ietf-quic-multipath}}, this would allow multiple QUIC paths to be active simultaneously, and this assumption will need revisiting when {{?I-D.draft-ietf-quic-multipath}} is approved.

## Implications of Replacing RTCP Feedback with QUIC Feedback  {#quic-rtcp}

{{Section 10.4 of !I-D.ietf-avtcore-rtp-over-quic}} describes how some RTCP feedback can be replaced by equivalent statistics that are already collected by QUIC. The exact RTCP feedback that can be replaced depends on the QUIC statistics exposed by the underlying QUIC implementation, and these QUIC statistics might depend in turn on QUIC extensions supported in the underlying QUIC implementation. The set of possible relevant QUIC extensions is not fixed, but some discussion appears in {{Section 11 of !I-D.ietf-avtcore-rtp-over-quic}}. For these reasons, decisions about what RTCP feedback can be replaced will always be media-dependent and implementation-dependent.

It is assumed that an implementer will review the application requirements, the RTP proto in use, the available RTCP feedback for the media types being transferred, and available QUIC statistics, and will do the right thing.

More information about what RTCP feedback might be replaced by QUIC statistics, and what is possible, appears in {{Appendix B of !I-D.ietf-avtcore-rtp-over-quic}}.

**Editors' Question:** The API between QUIC and RoQ is, of course, a private matter, but in at least some cases, it might be useful to specify specific QUIC feedback substitutions so that the other RoQ endpoint does not provide RTCP feedback that this RoQ endpoint does not need and does not plan to use. **We almost certainly need implementation and deployment experience before we can do more than offer a strawman proposal.** Spencer suggests that we include any IETF-specified QUIC feedback substitutions in separate documents, as we do with RTCP extensions today, or even include them in the GitHub repository Wiki for this document.

## Implications of Congestion Control {#cong-ctrl}

A significant distinction between QUIC transport and UDP transport is that QUIC transport is always congestion-controlled at the QUIC layer. For RTP media, this ought to be a difference without a difference. Any RTP application ought to perform flow control and congestion control using a control mechanism that is appropriate for the media being transferred, and this applies to RoQ applications as well.

Having said this, it is worth saying that RoQ applications can use any RTCP mechanisms such as Codec Control Messages {{?RFC5104}} that can affect variables such as the Maximum Media Stream Bit Rate, as long as the RTP application respects the relevant congestion control considerations (in the case of Codec Control Messages, these considerations appear in {{Section 5 of ?RFC5104}}).

RoQ applications can also use RTP Control Protocol (RTCP) Feedback for Congestion Control, as described in {{?RFC8888}}.

Because RoQ applications are always congestion controlled at the QUIC connection level, QUIC congestion control also acts as an RTP Circuit Breaker {{?RFC8083}}, with no special considerations for RoQ.

## Implications of using ICE with RoQ {#ice-impl}

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
|**SDP line** | **Notes** |
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
|a=quic-datagrams | Expects to use QUIC DATAGRAMs in this RTP session, as defined in this specification |
|a=setup:passive | Will wait for QUIC handshake (setup attribute from {{!RFC4145}} |
|a=connection:new | don't want to reuse an existing QUIC connection (connection attribute from {{!RFC4145}} |
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

## AV Profile-related Security Considerations

This document currently defines the QUIC/RTP/SAVP and QUIC/RTP/SAVPF secure profiles, although this might seem unnecessary, because RoQ already uses QUIC security mechanisms. That choice is made for two reasons:

* If an implementer wishes to use an existing RTP application over RoQ, continuing to support existing legacy secure profiles minimizes the changes required to the implementations at each end.
* If a RoQ RTP endpoint is communicating with a non-RoQ RTP endpoint using an Top-PtP-Translator RTP middlebox (as described in {{Section 3.2.1 of !RFC7667}}, the RoQ endpoint might reasonably use a secure AVP profile over QUIC when sending to the middlebox, because the sending endpoint doesn't have any way to control or even discover whether the RTP middlebox used a secure profile when forwarding RTP to a non-RoQ endpoint.

If this is not a good plan, there are two alternatives.

* We can REQUIRE conformant RoQ middleboxes to bridge between AVP and AVPF profiles carried over RoQ and SAVP and SAVPF profiles carried using other transports, so that insecure media flows are not relayed over insecure transport protocols.
* Alternatively, an implementation can use SFRAME as described in {{!RFC9605}} to achieve end-to-end media security, at the price of disallowing some types of translating middleboxes (for example, Topo-Media-Translator middleboxes, as described in {{Section 3.2.1.2 of !RFC7667}}.

**Editors' Question:** We will need discussion within the working group to determine whether we do need to include QUIC/RTP/SAVP and QUIC/RTP/SAVPF in this specification.

# IANA Considerations

This document defines new IANA values in the {{SDP-protos}} and {{SDP-attribute-name}} registries.

## QUIC and QUIC-related protos {#IANA-quic-protos}

This document defines five new SDP proto names, QUIC, QUIC/RTP/AVP, QUIC/RTP/AVPF, QUIC/RTP/SAVP, and QUIC/RTP/SAVPF/. The details of these protos are defined in {{quic}} and {{rtp-protos}}.

## quic-datagrams {#IANA-quic-datagrams}

This document defines a new SDP session media-level attribute, "quic-datagrams". The details of the attribute are defined in {{quic-datagrams}}.

## roq-flow-id

This document defines a new SDP media-level attribute, "roq-flow-id". The details of the attribute are defined in {{rtp-quic-flow-id}}.

--- back

# Acknowledgments
{:numbered="false"}

The authors thank Sam Hurst for sharing his thoughts about the challenges of developing SDP for RoQ, and for providing specific comments and draft text.

The authors thank Bernard Aboba and Mathis Westerlund for comments on various previous versions of this specification, under a variety of draft names.

**Editors' Question:** Who else should we name in this paragraph? I should look through the minutes from previous AVTCORE meetings, to see who I missed.

The authors thank Mathis Engelbart for helping to keep this draft aligned with {{!I-D.ietf-avtcore-rtp-over-quic}}.

A significant amount of work on this draft happened while Spencer was affiliated with Tencent America LLC. Spencer appreciates that support.
