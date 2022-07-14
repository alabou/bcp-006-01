# AMWA BCP-006-02: NMOS With H.264
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2021, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

[H.264][H.264] is a technology standardized in Rec. ITU-T H.264 (08/2021) for video contribution at high compression rate and video quality.
A companion RTP payload format specification was developed through the IETF Payloads working group, IETF [RFC 6184][RFC-6184].

The [Video Services Forum][VSF] developed Technical Recommendation [TR-??][] and [TR-??][] of the IPMX suite of protocols, which cover the end-to-end application use of constant and variable bitrate compression for video, using the SMPTE ST 2110 and IPMX suite of protocols.
TR-?? and TR-?? mandate the use of the AMWA [IS-04][] and [IS-05][] NMOS Specifications in IPMX compliant systems.  

AMWA IS-04 and IS-05 already have support for RTP transport and can signal the media type `video/h264` as defined in RFC 6184.

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY",
and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][RFC-2119].

## Definitions

The NMOS terms 'Node', 'Source', 'Flow', 'Sender', 'Receiver' are used as defined in the [NMOS Glossary](https://specs.amwa.tv/nmos/main/docs/Glossary.html).

## H.264 IS-04 Sources, Flows and Senders

Nodes capable of transmitting H.264 video streams MUST have Source, Flow and Sender resources in the IS-04 Node API.

### Sources

The Source resource MUST indicate `urn:x-nmos:format:video` for the `format`.
Source resources can be associated with many Flows at the same time.
The Source is therefore unaffected by the use of H.264 compression.

### Flows

The Flow resource MUST indicate `video/h264` in the `media_type` attribute, and `urn:x-nmos:format:video` for the `format`.
This has been permitted since IS-04 v1.1.

For Nodes implementing IS-04 v1.3 or higher, the following additional requirements on the Flow resource apply.

In addition to those attributes defined in IS-04 for all coded video Flows, the following attributes defined in the [Flow Attributes register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/) of the [NMOS Parameter Registers][] are used for H.264.

These attributes provide information for Controllers and Users to evaluate stream compatibility between Senders and Receivers.

- [Components](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#components)  
  The Flow resource MUST indicate the color (sub-)sampling using the `components` attribute.
  The `components` array value corresponds to the `sampling`, `width` and `height` values in the SDP format-specific parameters defined by RFC 9134.
- [Profile](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#profile)  
  The Flow resource MUST indicate the JPEG XS profile, which defines limits on the required algorithmic features and parameter ranges used in the codestream.
  The permitted `profile` values are strings, defined as per RFC 9134.
  The Unrestricted profile is indicated by omitting this attribute.
- [Level](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#level)  
  The Flow resource MUST indicate the JPEG XS level, which defines a lower bound on the required throughput for a decoder in the image (or decoded) domain.
  The permitted `level` values are strings, defined as per RFC 9134.
  The Unrestricted level is indicated by omitting this attribute.
- [Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#bit-rate)  
  The Flow resource MUST indicate the target bit rate (kilobits/second) of the bitstream.
  The `bit_rate` integer value is expressed in units of 1000 bits per second, rounding up.

An example Flow resource is provided in the [Examples](../examples/).

### Senders

The Sender resource MUST indicate `urn:x-nmos:transport:rtp` or one of its subclassifications for the `transport` attribute. TODO: ass ndi and srt transport when their specification is available.
Sender resources provide no indication of media type or format, since this is described by the associated Flow resource.

The SDP file at the `manifest_href` MUST comply with the requirements of RFC 6184.
If the Sender meets the traffic shaping and delivery timing requirements specified for ST 2110-22, the SDP file MUST also comply with the provisions of ST 2110-22.

For Nodes implementing IS-04 v1.3 or higher, the following additional requirements on the Sender resource apply.

In addition to those attributes defined in IS-04 for Senders, the following attributes defined in the [Sender Attributes register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/) of the NMOS Parameter Registers are used for H.264.

- [Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#bit-rate)  
  The Sender resource MUST indicate the bit rate (kilobits/second) including the RTP transport overhead.
  The `bit_rate` integer value is expressed in units of 1000 bits per second, rounding up.
  The value is for the IP packets, so for the RTP payload format per RFC 6184, that includes the RTP, UDP and IP packet headers and the payload.  
  Informative note: This definition is consistent with the definition of the bit rate attribute required by ST 2110-22 in the SDP media description.
- [Packet Transmission Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#packet-transmission-mode)  
  If the Sender is using the single or interleaved packetization mode, it MUST include the `packet_transmission_mode` attribute and set it to either `single_nal_unit` or `interleaved_nal_units`.
  Since the default value of this attribute is `non_interleaved_nal_units`, the Sender MAY omit this attribute when using non-interleaved packetization.
- [ST 2110-21 Sender Type](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#st-2110-21-sender-type)  
  If the Sender complies with the traffic shaping and delivery timing requirements for ST 2110-22, it MUST include the `st2110_21_sender_type` attribute.

An example Sender resource is provided in the [Examples](../examples/).

## H.264 IS-04 Receivers

Nodes capable of receiving H.264 video streams MUST have a Receiver resource in the IS-04 Node API, which lists `video/h264` in the `media_types` array within the `caps` object.
This has been permitted since IS-04 v1.1.

If the Receiver has limitations or perferences on the H.264 video streams that it supports, the Receiver resource MUST indicate constraints in accordance with the [BCP-004-01][] Receiver Capabilities specification.
The `constraint_sets` parameter within the `caps` object can be used to describe combinations of frame rates, width and height, and other parameters which the receiver can support, using the parameter constraints defined in the [Capabilities register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/) of the NMOS Parameter Registers.

The following parameter constraints can be used to express limits specifically defined by Rec. ITU-T H.264 and RFC 6184 for H.264 decoders:

- [Profile](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#profile)
- [Level](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#level)
- [Packet Transmission Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#packet-transmission-mode)  
- [Redundant Picture](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#redundant-picture)
-  [In-Band Parameter Sets](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#in-band-parameter-sets)
-  [SAR Supported](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#sar-supported) TODO: sar-understood should be assumed to match sar-supported

When the H.264 decoder supports has not restriction of profiles or levels, the Receiver can indicate that the parameter is unconstrained, as described in BCP-004-01. Otherwise a Receiver can indicate the supported profiles and levels as enumerated string constraints. When a profile/level is defined as a superset of other profiles/level, the subset profiles/levels need not be enumerated.

Other existing parameter constraints, such as the following, are also appropriate to express limitations on supported H.264 video streams:

- [Frame Width](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#frame-width)
- [Frame Height](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#frame-height)
- [Color Sampling](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#color-sampling)
- [Component Depth](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#component-depth)
- [Format Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#format-bit-rate)
- [Transport Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#transport-bit-rate)
- [ST 2110-21 Sender Type](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#st-2110-21-sender-type)

An example Receiver resource is provided in the [Examples](../examples/).

## H.264 IS-05 Senders and Receivers

Connection Management using IS-05 proceeds in exactly the same manner as for any other stream format carried within RTP.
The SDP file at the **/transportfile** endpoint on Senders MUST comply with the requirements of RFC 6184 and, if appropriate, ST 2110-22.

An SDP file provided in the `transport_file` attribute of a `PATCH` request on the **/staged** endpoint of Receivers MUST also comply with RFC 6184 and, if appropriate, ST 2110-22.

An example SDP file is provided in the [Examples](../examples/).

[BCP-004-01]: https://specs.amwa.tv/bcp-004-01/ "AMWA BCP-004-01 NMOS Receiver Capabilities"
[H.264]: https://www.itu.int/rec/T-REC-H.264/ " Advanced video coding for generic audiovisual services"
[RFC-2119]: https://tools.ietf.org/html/rfc2119 "Key words for use in RFCs"
[RFC-6184]: https://tools.ietf.org/html/rfc6184 "RTP Payload Format for H.264 Video"
[IS-04]: https://specs.amwa.tv/is-04/ "AMWA IS-04 NMOS Discovery and Registration Specification"
[IS-05]: https://specs.amwa.tv/is-05/ "AMWA IS-05 NMOS Device Connection Management Specification"
[NMOS Parameter Registers]: https://specs.amwa.tv/nmos-parameter-registers/ "Common parameter values for AMWA NMOS Specifications"
[TR-??]: https://vsf.tv/download/technical_recommendations/VSF_TR-??_2022-04-20.pdf ""
[VSF]: https://vsf.tv/ "Video Services Forum"
