# AMWA BCP-006-02: NMOS With H.264
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2021, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

H.264 is a technology standardized in Rec. [ITU-T H.264][H.264] | ISO/IEC 14496-10 for video contribution at high compression rate and video quality.
A companion RTP payload format specification was developed through the IETF Payloads working group, IETF [RFC 6184][RFC-6184].

The [Video Services Forum][VSF] developed Technical Recommendation [TR-??][] and [TR-??][] of the IPMX suite of protocols, which cover the end-to-end application use of constant and variable bitrate compression for video, using the SMPTE ST 2110 and IPMX suite of protocols.
TR-?? and TR-?? mandate the use of the AMWA [IS-04][] and [IS-05][] NMOS Specifications in IPMX compliant systems.  

AMWA IS-04 and IS-05 already have support for RTP transport and can signal the media type `video/h264` as defined in RFC 6184.

- [ ] TODO: Current ST-2110 suite of protocols only support constant bit-rate streams. An new protocol for variable bit-rate is required. How should we refer to this upcoming variable bit-rate specification?
- [ ] TODO: Should we reference the VSF's IPMX protocol here or limit to actual ST-2110 protocols which IPMX uses.
- [ ] TODO: References are made to the ITU-T H.264 specification instead of the ISO because it is more accessible (free).
- [ ] TODO: This specification shall be written without assuming ST-2110 transport ... It shall only be based on RFX 6184 and add is ST-2110 is used then ...

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
  The Flow resource MUST indicate the color (sub-)sampling, width, height and depth of the associated uncompressed picture using the `components` attribute.
  The `components` array values MUST correspond to the stream's current active parameter sets values. A Flow MUST track the stream's current active parameter sets.
  
- [ ] ST 2110-22 does not require sampling or depth. RFC 6184 does not define any such parameters.The information MUST be derived from the H.264 active parameter sets by the Receiver. If teh streams complies with ST 2110-22, the width and height MUST comply with the stream's active parameter sets.
- [ ] All the information encoded in the `components` attribute MUST also be provided in the SDP transport file. What a Controller observe, a Receiver shall also observe it.
  
