# AMWA BCP-006-02: NMOS With H.264
{:.no_toc}

* A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

_(c) AMWA 2021, CC Attribution-NoDerivatives 4.0 International (CC BY-ND 4.0)_

Advanced Audio Coding (AAC) is a technology standardized in [AAC][AAC] | ISO/IEC 14496-3 for audio contribution at high compression rate and audio quality.
A companion RTP payload format specification was developed through the IETF Payloads working group, IETF [RFC 3640][RFC-3640]. Another RTP payload format specification IETF [RFC 6416][RFC-6416] also exist and may be used as an alternate method for transporting the audio stream payload in RTP.

> RFC 3640 only support passing configuration information out-of-band while RFC 6416 support both in-band and out-of-band methods. When targetting MPEG2-TS transport the configuration information is provided in-band as per the H.222 specification.

> RFC 6416 is used in declarative mode as per RFC 6116 section 7.4.1 "Declarative SDP Usage for MPEG-4 Audio".

The [Video Services Forum][VSF] developed Technical Recommendation [TR-??][] and [TR-??][] of the IPMX suite of protocols, which cover the end-to-end application use of constant and variable bitrate compression for audio, using the SMPTE ST 2110 and IPMX suite of protocols.
TR-?? and TR-?? mandate the use of the AMWA [IS-04][] and [IS-05][] NMOS Specifications in IPMX compliant systems.  

AMWA IS-04 and IS-05 already have support for RTP transport and can signal the media type `audio/mpeg4-generic` as defined in RFC 3640 or `audio/MP4A-LATM` as defined in RFC 6416. For MPEG2-TS transport the AAC codec is signaled as `audio/MP4A-ADTS`.

> MPEG4 audio is a synonym for Advanced Audio Coding, corresponding to the AAC codec. The media type `audio/mpeg4-generic` indicates MPEG4 audio, hence the AAC codec. The media type `audio/MP4A-LATM` indicates MPEG4 audio framed using the LATM multiplexor to transport configuration information along with the audio stream. It indicate the AAC codec. The media type `audio/MP4A-ADTS` indicates MPEG4 audio framed using the ADTS multiplexor to transport configuration information along with the audio stream. Once again it indicate the AAC codec.

- [ ] TODO: The MP4A-ADTS is not registered to IANA but as it does not appear in any SDP transport file it may not be required.

- [ ] TODO: Issue in the way format specifications are written: what if a Flow has not associated Sender but is a child of a MUX Flow which is associated with a Sender. The Sender is the one streaming the multiplexed Flows and cannot receive the properties of each AAC Flows because there may be multiple such Flows and we get properties clashing at the Sender ....

## Use of Normative Language

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY",
and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119][RFC-2119].

## Definitions

