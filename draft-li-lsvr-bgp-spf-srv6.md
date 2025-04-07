---
###
#
# This template uses kramdown-rfc: https://github.com/cabo/kramdown-rfc
# You can replace the entire file if you prefer a different format.
# Change the file extension to match the format (.xml for XML, etc...)
#
###
title: "Applying BGP-LS Segment Routing over IPv6(SRv6) Extensions to BGP-LS-SPF"
abbrev: "BGP-SPF SRv6"
category: info

docname: draft-li-lsvr-bgp-spf-srv6-latest
category: std
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: Routing
workgroup: LSVR
keyword:
 - BGP-LS
 - BGP-LS-SPF
 - SRv6


author:
-  
 ins: L. Zhang
 name: Li Zhang
 organization: Huawei Technologies
 email: zhangli344@huawei.com
 street: No. 156 Beiqing Road
 city: Beijing
 country: China

-
 ins: J. Dong
 name: Jie Dong
 organization: Huawei Technologies
 email: jie.dong@huawei.com
 street: No. 156 Beiqing Road
 city: Beijing
 country: China


normative:

informative:


--- abstract

For network scenarios such as Massively Scaled Data Centers (MSDCs), BGP is extended for Link-State (LS) distribution and the Shortest Path First (SPF) algorithm based calculation. BGP-LS-SPF leverages the mechanisms of both BGP protocol and BGP-LS protocol extensions. Segment Routing over IPv6 (SRv6) provides a source routing mechanism that allows a flow to be restricted to a specific topological path, while maintaining per-flow state only at the ingress node(s) to the SRv6 domain. In some networks, it may be useful to enable SRv6 based source routing mechanism together with BGP-LS-SPF. This document proposes to introduce the BGP Link-State (BGP-LS) extensions for SRv6 to the BGP-LS-SPF SAFI.


--- middle

# Introduction {#intro}

{{!I-D.ietf-lsvr-bgp-spf}} extends BGP for Link-State (LS) distribution and the Shortest Path First (SPF) algorithm based calculation. BGP-LS-SPF leverages the mechanisms of both BGP protocol {{!RFC4271}} and BGP-LS protocol extensions {{!RFC9552}}, with the extensions to BGP-LS attribute and new NLRI selection rules.

Segment Routing over IPv6 (SRv6) allows for a flexible definition of end-to-end paths within various topologies by encoding paths as sequences of topological or functional sub-paths called "segments". SRv6 provides a mechanism that allows a flow to be restricted to a specific topological path, while maintaining per-flow state only at the ingress node(s) to the SRv6 domain.

In network scenarios such as Data Center networks, WAN networks or other networks where BGP-LS-SPF can be used as the underlay routing protocol, it may be useful to enable SRv6 based source routing mechanism for traffic engineering and optimization. This document proposes to introduce the BGP Link-State (BGP-LS) extensions for SRv6 to the BGP-LS-SPF SAFI, and discusses which SRv6 extensions can be applied to BGP-LS-SPF SAFI.

## Requirements Language

{::boilerplate bcp14-tagged}

# SRv6 Node Attribute TLVs

