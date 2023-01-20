# AMWA BCP-006-02: NMOS With H.264
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2021, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

H.264 is a technology standardized in Rec. [ITU-T H.264][H.264] | ISO/IEC 14496 for video contribution at high compression rate and video quality.
A companion RTP payload format specification was developed through the IETF Payloads working group, IETF [RFC 6184][RFC-6184].

- [ ] H.265 is a technology standardized in Rec. [ITU-T H.265][H.265] | ISO/IEC 23008 for video contribution at high compression rate and video quality.
A companion RTP payload format specification was developed through the IETF Payloads working group, IETF [RFC 7798][RFC-7798].

The [Video Services Forum][VSF] developed Technical Recommendation [TR-??][] and [TR-??][] of the IPMX suite of protocols, which cover the end-to-end application use of constant and variable bitrate compression for video, using the SMPTE ST 2110 and IPMX suite of protocols.
TR-?? and TR-?? mandate the use of the AMWA [IS-04][] and [IS-05][] NMOS Specifications in IPMX compliant systems.  

AMWA IS-04 and IS-05 already have support for RTP transport and can signal the media type `video/h264` as defined in RFC 6184.

- [ ] TODO: Current ST-2110 suite of protocols only support constant bit-rate streams. An new protocol for variable bit-rate is required. How should we refer to this upcoming variable bit-rate specification?
    - The ST-2110 definition of constant bit rate is not compatible with compressed video style H.264/H.265 "The video compression or the packetization of the video compression shall produce a constant number of bytes per frame. The packetization shall produce a constant number of RTP packets per frame." CBR for H.264/H.265 does not mean constant number of bits per frame but in a time period ... Unless using intra only compression.
- [ ] TODO: Should we reference the VSF's IPMX protocol here or limit to actual ST-2110 protocols which IPMX uses.
- [ ] TODO: References are made to the ITU-T H.264 specification instead of the ISO because it is more accessible (free).
- [ ] TODO: This specification shall be written without assuming ST-2110 transport ... It shall only be based on RFC 6184 and add "if ST-2110 is used then" ...
- [ ] TODO: Issue in the way format specifications are written: what if a Flow has not associated Sender but is a child of a MUX Flow which is associated with a Sender. The Sender is the one streaming the multiplexed Flows and cannot receive the properties of each H.264 Flows because there may be multiple such Flows and we get properties clashing at the Sender ....

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY",
and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][RFC-2119].

## Definitions

