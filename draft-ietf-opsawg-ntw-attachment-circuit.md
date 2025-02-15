---
title: "A Network YANG Data Model for Attachment Circuits"
abbrev: "A YANG Network Model for ACs"
category: std

docname: draft-ietf-opsawg-ntw-attachment-circuit-latest
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
    role: editor
    organization: Orange
    email: mohamed.boucadair@orange.com

-
    fullname: Richard Roberts
    organization: Juniper
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
    fullname: Ivan Bykov
    organization: Ribbon Communications
    email: Ivan.Bykov@rbbn.com

  -
    fullname: Qin Wu
    organization: Huawei
    email: bill.wu@huawei.com

  -
    fullname: Ogaki Kenichi
    organization: KDDI
    email: ke-oogaki@kddi.com

  -
    fullname: Luis Angel Munoz
    organization: Vodafone
    email: luis-angel.munoz@vodafone.com

normative:
  IEEE802.1Qcp:
    title: "IEEE Standard for Local and metropolitan area networks--Bridges and Bridged Networks--Amendment 30: YANG Data Model"
    author:
     -
      org: IEEE
    date: September 2018
    target: https://doi.org/10.1109/IEEESTD.2018.8467507

informative:


--- abstract

This document specifies a network model for attachment circuits. The model can be used for the provisioning of attachment circuits prior or during service provisioning (e.g., VPN, Network Slice Service). A companion service model is specified in the YANG Data Models for Bearers and 'Attachment Circuits'-as-a-Service (ACaaS) (I-D.ietf-opsawg-teas-attachment-circuit).

The module augments the base network ('ietf-network') and the Service Attachment Point (SAP) models with the detailed information for the provisioning of attachment circuits in Provider Edges (PEs).

--- middle

# Introduction

Connectivity services are provided by networks to customers via
   dedicated terminating points, such as Service Functions {{?RFC7665}},
   customer edges (CEs), peer Autonomous System Border Routers (ASBRs),
   data centers gateways, or Internet Exchange Points.

The procedure to provision a service in a service provider network may depend on the practices adopted by a service provider, including the flow put in place for the provisioning of advanced network services and how they are bound to an attachment circuit (AC). For example, the same attachment circuit may host multiple services (e.g., Layer 2 Virtual Private Network (VPN), or Layer 3 VPN, or Network Slice Service {{?RFC9543}}). In order to avoid service interference and redundant information in various locations, a service provider may expose an interface to manage ACs network-wide. Customers can then request a standalone attachment circuit to be put in place, and then refer to that attachment circuit when requesting services to be bound to that AC. {{!I-D.ietf-opsawg-teas-attachment-circuit}} specifies a data model for managing attachment circuits as a service.

{{sec-module}} specifies a network model for attachment circuits ("ietf-ac-ntw"). The model can be used for the provisioning of ACs in a provider network prior or during service provisioning. For example, {{?I-D.ietf-opsawg-ac-lxsm-lxnm-glue}} specifies augmentations to the L2VPN Network Model (L2NM) {{!RFC9291}} and the L3VPN Network Model (L3NM) {{!RFC9182}} to bind LxVPNs to ACs that are provisioned using the procedure defined in this document.

The document leverages {{!RFC9182}} and {{!RFC9291}} by adopting an AC provisioning structure that uses data nodes that are defined in those RFCs. Some refinements were introduced to cover not only conventional service provider networks, but also specifics of other target deployments (e.g., cloud network).

The AC network model is designed as augmentations of both the 'ietf-network' model {{!RFC8345}} and the Service Attachment Point (SAP) model {{!RFC9408}}. An attachment circuit can be bound to a single or multiple SAPs. Likewise, the model is designed to accommodate deployments where a SAP can be bound to one or multiple ACs (e.g., a parent AC and its child ACs).

