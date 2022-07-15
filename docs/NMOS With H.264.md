# AMWA BCP-006-02: NMOS With H.264
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2021, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

[H.264][H.264] is a technology standardized in Rec. ITU-T H.264 | ISO/IEC 14496-10 for video contribution at high compression rate and video quality.
A companion RTP payload format specification was developed through the IETF Payloads working group, IETF [RFC 6184][RFC-6184].

The [Video Services Forum][VSF] developed Technical Recommendation [TR-??][] and [TR-??][] of the IPMX suite of protocols, which cover the end-to-end application use of constant and variable bitrate compression for video, using the SMPTE ST 2110 and IPMX suite of protocols.
TR-?? and TR-?? mandate the use of the AMWA [IS-04][] and [IS-05][] NMOS Specifications in IPMX compliant systems.  

AMWA IS-04 and IS-05 already have support for RTP transport and can signal the media type `video/h264` as defined in RFC 6184.

- [ ] TODO: Current ST-2110 suite of protocols only support constant bit-rate streams. An new protocol for variable bit-rate is required.
- [ ] TODO: Do we really want to have IPMX protocol here or limite to actual ST-2110 protocols which IPMX uses.
- [ ] TODO: References are made to the ITU-T H.264 specification instead of the ISO becase it is more accessible (free).

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
  The Flow resource MUST indicate the H.264 profile, which defines algorithmic features and limits that shall be supported by all decoders conforming to that profile.
  The permitted `profile` values are strings, defined as per ITU-T Rec. H.264 Annex A
  - "ConstrainedBaseline"
  - "Baseline" (Default is not specified) 
  - "Main"
  - "Etended"
  - "High"
  - "HighProgressive"
  - "ConstrainedHigh"
  - "High10"
  - "High10Progressive"
  - "High-422"
  - "HighPredictive-444"
  - "High10Intra"
  - "HighIntra-422"
  - "HighIntra-422"
  - "CAVLCIntra-444"
  
