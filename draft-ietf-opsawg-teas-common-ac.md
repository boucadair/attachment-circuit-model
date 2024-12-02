---
title: "A Common YANG Data Model for Attachment Circuits"
abbrev: "Common Attachment Circuit YANG"
category: std

docname: draft-ietf-opsawg-teas-common-ac-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Operations and Management Area Working Group"
keyword:
 - Slice Service
 - L3VPN
 - L2VPN
 - Automation
 - Network Automation
 - Orchestration
 - service delivery
 - Service provisioning
 - service segmentation
 - service flexibility
 - service simplification
 - Network Service
 - 3GPP
 - Network Slicing
author:
 -
    fullname: Mohamed Boucadair
    organization: Orange
    role: editor
    email: mohamed.boucadair@orange.com

 -
    fullname: Richard Roberts
    organization: Juniper
    role: editor
    email: rroberts@juniper.net

 -
    fullname: Oscar Gonzalez de Dios
    organization: Telefonica
    email: oscar.gonzalezdedios@telefonica.com

 -
    fullname: Samier Barguil Giraldo
    organization: Nokia
    email: samier.barguil_giraldo@nokia.com

 -
    fullname: Bo Wu
    organization: Huawei Technologies
    email: lana.wubo@huawei.com

contributor:
  -
    name: Victor Lopez
    org: Nokia
    email: victor.lopez@nokia.com

  -
    name: Ivan Bykov
    org: Ribbon Communications
    email: Ivan.Bykov@rbbn.com

  -
    name: Qin Wu
    org: Huawei
    email: bill.wu@huawei.com

  -
    name: Kenichi Ogaki
    org: KDDI
    email: ke-oogaki@kddi.com

  -
    name: Luis Angel Munoz
    org: Vodafone
    email: luis-angel.munoz@vodafone.com

normative:
  ISO10589:
    title: Information technology - Telecommunications and information exchange between systems - Intermediate System to Intermediate System intra-domain routeing information exchange protocol for use in conjunction with the protocol for providing the connectionless-mode network service (ISO8473)
    author:
      org: ISO
    date: 2002
    target: https://www.iso.org/standard/30932.html


--- abstract

The document specifies a common Attachment Circuits (ACs) YANG module, which is designed with the intent to be reusable by other models. For example, this common model can be reused by service models to expose ACs as a service, service models that require binding a service to a set of ACs, network and device models to provision ACs, etc.

--- middle

# Introduction

Connectivity services are provided by networks to customers via dedicated terminating points (e.g., Service Functions (SFs), Customer Premises Equipment (CPEs), Autonomous System Border Routers (ASBRs), data centers gateways, or Internet Exchange Points). A connectivity service is basically about ensuring data transfer received from (or destined to) a given terminating point to (or from) other terminating points that belong to the same customer/service, an interconnection node, or an ancillary node. A set of objectives for the connectivity service may eventually be negotiated and agreed upon between a customer and a network provider. For that data transfer to take place within the provider network, it is assumed that adequate setup is provisioned over the links that connect customer terminating points and a provider network (a Provider Edge (PE), typically) so that data can be successfully exchanged over these links. The required setup is referred to in this document as Attachment Circuits (ACs), while the underlying link is referred to as "bearer".

This document adheres to the definition of an attachment circuit as provided in {{Section 1.2 of ?RFC4364}}, especially:

> Routers can be attached to each other, or to end systems, in a
   variety of different ways: PPP connections, ATM Virtual Circuits
   (VCs), Frame Relay VCs, ethernet interfaces, Virtual Local Area
   Networks (VLANs) on ethernet interfaces, GRE tunnels, Layer 2
   Tunneling Protocol (L2TP) tunnels, IPsec tunnels, etc.  We will use
   the term "attachment circuit" to refer generally to some such means
   of attaching to a router.  An attachment circuit may be the sort of
   connection that is usually thought of as a "data link", or it may be
   a tunnel of some sort; what matters is that it be possible for two
   devices to be network layer peers over the attachment circuit.