The NMOS terms 'Controller', 'Node', 'Source', 'Flow', 'Sender', 'Receiver' are used as defined in the [NMOS Glossary](https://specs.amwa.tv/nmos/main/docs/Glossary.html).

## H.264 IS-04 Sources, Flows and Senders

Nodes capable of transmitting H.264 video streams MUST have Source, Flow and Sender resources in the IS-04 Node API.

### Sources

The Source resource MUST indicate `urn:x-nmos:format:video` for the `format`.
Source resources can be associated with many Flows at the same time.
The Source is therefore unaffected by the use of H.264 compression.

### Flows

The Flow resource MUST indicate `video/h264` in the `media_type` attribute, and `urn:x-nmos:format:video` for the `format`.
This has been permitted since IS-04 v1.1. Flow resources can be associated with many Senders at the same time. The Flow is therefore unaffected by the use of a specific transport.

For Nodes implementing IS-04 v1.3 or higher, the following additional requirements on the Flow resource apply.

In addition to those attributes defined in IS-04 for all coded video Flows, the following attributes defined in the [Flow Attributes Register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/) of the [NMOS Parameter Registers][] are used for H.264.

These attributes provide information for Controllers and Users to evaluate stream compatibility between Senders and Receivers.

- [Components](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#components)  
  The Flow resource MUST indicate the color (sub-)sampling, width, height and depth of the associated uncompressed picture using the `components` attribute.
  The `components` array values MUST correspond to the stream's current active parameter set values. A Flow MUST track the stream's current active parameter set.
  
  Informative note: ST 2110-22 does not require the `sampling` or `depth` SDP parameters. RFC 6184 does not define any such SDP parameters. The `sampling`, `width`, `height` and `depth` of the associated uncompressed picture MUST be derived from the H.264 active parameter set by the Receiver. 
  
  If a stream complies with other specifications in addition to RFC 6184 about the parameters declared in the "fmtp=" attribute of an SDP transport file, those parameters MUST comply with the stream's active parameter set.
  
  Informative note: It implies that having SDP parameters describing aspects of the stream's active paramter set produces change of the SDP transport file content when the stream's active parameter set values change.
  
  Informative note: The information encoded in the `components` attribute should also be provided in the SDP transport file. What a Controller observes, a Receiver should also observe it. There is an exception with in-band parameter sets where the information is provided in-band for the Receiver, not in the SDP transport file. In such a case a Controller still has access to the `components` attribute. A Receiver would not have access to such information at `PATCH` request on the **/staged** endpoint but just-in time while decoding the bitstream active parameter set. 

- [Profile](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#profile)  
  The Flow resource MUST indicate the H.264 profile, which defines algorithmic features and limits that SHALL be supported by all decoders conforming to that profile. The Flow's `profile` attribute maps to the `profile-level-id` parameter of the SDP transport file which is OPTIONAL according to RFC 6184 and REQUIRED by this specification unless the `profile-level-id` parameter corresponds to the default value, in which case it MAY be omited.
  The permitted `profile` values are strings, defined as per ITU-T Rec. H.264 Annex A
  - "ConstrainedBaseline"
  - "Baseline" (Default if not specified in the SDP transport file) 
  - "Main"
  - "Extended"
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
  
    Informative note: The Flow's `profile` attribute is always required. It is the SDP transport file `profile-level-id` parameter that MAY be omited when matching the default value.
    
  - [ ] The Flow resource MUST indicate the H.265 profile, which defines algorithmic features and limits that SHALL be supported by all decoders conforming to that profile. The Flow's `profile` attribute maps to the `profile-id` parameter of the SDP transport file which is OPTIONAL according to RFC 7798 and REQUIRED by this specification unless the `profile-id` parameter corresponds to the default value, in which case it MAY be omited. The `profile-space` parameter of the SDP transprot file SHALL be omited or alwyas be 0.
  The permitted `profile` values are strings, defined as per ITU-T Rec. H.265 Annex A
  
  - "Main" (Default if not specified in the SDP transport file) 
  - "Main10" 
  - "Main10Still"
  - "MainStill"
  - "Monochrome" 
  - "Monochrome10"
  - "Monochrome12"
  - "Monochrome16" 
  - "Main12"
  - "Main10-422"
  - "Main12-422"
  - "Main444"
  - "Main10-444"
  - "Main12-444"
  - "MainIntra"
  - "Main10Intra"
  - "Main12Intra"
  - "Main10Intra-422"
  - "Main12Intra-422"
  - "MainIntra-444"
  - "Main10Intra-444"
  - "Main12Intra-444"
  - "Main16Intra-444"
  - "MainStill-444"
  - "Main16Still-444"
  - "HighThroughput-444"
  - "HighThroughput10-444"
  - "HighThroughput14-444"
  - "HighThroughput16Intra-444"
  - "ScreenExtendedMain"
  - "ScreenExtendedMain10"
  - "ScreenExtendedMain-444"
  - "ScreenExtendedMain10-444"
  - "ScreenExtendedHighThroughput-444"
  - "ScreenExtendedHighThroughput10-444"
  - "ScreenExtendedHighThroughput14-444"
  
  - [ ] Informative note: The Flow's `profile` attribute is always required. It is the SDP transport file `profile-id` parameter that MAY be omited when matching the default value.
  
- [Level](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#level)  
  The Flow resource MUST indicate the H.264 level, which defines a set of limits on the values that may be taken by the syntax elements of an H.264 bitstream. The Flow's `level` attribute map to the `profile-level-id` parameter of the SDP transport file which is OPTIONAL according to RFC 6184 and REQUIRED by this specification unless the `profile-level-id` parameter corresponds to the default value, in which case it MAY be omited.
  The permitted `level` values are strings, defined as per ITU-T Rec. H.264 Annex A
  - "1" (Default if not specified in the SDP transport file) 
  - "1b", "1.1", "1.2", "1.3"
  - "2", "2.1", "2.2"
  - "3", "3.1", "3.2"
  - "4", "4.1", "4.2"
  - "5", "5.1", "5.2"
  - "6", "6.1", "6.2" 
  
  Informative note: The Flow's `level` attribute is always required. It is the SDP transport file `profile-level-id` parameter that MAY be omited when matching the default value.
  
- [ ] The Flow resource MUST indicate the H.265 level, which defines a set of limits on the values that may be taken by the syntax elements of an H.265 bitstream. The Flow's `level` attribute map to the `level-id` and `tier-flag` parameters of the SDP transport file which is OPTIONAL according to RFC 7798 and REQUIRED by this specification unless the `level-id` and `tier-flag` parameters correspond to the default value, in which case they MAY be omited.
  The permitted `level` values are strings, defined as per ITU-T Rec. H.265 Annex A
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

  - [ ] Informative note: The Flow's `level` attribute is always required. It is the SDP transport file `level-id` and `tier-flag` parameters that MAY be omited when matching the default value.

- [Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#bit-rate)  
  The Flow resource MUST indicate the target encoding bit rate (kilobits/second) of the H.264 bitstream. It SHALL comply with the stream's active parameter set associated parameters.
  The `bit_rate` integer value is expressed in units of 1000 bits per second, rounding up.
  
  Informative note: The H.264 coded stream is not required to transport HRD parameters such that an H.264 decoder may not know the actual target bitrate of a stream. There are bit-rate limits imposed by the level of the coded bitstream. IS-11 may be used to constraint the Sender to a target bit-rate compatible with the Receiver Capabilities.
  
An example Flow resource is provided in the [Examples](../examples/).

### Senders

#### RTP based transport

For Nodes transmitting H.264 using the RTP payload mapping defined by RFC 6184, the Sender resource MUST indicate `urn:x-nmos:transport:rtp` or one of its subclassifications for the `transport` attribute. 

- [ ] For Nodes transmitting H.265 using the RTP payload mapping defined by RFC 7798, the Sender resource MUST indicate `urn:x-nmos:transport:rtp` or one of its subclassifications for the `transport` attribute. 

Sender resources provide no indication of media type or format, since this is described by the associated Flow resource.

The SDP file at the `manifest_href` MUST comply with the requirements of RFC 6184 in the [Usage in Declarative Session Descriptions](https://datatracker.ietf.org/doc/html/rfc6184#section-8.2.3) mode of operation. The SDP Offer/Answer Model described in RFC 6184 is not supported. The `fmtp` source attribute as specified in Section 6.3 of RFC 5576 (Source-Specific Media Attributes in the Session Description Protocol) is not supported.

Additionally, the SDP file needs to convey, so far as the defined parameters allow, the same information about the stream as conveyed by the Source, Flow and Sender attributes defined by this specification and IS-04.

Therefore:
  - The `profile-level-id` format-specific parameters MUST be included with the correct value unless it corresponds to the default value.
  - The `packetization-mode` format-specific parameters MUST be included with the correct value unless it corresponds to the default value.
  - The `sprop-interleaving-depth`, `sprop-deint-buf-req`, `sprop-init-buf-time` and `sprop-max-don-diff` format-specific parameters SHOULD be included with the correct value unless it corresponds to the default value if the `packetization-mode` equals one of the interleaved modes.
  - The `sprop-parameter-sets` MUST always be included if the Sender `parameter_sets_transport_mode` property is "out-of-band" or "in-and-out-of-band".

- [ ] The SDP file at the `manifest_href` MUST comply with the requirements of RFC 7798 in the [Usage in Declarative Session Descriptions](https://www.rfc-editor.org/rfc/rfc7798.html#section-7.2.3) mode of operation. The SDP Offer/Answer Model described in RFC 7798 is not supported. The `fmtp` source attribute as specified in Section 6.3 of RFC 5576 (Source-Specific Media Attributes in the Session Description Protocol) is not supported. The `tx-mode` parmeter of the SDP transport file SHALL always be set to SRST (Single RTP Stream Transport).

- [ ] Additionally, the SDP file needs to convey, so far as the defined parameters allow, the same information about the stream as conveyed by the Source, Flow and Sender attributes defined by this specification and IS-04.

Therefore:
  - The `profile-id`, `level-id` and `tier-flag` format-specific parameters MUST be included with the correct value unless it corresponds to the default value.
  - The `sprop-max-don-diff` format-specific parameters MUST be included with the correct value unless it corresponds to the default value.
  - `sprop-depack-buf-nalus`, `sprop-depack-buf-bytes` format-specific parameters SHOULD be included with the correct value unless it corresponds to the default value if `sprop-max-don-diff` is not 0
  - The`sprop-vps`, `sprop-sps` and `sprop-pps` MUST always be included if the Sender `parameter_sets_transport_mode` property is "out-of-band" or "in-and-out-of-band".

If the Sender meets the traffic shaping and delivery timing requirements specified for ST 2110-22, the SDP file MUST also comply with the provisions of ST 2110-22.

For Nodes implementing IS-04 v1.3 or higher, the following additional requirements on the Sender resource apply.

In addition to those attributes defined in IS-04 for Senders, the following attributes defined in the [Sender Attributes Register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/) of the NMOS Parameter Registers are used for H.264.

- [ ] In addition to those attributes defined in IS-04 for Senders, the following attributes defined in the [Sender Attributes Register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/) of the NMOS Parameter Registers are used for H.265.

- [Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#bit-rate)  
  The Sender resource SHOULD indicate the target bit rate (kilobits/second) including the transport overhead of the H.264 stream. If the Sender meets the traffic shaping and delivery timing requirements specified for ST 2110-22 it MUST indicate the transport bit-rate.
  The `bit_rate` integer value is expressed in units of 1000 bits per second, rounding up.
  The value is for the IP packets, so for the RTP payload format per RFC 6184, that includes the RTP, UDP and IP packet headers and the payload.  
  
  Informative note: This definition is consistent with the definition of the bit rate attribute required by ST 2110-22 in the SDP media description. There is no such SDP attribute in RFC 6184.
  
- [ ] MUST changed for SHOULD as RFC 6184 does not have a "b=" SDP attribute and future ST 2110-?? for VBR will likely not have a requried "b=" attribute. Transport of H.264 / H.265 streams should probably not be using ST 2110-22 in order to remove the requirement to have the "b=" attribute. For H.264 and H/265 coded streams there are elements of the bitstream describing the encoding bitrate properties. A receiver should from its knowledge of the transport derive an estimate of the transport bit-rate.
  
- [Packet Transmission Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#packet-transmission-mode)  
  If the Sender is using the non-interleaved or interleaved packetization modes, it MUST include the `packet_transmission_mode` attribute and set it to either `non_interleaved_nal_units` or `interleaved_nal_units`. The `packet_transmission_mode` attribute maps the the RFC 6184 `packetization-mode` parameter with `single_nal_unit` corresponding to value 0, `non_interleaved_nal_units` to value 1 and `interleaved_nal_units` to value 2. Since the default value of this attribute is `single_nal_unit`, the Sender MAY omit this attribute when using that mode. When the `packet-transmission-mode` attribute is included, the associated `packetization-mode` parameter of the SDP transport file MUST also be included.
  
  Parameters related to the `interleaved_nal_units` mode SHOULD be included in the SDP transport file unless their default value is used.

- [ ] If the Sender is using the interleaved mode, it MUST include the `packet_transmission_mode` attribute and set it to `interleaved_nal_units`. The `packet_transmission_mode` attribute maps the the RFC 7798 `sprop-max-don-diff` parameter with `non_interleaved_nal_units` corresponding to value 0, `interleaved_nal_units` to value greater than 0. Since the default value of this attribute is `non_interleaved_nal_units`, the Sender MAY omit this attribute when using that mode. When the `packet-transmission-mode` attribute is included, the associated `sprop-max-don-diff` parameter of the SDP transport file MUST also be included.

  Parameters related to the `interleaved_nal_units` mode SHOULD be included in the SDP transport file unless their default value is used.

- [ST 2110-21 Sender Type](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#st-2110-21-sender-type)  
  If the Sender complies with the traffic shaping and delivery timing requirements for ST 2110-22, it MUST include the `st2110_21_sender_type` attribute.
  - [ ] Add VBR case

- [Parameter Sets Flow Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#parameter-sets-flow-mode)
  - [ ] string enum possible values are: "static", "dynamic"
  - If a Sender operates in the static-Flow mode it MUST set the `parameter_sets_flow_mode` property to "static". Othersise it MAY omit or set the `parameter_sets_flow_mode` property to "dynamic". If unspecified the default value is "dynamic". See the "Parameter Sets" section for more details.

- [Parameter Sets Transport Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#parameter-sets-transport-mode)
  - [ ] string enum possible values are: "in-band", "out-of-band", "in-and-out-of-band"
  - If a Sender operates with out-of-band parameter sets it MUST set the `parameter_sets_transport_mode` property to either "out-of-band" or "in-and-out-of-band". Otherwise it MAY omit or set the `parameter_sets_transport_mode` property to "in-band". If unspecified the default value is "in-band". See the "Parameter Sets" section for more details.

An example Sender resource is provided in the [Examples](../examples/).

The H.264 encoder of a Sender MUST produces an H.264 bitstream that is compliant with the `profile-level-id` explicitely or implicitely declared in the stream's associated SDP transport file.

- [ ] The H.265 encoder of a Sender MUST produces an H.265 bitstream that is compliant with the `profile-id`, `level-id` and `tier-flag` explicitely or implicitely declared in the stream's associated SDP transport file.

- [ ] Should we have Sender Capabilities matching the Receiver Capabilities to indicate to a Controller what the Sender can do. The Sender can only be constrained by IS-11 but a Controller could at least know if a Sender is actually compatible with a Receiver.

#### Other transports

For Nodes transmitting H.264 using other transports, the Sender resource MUST indicate the associated `urn:x-nmos:transport:` label of the transport or one of its subclassifications for the `transport` attribute.

- [ ] For Nodes transmitting H.265 using other transports, the Sender resource MUST indicate the associated `urn:x-nmos:transport:` label of the transport or one of its subclassifications for the `transport` attribute. 

Sender resources provide no indication of media type or format, since this is described by the associated Flow resource.

The `manifest_href` attribute MAY be `null` if an SDP transport file is not supported by the transport. Otherwise the SDP transport file MUST comply the associated normative text of the "RTP based transport" section.

- [Parameter Sets Flow Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#parameter-sets-flow-mode)
  - [ ] string enum possible values are: "static", "dynamic"
  - If a Sender operates in the static-Flow mode it MUST set the `parameter_sets_flow_mode` property to "static". Othersise it MAY omit or set the `parameter_sets_flow_mode` property to "dynamic". If unspecified the default value is "dynamic". See the description of the modes at the "IS-04 Receivers" section.

- [Parameter Sets Transport Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#parameter-sets-transport-mode)
  - [ ] string enum possible values are: "in-band", "out-of-band", "in-and-out-of-band"
  - If a Sender operates with out-of-band parameter sets it MUST set the `parameter_sets_transport_mode` property to either "out-of-band" or "in-and-out-of-band". Otherwise it MAY omit or set the `parameter_sets_transport_mode` property to "in-band". If unspecified the default value is "in-band". See the description of the modes at the "IS-04 Receivers" section.

The H.264 encoder of a Sender MUST produces an H.264 bitstream that is compliant with the `profile-level-id` explicitely or implicitely declared in the stream's associated SDP transport file.

- [ ] The H.265 encoder of a Sender MUST produces an H.265 bitstream that is compliant with the `profile-id`, `level-id` and `tier-flag` explicitely or implicitely declared in the stream's associated SDP transport file.

#### Parent Flows

Flow resources can be associated with many Senders and many other Flows at the same time. A Flow resource MAY be the parent of many another Flow resources. A Sender associated with a Flow having some parents H.264 Flows MAY declare the `parameter_sets_flow_mode` and `parameter_sets_transport_mode` properties that apply for all the parents H.264 bistreams flowing through the Sender.

## H.264 IS-04 Receivers

Nodes capable of receiving H.264 video streams MUST have a Receiver resource in the IS-04 Node API, which lists `video/h264` in the `media_types` array within the `caps` object.

- [ ] Nodes capable of receiving H.265 video streams MUST have a Receiver resource in the IS-04 Node API, which lists `video/h265` in the `media_types` array within the `caps` object.

This has been permitted since IS-04 v1.1.

If the Receiver has limitations on or preferences regarding the H.264 video streams that it supports, the Receiver resource MUST indicate constraints in accordance with the [BCP-004-01][] Receiver Capabilities specification. The Receiver SHOULD express its constraints as precisely as possible, to allow a Controller to determine with a high level of confidence the Receiver's compatibility with the available streams. It is not always practical for the constraints to indicate every type of stream that a Receiver can or cannot consume successfully; however, they SHOULD describe most common operating points and any preferences among them.

- [ ] If the Receiver has limitations on or preferences regarding the H.265 video streams that it supports, the Receiver resource MUST indicate constraints in accordance with the [BCP-004-01][] Receiver Capabilities specification. The Receiver SHOULD express its constraints as precisely as possible, to allow a Controller to determine with a high level of confidence the Receiver's compatibility with the available streams. It is not always practical for the constraints to indicate every type of stream that a Receiver can or cannot consume successfully; however, they SHOULD describe most common operating points and any preferences among them.

The `constraint_sets` parameter within the `caps` object can be used to describe combinations of frame rates, width and height, and other parameters which the receiver can support, using the parameter constraints defined in the [Capabilities register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/) of the NMOS Parameter Registers.

The following parameter constraints can be used to express limits or preferences specifically defined by Rec. ITU-T H.264 and RFC 6184 for H.264 decoders:

- [Profile](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#profile)
  - Some H.264 profiles are superset of other profiles. The H.264 specification describe the relationship among the profiles. From the point of view of the H.264 specification, supporting such superset profile is required to also be supporting the associated subset profiles. To assist a Controller not having knowledge of the H.264 profiles relationship, the Receiver Capabilities SHOULD enumerate all the subset profiles in addition to the superset profile.

  - [ ] Some H.265 profiles are superset of other profiles. The H.265 specification describe the relationship among the profiles. From the point of view of the H.265 specification, supporting such superset profile is required to also be supporting the associated subset profiles. To assist a Controller not having knowledge of the H.265 profiles relationship, the Receiver Capabilities SHOULD enumerate all the subset profiles in addition to the superset profile.

- [Level](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#level)
  - Some H.264 levels are superset of other profiles. The H.264 specification describe the relationship among the levels. From the point of view of the H.264 specification, supporting such superset level is required to also be supporting the associated subset levels. To assist a Controller not having knowledge of the H.264 levels relationship, the Receiver Capabilities SHOULD enumerate all the subset levels in addition to the superset level.
 
  - [ ] Some H.265 levels are superset of other profiles. The H.265 specification describe the relationship among the levels. From the point of view of the H.265 specification, supporting such superset level is required to also be supporting the associated subset levels. To assist a Controller not having knowledge of the H.265 levels relationship, the Receiver Capabilities SHOULD enumerate all the subset levels in addition to the superset level.

- [Packet Transmission Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#packet-transmission-mode)
  - [ ] Target the `packet_transmission_mode` property of the Sender.

- [Parameter Sets Flow Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#parameter-sets-flow-mode)

  - [ ] string enum possible values are: "static", "dynamic"
  - [ ] Target the `parameter_sets_flow_mode` of the Sender.

  - A Receiver declares the `parameter_sets_flow_mode` capability to indicate that it supports bitstreams using one and/or multiple active parameter sets. As each active parameter set is associated to a specific Flow, this capability indicates that a Receiver is capable of decoding an H.264 bitstream where the associated Flow changes dynamically. All the parameter sets comply with the `profile-level-id` parameter of the stream's associated SDP transport file. The parameter sets MAY be specified out-of-band using the `sprop-parameter-sets` attribute of an SDP transport file, in-band through the H.264 bitstream or in-and-out-of-band usign both mechanisms. See the "Parameter Sets" section for more details.
 
  - [ ] A Receiver declares the `parameter_sets_flow_mode` capability to indicate that it supports bitstreams using one and/or multiple active parameter sets. As each active parameter set is associated to a specific Flow, this capability indicates that a Receiver is capable of decoding an H.265 bitstream where the associated Flow changes dynamically. All the parameter sets comply with the `profile-id`, `level-id` and `tier-flag` parameters of the stream's associated SDP transport file. The parameter sets MAY be specified out-of-band using the `sprop-vps`, `sprop-sps` and `sprop-pps` attributes of an SDP transport file, in-band through the H.265 bitstream or in-and-out-of-band usign both mechanisms.See the "Parameter Sets" section for more details.

  - A Receiver supporting the "dynamic" mode MUST also support the "static" mode. Such Receiver MUST have both "static" and "dynamic" values enumerated in the Receiver Capability in order to allow Senders operating in any parameter_sets_flow_mode.

- [ ] TODO: describe that a Sender always has the ability to switch the active parameter set independently of this capability (unless constrained by IS-11). A Receiver not supporting the dynamic-Flows capability would fail a PATCH if more than one parameter sets is provided in the SDP transport file. It would become inactive if the active parameter set change during decoding.

- [Parameter Sets Transport Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#parameter-sets-transport-mode)

  - [ ] string enum possible values are: "in-band", "out-of-band", "in-and-out-of-band"
  - [ ] Target the `parameter_sets_transport_mode` of the Sender.

  - A Receiver declares the `parameter_sets_transport_mode` capability to indicate that it supports bitstreams producing in-band parameter sets that update or augment the initial out-of-band parameter sets. If declaring "in-band" or "in-and-out-of-band" capabilities a Receiver SHALL be capable of decoding in-band parameter sets and behave according to its capabilities if those are not duplicate of the original out-of-band parameter sets.

 - A Receiver supporting "in-and-out-of-band" MUST also support "in-band" and "out-of-band" and SHOULD have all values "in-band", "out-of-band" and "in-and-out-of-band" in the enum Receiver Capability in order to allow Senders operating in any parameter_sets_transport_mode.

- [ ] TODO: describe that a Sender always has the ability to send in-band parameter sets independently of this capability (unless constrained by IS-11). A Receiver not supporting the in-band capability would become inactive if in-band parameter sets do not match the out-of-band ones.

- [SAR Supported](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#sar-supported)
  - A Receiver supporting `aspect_ratio_idc` values that are defined as `Reserved` in Table E-1 of the [H.264][] specification indicate, such the maximum value of `aspect_ratio_idc` using this Receiver Capability. The `sar_supported` capability is an integer indicating the maximum value of `aspect_ratio_idc` smaller than 255 that the Receiver understands and support (`sar-understood` SHOULD be assumed to match `sar_supported`, both being defined in RFC 6184). The value of this parameter is an integer in the range of 1 to `sar-understood`, inclusive, equal to 255. The value of `sar_supported` equal to N smaller than 255 indicates that the receiver supports all the SARs corresponding to H.264 `aspect_ratio_idc` values (see Table E-1 of [H.264][]) in the range from 1 to N, inclusive, without geometric distortion.  The value of `sar-supported` equal to 255 indicates that the receiver supports all sample aspect ratios that are expressible using two 16-bit integer values as the numerator and denominator, i.e., those that are expressible using the H.264 `aspect_ratio_idc` value of 255 (Extended_SAR, see Table E-1 of [H.264][]), without geometric distortion.

  - [ ] A Receiver supporting `aspect_ratio_idc` values that are defined as `Reserved` in Table E-1 of the [H.265][] specification indicate, such the maximum value of `aspect_ratio_idc` using this Receiver Capability. The `sar_supported` attribute is an integer indicating the maximum value of `aspect_ratio_idc` smaller than 255 that the Receiver understands and support (`sar-understood` SHOULD be assumed to match `sar_supported`, both being defined in RFC 6184 and such definitions reused for H.265 as they are not part of RFC 7798). The value of this parameter is an integer in the range of 1 to `sar-understood`, inclusive, equal to 255. The value of `sar_supported` equal to N smaller than 255 indicates that the receiver supports all the SARs corresponding to H.264 `aspect_ratio_idc` values (see Table E-1 of [H.265][]) in the range from 1 to N, inclusive, without geometric distortion.  The value of `sar_supported` equal to 255 indicates that the receiver supports all sample aspect ratios that are expressible using two 16-bit integer values as the numerator and denominator, i.e., those that are expressible using the H.264 `aspect_ratio_idc` value of 255 (Extended_SAR, see Table E-1 of [H.265][]), without geometric distortion.

- [ ] TODO: Have a Sender property to indicate actual SAR which is based on Video Usability Information (VUI) parameter sets. If send in-band then a Controller has no visibility. Another possibility is to indicate that this constraint targets the IS-11 API where a Controller can constraint a Sender without knowing the default configuration of the Sender (wihtout IS-11 constraint).

When the H.264 decoder has no restrictions of profiles or levels, the Receiver can indicate that the parameter is unconstrained, as described in BCP-004-01. Otherwise a Receiver can indicate the supported profiles and levels as enumerated string constraints. When a profile/level is defined as a superset of other profiles/level, the subset profiles/levels SHOULD be enumerated.

- [ ] When the H.265 decoder has no restrictions of profiles or levels, the Receiver can indicate that the parameter is unconstrained, as described in BCP-004-01. Otherwise a Receiver can indicate the supported profiles and levels as enumerated string constraints. When a profile/level is defined as a superset of other profiles/level, the subset profiles/levels SHOULD be enumerated.

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

A Receiver MUST be able to decode a bitstream conforming to the profiles and levels declared in the Receiver Capabilities. A Receiver MAY have preferences and more optimal profiles and levels that MAY be declared through Receiver Capabilities. A preferred constraint set MAY indicate such preferences while another constraint set MAY indicate full support of some profiles and levels. A Receiver MAY further constraint the support of a coded bitstream compliant with a profile and level using other constraints in its Receiver Capabilities.

#### Parent Flows

Flow resources can be associated with many Senders and many other Flows at the same time. A Flow resource MAY be the parent of many another Flow resources. A Receiver consuming a stream whose Sender is associated with a Flow having some parents H.264 Flows MAY declare constraints that apply for all the parents H.264 bistreams flowing through the Sender.

#### RTP based transport

For Nodes consuming H.264 using the RTP payload mapping defined by RFC 6184, the Receiver resource MUST indicate `urn:x-nmos:transport:rtp` or one of its subclassifications for the `transport` attribute. 

- [ ] For Nodes consuming H.265 using the RTP payload mapping defined by RFC 7798, the Receiver resource MUST indicate `urn:x-nmos:transport:rtp` or one of its subclassifications for the `transport` attribute. 

#### Other transport

The Receiver resource MUST indicate the associated `urn:x-nmos:transport:` label of the transport or one of its subclassifications for the `transport` attribute. 

## H.264 IS-05 Senders and Receivers

### RTP transport

Connection Management using IS-05 proceeds in exactly the same manner as for any other stream format carried within RTP.

The SDP file at the **/transportfile** endpoint on an IS-05 Sender MUST comply with the same requirements described for the SDP file at the IS-04 Sender `manifest_href`.

A `PATCH` request on the **/staged** endpoint of an IS-05 Receiver can contain an SDP file in the `transport_file` attribute. The SDP file for a H.264 stream is expected to comply with RFC 6184 and, if appropriate, ST 2110-22. It need not comply with the additional requirements specified for SDP files at Senders.

- [ ] A `PATCH` request on the **/staged** endpoint of an IS-05 Receiver can contain an SDP file in the `transport_file` attribute. The SDP file for a H.265 stream is expected to comply with RFC 7798 and, if appropriate, ST 2110-22. It need not comply with the additional requirements specified for SDP files at Senders. 

If the Receiver is not capable of consuming the stream described by the SDP file, it SHOULD reject the request. If it is unable to assess the stream compatibility because some optional parameters are not included in the SDP transport file, it MAY accept the request and postpone stream compatibility assessment.

An example SDP file is provided in the [Examples](../examples/).

### Other transports

Connection Management using IS-05 proceeds in exactly the same manner as for any other stream format carried by transport other than RTP.

If an SDP transport file is supported by teh transport, the SDP file at the **/transportfile** endpoint on an IS-05 Sender MUST comply with the same requirements described for the SDP file at the IS-04 Sender `manifest_href`.

### Parameter Sets

- [ ] TODO: Adapt for H.265 changing the references to specifications and SDP attributes.

The active parameter set of an H.264 stream is made of the active sequence and picture parameter sets. The parameter sets of an H.264 stream are are transmitted by a Sender to Receivers either out-of-band through an SDP transport file or in-band through the coded stream. The active parameter set of an H.264 coded stream MUST reference valid out-of-band or in-band parameter sets, the stream is invalid otherwise.

The `sprop-parameter-sets` parameter of an SDP transport file MAY contain a collection of out-of-band parameter sets. Those parameter sets provide initial parameter sets for the H.264 stream before the decoding starts.

An H.264 stream MAY transport in-band parameter sets to update parameter sets received out-of-band or to define addtional parameter sets. The `parameter_sets_transport_mode` Receiver Capability indicates when set to "in-band" or "in-and-out-of-band" that a Receiver supports getting updated or additional parameter sets in-band. When set to "out-of-band" a Receiver support only getting initial parameter sets from the SDP transport file.

The current active parameter set of an H.264 stream MUST comply with the current Flow associated with the Sender that produce the coded stream and the related parameters of the `fmtp=` attribute of the Sender's SDP transport file.

The current active parameter set of an H.264 stream MUST comply with the `profile-level-id` declared in the Sender's SDP transport file and the `profile` and `level` of the associated Flow.

A Receiver MUST verify that the current active parameter set comply with the Receiver's Capabilities. If a Receiver support only out-of-band parameter sets it SHOULD perform the verification when a Controller PATCH the **/staged** endpoint for activation. In this situation, all the out-of-band parameter sets MUST be compliant with the Receiver Capabilities. Otherwise if a Receiver supports both out-of-band and in-band parameter sets it SHOULD perform the verification of the out-of-band parameter sets when a Controller PATCH the **/staged** endpoint for activation and it MUST perform the verification of the in-band parameter sets just-in-time as it decodes the stream. In this situation, all the out-of-band and in-band parameter sets MUST be compliant with the Receiver Capabilities.

The `parameter_sets_flow_mode` Receiver Capability indicates when set to "dynamic" that a Receiver supports decoding an H.264 stream where the active parameter set changes dynamically. 

A Receiver with this capability set to "static" requires that a coded stream uses at most one active parameter set. Such active parameter set MAY be obtained out-of-band or in-band if the Receiver supports the `parameter_sets_transport_mode` Capability. When obtained out-of-band the `sprop-parameter-sets` parameter of an SDP transport file MUST contain only the active parameter set. When obtained in-band the `sprop-parameter-sets` parameter of an SDP transport file MUST be empty or omited and the Sender transmits the active parameter set in-band. At all time, the Sender is allowed to transmit in-band parameter sets that are duplicates of the initial active parameter set obtained either out-of-band or in-band. Out-of-band parameter sets have priority over in-band paraemeters sets, so receiving the active parameter set out-of-band from the `sprop-parameter-sets` parameter does not allow further receiving in-band parameter sets that are not duplicates. 

The "one active parameter set" rule allows a set of PPS to be used by the coded bitstream along with a single SPS. The relaxed rule allows for using various scaling-lists during the encoding.

- [ ] The "one active parameter set" rule allows a set of PPS to be used by the coded bitstream along with a single SPS and VPS. The relaxed rule allows for using various scaling-lists during the encoding.

A Receiver with this capability set to "dynamic" supports that a coded stream uses multiple active parameter sets. Such parameter sets MAY be obtained out-of-band and/or in-band according to the Receiver `parameter_sets_transport_mode` Capability. When obtained out-of-band the `sprop-parameter-sets` parameter of an SDP transport file MAY contain multiple initial parameter sets. When obtained in-band the Sender is allowed to transmit multiple in-band parameter sets to update parameter sets initially received out-of-band or to add and update new ones.

A Sender is allowed, unless constrained by IS-11, to produces H.264 coded streams that comply with the `profile-level-id` declared in the Sender's SDP transport file and the `profile` and `level` of the associated Flow. As such a Sender MAY use one or multiple active parameter sets as per the [H.264] specification. A Sender MAY seamlessly change dynamically the coded stream's active parameter set, provided that the Flow associated with the Sender changes accordingly and the content of the SDP transport file does not change. If the content of the SDP transport file changes, the Sender SHALL comply with IS-04, IS-05. A Sender indicates its mode of operation with the `parameter_sets_flow_mode` and `parameter_sets_transport_mode` attributes.

- [ ] Note to ST 2110-22: An H.264 stream will not be able to fully benefit from the seamless transitions of parameter sets because of the attributes width, height and exactframerate that are part of the SDP transport file are not allowed to change. 
- [ ] Note to IPMX: An IPMX H.264 stream will not be able to benefit from the seamless transitions of parameter sets because of the attributes measuredpixclk, htotal and vtotal that are part of the SDP transport file are not allowed to change and cannot be inferred from the parameter sets. 

## Controllers

Controllers MUST support the BCP-004-01 Receiver Capabilities mechanism and all the parameter constraints listed in this specification in order to evaluate the stream compatibility between H.264 Senders and Receivers.

- [ ] Controllers MUST support the BCP-004-01 Receiver Capabilities mechanism and all the parameter constraints listed in this specification in order to evaluate the stream compatibility between H.265 Senders and Receivers.

[BCP-004-01]: https://specs.amwa.tv/bcp-004-01/ "AMWA BCP-004-01 NMOS Receiver Capabilities"
[H.264]: https://www.itu.int/rec/T-REC-H.264/ " Advanced video coding for generic audiovisual services"
[H.265]: https://www.itu.int/rec/T-REC-H.265/ " High efficiency video coding"
[RFC-2119]: https://tools.ietf.org/html/rfc2119 "Key words for use in RFCs"
[RFC-6184]: https://tools.ietf.org/html/rfc6184 "RTP Payload Format for H.264 Video"
[RFC-7798]: https://tools.ietf.org/html/rfc7798 "RTP Payload Format for High Efficiency Video Coding (HEVC)"
[IS-04]: https://specs.amwa.tv/is-04/ "AMWA IS-04 NMOS Discovery and Registration Specification"
[IS-05]: https://specs.amwa.tv/is-05/ "AMWA IS-05 NMOS Device Connection Management Specification"
[NMOS Parameter Registers]: https://specs.amwa.tv/nmos-parameter-registers/ "Common parameter values for AMWA NMOS Specifications"
[TR-??]: https://vsf.tv/download/technical_recommendations/VSF_TR-??_2022-04-20.pdf ""
[VSF]: https://vsf.tv/ "Video Services Forum"
[ST-2110-20]: https://ieeexplore.ieee.org/document/8167389 "ST 2110-20:2017 - SMPTE Standard - Professional Media Over Managed IP Networks: Uncompressed Active Video"