~~~~ aasvg
{::include-fold ./figures/ac-ntw-example.txt}
~~~~
{: #sap-ac-ntw title="Attachment Circuits Examples" artwork-align="center"}

The AC network model uses the AC common model defined in {{!I-D.ietf-opsawg-teas-common-ac}}.

The YANG 1.1 {{!RFC7950}} data model in this document conforms to the Network Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

Sample examples are provided in {{sec-examples}}.

## Editorial Note (To be removed by RFC Editor)

Note to the RFC Editor: This section is to be removed prior to publication.

This document contains placeholder values that need to be replaced with finalized values at the time of publication. This note summarizes all of the substitutions that are needed.

Please apply the following replacements:

* CCCC --> the assigned RFC number for {{!I-D.ietf-opsawg-teas-common-ac}}
* SSSS --> the assigned RFC number for {{!I-D.ietf-opsawg-teas-attachment-circuit}}
* XXXX --> the assigned RFC number for this I-D
* 2025-01-07 --> the actual date of the publication of this document

# Conventions and Definitions

{::boilerplate bcp14-tagged}

The reader should be familiar with the terms defined in {{Section 2 of !RFC9408}}.

This document uses the term "network model" as defined in {{Section 2.1 of ?RFC8969}}.

The meanings of the symbols in the YANG tree diagrams are defined in {{?RFC8340}}.

LxSM refers to both the Layer 2 Service Model (L2SM) {{?RFC8466}} and the Layer 3 Service Model (L3SM) {{?RFC8299}}.

LxNM refers to both the L2VPN Network Model (L2NM) {{!RFC9291}} and the L3VPN Network Model (L3NM) {{!RFC9182}}.

LxVPN refers to both L2VPN and L3VPN.

The following are used in the module prefixes:

ac:
: Attachment circuit

ntw:
: Network

sap:
: Service Attchment Point

svc:
: Service

In addition, this document uses the following terms:

Bearer:
: A physical or logical link that connects a customer node (or site) to a provider network.
: A bearer can be a wireless or wired link. One or multiple technologies can be used to build a bearer. The bearer type can be specified by a customer.
: The operator allocates a unique bearer reference to identify a bearer within its network (e.g., customer line identifier). Such a reference can be retrieved by a customer and then used in subsequent service placement requests to unambiguously identify where a service is to be bound.
: The concept of bearer can be generalized to refer to the required underlying connection for the provisioning of an attachment circuit.
: One or multiple attachment circuits may be hosted over the same bearer (e.g., multiple Virtual Local Area Networks (VLANs) on the same bearer that is provided by a physical link).

Network controller:
: Denotes a functional entity responsible for the management of the service provider network. One or multiple network controllers can be deployed in a service provider network.

Service orchestrator:
: Refers to a functional entity that interacts with the customer of a network service.
: A service orchestrator is typically responsible for the attachment circuits, the Provider Edge (PE) selection, and requesting the activation of the requested services to a network controller.
: A service orchestrator may interact with one or more network controllers.

Service provider network:
: A network that is able to provide network services (e.g., LxVPN or Network Slice Services).

Service provider:
: An entity that offers network services (e.g., LxVPN or Network Slice Services).

The names of data nodes are prefixed using the prefix associated with the corresponding imported YANG module as shown in {{pref}}:

|Prefix|	Module| Reference |
| ac-common|ietf-ac-common| RFC CCCC|
| ac-svc|ietf-ac-svc|Section 5.2 of RFC SSSS|
| dot1q-types|ieee802-dot1q-types|{{IEEE802.1Qcp}}|
| if|ietf-interfaces| {{!RFC8343}}|
| inet|ietf-inet-types| {{Section 4 of !RFC6991}}|
| key-chain|ietf-key-chain| {{!RFC8177}}|
| nacm|ietf-netconf-acm|{{!RFC8341}}|
| nw|ietf-network| {{!RFC8345}}|
| rt-types|ietf-routing-types| {{!RFC8294}}|
| rt-pol|ietf-routing-policy| {{!RFC9067}}|
| sap|ietf-sap-ntw| {{!RFC9408}}|
| vpn-common|ietf-vpn-common|{{!RFC9181}}|
{: #pref title="Modules and Their Associated Prefixes"}

# Relationship to Other AC Data Models

{{ac-overview}} depicts the relationship between the various AC data models:

* "ietf-ac-common" ({{?I-D.ietf-opsawg-teas-common-ac}})
* "ietf-bearer-svc" ({{Section 5.1 of ?I-D.ietf-opsawg-teas-attachment-circuit}})
* "ietf-ac-svc" ({{Section 5.2 of ?I-D.ietf-opsawg-teas-attachment-circuit}})
* "ietf-ac-ntw" ({{sec-module}})
* "ietf-ac-glue" ({{?I-D.ietf-opsawg-ac-lxsm-lxnm-glue}})

~~~~ aasvg
                ietf-ac-common
                 ^     ^     ^
                 |     |     |
      .----------'     |     '----------.
      |                |                |
      |                |                |
ietf-ac-svc <--- ietf-bearer-svc        |
   ^    ^                               |
   |    |                               |
   |    '------------------------ ietf-ac-ntw
   |                                    ^
   |                                    |
   |                                    |
   '------------ ietf-ac-glue ----------'

X --> Y: X imports Y
~~~~
{: #ac-overview title="AC Data Models" artwork-align="center"}

The "ietf-ac-common" module is imported by the "ietf-bearer-svc", "ietf-ac-svc", and "ietf-ac-ntw" modules. Bearers managed using the "ietf-bearer-svc" module may be referenced by service ACs managed using the "ietf-ac-svc" module. Similarly, a bearer managed using the "ietf-bearer-svc" module may list the set of ACs that use that bearer. To facilitate correlation between an AC service request and the actual AC provisioned in the network, "ietf-ac-ntw" leverages the AC references exposed by the "ietf-ac-svc" module. Furthermore, to bind Layer 2 VPN or Layer 3 VPN services with ACs, the "ietf-ac-glue" module augments the LxSM and LxNM with AC service references exposed by the "ietf-ac-svc" module and AC network references exposed by the "ietf-ac-ntw" module.

# Sample Uses of the Attachment Circuit Data Models

## ACs Terminated by One or Multiple Customer Edges (CEs)

{{uc}} depicts a sample target topology that involve ACs:

* ACs are terminated by a SAP at the network side. See {{sap-ac-ntw}} for an example of SAPs within a PE.

* A CE can be either a physical device or a logical entity. Such logical entity is typically a software component (e.g., a virtual service function that is hosted within the provider's network or a third-party infrastructure). A CE is seen by the network as a peer SAP {{?RFC9408}}.

* CEs may be either dedicated to one single connectivity service or host multiple connectivity services (e.g., CEs with roles of service functions {{?RFC7665}}).

* A network provider may bind a single AC to one or multiple peer SAPs (e.g., CE1 and CE2 are tagged as peer SAPs for the same AC). For example, and as discussed in {{?RFC4364}}, multiple CEs can be attached to a PE over the same attachment circuit. This scenario is typically implemented when the Layer 2 infrastructure between the CE and the network is a multipoint service.

* A single CE may terminate multiple ACs, which can be associated with the same bearer or distinct bearers (e.g., CE4).

* Customers may request protection schemes in which the ACs associated with their endpoints are terminated by the same PE (e.g., CE3), distinct PEs (e.g., CE4), etc. The network provider uses this request to decide where to terminate the AC in the service provider network and also whether to enable specific capabilities (e.g., Virtual Router Redundancy Protocol (VRRP)).

The "ietf-ac-ntw" is a network model that is used to manage the PE side of ACs at a provider network.

~~~~ aasvg
{::include ./figures/acs-examples.txt}
~~~~
{: #uc title='Examples of ACs' artwork-align="center"}

## Positioning the AC Network Model in the Overall Service Delivery Process

{{u-ex}} shows the positioning of the AC network model in the overall service delivery process. The "ietf-ac-ntw" module is a network model which augments the SAP with a comprehensive set of parameters to reflect the attachment circuits that are in place in a network. The model also maintains the mapping with the service references that are used to expose those ACs to customer using the 'ietf-ac-svc' module defined in {{!I-D.ietf-opsawg-teas-attachment-circuit}}. Whether the same naming conventions to reference an AC are used in the service and network layers is deployment-specific.

~~~~ aasvg
{::include-fold ./figures/arch.txt}
~~~~
{: #u-ex title="An Example of the Network AC Model Usage" artwork-align="center"}

Similar to {{!RFC9408}}, the "ietf-ac-ntw" module can be used for both User-to-Network Interface (UNI) and
Network-to-Network Interface (NNI). For example, all the ACs shown in {{fig-inter-pn}} have a 'role' set
to 'ietf-sap-ntw:nni'. Typically, ASBRs of each network are directly
connected to ASBRs of a neighboring network via one or multiple links (bearers). ASBRs of "Network#1" behave as a PE and treat the other adjacent ASBRs as if it were a CE.

~~~~ aasvg
{::include-fold ./figures//ntw/inter-pn.txt}
~~~~
{: #fig-inter-pn title="An Example of the Network AC Model Usage Between Provider Networks" artwork-align="center"}

# Description of the Attachment Circuit YANG Module

The full tree diagram of the "ietf-ac-ntw" module is provided in {{AC-Ntw-Tree}}. Subtrees are provided in the following subsections
for the reader's convenience.

## Overall Structure of the Module

The overall tree structure of the "ietf-ac-ntw" module is shown in {{o-ntw-tree}}.

~~~~
augment /nw:networks/nw:network:
  +--rw specific-provisioning-profiles
  |  ...
  +--rw ac-profile* [name]
     ...
augment /nw:networks/nw:network/nw:node:
  +--rw ac* [name]
     +--rw name                 string
     +--rw svc-ref?             ac-svc:attachment-circuit-reference
     +--rw profile* [ac-profile-ref]
     |  +--rw ac-profile-ref    leafref
     |  +--rw network-ref?      -> /nw:networks/network/network-id
     +--rw parent-ref
     |  +--rw ac-ref?        leafref
     |  +--rw node-ref?      leafref
     |  +--rw network-ref?   -> /nw:networks/network/network-id
     +--ro child-ref
     |  +--ro ac-ref*        leafref
     |  +--ro node-ref?      leafref
     |  +--ro network-ref?   -> /nw:networks/network/network-id
     +--rw peer-sap-id*         string
     +--rw group* [group-id]
     |  +--rw group-id      string
     |  +--rw precedence?   identityref
     +--rw status
     |  +--rw admin-status
     |  |  +--rw status?        identityref
     |  |  +--ro last-change?   yang:date-and-time
     |  +--ro oper-status
     |     +--ro status?        identityref
     |     +--ro last-change?   yang:date-and-time
     +--rw description?         string
     +--rw l2-connection  {ac-common:layer2-ac}?
     |  ...
     +--rw ip-connection  {ac-common:layer3-ac}?
     |  ...
     +--rw routing-protocols
     |  ...
     +--rw oam
     |  ...
     +--rw security
     |  ...
     +--rw service
        ...
  augment /nw:networks/nw:network/nw:node/sap:service/sap:sap:
    +--rw ac* [ac-ref]
       +--rw ac-ref         leafref
       +--rw node-ref?      leafref
       +--rw network-ref?   -> /nw:networks/network/network-id
~~~~
{: #o-ntw-tree title="Overall Tree Structure"}

A node can host one or more SAPs. Per {{!RFC9408}}, a SAP is an abstraction of the network
reference point (the PE side of an AC, in the context of this document) where network services can be delivered and/or are delivered to customers. Each SAP terminates one or multiple ACs. Each AC in turn may be terminated by one or more peer SAPs ('peer-sap'). In order to expose such AC/SAP binding information, the SAP model {{!RFC9408}} is augmented with required AC-related information.

Unlike the AC service model {{!I-D.ietf-opsawg-teas-attachment-circuit}}, an AC is uniquely identified by a name within the scope of a node, not a network. A textual description of the AC may be provided ('description').

Also, in order to ease the correlation between the AC exposed at the service layer and the AC that is actually provisioned in the network operation, a reference to the AC exposed to the customer ('svc-ref') is stored in the "ietf-ac-ntw" module.

ACs that are terminated by a SAP are listed in the 'ac' container under '/nw:networks/nw:network/nw:node/sap:service/sap:sap'. A controller may indicate a filter based on the service type (e.g., Network Slice or L3VPN) to retrieve the list of available SAPs, and thus ACs, for that service.

In order to factorize common data that is provisioned for a group of ACs, a set of profiles ({{sec-profiles}}) can be defined at the network level, and then called under the node level. The information contained in a profile is thus inherited, unless the corresponding data node is refined at the AC level. In such a case, the value provided at the AC level takes precedence over the global one.

In contexts where the same AC is terminated by multiple peer SAPs (e.g., an AC with multiple CEs) but a subset of them have specific information, the module allows operators to:

* Define a parent AC that may list all these CEs as peer SAPs.
* Create individual ACs that are bound to the parent AC using 'parent-ref'.
* Indicate for each individual AC one or a subset of the CEs as peer SAPs. All these individual ACs will inherit the properties of the parent AC.

Whenever a parent AC is deleted, then all child ACs of that AC MUST be deleted. Child ACs are referenced using 'child-ref'.

An AC may belong to one or multiple groups {{!RFC9181}}. For example, the 'group-id' is used to associate redundancy or protection constraints with ACs.

The status of an AC can be tracked using 'status'. Both operational status and administrative status are maintained. A mismatch between the administrative status vs. the operational status can be used as a trigger to detect anomalies.

An AC can be characterized using Layer 2 connectivity ({{sec-l2}}), Layer 3 connectivity ({{sec-l3}}), routing protocols ({{sec-rtg}}), Operations, Administration, and Maintenance (OAM) ({{sec-oam}}), security ({{sec-sec}}), and service ({{sec-svc}}) considerations. Features are used to tag conditional protions to accomodate various deployments (support of layer 2 ACs, Layer 3 ACs, IPv4, IPv6, routing protocols, BFD, etc.).

## References

The AC network module defines a set of groupings depicted in {{references-tree}} for referencing purposes. These references are used within or outside the AC network module. The use of such groupings is consistent with the design in {{!RFC8345}}.

~~~~
{::include ./yang/subtrees/ac-ntw/references.txt}
~~~~
{: #references-tree title="References Groupings"}

The groupings shown in {{references-tree}} contain the information necessary to reference:

* an attachment circuit that is terminated by a specific node in a given network,
* an attachment circuit profile of a specific network ({{sec-profiles}}), and
* specific provisioning profiles that are bound to a specific network ({{sec-profiles}}).

## Provisioning Profiles {#sec-profiles}

The AC and specific provisioning profiles tree structure is shown in {{profiles-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/profiles-tree.txt}
~~~~
{: #profiles-tree title="Profiles Tree Structure"}

Similar to {{!RFC9182}} and {{!RFC9291}}, the exact definition of the specific provisioning profiles is local to each service provider. The model only includes an identifier for these profiles in order to ease identifying and binding local policies when building an AC. As shown in {{profiles-tree}}, the following identifiers can be included:

'encryption-profile-identifier':
: An encryption profile refers to a set of policies related to the encryption schemes and setup that can be applied on the AC. See also {{sec-sec}}.

'qos-profile-identifier':
: A Quality of Service (QoS) profile refers to a set of policies such as classification, marking, and actions (e.g., {{?RFC3644}}). See also {{sec-svc}}.

'failure-detection-profile-identifier':
: A failure detection profile refers to a set of failure detection policies such as Bidirectional Forwarding Detection (BFD) policies {{!RFC5880}} that can be invoked when building an AC. Such a profile can be, for example, referenced in static routes ({{sec-static-rtg}}) or under the OAM level ({{sec-oam}}). The use of this profile is similar to the detailed examples depicted in Appendices A.11.3 and A.12 of {{?I-D.ietf-opsawg-teas-attachment-circuit}}.

'forwarding-profile-identifier':
: A forwarding profile refers to the policies that apply to the forwarding of packets conveyed over an AC. Such policies may consist of, for example, applying Access Control Lists (ACLs) as in {{sec-svc}}.

'routing-profile-identifier':
: A routing profile refers to a set of routing policies that will be invoked (e.g., BGP policies) for an AC. Refer to {{sec-rtg}}.

The 'ac-profile' defines parameters that can be factorized among a set of ACs. Each profile is identified by 'name' that is unique in a network. Some of the data nodes can be adjusted at the node level. These adjusted values take precedence over the values in the profile.

## L2 Connection {#sec-l2}

The 'l2-connection' container is used to manage the Layer 2 properties of an AC (mainly, the PE side of an AC). The Layer 2 connection tree structure is shown in {{l2-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/l2-tree.txt}
~~~~
{: #l2-tree title="Layer 2 Connection Tree Structure"}

The 'encapsulation' container specifies the Layer 2 encapsulation to use (if any) and allows the configuration of the relevant tags. Also, the model supports tag manipulation operations (e.g., tag rewrite).

The 'l2-tunnel-service' container is used to specify the required parameters to set a Layer 2 tunneling service (e.g., a Virtual Private LAN Service (VPLS), a Virtual eXtensible Local Area Network (VXLAN), or a pseudowire ({{Section 6.1 of !RFC8077}})). 'l2vpn-id' is used to identify a L2VPN service that is associated with an Integrated Routing and Bridging (IRB) interface.

Specific Layer 2 sub-interfaces may be required to be configured in some implementations/deployments. Such a Layer-2-specific interface can be included in 'l2-termination-point'.

To accommodate implementations that require internal bridging, a local bridge reference can be specified in 'local-bridge-reference'. Such a reference may be a local bridge domain.

A reference to the bearer used by this AC is maintained using 'bearer-reference'.

## IP Connection {#sec-l3}

This 'ip-connection' container is used to group Layer 3 connectivity information, particularly the IP addressing information, of an AC.

The  Layer 3 connection tree structure is shown in {{l3-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/l3-tree.txt}
~~~~
{: #l3-tree title="IP Connection Tree Structure"}

A distinct Layer 3 interface other than the interface indicated under the 'l2-connection' container may be needed to terminate the Layer 3 connectivity. The identifier of such an interface is included in 'l3-termination-point'. For example, this data node can be used to carry the identifier of a bridge domain interface.

This container can include IPv4, IPv6, or both if dual-stack is enabled. For both IPv4 and IPv6, the IP connection supports three IP address assignment modes for customer addresses: provider DHCP, DHCP relay, and static addressing. Note that for the IPv6 case, Stateless Address Autoconfiguration (SLAAC) {{?RFC4862}} can be used.

For both IPv4 and IPv6, 'address-allocation-type' is used to indicate the IP address allocation mode to activate for an AC. The allocated address represents the PE interface address configuration. When 'address-allocation-type' is set to 'provider-dhcp', DHCP assignments can be made locally or by an external DHCP server. Such behavior is controlled by setting 'dhcp-service-type'.

For IPv6, if 'address-allocation-type' is set to 'slaac', the Prefix Information option of Router Advertisements that will be issued for SLAAC purposes will carry the IPv6 prefix that is determined by 'local-address' and 'prefix-length'. For example, if 'local-address' is set to '2001:db8:0:1::1' and 'prefix-length' is set to '64', the IPv6 prefix that will be used is '2001:db8:0:1::/64'.

In some deployment contexts (e.g., network merging), multiple IP subnets may be used in a transition period. For such deployments, multiple ACs (typically, two) with overlapping information may be maintained during a transition period. The correlation between these ACs may rely upon the same 'svc-ref'.

## Routing {#sec-rtg}

The overall routing subtree structure is shown in {{rtg-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/rtg-tree.txt}
~~~~
{: #rtg-tree title="Routing Tree Structure"}

Multiple routing instances ('routing-protocol') can be defined, each uniquely identified
by an 'id'. Specifically, each instance is uniquely identified to accommodate scenarios
where multiple instances of the same routing protocol have to be configured on the same AC.

The type of a routing instance is indicated in 'type'.
The values of this attribute are those defined in {{!RFC9181}} (the
'routing-protocol-type' identity). Specific data nodes are then provided
as a function of the 'type'. See more details in the following subsections.

One or multiple routing profiles ('routing-profile') can be provided for
a given routing instance.

### Static Routing {#sec-static-rtg}

The static routing subtree structure is shown in {{static-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/static-tree.txt}
~~~~
{: #static-tree title="Static Routing Tree Structure"}

The following data nodes can be defined for a given IP prefix:

'lan-tag':
: Indicates a local tag (e.g., "myfavorite-lan") that is used to enforce local policies.

'next-hop':
: Indicates the next hop to be used for the static route.
: It can be identified by an IP address, a predefined next-hop type (e.g., 'discard' or 'local-link'), etc.

'bfd':
: Indicates whether BFD is enabled or disabled for this static route entry. A BFD profile may also be provided.

'metric':
: Indicates the metric associated with the static route entry. This metric is used when the route is exported into an IGP.

'preference':
: Indicates the preference associated with the static route entry.
: This preference is used to select a preferred route among routes to the same destination prefix.

'status':
: Used to convey the status of a static route entry. This data node can also be used to control the (de)activation of individual static route entries.

### BGP {#sec-bgp-rtg}

The BGP routing subtree structure is shown in {{bgp-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/bgp-tree.txt}
~~~~
{: #bgp-tree title="BGP Routing Tree Structure"}

The following data nodes are supported for each 'peer-group':

'name':
: Defines a name for the peer group.

'local-address':
: Specifies an address or a reference to an interface to use when establishing the BGP transport session.

'description':
:  Includes a description of the peer group.

'apply-policy':
: Lists a set of import/export policies {{!RFC9067}} to apply for this group.

'local-as':
: Indicates a local AS Number (ASN).

'peer-as':
:  Indicates the peer's ASN.

'address-family':
:  Indicates the address family of the peer.  It can
      be set to 'ipv4', 'ipv6', or 'dual-stack'.
: This address family might be used together with the service type that uses an AC (e.g., 'vpn-type' {{?RFC9182}}) to derive the appropriate Address Family Identifiers (AFIs) / Subsequent Address Family Identifiers (SAFIs) that will be part of the derived device configurations (e.g., unicast IPv4 MPLS L3VPN (AFI,SAFI = 1,128) as defined in {{Section 4.3.4 of !RFC4364}}).

'role':
: Specifies the BGP role in a session. Role values are taken	from the list defined in {{Section 4 of ?RFC9234}}.

'multihop':
:  Indicates the number of allowed IP hops to reach a BGP peer.

'as-override':
:  If set, this parameter indicates whether ASN override
      is enabled, i.e., replacing the ASN of the customer specified in
      the AS_PATH BGP attribute with the ASN identified in the 'local-
      as' attribute.

'allow-own-as':
:  Used in some topologies (e.g., hub-and-spoke) to
      allow the provider's ASN to be included in the AS_PATH BGP
      attribute received from a peer.  Loops are prevented by setting
      'allow-own-as' to a maximum number of the provider's ASN
      occurrences.  By default, this parameter is set to '0' (that is,
      reject any AS_PATH attribute that includes the provider's ASN).

'prepend-global-as':
:  When distinct ASNs are configured at the
      node and AC levels, this parameter controls whether
      the ASN provided at the node level is prepended to the AS_PATH
      attribute.

'send-default-route':
:  Controls whether default routes can be advertised to the peer.

'site-of-origin':
:  Meant to uniquely identify the set of routes
      learned from a site via a particular AC.  It is used
      to prevent routing loops ({{Section 7 of !RFC4364}}).  The Site of
      Origin attribute is encoded as a Route Origin Extended Community.

'ipv6-site-of-origin':
: Carries an IPv6 Address Specific BGP Extended
      Community that is used to indicate the Site of Origin {{!RFC5701}}.  It is used to prevent routing loops.

'redistribute-connected':
:  Controls whether the AC is advertised to other PEs.

'bgp-max-prefix':  Controls the behavior when a prefix maximum is
      reached.

   'max-prefix':
   : Indicates the maximum number of BGP prefixes
         allowed in a session for this group.  If the limit is reached, the
         action indicated in 'violate-action' will be followed.

   'warning-threshold':
   : A warning notification is triggered when this limit is reached.

   'violate-action':
   : Indicates which action to execute when the
         maximum number of BGP prefixes is reached.  Examples of such
         actions include sending a warning message, discarding extra
         paths from the peer, or restarting the session.

   'restart-timer':
   : Indicates, in seconds, the time interval after
      which the BGP session will be reestablished.

'bgp-timers':
:  Two timers can be captured in this container: (1)
      'hold-time', which is the time interval that will be used for the
      Hold Timer ({{Section 4.2 of !RFC4271}}) when establishing a BGP
      session and (2) 'keepalive', which is the time interval for the
      KeepaliveTimer between a PE and a BGP peer ({{Section 4.4 of !RFC4271}}).
:  Both timers are expressed in seconds.

'bfd':
: Indicates whether BFD is enabled or disabled for this nighbor. A BFD profile to apply may also be provided.

'authentication':
:  The module adheres to the recommendations in
      {{Section 13.2 of !RFC4364}}, as it allows enabling the TCP
      Authentication Option (TCP-AO) {{!RFC5925}} and accommodates the
      installed base that makes use of MD5.
: This version of the model assumes that parameters specific to the
      TCP-AO are preconfigured as part of the key chain that is
      referenced in the model.  No assumption is made about how such a
      key chain is preconfigured.  However, the structure of the key
      chain should cover data nodes beyond those in {{!RFC8177}}, mainly
      SendID and RecvID ({{Section 3.1 of !RFC5925}}).

For each neighbor, the following data nodes are supported in addition to similar parameters that are provided for a peer group:

'remote-address':
: Specifies the remote IP address of a BGP neighbor.

'peer-group':
: A name of a peer group.
: Parameters that are provided at the 'neighbor' level takes precedence over the ones provided in the peer group.

'status':
: Indicates the status of the BGP session.

### OSPF {#sec-ospf-rtg}

The OSPF routing subtree structure is shown in {{ospf-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/ospf-tree.txt}
~~~~
{: #ospf-tree title="OSPF Routing Tree Structure"}

The following OSPF data nodes are supported:

'address-family':
:  Indicates whether IPv4, IPv6, or both address
      families are to be activated.
: When the IPv4 or dual-stack address family is requested, it is up
      to the implementation (e.g., network orchestrator) to decide
      whether OSPFv2 {{!RFC4577}} or OSPFv3 {{!RFC6565}} is used to announce
      IPv4 routes.

'area-id':
:  Indicates the OSPF Area ID.

'metric':
:  Associates a metric with OSPF routes.

'sham-links':
:  Used to create OSPF sham links between two ACs sharing the same area and having a backdoor link
      ({{Section 4.2.7 of !RFC4577}} and {{Section 5 of !RFC6565}}).

'max-lsa':
:  Sets the maximum number of Link State Advertisements
      (LSAs) that the OSPF instance will accept.

'passive':
:  Controls whether an OSPF interface is passive or active.

'authentication':
:  Controls the authentication schemes to be enabled
      for the OSPF instance. The module supports authentication options that are common to both OSPF versions: the Authentication
      Trailer for OSPFv2 {{!RFC5709}} {{!RFC7474}} and OSPFv3 {{!RFC7166}}; as such, the model does not support {{?RFC4552}}.

'status':
:  Indicates the status of the OSPF routing instance.

### IS-IS {#sec-isis-rtg}

The IS-IS routing subtree structure is shown in {{isis-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/isis-tree.txt}
~~~~
{: #isis-tree title="IS-IS Routing Tree Structure"}

The following IS-IS data nodes are supported:

'address-family':
:  Indicates whether IPv4, IPv6, or both address families are to be activated.

'area-address':
: Indicates the IS-IS area address.

'level':
:  Indicates the IS-IS level: Level 1, Level 2, or both.

'metric':
:  Associates a metric with IS-IS routes.

'passive':
:  Controls whether an IS-IS interface is passive or active.

'authentication':
:  Controls the authentication schemes to be enabled
   for the IS-IS instance.  Both the specification of a key chain
   {{!RFC8177}} and the direct specification of key and authentication
   algorithms are supported.

'status':
:  Indicates the status of the IS-IS routing instance.

### RIP {#sec-rip-rtg}

The RIP routing subtree structure is shown in {{rip-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/rip-tree.txt}
~~~~
{: #rip-tree title="RIP Routing Tree Structure"}

The following RIP data nodes are supported:

'address-family':
:  Indicates whether IPv4, IPv6, or both address
      families are to be activated.  This parameter is used to determine
      whether RIPv2 {{!RFC2453}}, RIP Next Generation (RIPng) {{!RFC2080}}, or both are
      to be enabled.

'timers':
:  Indicates the following timers (expressed in seconds):

      * 'update-interval':
      :  The interval at which RIP updates are sent.

      * 'invalid-interval':
      :  The interval before a RIP route is declared invalid.

      * 'holddown-interval':
      : The interval before better RIP routes are released.

      * 'flush-interval':
      :  The interval before a route is removed from the routing table.

'default-metric':
:  Sets the default RIP metric.

'authentication':
:  Controls the authentication schemes to be enabled for the RIP instance.

'status':
:  Indicates the status of the RIP routing instance.

### VRRP {#sec-VRRP-rtg}

The VRRP subtree structure is shown in {{vrrp-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/vrrp-tree.txt}
~~~~
{: #vrrp-tree title="VRRP Tree Structure"}

The following VRRP data nodes are supported:

'address-family':
:  Indicates whether IPv4, IPv6, or both address
      families are to be activated.  Note that VRRP version 3 {{!RFC9568}}
      supports both IPv4 and IPv6.

'vrrp-group':
:  Used to identify the VRRP group.

'backup-peer':
:  Carries the IP address of the peer.

'virtual-ip-address':
:  Includes virtual IP addresses for a single VRRP group.

'priority':
:  Assigns the VRRP election priority for the backup virtual router.

'ping-reply':
:  Controls whether the VRRP speaker should reply to ping requests.

'status':
:  Indicates the status of the VRRP instance.

Note that no authentication data node is included for VRRP, as there
isn't any type of VRRP authentication at this time (see {{Section 9 of !RFC9568}}).

## OAM {#sec-oam}

The OAM subtree structure is shown in {{oam-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/oam-tree.txt}
~~~~
{: #oam-tree title="OAM Tree Structure"}

The following OAM data nodes can be specified for each BFD session:

'dest-addr':
: Specifies the BFD peer address. This data node is mapped to 'remote-address' of BFD container in {{I-D.ietf-opsawg-teas-attachment-circuit}}. 'dest-address' is used here to ease the mapping with the underlying device model defind in {{?RFC9127}}.

'source-address':
: Specifies the local IP address or interface to use for the session. This data node is mapped to 'local-address' of BFD container in {{I-D.ietf-opsawg-teas-attachment-circuit}}. 'source-address' is used here to ease the mapping with the underlying device model defind in {{?RFC9127}}.

'failure-detection-profile-ref':
: Refers to a BFD profile in {{sec-profiles}}.

'network-ref':
: Includes a network reference to uniquely identify a BFD profile.

'session-type':
: Indicates which BFD flavor is used to set up the session (e.g., classic BFD {{!RFC5880}}, Seamless BFD {{?RFC7880}}). By default, it is assumed that the BFD session will follow the behavior specified in {{!RFC5880}}.

'desired-min-tx-interval':
: The minimum interval, in microseconds, to use when transmitting BFD Control packets, less any jitter applied.

'required-min-rx-interval':
: The minimum interval, in microseconds, between received BFD Control packets less any jitter applied by the sender.

'local-multiplier':
: The negotiated transmit interval, multiplied by this value, provides the detection time for the peer.

'holdtime':
: Used to indicate the expected BFD holddown time, in milliseconds.

'authentication':
: Includes the required information to enable the BFD authentication modes discussed in {{Section 6.7 of !RFC5880}}. In particular, 'meticulous' controls the activation of meticulous mode as discussed in Sections 6.7.3 and 6.7.4 of {{!RFC5880}}.

'status':
: Indicates the status of BFD.


## Security {#sec-sec}

The security subtree structure is shown in {{sec-tree}}. The 'security' container specifies the the encryption to be applied to traffic for a given AC. The model can be used to directly control the encryption to be applied (e.g., Layer 2 or Layer 3 encryption) or invoke a local encryption profile.

~~~~
{::include ./yang/subtrees/ac-ntw/security-tree.txt}
~~~~
{: #sec-tree title="Security Tree Structure"}

## Service {#sec-svc}

The service subtree structure is shown in {{svc-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/service-tree.txt}
~~~~
{: #svc-tree title="Service Tree Structure"}

The description of the service data nodes is as follows:

'mtu':
: Specifies the Layer 2 MTU, in bytes, for the AC.

'svc-pe-to-ce-bandwidth' and 'svc-ce-to-pe-bandwidth':
: Specify the service bandwidth for the AC.

: 'svc-pe-to-ce-bandwidth' indicates the inbound bandwidth of the connection (i.e., download bandwidth from the service provider to the site).

: 'svc-ce-to-pe-bandwidth' indicates the outbound bandwidth of the connection (i.e., upload bandwidth from the site to the service provider).

: 'svc-pe-to-ce-bandwidth' and 'svc-ce-to-pe-bandwidth' can be represented using the Committed Information Rate (CIR), the Committed Burst Size (CBS), the Excess Information Rate (EIR), the Excess Burst Size (EBS), the Peak Information Rate (PIR), and the Peak Burst Size (PBS). CIR, EIR, and PIR are expressed in bps, while CBS, EBS, and PBS are expressed in bytes.

: The following types, defined in {{!RFC9181}}, can be used to indicate the bandwidth type:

  'bw-per-cos':
  : The bandwidth is per CoS.

  'bw-per-port':
  : The bandwidth is per port.

   'bw-per-site':
   : The bandwidth is to all peer SAPs that belong to the same site.

   'bw-per-service':
   : The bandwidth is per service instance that is bound to an AC.

'qos':
: Specifies a list of QoS profiles to apply for this AC.

'access-control-list':
: Specifies a list of ACL profiles to apply for this AC.

#  YANG Module {#sec-module}

This module uses types defined in {{!RFC6991}}, {{!RFC8177}}, {{!RFC8294}}, {{!RFC8343}}, {{!RFC9067}}, {{!RFC9181}}, {{!I-D.ietf-opsawg-teas-common-ac}}, and {{IEEE802.1Qcp}}.

~~~~ yang
<CODE BEGINS> file "ietf-ac-ntw@2025-01-07.yang"
{::include-fold ./yang/ietf-ac-ntw.yang}
<CODE ENDS>
~~~~


# Security Considerations

This section is modeled after the template described in in {{Section 3.7 of ?I-D.ietf-netmod-rfc8407bis}}.

The "ietf-ac-ntw" YANG module defines a data model that is
designed to be accessed via YANG-based management protocols, such as
   NETCONF {{?RFC6241}} and RESTCONF {{?RFC8040}}. These protocols have to
   use a secure transport layer (e.g., SSH {{?RFC4252}}, TLS {{?RFC8446}}, and
   QUIC {{?RFC9000}}) and have to use mutual authentication.

   The Network Configuration Access Control Model (NACM) {{!RFC8341}}
   provides the means to restrict access for particular NETCONF or
   RESTCONF users to a preconfigured subset of all available NETCONF or
   RESTCONF protocol operations and content.

   There are a number of data nodes defined in this YANG module that are
   writable/creatable/deletable (i.e., config true, which is the
   default).  These data nodes may be considered sensitive or vulnerable
   in some network environments.  Write operations (e.g., edit-config)
   and delete operations to these data nodes without proper protection
   or authentication can have a negative effect on network operations.
   Specifically, the following
subtrees and data nodes have particular sensitivities/vulnerabilities:

   'specific-provisioning-profiles':
   : This container includes a set of sensitive data that
      influence how an AC is delivered.  For example, an
      attacker who has access to these data nodes may be able to
      manipulate routing policies, QoS policies, or encryption
      properties. These data nodes are defined with "nacm:default-deny-
      write" tagging {{I-D.ietf-opsawg-teas-common-ac}}.

   'ac':
   : An attacker who is able to access network nodes can
      undertake various attacks, such as modify the attributes of an AC (e.g.,
      QoS, bandwidth, routing protocols, keying material), leading to
      malfunctioning of services that are delivered over that AC and therefore to Service Level
      Agreement (SLA) violations.  In addition, an attacker could
      attempt to add a new AC.
    : In addition to using NACM to prevent unauthorized access, such
      activity can be detected by adequately monitoring and tracking
      network configuration changes.

   Some of the readable data nodes in this YANG module may be considered
   sensitive or vulnerable in some network environments.  It is thus
   important to control read access (e.g., via get, get-config, or
   notification) to these data nodes. Specifically, the following
subtrees and data nodes have particular sensitivities/vulnerabilities:

   'ac':
   : Unauthorized access to this subtree can disclose the identity
      of a customer 'peer-sap-id'.

   'l2-connection' and 'ip-connection':
   :  An attacker can retrieve
      privacy-related information, which can be used to track a
      customer.  Disclosing such information may be considered a
      violation of the customer-provider trust relationship.

   'keying-material' and 'customer-key-chain':
   :  An attacker can retrieve the cryptographic keys
      protecting an AC (routing, in particular). These keys could
      be used to inject spoofed routing  advertisements.

Several data nodes ('bgp', 'ospf', 'isis', 'rip', and 'customer-key-chain') rely upon {{!RFC8177}} for authentication purposes. As such, the AC network module inherits the security considerations discussed in {{Section 5 of !RFC8177}}. Also, these data nodes support supplying explicit keys as strings in ASCII format. The use of keys in hexadecimal string format would afford greater key entropy with the same number of key-string octets. However, such a format is not included in this version of the AC network model, because it is not supported by the underlying device modules (e.g., {{?RFC8695}}).

{{sec-sec}} specifies the the encryption to be applied to traffic for a given AC.

# IANA Considerations

   IANA is requested to register the following URI in the "ns" subregistry within
   the "IETF XML Registry" {{!RFC3688}}:

~~~~
   URI:  urn:ietf:params:xml:ns:yang:ietf-ac-ntw
   Registrant Contact:  The IESG.
   XML:  N/A; the requested URI is an XML namespace.
~~~~

   IANA is requested to register the following YANG module in the "YANG Module
   Names" subregistry {{!RFC6020}} within the "YANG Parameters" registry:

~~~~
   Name:  ietf-ac-ntw
   Namespace:  urn:ietf:params:xml:ns:yang:ietf-ac-ntw
   Prefix:  ac-ntw
   Maintained by IANA?  N
   Reference:  RFC XXXX
~~~~

--- back

# Examples {#sec-examples}

## VPLS

Let us consider the example depicted in {{ex-topo}} with two customer terminating points (CE1 and CE2). Let us also assume that the bearers to attach these CEs to the provider network are already in place. References to the identify these bearers are shown in the figure.

~~~~~~~~~~ aasvg
{::include ./figures/glue/ex-topo.txt}
~~~~~~~~~~
{: #ex-topo title="Topology Example" artwork-align="center"}

The AC service model {{!I-D.ietf-opsawg-teas-attachment-circuit}} can be used by the provider to manage and expose the ACs over existing bearers as shown in {{ex-ac}}.

~~~~~~~~~~
{::include-fold ./json-examples/glue/example-acsvc-vpls.json}
~~~~~~~~~~
{: #ex-ac title="ACs Created Using ACaaS"}

The provisioned AC at PE1 can be retrieved using the AC network model as depicted in {{ex-acntw-query}}. A similar query can be used for the AC at PE2.

~~~~~~~~~~
{::include-fold ./json-examples/glue/example-acntw-2.json}
~~~~~~~~~~
{: #ex-acntw-query title="Example of AC Network Response (Message Body)"}

Also, the AC network model can be used to retrieve the list of SAPs to which the ACs are bound as shown in {{ex-acntw-query}}.

~~~~~~~~~~
{::include-fold ./json-examples/glue/example-acntw.json}
~~~~~~~~~~
{: #ex-acntw-query-2 title="Example of AC Network Response to Retrieve the SAP (Message Body)"}

## Parent AC

In reference to the topology depicted in {{sap-ac-ntw}}, PE2 has a SAP which terminates an AC with two peer SAPs (CE2 and CE5). In order to control data that is specific to each of these peer SAPs over the same AC, child ACs can be instantiated as depicted in {{ex-parent-ac}}.

~~~~~~~~~~
{::include-fold ./json-examples/ntw/multiple-acs-same-sap-2.json}
~~~~~~~~~~
{: #ex-parent-ac title="Example of Child ACs"}

{{ex-parent-ac-sap}} shows how to bind the parent AC to a SAP.

~~~~~~~~~~
{::include-fold ./json-examples/ntw/multiple-acs-same-sap.json}
~~~~~~~~~~
{: #ex-parent-ac-sap title="Example of Binding Parent AC to SAPs"}

# Full Tree {#AC-Ntw-Tree}

~~~~~~~~~~
{::include-fold ./yang/full-trees/ac-ntw-without-groupings.txt}
~~~~~~~~~~


# Acknowledgments
{:numbered="false"}

This document builds on {{!RFC9182}} and {{!RFC9291}}.

Thanks to Moti Morgenstern for the review and comments.

Thanks to Martin Björklund for the yangdoctors review, Gyan Mishra for an early rtg-dir review, Joel Halpern for the rtg-dir review,
Giuseppe Fioccola for the ops-dir review, and Russ Housley for the sec-dir review.

Thanks to Krzysztof Szarkowicz for the Shepherd review.

Thanks for Mahesh Jethanandani for the AD review.