- [Profile](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#profile)  
  The Flow resource MUST indicate the H.264 profile, which defines algorithmic features and limits that shall be supported by all decoders conforming to that profile. The Flow's `profile` attribute map to the `profile-level-id` parameter of the SDP transport file which is optional according to RCS 6184 and REQUIRED by this specification unless the `profile-level-id` parameter corresponds to the default value in which case it MAY be omited.
  The permitted `profile` values are strings, defined as per ITU-T Rec. H.264 Annex A
  - "ConstrainedBaseline"
  - "Baseline" (Default if not specified in the SDP transport file) 
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
  - "HighIntra-444"
  - "CAVLCIntra-444"
  
- [ ] For H.265 the profiles are as follow and map to `profile-id`. The profile-space parameter of the SDP transprot file SHALL be omited or alwyas be 0.
  - Main (Default if not specified in the SDP transport file) 
  - Main10 
  - Main10Still 
  - MainStill 
  - Monochrome, 
  - Monochrome10 
  - Monochrome12 
  - Monochrome16  
  - Main12 
  - Main10-422 
  - Main12-422 
  - Main444 
  - Main10-444 
  - Main12-444 
  - MainIntra 
  - Main10Intra 
  - Main12Intra 
  - Main10Intra-422 
  - Main12Intra-422 
  - MainIntra-444 
  - Main10Intra-444 
  - Main12Intra-444 
  - Main16Intra-444 
  - MainStill-444 
  - Main16Still-444 
  - HighThroughput-444 
  - HighThroughput10-444 
  - HighThroughput14-444 
  - HighThroughput16Intra-444 
  - ScreenExtendedMain 
  - ScreenExtendedMain10 
  - ScreenExtendedMain-444 
  - ScreenExtendedMain10-444 
  - ScreenExtendedHighThroughput-444 
  - ScreenExtendedHighThroughput10-444 
  - ScreenExtendedHighThroughput14-444 
  
- [Level](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#level)  
  The Flow resource MUST indicate the H.264 level, which defines a set of limits on the values that may be taken by the syntax elements of an H.264 bitstream. The Flow's `level` attribute map to the `profile-level-id` parameter of the SDP transport file which is optional accroding to RCS 6184 and REQUIRED by this specification unless the `profile-level-id` parameter corresponds to the default value in which case it MAY be omited.
  The permitted `level` values are strings, defined as per ITU-T Rec. H.264 Annex A
  - "1" (Default if not specified in the SDP transport file) 
  - "1b", "1.1", "1.2", "1.3"
  - "2", "2.1", "2.2"
  - "3", "3.1", "3.2"
  - "4", "4.1", "4.2"
  - "5", "5.1", "5.2"
  - "6", "6.1", "6.2" 

- [ ] For H.265 the levels are as follow and map to `level-id` and `tier-flag`
  - "1" 
  - "2", "2.1" 
  - "3" 
  - "3.1" (Default if not specified in the SDP transport file) 
  - "4", "4.1" 
  - "5", "5.1", "5.2" 
  - "6", "6.1", "6.2" 
  - "High-1" 
  - "High-2", "High-2.1" 
  - "High-3", "High-3.1" 
  - "High-4", "High-4.1" 
  - "High-5", "High-5.1", "High-5.2" 
  - "High-6", "High-6.1", "High-6.2" 

- [Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#bit-rate)  
  The Flow resource MUST indicate the target encoding bit rate (kilobits/second) of the H.264 bitstream. This attribute has no equivalent in the SDP transport file.
  The `bit_rate` integer value is expressed in units of 1000 bits per second, rounding up.
  
- [ ] TODO: Indicate that the bit-rate SHALL comply with assocaited declaration within the bitstream, if any (ex. hrd_parameters of the bitstream).

An example Flow resource is provided in the [Examples](../examples/).

### Senders

The Sender resource MUST indicate `urn:x-nmos:transport:rtp` or one of its subclassifications for the `transport` attribute. 

- [ ] TODO: add ndi and srt transport when the specification is available.

Sender resources provide no indication of media type or format, since this is described by the associated Flow resource.

The SDP file at the `manifest_href` MUST comply with the requirements of RFC 6184 in the [Declarative Session Description](https://datatracker.ietf.org/doc/html/rfc6184#section-8.2.3) mode of operation. The SDP Offer/Answer Model described in RFC 6184 is not supported. The "fmtp" source attribute as specified in Section 6.3 of RFC 5576 (Source-Specific Media Attributes in the Session Description Protocol) is not supported. 

- [ ] For H.265 indicate also that "The tx-mode parmeter of the SDP transport file SHALL not be omited or always set to SRST (Single RTP Stream Transport). 

If the Sender meets the traffic shaping and delivery timing requirements specified for ST 2110-22, the SDP file MUST also comply with the provisions of ST 2110-22.

For Nodes implementing IS-04 v1.3 or higher, the following additional requirements on the Sender resource apply.

In addition to those attributes defined in IS-04 for Senders, the following attributes defined in the [Sender Attributes register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/) of the NMOS Parameter Registers are used for H.264.

- [Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#bit-rate)  
  The Sender resource MUST indicate the bit rate (kilobits/second) including the RTP transport overhead of hte H.264 stream.
  The `bit_rate` integer value is expressed in units of 1000 bits per second, rounding up.
  The value is for the IP packets, so for the RTP payload format per RFC 6184, that includes the RTP, UDP and IP packet headers and the payload.  
  Informative note: This definition is consistent with the definition of the bit rate attribute required by ST 2110-22 in the SDP media description.
  
- [Packet Transmission Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#packet-transmission-mode)  
  If the Sender is using the non-interleaved or interleaved packetization modes, it MUST include the `packet_transmission_mode` attribute and set it to either `non_interleaved_nal_units` or `interleaved_nal_units`. The packet_transmission_mode attribute map the the RFC 6184 packetization-mode parameter with `single_nal_unit` corresponding to value 0, `non_interleaved_nal_units` to value 1 and `interleaved_nal_units` to value 2.
  Since the default value of this attribute is `single_nal_unit`, the Sender MAY omit this attribute when using that mode. When the `packet-transmission-mode` attribute is included, the associated `packetization-mode` parameter of the SDP transport file MUST also be included.
  Parameters related to the `interleaved_nal_units` mode SHOULD be included in the SDP transport file unless their default value is used.

- [ ] For H.265 the `packet_transmission_mode` attribute can either be `non_interleaved_nal_units` or `interleaved_nal_units`. The `packet_transmission_mode` maps to sprop-max-don-diff of hte SDP transport file being 0 for `non_interleaved_nal_units` or greater than 0 for `interleaved_nal_units`.

- [ST 2110-21 Sender Type](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#st-2110-21-sender-type)  
  If the Sender complies with the traffic shaping and delivery timing requirements for ST 2110-22, it MUST include the `st2110_21_sender_type` attribute.

- [ ] TODO: Indicate that the bit-rate SHALL comply with assocaited declaration within the bitstream, if any (ex. hrd_parameters of the bitstream).

An example Sender resource is provided in the [Examples](../examples/).

A Sender MUST produces an H.264 bitstream that is compliant with the profile and level declared in the stream's associated SDP transport file.

A Sender MUST provide initial active parameter sets in the `sprop-parameter-sets` parameter of the stream's associated SDP transport file, compliant with the declared profile and level. The initial active parameter sets SHALL comply with the Flow associated with the Sender on activation and the related parameters of the `fmtp=` attribute of the SDP transport file. A Sender MAY provide many out-of-band parameter sets (active and inactive) in `sprop-parameter-sets` parameter.

- [ ] A Receiver SHALL know as much as a Controller about a stream. As there is a mandatiry Flow associated with a Sender that SHALL be an initial active parameter sets delcared in the SDP transport file for the Receiver.

## H.264 IS-04 Receivers

Nodes capable of receiving H.264 video streams MUST have a Receiver resource in the IS-04 Node API, which lists `video/h264` in the `media_types` array within the `caps` object.
This has been permitted since IS-04 v1.1.

If the Receiver has limitations or perferences on the H.264 video streams that it supports, the Receiver resource MUST indicate constraints in accordance with the [BCP-004-01][] Receiver Capabilities specification.
The `constraint_sets` parameter within the `caps` object can be used to describe combinations of frame rates, width and height, and other parameters which the receiver can support, using the parameter constraints defined in the [Capabilities register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/) of the NMOS Parameter Registers.

The following parameter constraints can be used to express limits or preferences specifically defined by Rec. ITU-T H.264 and RFC 6184 for H.264 decoders:

- [Profile](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#profile)
  - Some H.264 profiles are superset of other profiles. From the point of view of Receiver Capabilities such superset profile is assumed to also correspond to the associated subset profiles such that Receiver Capabilities does not have to include the subset profiles. The H.264 specification describe the relationship among the profiles. If a Receiver does not indicate a `profile` constraint it is assumed as supporting the "Baseline" profile.

- [Level](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#level)
  - Some H.264 levels are superset of other levels. From the point of view of Receiver Capabilities such superset level is assumed to also correspond to the associated subset levels such that Receiver Capabilities does not have to include the subset level. The H.264 specification describe the relationship among the levels.  If a Receiver does not indicate a `level` constraint it is assumed as supporting level "1".

- [Packet Transmission Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#packet-transmission-mode)
  - Only one mode MUST be specified.  If a Receiver does not indicate a `packet_transmissin_mode` constraint it is assumed as supporting "single_nal_unit" mode.

- [MultiFlow Parameter Sets](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#multiflow-parameter-sets)

  - A Receiver declares the multi-flow capability to indicate the it supports H.264 streams using various parameter sets. As each parameter sets is associated to a specific Flow, this capability indicate that a Receiver is capable of decoding seamlessly H.264 streams where the associated Flow changes dynamically. All the parameter sets comply with the `profile-level-id` parameter of the stream associated SDP transport file. The parameter sets are specified out-of-band using the `sprop-parameter-sets` attribute of an SDP transport file if the following in-band capability is not declared and both out-of-band and in-band otherwise.

- [ ] TODO: describe that a Sender always has the ability to switch active parameter sets independently of this capability (unless constrained by IS-11). A Receiver not supporting the multi-flow capability would fail a PATCH if more than one parameter sets is provided in the SDP transport file.

- [In-Band Parameter Sets](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#in-band-parameter-sets)

  - A Receiver declares the in-band capability to indicate the it supports H.264 streams producing in-band parameter sets that update or augment the initial out-of-band parameter sets. A receiver SHALL be capable of decoding in-band parameter sets and behave according to its capabilities if those are not duplicate of the original out-of-band parameter sets.

- [ ] TODO: describe that a Sender always has the ability to switch active parameter sets independently of this capability (unless constrained by IS-11). A Receiver not supporting the in-band capability would become inactive if in-band parameter sets do not match the out-of-band ones.

- [SAR Supported](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#sar-supported) 

- [ ] TODO: sar-understood should be assumed to match sar-supported

When the H.264 decoder has no restrictions of profiles or levels, the Receiver can indicate that the parameter is unconstrained, as described in BCP-004-01. Otherwise a Receiver can indicate the supported profiles and levels as enumerated string constraints. When a profile/level is defined as a superset of other profiles/level, the subset profiles/levels need not be enumerated.

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

A Receiver MUST be able to decode a bitstream conforming to the profiles and levels declared in the Receiver Capabilities or the implied default value of such capabilities. A Receiver may have preferences and more optimal profiles and levels that may be declared through Receiver Capabilities. A preferred constraint set MAY indicate such preferences while another constraint set MAY indicate full support of some profiles and levels. A Receiver MAY further constraint the support of an H.264 bitstream compliant with a profile and level using other constraints in its Receiver Capabilities.

## H.264 IS-05 Senders and Receivers

Connection Management using IS-05 proceeds in exactly the same manner as for any other stream format carried within RTP.
The SDP file at the **/transportfile** endpoint on Senders MUST comply with the requirements of RFC 6184 and, if appropriate, ST 2110-22.

An SDP file provided in the `transport_file` attribute of a `PATCH` request on the **/staged** endpoint of Receivers MUST also comply with RFC 6184 and, if appropriate, ST 2110-22.

An example SDP file is provided in the [Examples](../examples/).

#### Parameter Sets

The `sprop-parameter-sets` parameter of an SDP transport file MAY contain a collection of out-of-band parameter sets. An H.264 stream's current active parameter sets referencing a parameter sets from this collection is said to be using an out-of-band parameter sets. 

An H.264 stream may transport a collection of in-band parameter sets. An H.264 stream's current active parameter sets referencing a parameter sets from !@#$!@##$!@##$!@#$

Otherwise it is said to be using an in-band parameter sets or it is an invalid stream if the current active parameter sets is not defined.

The current active parameter sets of an H.264 stream MUST comply with the Flow associated with the Sender and the related parameters of the `fmtp=` attribute of the SDP transport file.

The current active parameter sets of an H.164 stream MUST comply with the `profile-level-id` declared in the SDP transport file and the `profile` and `level` of the associated Flow.

The `in_band_parameter_sets` Receiver Capability indicates that a Receiver supports getting additional or updated parameter sets in-band in addition to the out-of-band ones received from the SDP transport file.

The `multiflow_parameter_sets` Receiver Capability indicates that a Receiver supports decoding an H.264 stream where the current active parameter sets changes dynamically.

A Receiver SHOULD verify that the current active parameter sets comply with the Receiver's Capabilities, infering the parameters of the `fmtp=` attribute of the SDP transport file from the curent active parameter sets. If a Receiver only support out-of-band parameter sets it MUST perform the verification when a Controller PATCH the **/staged** endpoint for activation. In this situation, all the out-of-band parameter sets MUST be compliant with the Receiver Capabilities. Otherwise if a Receiver supports both out-of-band and in-band parameter sets it MUST perform the verification of the out-of-band parameter sets when a Controller PATCH the **/staged** endpoint for activation and it MUST verify the in-band parameter sets just-in-time as it decodes the stream. In this situation, all the out-of-band and in-band parameter sets MUST be compliant withthe Receiver Capabilities.


The active the parameter sets of the `sprop-parameter-sets` parameter of an SDP transport file SHALL comply with the Flow associated with the Sender and the related parameters of the `fmtp=` attribute of the SDP transport file. A Sender MAY provide many out-of-band parameter sets in the SDP transport file or none. The `multiflow_parameter_sets` capability of a Receiver indicate if it supports the use of multiple active parameter sets. If the Sender provides multiple out-of-band parameter sets and the Receiver `multiflow_parameter_sets` capability is not set, it SHALL consider

As per RFC 6184 the out-of-band parameter sets MUST comply with the `profile-level-id` declared in the SDP transport file and by this specification to the `profile` and `level` of the associated Flow.

The parameters of the `fmtp=` attribute of the SDP transport file related to the current active parameter sets MAY keep their initial value related to the initial active parameter sets when a Sender's current active parameter sets change after activation among the parameter sets declared in the `sprop-parameter-sets` attribute. While swiching among the parameter sets the Sender MUST update the associated Flow to comply with the current active parameter sets.

A Sender MAY seamlessly switch among the out-of-band parameter sets dynamically, provided that the Flow associated with the Sender changes accordingly and the content of the SDP transport file does not change. If the content of the SDP transport file changes, the Sender SHALL comply with IS-04, IS-05.

A Receiver infers updated SDP transport file parameters from the current active parameter sets. The SDP transport file of the Sender is not allowed to change in order to benefit from the seamless transitions between parameter sets. If the content of the Sender's SDP transport file changes, the Receiver SHALL comply with IS-04, IS-05. 

A Receiver declaring the `multiflow_parameter_sets` capability SHOULD verify that all the parameter sets of the SDP transport file comply with the Receiver Capabilities, infering the parameters of the `fmtp=` attribute from the paremeter sets for each possibly active parameter sets. A Receiver not supporting the `multiflow_parameter_sets` capability SHOULD verify that only one active parameter sets is declared in the `sprop-parameter-sets` attribute.

- [ ] Note to IPMX: An IPMX H.264 stream will not be able to benefit from the seamless transitions of parameter sets because of the attributes measuredpixclk, htotal and vtotal that are part of the SDP transport file and that cannot be inferred from the parameter sets. 

#### In-band transporting of parameter sets through the media bitstream

The `in_band_parameter_sets` Receiver Capabilities indicate that a Receiver supports getting additional or updated parameter sets in-band. 

As per RFC 6184 the out-of-band and in-band parameter sets MUST comply with the profile and level declared in the SDP transport file and the associated Flow.





[BCP-004-01]: https://specs.amwa.tv/bcp-004-01/ "AMWA BCP-004-01 NMOS Receiver Capabilities"
[H.264]: https://www.itu.int/rec/T-REC-H.264/ " Advanced video coding for generic audiovisual services"
[RFC-2119]: https://tools.ietf.org/html/rfc2119 "Key words for use in RFCs"
[RFC-6184]: https://tools.ietf.org/html/rfc6184 "RTP Payload Format for H.264 Video"
[IS-04]: https://specs.amwa.tv/is-04/ "AMWA IS-04 NMOS Discovery and Registration Specification"
[IS-05]: https://specs.amwa.tv/is-05/ "AMWA IS-05 NMOS Device Connection Management Specification"
[NMOS Parameter Registers]: https://specs.amwa.tv/nmos-parameter-registers/ "Common parameter values for AMWA NMOS Specifications"
[TR-??]: https://vsf.tv/download/technical_recommendations/VSF_TR-??_2022-04-20.pdf ""
[VSF]: https://vsf.tv/ "Video Services Forum"
[ST-2110-20]: https://ieeexplore.ieee.org/document/8167389 "ST 2110-20:2017 - SMPTE Standard - Professional Media Over Managed IP Networks: Uncompressed Active Video"