The NMOS terms 'Controller', 'Node', 'Source', 'Flow', 'Sender', 'Receiver' are used as defined in the [NMOS Glossary](https://specs.amwa.tv/nmos/main/docs/Glossary.html).

## AAC IS-04 Sources, Flows and Senders

Nodes capable of transmitting AAC audio streams MUST have Source, Flow and Sender resources in the IS-04 Node API.

### Sources

The Source resource MUST indicate `urn:x-nmos:format:audio` for the `format`.
Source resources can be associated with many Flows at the same time.
The Source is therefore unaffected by the use of AAC compression.

> IS-08 MAY be used to re-arrange the audio channels of the Source feeding an AAC coded Flow.

### Flows

The Flow resource MUST indicate `audio/mpeg4-generic` or `audio/MP4A-LATM` or `audio/MP4A-ADTS` in the `media_type` attribute, and `urn:x-nmos:format:audio` for the `format`.
This has been permitted since IS-04 v1.1. Flow resources can be associated with many Senders at the same time. The Flow is therefore unaffected by the use of a specific transport.

For Nodes implementing IS-04 v1.3 or higher, the following additional requirements on the Flow resource apply.

In addition to those attributes defined in IS-04 for all coded audio Flows, the following attributes defined in the [Flow Attributes Register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/) of the [NMOS Parameter Registers][] are used for AAC.

These attributes provide information for Controllers and Users to evaluate stream compatibility between Senders and Receivers.

- [Profile](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#profile)  
  The Flow resource MUST indicate the AAC profile, which defines algorithmic features and limits that SHALL be supported by all decoders conforming to that profile. The Flow's `profile` attribute maps to the `profile-level-id` parameter of the SDP transport file which is REQUIRED according to RFC 3640, OPTIONAL according to RFC 6416 and REQUIRED by this specification in both cases. There is no default value defied by RFC 3640 or RFC 6416.

  The permitted `profile` values are strings, defined as per ISO ISO/IEC 14496-3
  - "Speech"
  - "Synthetic"
  - "Scalable"
  - "Main"
  - "HighQuality"
  - "LowDelay"
  - "Natural"
  - "Mobile"
  - "AAC"
  - "HighEfficiencyAAC"
  - "HighEfficiencyAACv2"
  - "LowDelayAAC"
  - "LowDelayAACv2"
  - "ExtendedHighEfficiencyAAC"
  
  Informative note: The value of `profile-level-id` corresponds to the member MPEG-4_audio_profile_and_level of the MPEG-4_audio_descriptor of an MPEG2-TS transport stream.
 
- [Level](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#level)  
  The Flow resource MUST indicate the AAC level, which defines a set of limits on the values that may be taken by the syntax elements of an AAC bitstream. The Flow's `level` attribute map to the `profile-level-id` parameter of the SDP transport file which is REQUIRED according to RFC 3640, OPTIONAL according to RFC 6416 and REQUIRED  and by this specification.

  The permitted `level` values are strings, defined as per ISO ISO/IEC 14496-3
  - "1"
  - "2"
  - "3"
  - "4"
  - "5"
  - "6"
  - "7"
  - "8"
  
  Informative note: The value of `profile-level-id` corresponds to the member MPEG-4_audio_profile_and_level of the MPEG-4_audio_descriptor of an MPEG2-TS transport stream.

- [Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#bit-rate)  
  The Flow resource MUST indicate the target encoding bit rate (kilobits/second) of the AAC bitstream. The Flow's `bit_rate` attribute map to the `bitrate` parameter of the SDP transport file which is OPTIONAL according to RFC 6416, not specified by RFC 3640 and REQUIRED and by this specification.

  The `bit_rate` integer value is expressed in units of 1000 bits per second, rounding up.

- [Constant Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/flow-attributes/#constant-bit-rate)
  The Flow resource MUST indicate if it operates une constant bit rate (CBR) mode or variable bit rate mode (VBR or other). When operating in CBR mode the `bit_rate` corresponds to the target constant encoding bit rate. Otherwise it corresponds to the maximum encoding bit rate. Since the default value of this attribute is `false`, the Flow MAY omit this attribute when using a variable bitrate mode.

  Informative note: The maximum bit rate information relates to the codec profile / level limits and the Bit Reservoir buffering model. It corredponds to the maximum bitrate in any time window of one second duration as per ISO/IEC 14496-1. The CBR versus VBR mode of operation of the encoder provide essential clues about the coded bitstream produced.

  > !!! TODO: The target bitrate is propably more specifically the maximum bitrate that is used to calculate the buffering model betweenthe encoder and the decoder, aka bit reservoir, etc ... In variable bitrate mdoes, this would more precisely describe the stream. For constnt bitrate it is the target, for variable bitrate it is the maximum.
  > AAC specification indicates: 4.5.3.3 Maximum bit rate: The maximum bitrate depends on the audio sampling rate. It can be calculated based on the minimum input buffer size according to the formula: *** Table 4.126 – Maximum bitrate depending on the sampling frequency sampling_frequency maximum bitrate / NCC
48 kHz => 288 kbit/s,
44.1 kHz => 264.6 kbit/s,
32 kHz => 192 kbit/s


  Informative note: The RFC-3640 standard does not define a bitrate SDP transport file parameter. RFC 6416 defines an OPTIONAL bitrate parameter in bits per seconds.
  RFC-3890 propose an SDP bitrate attribute that does not include transport overhead for media type mpeg4-generic. EBU TECH 3326 require specifying the coding bitrate using the TIAS bandwidth modifier of RFC-3890 and recommend adding a `bitrate` parameter in bits per second to the fmtp attribute. As RFC 3640 indicates "Applications MAY use more parameters, in addition to those defined above." it seems acceptable to include the bitrate parameter to the SDP transport file for both RFC 3640 and RFC 6416 in a uniform way in bits per seconds (Flow property remain in Kbps units).
  
An example Flow resource is provided in the [Examples](../examples/).

### Senders

#### RTP based transport

For Nodes transmitting AAC using the RTP payload mapping defined by RFC 3640 or RFC 6416, the Sender resource MUST indicate `urn:x-nmos:transport:rtp` or one of its subclassifications for the `transport` attribute. 

Sender resources provide no indication of media type or format, since this is described by the associated Flow resource.

The SDP file at the `manifest_href` MUST comply with the requirements of RFC 3640 or RFC 6416.

Additionally, the SDP file needs to convey, so far as the defined parameters allow, the same information about the stream as conveyed by the Source, Flow and Sender attributes defined by this specification and IS-04.

Therefore:
  - The `profile-level-id` format-specific parameter MUST be included with the correct value.
  - [ ] TODO: check what can be done. Is this a maximum bitrate such that the sender could use any value lower than this maximum. It is expected that flexibility in changing the encoding bitrate is required. If static in the SDP file this may cause a problem.
  - The `config` format-specific parameter MUST MUST always be included if the Sender `parameter_sets_transport_mode` property is `out_of_band` or `in_and_out_of_band`. The hexadecimal value of the "config" parameter is the AudioSpecificConfig(), as defined in ISO/IEC 14496-3. Ex config=AB8902. To explicitely indicate that `parameter_sets_transport_mode` property is `in_band` the value "" MUST be used with RFC 3640. Ex config=""
  - The `mode` format-specific parameter MUST be included with the correct value for RFC 3640. Actually only the `AAC-hbr` mode is supported.
  - The `streamType` format-specific parameter MUST be included with the value 5 for RFC 3640. Actually only the `audio` stream type is supported.
  - The `maxDisplacement`, `constantDuration` and `de-interleaveBufferSize` format-specific parameters SHOULD be included with the correct value for RFC 3640 if the `packetization-mode` equals one of the interleaved modes.
  - The `sizeLength`, `indexLength` and `indexDeltaLength` MUST always be included for RFC 3640 as only mode AAC-hbr is supported. Expected values for AAC-hbr are `sizeLength` = 13, `indexLength` = 3 and `indexDeltaLength` = 3.

Informative note: The Flow bit_rate information is assumed to be conveyed according to the rules of the standards being implemented by the device. In some scenarios the encoding is not declared out-of-band through the SDP transport file but in-band through the coded stream.

For Nodes implementing IS-04 v1.3 or higher, the following additional requirements on the Sender resource apply.

In addition to those attributes defined in IS-04 for Senders, the following attributes defined in the [Sender Attributes Register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/) of the NMOS Parameter Registers are used for AAC.

- [Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#bit-rate)  
  The Sender resource SHOULD indicate the target bit rate (kilobits/second) including the transport overhead of the AAC stream. 
  The `bit_rate` integer value is expressed in units of 1000 bits per second, rounding up.
  The value is for the IP packets, so for the RTP payload format per RFC 3640 or RFC 6416, that includes the RTP, UDP and IP packet headers and the payload.  
  
- [Packet Transmission Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/sender-attributes/#packet-transmission-mode)  
  The Sender MUST include the `packet_transmission_mode` attribute and set it to either `non_interleaved_access_units` or `interleaved_access_units`. The parameters of the SDP transport file for RFC 3640 MUST describe the use of the interleaved mode and provide all the necessary information required by the decoder to reconstrauct the audio stream.
  Informative note: The interleaved mode is not supported by RFC 6416.
  
- [Parameter Sets Flow Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#parameter-sets-flow-mode)
  - [ ] string enum possible values are: "strict", "static", "dynamic"
  - If a Sender operates in the strict-Flow mode it MUST set the `parameter_sets_flow_mode` property to `strict`. Otherwise if it operates in the static-Flow mode it MUST set the `parameter_sets_flow_mode` property to `static`. Otherwise it MAY omit or set the `parameter_sets_flow_mode` property to `dynamic`. If unspecified the default value is `dynamic`. See the "Parameter Sets" section for more details.

- [Parameter Sets Transport Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#parameter-sets-transport-mode)
  - [ ] string enum possible values are: "in_band", "out_of_band", "in_and_out_of_band"
  - If a Sender operates with out-of-band parameter sets it MUST set the `parameter_sets_transport_mode` property to either `out_of_band` or `in_and_out_of_band`. Otherwise it MAY omit or set the `parameter_sets_transport_mode` property to `in_band`. If unspecified the default value is `in_band`. See the "Parameter Sets" section for more details.

  RFC 3640 does not support the `in_band` mode such that `parameter_sets_transport_mode` MUST be set to `out_of_band`.

  > From ISO/IEC 14496-3 "The header streams are transported via MPEG-4 systems. These streams contain configuration information, which is necessary for the decoding process and parsing of the raw data streams. However, an update is only necessary if there are changes in the configuration." AudioSpecificConfig is part of the header streams.

An example Sender resource is provided in the [Examples](../examples/).

The AAC encoder of a Sender MUST produces an AAC bitstream that is compliant with the `profile-level-id` explicitely declared in the stream's associated SDP transport file or the MPEG-4_audio_profile_and_level of the MPEG-4_audio_descriptor of an MPEG2-TS transport stream.

- [ ] Should we have Sender Capabilities matching the Receiver Capabilities to indicate to a Controller what the Sender can do. The Sender can only be constrained by IS-11 but a Controller could at least know if a Sender is actually compatible with a Receiver.

#### Other transports

For Nodes transmitting AAC using other transports, the Sender resource MUST indicate the associated `urn:x-nmos:transport:` label of the transport or one of its subclassifications for the `transport` attribute.

Sender resources provide no indication of media type or format, since this is described by the associated Flow resource.

The `manifest_href` attribute MAY be `null` if an SDP transport file is not supported by the transport. Otherwise the SDP transport file MUST comply the associated normative text of the "RTP based transport" section.

- [Parameter Sets Flow Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#parameter-sets-flow-mode)
  - [ ] string enum possible values are: "strict", "static", "dynamic"
  - If a Sender operates in the strict-Flow mode it MUST set the `parameter_sets_flow_mode` property to `strict`. Otherwise if it operates in the static-Flow mode it MUST set the `parameter_sets_flow_mode` property to `static`. Otherwise it MAY omit or set the `parameter_sets_flow_mode` property to `dynamic`. If unspecified the default value is `dynamic`. See the "Parameter Sets" section for more details.

- [Parameter Sets Transport Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#parameter-sets-transport-mode)
  - [ ] string enum possible values are: "in_band", "out_of_band", "in_and_out_of_band"
  - If a Sender operates with out-of-band parameter sets it MUST set the `parameter_sets_transport_mode` property to either `out_of_band` or `in_and_out_of_band`. Otherwise it MAY omit or set the `parameter_sets_transport_mode` property to `in_band`. If unspecified the default value is `in_band`. See the description of the modes at the "IS-04 Receivers" section.

The AAC encoder of a Sender MUST produces an AAC bitstream that is compliant with the `profile-level-id` explicitely declared in the stream's associated SDP transport file or the MPEG-4_audio_profile_and_level of the MPEG-4_audio_descriptor of an MPEG2-TS transport stream.

#### Parent Flows

Flow resources can be associated with many Senders and many other Flows at the same time. A Flow resource MAY be the parent of many another Flow resources. A Sender associated with a Flow having some AAC parents Flows MAY declare the `parameter_sets_flow_mode` and `parameter_sets_transport_mode` properties that apply for all the parents AAC bistreams flowing through the Sender.

## AAC IS-04 Receivers

Nodes capable of receiving AAC audio streams MUST have a Receiver resource in the IS-04 Node API, which lists `audio/mpeg4-generic`, `audio/MP4A-LATM` or `audio/MP4A-ADTS`in the `media_types` array within the `caps` object.

This has been permitted since IS-04 v1.1.

If the Receiver has limitations on or preferences regarding the AAC audio streams that it supports, the Receiver resource MUST indicate constraints in accordance with the [BCP-004-01][] Receiver Capabilities specification. The Receiver SHOULD express its constraints as precisely as possible, to allow a Controller to determine with a high level of confidence the Receiver's compatibility with the available streams. It is not always practical for the constraints to indicate every type of stream that a Receiver can or cannot consume successfully; however, they SHOULD describe as many of its commonly used operating points as practical and any preferences among them.

The `constraint_sets` parameter within the `caps` object can be used to describe combinations of frame rates, width and height, and other parameters which the receiver can support, using the parameter constraints defined in the [Capabilities register](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/) of the NMOS Parameter Registers.

The following parameter constraints can be used to express limits or preferences specifically defined by Standard  ISO/IEC 14496-3 and RFC 3640 for AAC decoders:

- [Profile](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#profile)
  - Some AAC profiles are superset of other profiles. The AAC specification describe the relationship among the profiles. From the point of view of the AAC specification, supporting such superset profile is required to also be supporting the associated subset profiles. To assist a Controller not having knowledge of the AAC profiles relationship, the Receiver Capabilities SHOULD enumerate subset profiles in addition to the superset profile.

- [Level](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#level)
  - Some AAC levels are superset of other profiles. The AAC specification describe the relationship among the levels. From the point of view of the AAC specification, supporting such superset level is required to also be supporting the associated subset levels. To assist a Controller not having knowledge of the AAC levels relationship, the Receiver Capabilities SHOULD enumerate all the subset levels in addition to the superset level.
 
- [Packet Transmission Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#packet-transmission-mode)
  - [ ] string enum possible values are: "non_interleaved_access_units", "interleaved_access_units"
  - [ ] Target the `packet_transmission_mode` property of the Sender. 

  - An Receiver based on RTP transport declares the `packet_transmission_mode` capability to indicate the access units packetization modes that it supports.
  - RFC 3640 for the AAC-hbr mode require the `non_interleaved_access_units` and `interleaved_access_units` packetization modes to be supported by all the AAC Receivers. A constrained Receiver SHALL have `packet_transmission_mode` capabilities indicating support for both the "non_interleaved_access_units" and "interleaved_access_units" modes and MAY use the preference property to indicate which mode it prefers while still being required to support both.
  - RFC 6416 only supports the `non_interleaved_access_units` mode. A Receiver SHALL have `packet_transmission_mode` capabilities indicating support for the `non_interleaved_access_units` mode.

- [Parameter Sets Flow Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#parameter-sets-flow-mode)
  - [ ] string enum possible values are: "strict", "static", "dynamic"
  - [ ] Target the `parameter_sets_flow_mode` of the Sender.

  - A Receiver declares the `parameter_sets_flow_mode` capability to indicate that it supports bitstreams using config associated to strictly one (`strict`), one (`static`) or multiple (`dynamic`) Flows. Considering that an active config is associated to a specific Flow, this capability indicates that a Receiver is capable or not of decoding an AAC bitstream where the associated Flow properties change dynamically. A Receiver supporting a `static` Flow SHOULD become inactive if the config received in-band and out-of-band are associated to multiple Flows. A Receiver supporting a `strict` Flow SHOULD become inactive if the config received in-band and out-of-band are not new or duplicates or are associated to multiple Flows.
 
  Informative note: in strict mode one config can be defined once either out-of-band or in-band. They cannot be redefined unless strictly being duplicates.

  - A Receiver supporting the `dynamic` mode MUST also support the `strict` and `static` mode. Such Receiver SHOULD have `strict`, `static` and and `dynamic` values enumerated in the Receiver Capability in order to allow Senders operating in any `parameter_sets_flow_mode`.

- [Parameter Sets Transport Mode](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#parameter-sets-transport-mode)
  - [ ] string enum possible values are: "in_band", "out_of_band", "in_and_out_of_band"
  - [ ] Target the `parameter_sets_transport_mode` of the Sender.

  - A Receiver declares the `parameter_sets_transport_mode` capability to indicate that it supports bitstreams using config provided either only out-of-band, only in-band or in-and-out-of-band. The in-band config MAY update, augment or duplicate the config received out-of-band. A Receiver declaring the `in_band` or `in_and_out_of_band` capabilities SHALL be capable of decoding in-band config that update, augment or duplicate the config received out-of-band. A Receiver declaring the `out_of_band` capabilities SHALL be capable of decoding in-band parameter sets that duplicate the config received out-of-band.

  The config used by the bitstream MUST comply with the `profile-level-id` parameter of the stream's associated SDP transport file or the MPEG-4_audio_profile_and_level of the MPEG-4_audio_descriptor of an MPEG2-TS transport stream. The config MAY be specified out-of-band using the `config` attribute of an SDP transport file, in-band through the AAC bitstream or in-and-out-of-band usign both mechanisms. See the "Parameter Sets" section for more details.

 - A Receiver supporting `in_and_out_of_band` MUST also support the `in_band` and `out_of_band` modes. Such Receiver SHOULD have all "in_band", "out_of_band" and "in_and_out_of_band" values enumerated in the Receiver Capability in order to allow Senders operating in any `parameter_sets_transport_mode`.
 
When the AAC decoder has no restrictions on the value of some parameter, the Receiver can indicate that the parameter is unconstrained, as described in BCP-004-01. Otherwise a Receiver can indicate the supported values as enumerated string constraints. When a profile/level is defined as a superset of other profiles/level, subset profiles/levels SHOULD be enumerated.

Other existing parameter constraints, such as the following, are also appropriate to express limitations on supported AAC audio streams:

- [Channel Count](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#channel-count)
- [Sample Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#sample-rate)
- [Sample Depth](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#sample-depth)
- [Packet Time](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#packet-time)
- [Max Packet Time](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#max-packet-time)
- [Format Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#format-bit-rate)
- [Format Constant Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#format-constant-bit-rate)
- [Transport Bit Rate](https://specs.amwa.tv/nmos-parameter-registers/branches/main/capabilities/#transport-bit-rate)

An example Receiver resource is provided in the [Examples](../examples/).

A Receiver MUST be able to decode a bitstream conforming to the profiles and levels declared in the Receiver Capabilities. A Receiver MAY have preferences and more optimal profiles and levels that MAY be declared through Receiver Capabilities. A preferred constraint set MAY indicate such preferences while another constraint set MAY indicate full support of some profiles and levels. A Receiver MAY further constraint the support of a coded bitstream compliant with a profile and level using other constraints in its Receiver Capabilities.

#### Parent Flows

Flow resources can be associated with many Senders and many other Flows at the same time. A Flow resource MAY be the parent of many another Flow resources. A Receiver consuming a stream whose Sender is associated with a Flow having some parents AAC Flows MAY declare constraints that apply for all the parents AAC bistreams flowing through the Sender.

#### RTP based transport

For Nodes consuming AAC using the RTP payload mapping defined by RFC 3640 or RFC 6416, the Receiver resource MUST indicate `urn:x-nmos:transport:rtp` or one of its subclassifications for the `transport` attribute. 

#### Other transport

The Receiver resource MUST indicate the associated `urn:x-nmos:transport:` label of the transport or one of its subclassifications for the `transport` attribute. 

## AAC IS-05 Senders and Receivers

### RTP transport

Connection Management using IS-05 proceeds in exactly the same manner as for any other stream format carried within RTP.

The SDP file at the **/transportfile** endpoint on an IS-05 Sender MUST comply with the same requirements described for the SDP file at the IS-04 Sender `manifest_href`.

A `PATCH` request on the **/staged** endpoint of an IS-05 Receiver can contain an SDP file in the `transport_file` attribute. The SDP file for a AAC stream is expected to comply with RFC 3640. It need not comply with the additional requirements specified for SDP files at Senders.

If the Receiver is not capable of consuming the stream described by the SDP file, it SHOULD reject the request. If it is unable to assess the stream compatibility because some optional parameters are not included in the SDP transport file, it MAY accept the request and postpone stream compatibility assessment.

An example SDP file is provided in the [Examples](../examples/).

### Other transports

Connection Management using IS-05 proceeds in exactly the same manner as for any other stream format carried by transport other than RTP.

If an SDP transport file is supported by the transport, the SDP file at the **/transportfile** endpoint on an IS-05 Sender MUST comply with the same requirements described for the SDP file at the IS-04 Sender `manifest_href`.

### Parameter Sets

- [ ] TODO: Adapt for AAC

The config of an AAC stream is transmitted by a Sender to a number of Receivers either out-of-band through an SDP transport file or in-band through the coded stream.

The `config` parameter of an SDP transport file MAY contain an out-of-band config. This config provide initial parameters for the AAC stream before the decoding starts.

An AAC stream using LATM or ADTS MAY transport in-band configs to update or duplicate a config received out-of-band, to define additional config or to update or duplicate a config received in-band. The `parameter_sets_transport_mode` Receiver Capability indicates when set to `in_band` or `in_and_out_of_band` that a Receiver supports getting updated, duplicated or additional configs in-band. When set to `out_of_band` a Receiver supports only getting initial config from the SDP transport file. In all the cases the Receiver MUST support getting duplicated config in-band (if any).

Informative note: Using RFC 3640, there will never be in-band config.

The `config` parameter MAY be used by the Receiver to assert the `parameter_sets_transport_mode` in use by the Sender. The in-and-out-of-band mode is signaled if `config` is present and not empty and terminates by a comma ','. The terminating colon ',' indicates that more configs will be received in-band, indicating the in-and-out-of-band mode. The out-of-band mode is signaled when `config` is present and not empty and not terminated by a comma ','. The in-band mode is signaled when `config` is not present or empty. A Sender MUST terminate the `conifig` parameter by a comma ',' if it operates in in-and-out-of-band mode and MUST not terminate it by a comma ',' in out-of-band mode.

Informative note: It results that an AAC Sender operating in in-and-out-of-band mode that is not sending parameter sets out-of-band will set the  `config` parameter to a single comma ',' value.

The current config of an AAC stream MUST comply with the current Flow associated with the Sender that produce the coded stream and the related parameters of the `fmtp=` attribute of the Sender's SDP transport file.

The current config of an AAC stream MUST comply with the `profile-level-id` declared in the Sender's SDP transport file or the MPEG2-TS transport stream and the `profile` and `level` of the associated Flow.

A Receiver MUST verify that the current config comply with the Receiver's Capabilities. If a Receiver support only out-of-band config it SHOULD perform the verification when a Controller PATCH the **/staged** endpoint for activation. In this situation, the out-of-band config MUST be compliant with the Receiver Capabilities. Otherwise if a Receiver supports both out-of-band and in-band configs it SHOULD perform the verification of the out-of-band coinfig when a Controller PATCH the **/staged** endpoint for activation and it MUST perform the verification of the in-band configs just-in-time as it decodes the stream. In this situation, all the out-of-band and in-band configs MUST be compliant with the Receiver Capabilities.

The `parameter_sets_flow_mode` Receiver Capability indicates when set to "dynamic" that a Receiver supports decoding an AAC stream where the active parameter set values associated to Flow properties may changes dynamically. The Flow properties that are considered are those of a audio coded Flow: sample_rate, format, media_type, profile, level and audio Source: channels. The "static" mode is more restrictive, requiring active parameter set values associated to Flow properties to be constant. The "strict" more further restrict that at most one SPS be used by the coded stream.

> The Flow bit_rate property is not included in the previous criterion to allow adapting to IP usable bandwidth changing conditions in `static` mode.
> The Source channels is considered part of the information assocaited with a Flow.

A Receiver with this capability set to "strict" requires that a coded stream uses a config defined once and associated to at most one Flow. Such config MAY be obtained out-of-band or in-band depending on the Receiver `parameter_sets_transport_mode` Capability. When obtained out-of-band the `config` parameter of an SDP transport file MUST contain the config. When obtained in-band the `config` parameter of an SDP transport file MUST be empty or omited and the Sender transmits the config associated to the Flow in-band. At all time, the Sender is allowed to transmit in-band configs that are duplicates of the configs obtained either out-of-band or in-band. When obtained in-and-out-of-band, in-band configs have priority over the out-of-band one.

A Receiver with this capability set to "static" requires that a coded stream uses configs associated to at most one Flow. Such configs MAY be obtained out-of-band or in-band depending on the Receiver `parameter_sets_transport_mode` Capability. When obtained out-of-band the `config` parameter of an SDP transport file MUST contain a config associated to only one Flow. When obtained in-band the `config` parameter of an SDP transport file MUST be empty or omited and the Sender transmits the config associated to the Flow in-band. At all time, the Sender is allowed to transmit in-band configs that are duplicates of the configs obtained either out-of-band or in-band. When obtained in-and-out-of-band, in-band configs have priority over out-of-band ones.

Informative note: RFC 3640 does not allow in-band configs.

A Receiver with this capability set to "dynamic" supports that a coded stream uses configs that MAY be associated to multiple Flows. Such configs MAY be obtained out-of-band or in-band according to the Receiver `parameter_sets_transport_mode` Capability. When obtained out-of-band the `config` parameter of an SDP transport file MAY contain a config associated to single Flow. When obtained in-band the `config` parameter of an SDP transport file MUST be empty or omited and the Sender MAY transmit in-band configs to update the config initially received out-of-band or to add and update new ones. At all time, the Sender is allowed to transmit in-band configs that are duplicates of the configs obtained either out-of-band or in-band. When obtained in-and-out-of-band, in-band configs have priority over out-of-band ones 

A Sender is allowed, unless constrained by IS-11, to produces AAC coded streams that comply with the `profile-level-id` declared in the Sender's SDP transport file and the `profile` and `level` of the associated Flow. A Sender MAY seamlessly change dynamically the coded stream's active config, provided that the Flow associated with the Sender changes accordingly and the content of the SDP transport file does not change. If the content of the SDP transport file changes, the Sender SHALL comply with IS-04, IS-05. A Sender indicates its mode of operation with the `parameter_sets_flow_mode` and `parameter_sets_transport_mode` attributes.

A Sender MUST transport configs `in_band` when the AAC stream is transmitted over an MPEG2-TS based transport as per the [H.222] specification.

A Sender operating with `parameter_sets_flow_mode` set to `strict` MUST produce a coded bitstream using at most one config that MUST be associated to at most one Flow. The config MUST be defined once in-band or out-of-band and MAY be refreshed by in-band duplicates.

A Sender operating with `parameter_sets_flow_mode` set to `static` MAY produce a coded bitstream using a number of configs that MUST be associated to at most one Flow. The configs MAY be defined in-band or out-of-band and MAY be refreshed by in-band duplicates or updated by in-band values.

A Sender operating with `parameter_sets_flow_mode` set to `dynamic` MAY produce a coded bitstream using a number of configs that MAY be associated to multiple Flows. The configs MAY be defined in-band or out-of-band and MAY be refreshed by in-band duplicates or updated by in-band values.

## Controllers

Controllers MUST support the BCP-004-01 Receiver Capabilities mechanism and all the parameter constraints listed in this specification in order to evaluate the stream compatibility between H.264 Senders and Receivers.

- [ ] Controllers MUST support the BCP-004-01 Receiver Capabilities mechanism and all the parameter constraints listed in this specification in order to evaluate the stream compatibility between H.265 Senders and Receivers.

[BCP-004-01]: https://specs.amwa.tv/bcp-004-01/ "AMWA BCP-004-01 NMOS Receiver Capabilities"
[AAC]: https://www.iso.org/standard/76383.html "ISO/IEC 14496-3 Coding of audio-visual objects"
[H.222]: https://www.itu.int/rec/T-REC-H.222.0 "Generic coding of moving pictures and associated audio information: Systems"
[RFC-2119]: https://tools.ietf.org/html/rfc2119 "Key words for use in RFCs"
[RFC-3640]: https://datatracker.ietf.org/doc/html/rfc3640 "RTP Payload Format for Transport of MPEG-4 Elementary Streams"
[RFC-6416]: https://datatracker.ietf.org/doc/html/rfc6416 "RTP Payload Format for MPEG-4 Audio/Visual Streams"
[IS-04]: https://specs.amwa.tv/is-04/ "AMWA IS-04 NMOS Discovery and Registration Specification"
[IS-05]: https://specs.amwa.tv/is-05/ "AMWA IS-05 NMOS Device Connection Management Specification"
[NMOS Parameter Registers]: https://specs.amwa.tv/nmos-parameter-registers/ "Common parameter values for AMWA NMOS Specifications"
[VSF]: https://vsf.tv/ "Video Services Forum"
[ST-2110-20]: https://ieeexplore.ieee.org/document/8167389 "ST 2110-20:2017 - SMPTE Standard - Professional Media Over Managed IP Networks: Uncompressed Active Video"


- [ ] TODO: consider adding channel-order as per ST2110-30/31