Based on {{!RFC9514}}, the following SRv6 Node Attributes TLV SHOULD be supported in BGP-LS-SPF.

 |======+==========================+===============|
 | Type | Description              | Reference     |
 |======+==========================+===============|
 | 1138 | SRv6 Capabilities TLV    | RFC 9514      |
 |------+--------------------------+---------------|
 | 266  | Node MSD TLV             | RFC 8814      |
 |------+--------------------------+---------------|
 | 1035 | SR Algorithm TLV         | RFC 9085      |
 |------+--------------------------+---------------|
{: #ref-to-tab4 title="Node Attribute TLVs for SRv6 with BGP-LS-SPF"}

## SRv6 Capabilities TLV

The SRv6 Capabilities TLV defined in {{!RFC9514}} is used to announce the SRv6 capabilities of the node along with the BGP-LS Node NLRI and indicates the SRv6 support by the node.

For BGP-LS-SPF, it SHOULD support this TLV to announce the SRv6 capabilities of BGP-LS-SPF nodes. A single instance of this TLV MUST be included in the BGP-SPF Node Attribute for each SRv6-capable node. {{!RFC9514}} defines the value of this TLV is copied from the ISIS SRv6 Capabilities sub-TLV or from the OSPFv3 SRv6 Capabilities TLV, but both of them have the same flag definition, therefore, the flags format of SRv6 Capabilities TLV of BGP-LS-SPF is consistent with that in ISIS SRv6 Capabilities sub-TLV and OSPFv3 SRv6 Capabilities TLV.

## SRv6 Node MSD Types

The Node MSD TLV defined in {{!RFC8814}} is used to advertise the limits and the Segment Routing Header (SRH) operations supported by the SRv6-capable node.

For BGP-LS-SPF, it SHOULD support this TLV to advertise the limits and operations supported by BGP-LS-SPF node to enable segment routing. The SRv6 MSD types specified in {{Section 4 of !RFC9352}} are also used with the BGP-LS-SPF Node MSD TLV.

The SRv6 Node MSD TLV is an optional TLV, for a specific MSD type, if the advertised value is zero or no value is advertised, then the headend can apply an SR Policy that only contains one segment without inserting any SRH header.

## SR-Algorithm TLV

{{!RFC9514}} specifies that the algorithm support for SRv6 is advertised via the SR-Algorithm TLV specified in {{!RFC9085}}. The SR-Algorithm is used to advertise the SR algorithms supported by the node.

This TLV is a basic TLV of SR and SHOULD be supported in BGP-LS-SPF. The format of Algorithm fields in this TLV is consistent with that in ISIS, as defined in {{Section 3.2 of ?RFC8667}}.

The SR-Algorithm TLV is an optional TLV, when the originating router does not advertise the SR-Algorithm TLV, it implies that algorithm 0 is the only algorithm supported by the router.

# SRv6 Link Attribute TLVs

Based on {{!RFC9514}} and {{!RFC9085}}, the following Link Attributes SHOULD be supported in BGP-LS-SPF.

 |======+==========================+===============|
 | Type | Description              | Reference     |
 |======+==========================+===============|
 | 1106 | SRv6 End.X SID TLV       | RFC 9514      |
 |------+--------------------------+---------------|
 | 1106 |L2 Bundle Member Attributes TLV| RFC 9085 |
 |------+--------------------------+---------------|
 | 267  | SRv6 Link MSD TLV        | RFC 8814      |
 |------+--------------------------+---------------|
{: #ref-to-tab5 title="Link Attribute TLVs for SRv6 with BGP-LS-SPF"}


## SRv6 End.X SID TLV

The SRv6 End.X SID TLV defined in {{!RFC9514}} is used to advertise the SRv6 SIDs associated with an IGP Adjacency SID behavior that correspond to a point-to-point or point-to-multipoint link or adjacency of the node running the IS-IS or OSPFv3 protocols. It is also used by BGP-LS to advertise the BGP EPE Peer Adjacency SID for SRv6 on the same lines as specified for SR-MPLS in {{?RFC9086}}.

BGP-LS-SPF SHOULD support this TLV to advertise the SRv6 SIDs correspond to a point-to-point or adjacency of the node running the BGP-LS-SPF.

## L2 Bundele Member Attributes TLV

The L2 Bundle Member Attributes TLV is defined in {{!RFC9085}}, it identifies an L2 Bundle Member link, which in turn is associated with a parent L3 link. The TLV MAY include sub-TLVs that describe attributes associated with the bundle member.

The network deployed BGP-LS-SPF may include trunk links. Therefore, BGP-LS-SPF MAY support this TLV to advertise L2 bundle member link attributes.

## SRv6 Link MSD Types

The Link MSD TLV defined in {{!RFC8814}} is used to advertise the limits and the SRH operations supported on the specific link by the SRv6-capable node. BGP-LS-SPF SHOULD support this TLV to advertise the limits and operations supported on the specific link to enable segment routing. The SRv6 MSD types specified in {{Section 4 of !RFC9352}} are also used with the BGP-LS-SPF Link MSD TLV.

The SRv6 Link MSD TLV is an optional TLV, for a specific MSD type, if the advertised value is zero or no value is advertised, then the headend can apply an SR Policy that only contains one segment without inserting any SRH header.


# SRv6 Prefix Attribute TLVs

Based on {{!RFC9514}}, the BGP-LS SRv6 Prefix Attributes only includes the SRv6 Locator TLV. This TLV SHOULD be supported by BGP-LS-SPF.

 |======+==========================+===============|
 | Type | Description              | Reference     |
 |======+==========================+===============|
 | 1162 | SRv6 Locator TLV         | RFC 9514      |
 |------+--------------------------+---------------|
{: #ref-to-tab6 title="Prefix Attribute TLVs for SRv6 with BGP-LS-SPF"}

## SRv6 Locator TLV

The SRv6 Locator TLV defined in {{!RFC9514}} is used to advertise the locators supported by each node. Each locator is advertised in a SRv6 Locator TLV. Locator is the key component of SRv6 SID, BGP-LS-SPF SHOULD support this TLV to enable segment routing.

The flags fields of this TLV in BGP-LS-SPF is not used and MUST be set to zero on transmission and MUST be ignored on receipt.

# SRv6 SID NLRI

The SRv6 SID NLRI defined in {{!RFC9514}} is used to carry the SRv6 SID information. When SRv6 SIDs need to be advertised in BGP-SPF, the following NLRI type and attributes TLV for SRv6 SID SHOULD be supported in BGP-LS-SPF SAFI:

 |======+=============================+===========|
 | Type | NLRI Type                   | Reference |
 |======+=============================+===========|
 | 6    | SRv6 SID NLRI               | RFC 9514  |
 |------+-----------------------------+-----------|
 | 518  | SRv6 SID Information TLV    | RFC 9514  |
 |------+-----------------------------+-----------|
 | 1250 | SRv6 Endpoint Behavior TLV  | RFC 9514  |
 |------|-----------------------------|-----------|
 | 1252 | SRv6 SID Structure TLV      | RFC 9514  |
 |------|-----------------------------|-----------|
{: #ref-to-tab7 title="SRv6 SID NLRI with BGP-LS-SPF"}

The format of local node descriptors in this NLRI is consistent with BGP-LS local node descriptors {{Section 5.2.1.2 of ?RFC9552}}, which MAY contain the Autonomous System and BGP-LS Identifier sub-TLV.

## SRv6 SID Information TLV

The SRv6 SID Information TLV is used to carry the SRv6 SID associated with a node in a SRv6 SID NLRI. This TLV SHOULD be supported in BGP-LS-SPF to advertise SRv6 SIDs.

## SRv6 Endpoint Behavior TLV

The Endpoint Behavior TLV defined in {{!RFC9514}} is a mandatory TLV included in the BGP-LS Attribute, used to advertise the behaviors associated with a SID. The BGP-LS-SPF SHOULD support this TLV to enable advertisement of behaviors associated with a SRv6 SID.

## SRv6 SID Structure TLV

The SRv6 SID Structure TLV defined in {{!RFC9514}} is used to advertise the length of each individual part of the SRv6 SID as defined in {{?RFC8986}}. It is an optional TLV in the BGP-LS Attribute for SRv6 SID NLRI and a sub-TLV of the SRv6 End.X SID. BGP-LS-SPF MAY support this TLV to indicate the length of each individual part of the SRv6 SID of BGP-LS-SPF especially in some scenarios using compressed SID.

# Operational Considerations

TBD

# Security Considerations

This document introduces no additional security vulnerabilities in addition to the ones as described in {{!RFC9552}}.

# IANA Considerations

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