When a customer requests a new value-added service, the service can be bound to existing attachment circuits or trigger the instantiation of new attachment circuits. Whether these attachment circuits are specific for a given service or are shared to deliver a variety of services is deployment-specific.

An example of attachment circuits is depicted in {{uc}}. A Customer Edge (CE) may be a physical node or a logical entity. A CE is seen by the network as a peer Service Attachment Point (SAP) {{?RFC9408}}. CEs may be dedicated to one single service (e.g., Layer 3 Virtual Private Network (VPN) or Layer 2 VPN) or host multiple services (e.g., Service Functions {{?RFC7665}}). A single AC (as seen by a network provider) may be bound to one or multiple peer SAPs (e.g., "CE1" and "CE2"). For example, and as discussed in {{?RFC4364}}, multiple CEs can be attached to a PE over the same attachment circuit. This is typically implemented if the Layer 2 infrastructure between the CE and the network provides a multipoint service. The same CE may terminate multiple ACs. These ACs may be over the same or distinct bearers.

~~~~ aasvg
{::include ./figures/acs-examples.txt}
~~~~
{: #uc title='Examples of ACs' artwork-align="center"}

This document specifies a common module ("ietf-ac-common") for attachment circuits ({{sec-module}}). The model is designed with the intent to be reusable by other models and, therefore, ensure consistent AC structures among modules that manipulate ACs. For example, the common model can be reused by service models to expose AC-as-a-Service (ACaaS) (e.g., {{?I-D.ietf-opsawg-teas-attachment-circuit}}), service models that require binding a service to a set of ACs (e.g., Network Slice Service {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}})), network models to provision ACs (e.g., {{?I-D.ietf-opsawg-ntw-attachment-circuit}}), device models, etc.

The common AC module eases data inheritance between modules (e.g., from service to network models as per {{?RFC8969}}).

The YANG data model in this document conforms to the Network Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

## Editorial Note (To be removed by RFC Editor)

Note to the RFC Editor: This section is to be removed prior to publication.

This document contains placeholder values that need to be replaced with finalized values at the time of publication. This note summarizes all of the substitutions that are needed.

Please apply the following replacements:

* XXXX --> the assigned RFC number for this I-D
* 2023-11-13 --> the actual date of the publication of this document

# Conventions and Definitions

The meanings of the symbols in the YANG tree diagrams are defined in {{?RFC8340}}.

LxSM refers to both the Layer 2 Service Model (L2SM) {{?RFC8466}} and the Layer 3 Service Model (L3SM) {{?RFC8299}}.

LxNM refers to both the Layer 2 Network Model (L2NM) {{?RFC9291}} and the Layer 3 Network Model (L3NM) {{?RFC9182}}.

This document uses the following term:

Bearer:
: A physical or logical link that connects a customer node (or site) to a provider network.
: A bearer can be a wireless or wired link. One or multiple technologies can be used to build a bearer. The bearer type can be specified by a customer.
: The operator allocates a unique bearer reference to identify a bearer within its network (e.g., customer line identifier). Such a reference can be retrieved by a customer and then used in subsequent service placement requests to unambiguously identify where a service is to be bound.
: The concept of bearer can be generalized to refer to the required underlying connection for the provisioning of an attachment circuit.
: One or multiple attachment circuits may be hosted over the same bearer (e.g., multiple Virtual Local Area Networks (VLANs) on the same bearer that is provided by a physical link).

The names of data nodes are prefixed using the prefix associated with the corresponding imported YANG module as shown in {{pref}}:

|Prefix|	Module| Reference |
| inet|ietf-inet-types| {{Section 4 of !RFC6991}}|
| key-chain|ietf-key-chain| {{!RFC8177}}|
| nacm|ietf-netconf-acm|{{!RFC8341}}|
| vpn-common|ietf-vpn-common|{{!RFC9181}}|
| yang|ietf-yang-types| {{Section 3 of !RFC6991}}|
{: #pref title="Modules and Their Associated Prefixes"}

# Relationship to Other AC Data Models

{{ac-overview}} depicts the relationship between the various AC data models:

* "ietf-ac-common" ({{sec-module}})
* "ietf-bearer-svc" ({{Section 5.1 of ?I-D.ietf-opsawg-teas-attachment-circuit}})
* "ietf-ac-svc" ({{Section 5.2 of ?I-D.ietf-opsawg-teas-attachment-circuit}})
* "ietf-ac-ntw" ({{?I-D.ietf-opsawg-ntw-attachment-circuit}})
* "ietf-ac-glue" ({{?I-D.ietf-opsawg-ac-lxsm-lxnm-glue}})

~~~~ aasvg
                ietf-ac-common
                 ^     ^     ^
                 |     |     |
      +----------+     |     +----------+
      |                |                |
      |                |                |
ietf-ac-svc <--> ietf-bearer-svc        |
   ^    ^                               |
   |    |                               |
   |    +------------------------ ietf-ac-ntw
   |                                    ^
   |                                    |
   |                                    |
   +----------- ietf-ac-glue -----------+
~~~~
{: #ac-overview title="AC Data Models" artwork-align="center"}

"ietf-ac-common" is imported  by "ietf-bearer-svc", "ietf-ac-svc", and "ietf-ac-ntw".
Bearers managed using "ietf-bearer-svc" may be referenced in the service ACs managed using "ietf-ac-svc".
Similarly, a bearer managed using "ietf-bearer-svc" may list the set of ACs that use that bearer.
In order to ease correlation between an AC service requests and the actual AC provisioned in the network, "ietf-ac-ntw" uses the AC references exposed by "ietf-ac-svc".
To bind Layer 2 VPN or Layer 3 VPN services with ACs, "ietf-ac-glue" augments the LxSM and LxNM with AC service references exposed by "ietf-ac-svc" and AC network references exposed by "ietf-ac-ntw".

# Description of the AC Common YANG Module

The full tree diagram of the module is provided in {{AC-Common-Tree}}.  Subtrees are provided in the following subsections
for the reader's convenience.

## Features

The module defines the following features:

'layer2-ac':
: Used to indicate support of ACs with Layer 2 properties.

'layer3-ac':
: Used to indicate support of ACs with Layer 3 properties.

'server-assigned-reference':
: Used to indicate support of server-generated references to access relevant resources.
: For example, a bearer request is first created using a name which is assigned by the client, but if this feature is supported, the request will also include a server-generated reference. That reference can be used when requesting the creating of an AC over the existing bearer.

## Identities

The module defines a set of identities, including the following:

'address-allocation-type':
: Used to specify the IP address allocation type in an AC. For example, this identity can used to indicate whether the provider network provides DHCP service, DHCP relay, or static addressing. Note that for the IPv6 case, Stateless Address Autoconfiguration (SLAAC) {{?RFC4862}} can be used.

'local-defined-next-hop':
: Used to specify next hop actions. For example, this identity can be used to indicate an action to discard traffic for a given destination or treat traffic towards addresses within the specified next-hop prefix as though they are connected to a local link.

'l2-tunnel-type':
: Uses to control the Layer 2 tunnel selection for an AC. The current version supports indicating pseudowire, Virtual Private LAN Service (VPLS), and Virtual eXtensible Local Area Network (VXLAN).

'precedence-type':
: Used to indicate the redundancy type when requesting ACs. For example, this identity can be used to tag primary and secondary ACs.

'role':
: Used to indicate the type of an AC: User-to-Network Interface (UNI), Network-to-Network Interface (NNI), or public NNI.

New administrative status types:
: In addition to the status types already defined in {{!RFC9181}}, this document defines:

   + 'awaiting-validation' to report that a request is pending an adiministrator approval.
   + 'awaiting-processing' to report that a request was	approved and validated, but is awaiting more processing before activation.
   + 'admin-prohibited' to report that a request cannot	be handled because of administrative policies.
   + 'rejected' to report that a request was rejected reasons not covered by the other status types.

## Reusable Groupings

The module also defines a set of reusable groupings, including the following:

'op-instructions' ({{op-full-tree}}):
: Defines a set of parameters to specify scheduling instructions and report related events for a service request (e.g., AC or bearer).

~~~~
{::include ./yang/subtrees/ac-common/ac-common-op.txt}
~~~~
{: #op-full-tree title="Operational Instructions Grouping"}

Layer 2 encapsulations ({{l2-full-tree}}):
: Groupings for the following encapsulation schemes are supported: dot1Q, QinQ, and priority-tagged.

Layer 2 tunnel services  ({{l2-full-tree}}):
:  These groupings are used to define Layer 2 tunnel services that may be needed for the activation of an AC. Examples of supported Layer 2 services are the pseudowire
   ({{Section 6.1 of !RFC8077}}), VPLS, or VXLAN {{!RFC7348}}.

~~~~
{::include ./yang/subtrees/ac-common/ac-common-l2-encap.txt}
~~~~
{: #l2-full-tree title="Layer 2 Connection Groupings"}

Layer 3 address allocation ({{l3-full-tree}}):
: Defines both IPv4 and IPv6 groupings to specify IP address allocation over an AC. Both dynamic and static address schemes are supported.
: For both IPv4 and IPv6, 'address-allocation-type' is used to indicate the IP address allocation mode to activate. When 'address-allocation-type' is set to 'provider-dhcp', DHCP assignments can be made locally or by an external DHCP server. Such behavior is controlled by setting 'dhcp-service-type'.
: Note that if 'address-allocation-type' is set to 'slaac', the Prefix Information option of Router Advertisements that will be issued for SLAAC purposes will carry the IPv6 prefix that is determined by 'local-address' and 'prefix-length'.

IP connections ({{l3-full-tree}})::
: Defines IPv4 and IPv6 groupings for managing Layer 3 connectivity over an AC. Both basic and more elaborated IP connection groupings are supported.

~~~~
{::include ./yang/subtrees/ac-common/ac-common-ipc.txt}
~~~~
{: #l3-full-tree title="Layer 3 Connection Groupings"}

Routing parameters & OAM ({{rtg-full-tree}}):
: In addition to static routing, the module supports the following routing protocols: BGP {{!RFC4271}}, OSPF {{!RFC4577}} or {{!RFC6565}}, IS-IS {{ISO10589}}{{!RFC1195}}{{!RFC5308}}, and RIP {{!RFC2453}}. For all supported routing protocols, 'address-family' indicates whether IPv4, IPv6, or both address families are to be activated. For example, this parameter is used to determine whether RIPv2 {{!RFC2453}}, RIP Next Generation (RIPng), or both are to be enabled {{!RFC2080}}. More details about supported routing groupings are provided hereafter:

  * Authentication: These groupings include the required information to manage the authentication of OSPF, IS-IS, BGP, and RIP. The groupings support local specification of authentication keys and the associated authentication algorithm to accomodate legacy implementations that do not support key chains {{!RFC8177}}. Similar to {{?RFC9182}}, this version of the common AC model assumes that parameters specific to the TCP-AO are preconfigured as part of the key chain that is referenced in the model. No assumption is made about how such a key chain is preconfigured. However, the structure of the key chain should cover data nodes beyond those in {{!RFC8177}}, mainly SendID and RecvID (Section 3.1 of {{!RFC5925}}).

  * BGP peer groups: Includes a set of parameters to identify a BGP peer group. Such a group can be defined by providing a local AS Number (ASN), a customer's ASN, and the address families to be activated for this group. BGP peer groups can be identified by a name.
  * Basic parameters: These groupings include the minimal set of routing configuration that is required for the activation of OSPF, IS-IS, BGP, and RIP.
  * Static routing: Parameters to configure an entry of a list of IP static routing entries.

: The 'redundancy-group' grouping lists the groups to which an AC belongs {{!RFC9181}}. For example, the 'group-id' is used to associate redundancy or protection constraints of ACs.

~~~~
{::include ./yang/subtrees/ac-common/ac-common-rtg.txt}
~~~~
{: #rtg-full-tree title="Layer 3 Connection Groupings"}

Bandwidth parameters ({{bw-full-tree}}):
: Bandwidth parameters can be represented using the Committed
  Information Rate (CIR), the Excess Information Rate (EIR), or the Peak
  Information Rate (PIR).

: These parameters can be provided per bandwidth type. Type values are
  taken from {{!RFC9181}}, e.g.,:

    * 'bw-per-cos':
    :  The bandwidth is per Class of Service (CoS).

    * 'bw-per-site':
    :  The bandwidth is to all ACs that belong to the same site.

~~~~
{::include ./yang/subtrees/ac-common/ac-common-bw.txt}
~~~~
{: #bw-full-tree title="Bandwidth Groupings"}

# Common Attachment Circuit YANG Module {#sec-module}

This module uses types defined in {{!RFC6991}}, {{!RFC8177}}, and  {{!RFC9181}}.

~~~~~~~~~~
<CODE BEGINS> file "ietf-ac-common@2023-11-13.yang"
{::include-fold ./yang/ietf-ac-common.yang}
<CODE ENDS>
~~~~~~~~~~

# Security Considerations

This section is modeled after the template described in {{Section 3.7 of ?I-D.ietf-netmod-rfc8407bis}}.

The "ietf-ac-common" YANG module defines a data model that is
designed to be accessed via YANG-based management protocols, such as
NETCONF {{?RFC6241}} and RESTCONF {{?RFC8040}}. These protocols have to
use a secure transport layer (e.g., SSH {{?RFC4252}}, TLS {{?RFC8446}}, and
QUIC {{?RFC9000}}) and have to use mutual authentication.

The Network Configuration Access Control Model (NACM) {{!RFC8341}}
provides the means to restrict access for particular NETCONF or
RESTCONF users to a preconfigured subset of all available NETCONF or
RESTCONF protocol operations and content.

The YANG module defines a set of identities, types, and
groupings. These nodes are intended to be reused by other YANG
modules. The module by itself does not expose any data nodes that
are writable, data nodes that contain read-only state, or RPCs.
As such, there are no additional security issues related to
the YANG module that need to be considered.

Modules that use the groupings that are defined in this document
should identify the corresponding security considerations. For
   example, reusing some of these groupings will expose privacy-related
   information (e.g., 'ipv6-lan-prefixes' or 'ipv4-lan-prefixes').  Disclosing such information may
   be considered a violation of the customer-provider trust
   relationship.

Several groupings ('bgp-authentication', 'ospf-authentication', 'isis-authentication', and 'rip-authentication') rely
   upon {{!RFC8177}} for authentication purposes.  As such, modules that will reuse these groupings
   will inherit the security considerations discussed in
   {{Section 5 of !RFC8177}}.  Also, these groupings support supplying explicit keys as
   strings in ASCII format.  The use of keys in hexadecimal string
   format would afford greater key entropy with the same number of key-
   string octets.  However, such a format is not included in this
   version of the common AC model, because it is not supported by the underlying
   device modules (e.g., {{?RFC8695}}).

# IANA Considerations

   IANA is requested to register the following URI in the "ns" subregistry within
   the "IETF XML Registry" {{!RFC3688}}:

~~~~
   URI:  urn:ietf:params:xml:ns:yang:ietf-ac-common
   Registrant Contact:  The IESG.
   XML:  N/A; the requested URI is an XML namespace.
~~~~

   IANA is requested to register the following YANG module in the "YANG Module
   Names" subregistry {{!RFC6020}} within the "YANG Parameters" registry:

~~~~
   Name:  ietf-ac-common
   Namespace:  urn:ietf:params:xml:ns:yang:ietf-ac-common
   Prefix:  ac-common
   Maintained by IANA?  N
   Reference:  RFC XXXX
~~~~

--- back

# Full Tree {#AC-Common-Tree}

~~~~~~~~~~
{::include-fold ./yang/full-trees/ac-common-with-groupings.txt}
~~~~~~~~~~

# Acknowledgments
{:numbered="false"}

The document reuses many of the structures that were defined
in {{!RFC9181}} and {{?RFC9182}}.

Thanks to Ebben Aries for the YANG Doctors review, Andy Smith and Gyanh Mishra for the
rtg-dir reviews, and Watson Ladd for the sec-dir review.

Thanks to Reza Rokui for the Shepherd review.

Thanks to Mahesh Jethanandani for the AD review.