- [Level](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#level)  
  The Flow resource MUST indicate the H.264 level, which defines a set of limits on the values that may be taken by the syntax elements of an H.264 bitstream.
  The permitted `level` values are strings, defined as per ITU-T Rec. H.264 Annex A
  - "1" (Default is not specified) 
  - "1b", "1.1", "1.2", "1.3"
  - "2", "2.1", "2.2"
  - "3", "3.1", "3.2"
  - "4", "4.1", "4.2"
  - "5", "5.1", "5.2"
  - "6", "6.1", "6.2" 

- [Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#bit-rate)  
  The Flow resource MUST indicate the target bit rate (kilobits/second) of the bitstream.
  The `bit_rate` integer value is expressed in units of 1000 bits per second, rounding up.
  
- [ ] TODO: Indicate that the bit-rate SHALL comply with assocaited declaration within the bitstream, if any (ex. hrd_parameters of the bitstream).

An example Flow resource is provided in the [Examples](../examples/).

### Senders

The Sender resource MUST indicate `urn:x-nmos:transport:rtp` or one of its subclassifications for the `transport` attribute. 

- [ ] TODO: add ndi and srt transport when the specification is available.

Sender resources provide no indication of media type or format, since this is described by the associated Flow resource.

The SDP file at the `manifest_href` MUST comply with the requirements of RFC 6184 in the [Declarative Session Description](https://datatracker.ietf.org/doc/html/rfc6184#section-8.2.3) mode of operation. The SDP Offer/Answer Model described in RFC 6184 is not supported. The "fmtp" source attribute as specified in Section 6.3 of RFC 5576 (Source-Specific Media Attributes in the Session Description Protocol) is not supported. 
If the Sender meets the traffic shaping and delivery timing requirements specified for ST 2110-22, the SDP file MUST also comply with the provisions of ST 2110-22.

For Nodes implementing IS-04 v1.3 or higher, the following additional requirements on the Sender resource apply.

In addition to those attributes defined in IS-04 for Senders, the following attributes defined in the [Sender Attributes register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/) of the NMOS Parameter Registers are used for H.264.

- [Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#bit-rate)  
  The Sender resource MUST indicate the bit rate (kilobits/second) including the RTP transport overhead.
  The `bit_rate` integer value is expressed in units of 1000 bits per second, rounding up.
  The value is for the IP packets, so for the RTP payload format per RFC 6184, that includes the RTP, UDP and IP packet headers and the payload.  
  Informative note: This definition is consistent with the definition of the bit rate attribute required by ST 2110-22 in the SDP media description.
- [Packet Transmission Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#packet-transmission-mode)  
  If the Sender is using the single or interleaved packetization mode, it MUST include the `packet_transmission_mode` attribute and set it to either `single_nal_unit` or `interleaved_nal_units`. The packet_transmission_mode attribute map the the RFC 6184 packetization-mode with `single_nal_unit` corresponding to value 0, `non_interleaved_nal_units` to value 1 and `interleaved_nal_units` to value 2.
  Since the default value of this attribute is `non_interleaved_nal_units`, the Sender MAY omit this attribute when using non-interleaved packetization.
- [ST 2110-21 Sender Type](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#st-2110-21-sender-type)  
  If the Sender complies with the traffic shaping and delivery timing requirements for ST 2110-22, it MUST include the `st2110_21_sender_type` attribute.

- [ ] TODO: Indicate that the bit-rate SHALL comply with assocaited declaration within the bitstream, if any (ex. hrd_parameters of the bitstream).

An example Sender resource is provided in the [Examples](../examples/).

A Sender MUST produces an H.264 bitstream that is compliant with the profile and level declared in the stream's associated SDP transport file.

## H.264 IS-04 Receivers

Nodes capable of receiving H.264 video streams MUST have a Receiver resource in the IS-04 Node API, which lists `video/h264` in the `media_types` array within the `caps` object.
This has been permitted since IS-04 v1.1.

If the Receiver has limitations or perferences on the H.264 video streams that it supports, the Receiver resource MUST indicate constraints in accordance with the [BCP-004-01][] Receiver Capabilities specification.
The `constraint_sets` parameter within the `caps` object can be used to describe combinations of frame rates, width and height, and other parameters which the receiver can support, using the parameter constraints defined in the [Capabilities register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/) of the NMOS Parameter Registers.

The following parameter constraints can be used to express limits or preferences specifically defined by Rec. ITU-T H.264 and RFC 6184 for H.264 decoders:

- [Profile](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#profile)
  - Some H.264 profiles are superset of other profiles. From the point of view of Receiver Capabilities such superset profile is assumed to also correspond to the associated subset profiles such that Receiver Capabilities does not have to include the subset profiles. The H.264 specification describe the relationship among the profiles.

- [Level](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#level)
  - Some H.264 levels are superset of other levels. From the point of view of Receiver Capabilities such superset level is assumed to also correspond to the associated subset levels such that Receiver Capabilities does not have to include the subset level. The H.264 specification describe the relationship among the levels.

- [Packet Transmission Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#packet-transmission-mode)

- [Redundant Picture](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#redundant-picture)
  - A Receiver declare the redundant-picture capability to indicate to the Sender that it is capable of decoding and using redundant pictures. With or without this capability a Receiver is required to decode redundant picture fro teh bitstream even it they are not used by the decoder. This capability is mainly an bandwidth  optimisation to indicate to the Sender that redundant picture shall not be sent as the decoder is not capable of using them.

- [MultiFlow Parameter Sets](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#multiflow-parameter-sets)
- [In-Band Parameter Sets](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#in-band-parameter-sets)

- [ ] TODO: describe that a Sender always has the ability to switch active parameter sets independently of this capability (unless constrained by IS-11). A Receiver not supporting the multiflow capability would fail a PATCH if more than one parameter sets is provided in the SDP transport file.

- [SAR Supported](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#sar-supported) 

- [ ] TODO: sar-understood should be assumed to match sar-supported

When the H.264 decoder has not restriction of profiles or levels, the Receiver can indicate that the parameter is unconstrained, as described in BCP-004-01. Otherwise a Receiver can indicate the supported profiles and levels as enumerated string constraints. When a profile/level is defined as a superset of other profiles/level, the subset profiles/levels need not be enumerated.

Other existing parameter constraints, such as the following, are also appropriate to express limitations on supported H.264 video streams:

- [Frame Width](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#frame-width)
- [Frame Height](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#frame-height)
- [Grain Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#grain-rate)
- [Color Sampling](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#color-sampling)
- [Component Depth](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#component-depth)
- [Format Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#format-bit-rate)
- [Transport Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#transport-bit-rate)
- [ST 2110-21 Sender Type](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#st-2110-21-sender-type)

An example Receiver resource is provided in the [Examples](../examples/).

A Receiver MUST be able to decode a bitstream conforming to the profiles and levels declared in the Receiver Capabilities. A Receiver may have preferences and more optimal profiles and levels that may be declared through Receiver Capabilities. A preferred constraint set MAY indicate such preferences and another constraint set MAY indicate full support of some profiles and levels. A Receiver MAY further constraint the support of an H.264 bitstream compliant with a profile and level using other constraints in its Receiver Capabilities.

## H.264 IS-05 Senders and Receivers

Connection Management using IS-05 proceeds in exactly the same manner as for any other stream format carried within RTP.
The SDP file at the **/transportfile** endpoint on Senders MUST comply with the requirements of RFC 6184 and, if appropriate, ST 2110-22.

An SDP file provided in the `transport_file` attribute of a `PATCH` request on the **/staged** endpoint of Receivers MUST also comply with RFC 6184 and, if appropriate, ST 2110-22.

An example SDP file is provided in the [Examples](../examples/).

#### Out-of-band transporting of parameter sets through the SDP transport file

The initial active the parameter sets (SPS, PPS) of the `sprop-parameter-sets` attribute of an SDP transport file SHALL comply with the Flow associated with the Sender on activation and the related parameters of the `fmtp=` attribute of the SDP transport file (Ex. ST2110-22 requires the parameters width, height, exactframerate, TP, mediaclk, ts-refclk and b= to be declared in the SDP transport file). A Sender MAY provide many out-of-band parameter sets in the SDP transport file.

As per RFC 6184 the out-of-band parameter sets MUST comply with the profile and level declared in the SDP transport file and the associated Flow.

The parameters of the `fmtp=` attribute of the SDP transport file related to the current active parameter sets MAY keep their initial value related to the initial active parameter sets when a Sender's current active parameter sets change after activation among the parameter sets declared in the `sprop-parameter-sets` attribute. While swiching among the parameter sets the Sender MUST update the associated Flow to comply with the current active parameter sets.

A Sender MAY seamlessly switch among the out-of-band parameter sets dynamically, provided that the Flow associated with the Sender changes accordingly and the content of the SDP transport file does not change. If the content of the SDP transport file changes, the Sender SHALL comply with IS-04, IS-05.

A Receiver infers updated SDP transport file parameters from the current active parameter sets. The SDP transport file of the Sender is not allowed to change in order to benefit from the seamless transitions between parameter sets.  If the content of the Sender's SDP transport file changes, the Receiver SHALL comply with IS-04, IS-05. 

A Receiver declaring the `multiflow_parameter_sets` capability SHOULD verify that all the parameter sets of the SDP transport file comply with the Receiver Capabilities, infering the parameters of the `fmtp=` attribute from the paremeter sets for each possibly active parameter sets. A Receiver not supporting the -`multiflow_parameter_sets` capability SHOULD verify that only one active parameter sets is declared in the `sprop-parameter-sets` attribute.

Note to IPMX: An IPMX H.264 stream will not be able to benefit from the seamless transitions of parameter sets because of the attributes measuredpixclk, htotal and vtotal that are part of the SDP transport file and that cannot be inferred from the parameter sets. 

#### In-band transporting of parameter sets through the media bitstream

The `in_band_parameter_sets` Receiver Capabilities indicate that a Receiver supports getting additional parameter sets in-band. A Receiver SHALL declare the `multiflow_parameter_sets` capability along with the `in_band_parameter_sets` capability. A Sender MUST provide an initial active parameter sets in the SDP transport file `sprop-parameter-sets` attribute.

As per RFC 6184 the out-of-band and in-band parameter sets MUST comply with the profile and level declared in the SDP transport file and the associated Flow.

A Receiver SHOULD verify that the current active parameter sets comply with the Receiver Capabilities, infering the parameters of the `fmtp=` attribute of the SDP transport file from the curent active parameter sets.



[BCP-004-01]: https://specs.amwa.tv/bcp-004-01/ "AMWA BCP-004-01 NMOS Receiver Capabilities"
[H.264]: https://www.itu.int/rec/T-REC-H.264/ " Advanced video coding for generic audiovisual services"
[RFC-2119]: https://tools.ietf.org/html/rfc2119 "Key words for use in RFCs"
[RFC-6184]: https://tools.ietf.org/html/rfc6184 "RTP Payload Format for H.264 Video"
[IS-04]: https://specs.amwa.tv/is-04/ "AMWA IS-04 NMOS Discovery and Registration Specification"
[IS-05]: https://specs.amwa.tv/is-05/ "AMWA IS-05 NMOS Device Connection Management Specification"
[NMOS Parameter Registers]: https://specs.amwa.tv/nmos-parameter-registers/ "Common parameter values for AMWA NMOS Specifications"
[TR-??]: https://vsf.tv/download/technical_recommendations/VSF_TR-??_2022-04-20.pdf ""
[VSF]: https://vsf.tv/ "Video Services Forum"
