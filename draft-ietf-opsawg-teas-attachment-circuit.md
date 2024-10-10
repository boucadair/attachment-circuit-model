---
title: "YANG Data Models for Bearers and 'Attachment Circuits'-as-a-Service (ACaaS)"
abbrev: "ACaaS"
category: std

docname: draft-ietf-opsawg-teas-attachment-circuit-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "OPSAWG"
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

informative:

  AC-svc-Tree:
    title: Full ACaaS Tree Structure
    date: 2024
    target: https://github.com/boucadair/attachment-circuit-model/blob/main/yang/full-trees/ac-svc-without-groupings.txt

  Instance-Data:
    title: Example of AC SVC Instance Data
    date: 2024
    target: https://github.com/boucadair/attachment-circuit-model/blob/main/xml-examples/svc-full-instance.xml

  PYANG:
    title: pyang
    date: 2024
    target: https://github.com/mbj4668/pyang

  IEEE802.1AB:
    title: "IEEE Standard for Local and metropolitan area networks - Station and Media Access Control Connectivity Discovery"
    author:
     -
      org: IEEE
    target: https://standards.ieee.org/ieee/802.1AB/6047/
    date: January 2016

  IEEE802.1AX:
    title: "IEEE Standard for Local and Metropolitan Area Networks--Link Aggregation"
    author:
     -
      org: IEEE
    target: https://doi.org/10.1109/IEEESTD.2020.9105034
    date: May 2020

  ITU-T-G.781:
    title: "Synchronization layer functions for frequency synchronization based on the physical layer"
    author:
     -
      org: ITU-T
    target: https://www.itu.int/rec/T-REC-G.781
    date: January 2024

--- abstract

This document specifies a YANG service data model for Attachment Circuits (ACs). This model can be used for the provisioning of ACs before or during service provisioning (e.g., Network Slice Service). The document also specifies a service model for managing bearers over which ACs are established.

Also, the document specifies a set of reusable groupings. Whether other service models reuse structures defined in the AC models or simply include an AC reference is a design choice of these service models. Utilizing the AC service model to manage ACs over which a service is delivered has the advantage of decoupling service management from upgrading AC components to incorporate recent AC technologies or features.

--- middle

# Introduction

## Scope and Intended Use

Connectivity services are provided by networks to customers via dedicated terminating points, such as Service Functions (SFs) {{?RFC7665}}, Customer Edges (CEs), peer Autonomous System Border Routers (ASBRs), data centers gateways, or Internet Exchange Points. A connectivity service is basically about ensuring data transfer received from or destined to a given terminating point to or from other terminating points within the same customer/service, an interconnection node, or an ancillary node. The objectives for the connectivity service can be negotiated and agreed upon between the customer and the network provider. To facilitate data transfer within the provider network, it is assumed that the appropriate setup is provisioned over the links that connect customer terminating points and a provider network (usually via a Provider Edge (PE)), allowing successfully data exchanged over these links. The required setup is referred to in this document as Attachment Circuit (AC), while the underlying link is referred to as "bearer".

This document adheres to the definition of an Attachment Circuit as provided in "BGP/MPLS IP Virtual Private Networks (VPNs)" ({{Section 1.2 of !RFC4364}}), especially:

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

When a customer requests a new value-added service, the service can be bound to existing attachment circuits or trigger the instantiation of new attachment circuits. The provisioning of a value-added service should, thus, accommodate both deployments.

Also, because the instantiation of an attachment circuit requires coordinating the provisioning of endpoints that might not belong to the same administrative entity (customer vs. provider or distinct operational teams within the same provider, etc.), providing programmatic means to expose 'Attachment Circuits'-as-a-Service (ACaaS) greatly simplifies the provisioning of value-added services delivered over an attachment circuit. For example, management systems of adjacent domains that need to connect via an AC will use such means to agree upon the resources that are required for the activation of both sides of an AC (e.g., Layer 2 tags, IP address family, or IP subnets).

This document specifies a YANG service data model ("ietf-ac-svc") for managing attachment circuits that are exposed by a network to its customers, such as an enterprise site, an SF, a hosting infrastructure, or a peer network provider. The model can be used for the provisioning of ACs prior or during advanced service provisioning (e.g., IETF Network Slice Service defined in "A Framework for Network Slices in Networks Built from IETF Technologies" {{?RFC9543}}).

The "ietf-ac-svc" module ({{sec-ac-module}}) includes a set of reusable groupings. Whether a service model reuses structures defined in the "ietf-ac-svc" or simply includes an AC reference (that was communicated during AC service instantiation) is a design choice of these service models. Relying upon the AC service model to manage ACs over which services are delivered has the merit of decorrelating the management of the (core) service vs. upgrade the AC components to reflect recent AC technologies or new features (e.g., new encryption scheme, additional routing protocol). This document favors the approach of completely relying upon the AC service model instead of duplicating data nodes into specific modules of advanced services that are delivered over an Attachment Circuit.

Since the provisioning of an AC requires a bearer to be in place, this document introduces a new module called "ietf-bearer-svc" that enables customers to manage their bearer requests ({{sec-bearer-module}}). The customers can then retrieve a provider-assigned bearer reference that they will include in their AC service requests. Likewise, a customer may retrieve whether their bearers support a synchronization mechanism such as Sync Ethernet (SyncE) {{ITU-T-G.781}}. An example of retrieving a bearer reference is provided in {{ex-create-bearer}}.

An AC service request can provide a reference to a bearer or a set of peer Service Attachment Points (SAPs) specified in "A YANG Network Data Model for Service Attachment Points (SAPs)" {{!RFC9408}}. Both schemes are supported in the AC service model. When several bearers are available, the AC service request may filter them based on the bearer type, synchronization support, etc.

Each AC is identified with a unique identifier within a provider domain. From a network provider standpoint, an AC can be bound to a single or multiple SAPs {{!RFC9408}}. Likewise, the same SAP can be bound to one or multiple ACs. However, the mapping between an AC and a PE in the provider network that terminates that AC is hidden to the application that makes use of the AC service model. Such mapping information is internal to the network controllers. As such, the details about the (node-specific) attachment interfaces are not exposed in the AC service model. However, these details are exposed at the network model per "A Network YANG Data Model for Attachment Circuits" specification {{?I-D.ietf-opsawg-ntw-attachment-circuit}}. "A YANG Data Model for Augmenting VPN Service and Network Models with Attachment Circuits" {{?I-D.ietf-opsawg-ac-lxsm-lxnm-glue}} specifies augmentations to the	L2VPN Service Model (L2SM) {{?RFC8466}} and the L3VPN Service Model	(L3SM) {{?RFC8299}} to bind LxVPN services to ACs.

The AC service model does not make any assumptions about the internal structure or even the nature or the services that will be delivered over an attachment circuit or a set of attachment circuits. Customers do not have access to that network view other than the ACs that they ordered. For example, the AC service model can be used to provision a set of ACs to connect multiple sites (Site1, Site2, ..., SiteX) for customer who also requested VPN services. If the provisioning of these services requires specific configuration on ASBR nodes, such configuration is handled at the network level and is not exposed to the customer at the service level. However, the network controller will have access to such a view as the service points in these ASBRs will be exposed as SAPs with "role" set to "ietf-sap-ntw:nni" {{!RFC9408}}.

The AC service model can be used in a variety of contexts, such as (but not limited to) those provided in {{examples}}:

* Create an AC over an existing bearer {{ac-bearer-exist}}.
* Request an attachment circuit for a known peer SAP ({{ac-no-bearer-peer-sap}}).
* Instantiate multiple attachment circuits over the same bearer ({{sec-ex-one-ce-multi-acs}}).
* Control the precedence over multiple attachment circuits ({{sec-ex-prec}}).
* Create Multiple ACs bound to Multiple CEs ({{sec-multiple-ces}}).
* Bind a slice service to a set of pre-provisioned attachment circuits ({{sec-ex-slice}}).
* Connect a Cloud Infrastructure to a service provider network ({{sec-ex-cloud}}).
* Interconnect provider networks (e.g., {{?RFC8921}} or {{?I-D.ramseyer-grow-peering-api}}). Such ACs are identified with a "role" set to "ac-common:nni" or "ac-common:public-nni". See {{sec-peering}} to illustrate the use of the AC model for interconnection/peering.
* Manage connectivity for complex containerized or virtualized functions in the cloud ({{sec-cloudified-nfs}}).

The document adheres to the principles discussed in "Service Models Explained" ({{Section 3 of ?RFC8309}}) for the encoding and communication protocols used
for the interaction between a customer and a provider. Also, consistent with "A Framework for Automating Service and Network Management with YANG" {{?RFC8969}}, the service models defined in the document can be used independently of NETCONF/RESTCONF.

The YANG data models in this document conform to the Network Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

## Positioning ACaaS vs. Other Data Models

The AC model specified in this document is not a network model {{?RFC8969}}. As such, the model does not expose details related to specific nodes in the provider's network that terminate an AC (e.g., network node identifiers). The mapping between an AC as seen by a customer and the network implementation of an AC is maintained by the network controllers and is not exposed to the customer. This mapping can be maintained using a variety of network models, such as augmented SAP AC network model {{?I-D.ietf-opsawg-ntw-attachment-circuit}}.

The AC service model is not a device model. A network provider may use a variety of device models (e.g., "A YANG Data Model for Routing Management (NMDA Version)" {{?RFC8349}} or "YANG Model for Border Gateway Protocol (BGP-4)" {{?I-D.ietf-idr-bgp-model}}) to provision an AC service in relevant network nodes.

### Why Not Use the L2SM as Reference Data Model for ACaaS?

The L2VPN Service Model (L2SM) {{?RFC8466}} covers some AC-related considerations. Nevertheless, the L2SM structure is primarily focused on Layer 2 aspects. For example, the L2SM does not cover Layer 3 provisioning, which is required for the typical AC instantiation.

### Why Not Use the L3SM as Reference Data Model for ACaaS?

Like the L2SM, the L3VPN Service Model (L3SM) {{?RFC8299}} addresses certain AC-related aspects. However, the L3SM structure does not sufficiently address Layer 2 provisioning requirements. Additionally, the L3SM is primarily designed for conventional L3VPN deployments and, as such, has some limitations for instantiating ACs in other deployment contexts (e.g., cloud environments). For example, the L3SM does not provide the capability to provision multiple BGP peer groups over the same AC.

## Editorial Note (To be removed by RFC Editor)

Note to the RFC Editor: This section is to be removed prior to publication.

This document contains placeholder values that need to be replaced with finalized values at the time of publication. This note summarizes all of the substitutions that are needed.

Please apply the following replacements:

* XXXX --> the assigned RFC number for this I-D
* 2023-11-13 --> the actual date of the publication of this document

# Conventions and Definitions

{::boilerplate bcp14-tagged}

The meanings of the symbols in the YANG tree diagrams are defined in "YANG Tree Diagrams" {{?RFC8340}}.

LxSM refers to both the L2SM and the L3SM.

LxNM refers to both the L2NM and the L3NM.

This document uses the following terms:

Bearer:
: A physical or logical link that connects a customer node (or site) to a provider network. A bearer can be a wireless or wired link. One or multiple technologies can be used to build a bearer (e.g., Link Aggregation Group (LAG) {{IEEE802.1AX}}). The bearer type can be specified by a customer.
: The operator allocates a unique bearer reference to identify a bearer within its network (e.g., customer line identifier). Such a reference can be retrieved by a customer and used in subsequent service placement requests to unambiguously identify where a service is to be bound.
: The concept of bearer can be generalized to refer to the required underlying connection for the provisioning of an attachment circuit. One or multiple attachment circuits may be hosted over the same bearer (e.g., multiple VLANs on the same bearer that is provided by a physical link).

Customer Edge (CE):
:  Equipment that is dedicated to a customer and is connected to one or more PEs via ACs.
:  A CE can be a router, a bridge, a switch, etc.

Provider Edge (PE):
: Equipment owned and managed by the service provider that can support multiple services for different customers.
: Per "Provider Provisioned Virtual Private Network (VPN) Terminology" ({{Section 5.2 of ?RFC4026}}), a PE is a device located at the edge of the service network with the functionality that is needed to interface with the customer.
: A PE is connected to one or more CEs via ACs.

Network controller:
: Denotes a functional entity responsible for the management of the service provider network.

Network Function (NF):
: Used to refer to the same concept as Service Function (SF) ({{Section 1.4 of ?RFC7665}}).
: NF is also used in this document as this term is widely used outside the IETF.
: NF and SF are used interchangeably.

Parent Bearer:
: Refers to a bearer (e.g., LAG) that is used to build other bearers. These bearers (called, child bearers) inherit th parent bearer properties.

Parent AC:
: Refers to an AC that is used to build other ACs. These ACs (called, child ACs) inherit th parent AC properties.

Service orchestrator:
: Refers to a functional entity that interacts with the customer of a network service. The service orchestrator is typically responsible for the attachment circuits, the PE selection, and requesting the activation of the requested service to a network controller.

Service provider network:
: A network that is able to provide network services (e.g., Layer 2 VPN, Layer 3 VPN, or Network Slice Services).

Service provider:
: A service provider that offers network services (e.g., Layer 2 VPN, Layer 3 VPN, or Network Slice Services).

The names of data nodes are prefixed using the prefix associated with the corresponding imported YANG module as shown in {{pref}}:

|Prefix|	Module| Reference |
| inet|ietf-inet-types| {{Section 4 of !RFC6991}}|
| key-chain|ietf-key-chain| {{!RFC8177}}|
| nacm|ietf-netconf-acm|{{!RFC8341}}|
| vpn-common|ietf-vpn-common|{{!RFC9181}}|
{: #pref title="Modules and Their Associated Prefixes"}

# Relationship to Other AC Data Models

{{ac-overview}} depicts the relationship between the various AC data models:

* "ietf-ac-common" ({{!I-D.ietf-opsawg-teas-common-ac}})
* "ietf-bearer-svc" ({{sec-ac-module}})
* "ietf-ac-svc" ({{sec-bearer-module}})
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
To bind Layer 2 VPN or Layer 3 VPN services with ACs, "ietf-ac-glue" augments the LxSM and LxNM with AC service references exposed by "ietf-ac-svc" and AC network references exposed bt "ietf-ac-ntw".

# Sample Uses of the Data Models

## ACs Terminated by One or Multiple Customer Edges (CEs)

{{uc}} depicts two target topology flavors that involve ACs. These topologies have the following characteristics:

* A CE can be either a physical device or a logical entity. Such logical entity is typically a software component (e.g., a virtual service function that is hosted within the provider's network or a third-party infrastructure). A CE is seen by the network as a peer SAP.

* An AC service request may include one or multiple ACs, which may be associated to a single CE or multiple CEs.

* CEs may be either dedicated to one single connectivity service or host multiple connectivity services (e.g., CEs with roles of SFs {{?RFC7665}}).

* A network provider may bind a single AC to one or multiple peer SAPs (e.g., CE#1 and CE#2 are tagged as peer SAPs for the same AC). For example, and as discussed in {{!RFC4364}}, multiple CEs can be attached to a PE over the same attachment circuit. This scenario is typically implemented when the Layer 2 infrastructure between the CE and the network is a multipoint service.

* A single CE may terminate multiple ACs, which can be associated with the same bearer or distinct bearers.

* Customers may request protection schemes in which the ACs associated with their endpoints are terminated by the same PE (e.g., CE#3), distinct PEs (e.g., CE#34), etc. The network provider uses this request to decide where to terminate the AC in the provider network (i.e., select which PE(s) to use) and also whether to enable specific capabilities (e.g., Virtual Router Redundancy Protocol (VRRP) {{?RFC9568}}). Note that placement constraints may also be requested during the instantiation of the underlying bearers ({{sec-bearer}}).

~~~~ aasvg
{::include ./figures/acs-examples.txt}
~~~~
{: #uc title='Examples of ACs' artwork-align="center"}

## Separate AC Provisioning vs. Actual Service Provisioning

The procedure to provision a service in a service provider network may depend on the practices adopted by a service provider. This includes the workflow put in place for the provisioning of network services  and how they are bound to an attachment circuit. For example, a single attachment circuit may be used to host multiple connectivity services. In order to avoid service interference and redundant information in various locations, a service provider may expose an interface to manage ACs network-wide. Customers can then request a bearer or an attachment circuit to be put in place, and then refer to that bearer or AC when requesting services that are bound to the bearer or AC. {{?I-D.ietf-opsawg-ac-lxsm-lxnm-glue}} specifies augmentations to the L2SM and the L3SM to bind LxVPN services to ACs.

{{u-ex-c}} shows an example to illustrate how the bearer/AC service models can be used between a customer and a provider. Internals to the provider orchestration domain (or customer orchestration domain) are hidden to the customer (or provider).

~~~~ aasvg
.----------------------.     Bearer/AC       .--------------------.
|      Customer        |    Service Models   |     Provider       |
|   Service Ordering   | <-----------------> |   Service Order    |
|                      |                     |     Handling       |
'----------^-----------'                     '---------^----------'
           |                                           |
      Provisioning                                Provisioning
           |                                           |
.----------v-----------.                     .---------v----------.
|                      |========Bearer=======|                    |
|    Customer Site     +----------AC---------|  Provider Network  |
|                      |========Bearer=======|                    |
'----------------------'                     '--------------------'
~~~~
{: #u-ex-c title="Example of Interaction Between Customer and Provider Orchestrations" artwork-align="center"}

{{u-ex-r}} shows an example to illustrate how the bearer/AC service models which involve a third party. This deployment model follows a recursive approach but other Client/Server alternative modes with a third party can be considered. In a recursive deployment, the Service Broker
exposes a server to a customer for the ordering of AC services, but it also acts as a client when communicating with the a provider. How the Service Broker
decides to terminate a recursion for a given service request or create child service requests is deployment specific.

~~~~ aasvg
.----------.   Bearer/AC    .----------.   Bearer/AC    .--------------.
| Customer | Service Models | Service  | Service Model  |   Provider    |
| Service  |<-------------->|  Broker  |<-------------->| Service Order |
| Ordering |                |  B2B C/S |                |    Handling   |
'----------'                '----------'                '---------------'

B2B C/S: Back-to-back Client/Server
~~~~
{: #u-ex-r title="Example of Recursive Deployment" artwork-align="center"}

{{u-ex}} shows the positioning of the AC service model in the overall service delivery process, with a focus the provider.

~~~~ aasvg
{::include ./figures/arch.txt}
~~~~
{: #u-ex title="An Example of AC Model Usage (Focus on the Provider's Internals)" artwork-align="center"}

In order to ease the mapping between the service model and underlying network models (e.g., the L3VPN Network Model (L3NM), SAP), the name conventions used in existing network data models are reused as much as possible. For example, "local-address" is used rather than "provider-address" (or similar) to refer to an IP address used in the provider network. This approach is consistent with the automation framework defined in {{?RFC8969}}.

# Description of the Data Models

## The Bearer Service ("ietf-bearer-svc") YANG Module {#sec-bearer}

{{bearer-st}} shows the tree for managing the bearers (that is, the properties of an attachment that are below Layer 3). A bearer can be a physical or logical link (e.g., LAG {{IEEE802.1AX}}). Also, a bearer can be a wireless or wired link. A reference to a bearer is generated by the operator.
Such a reference can be used, e.g., in a subsequent service request to create an AC. The anchoring of the AC can also be achieved by indicating (with or without a bearer reference), a peer SAP identifier (e.g., an identifier of an SF).

~~~~
{::include ./yang/full-trees/bearers-stree.txt}
~~~~
{: #bearer-st title="Bearer Service Tree Structure" artwork-align="center"}

In some deployments, a customer may first retrieve a list of available presence locations before actually placing an order for a bearer creation. The request is filtered based upon a customer name and an Autonomous System Number (ASN). The reserved value "AS 0" {{?RFC7607}} is used for customers with no ASN. The retrieved location names may be then referenced in a bearer creation request ("provider-location-reference"). See the example provided in {{sec-ret-loc}}.

The same customer site (CE, SF, etc.) can terminate one or multiple bearers; each of them uniquely identified by a reference that is assigned by the network provider. These bearers can terminate on the same or distinct network nodes. CEs that terminate multiple bearers are called multi-homed CEs.

A bearer can be created, modified, or discovered from the network. For example, the following deployment options can be considered:

Greenfield creation:
: In this scenario, bearers are created from scratch using specific requests made to a network controller. This method  allows providers to tailor bearer creation to meet customer-specific needs. For example, a bearer request may indicate some hints about the placement constraints ('placement-constraints'). These constraints are used by a provider to determine how/where to terminate a bearer in the network side (e.g., Point of Presence (PoP) or PE selection).

Auto-discovery using network protocols:
: Devices can use specific protocols (e.g., Link Layer Discovery Protocol (LLDP) {{IEEE802.1AB}}) to automatically discover and connect to available network resources. A network controller can use such reported information to expose discovered bearers from the network using the same bearer data model structure.

A request to create a bearer may include a set of constraints ("placement-constraints") that are used by a controller to decide the network terminating side of a bearer (e.g., PE selection, PE redundancy, or PoP selection). Future placement criteria ("constraint-type") may be defined in the future to accommodate specific deployment contexts.


The descriptions of the bearer data nodes are as follows:

'name':
: Used to uniquely identify a bearer. This name is typically selected by the client when requesting a bearer.

'customer-name':
: Indicates the name of the customer who ordered the bearer.

'description':
: Includes a textual description of the bearer.

'group':
: Tags a bearer with one ore more identifiers that are used to group a set of bearers.

'op-comment':
: Includes operational comments that may be useful for managing the bearer (building, level, etc.). No structure is associated with this data node to accommodate all deployments.

'bearer-parent-ref':
: Specifies the parent bearer. This data node can be used, e.g., if a bearer is a member of a LAG.

'bearer-lag-member':
: Lists the bearers that are members of a LAG. Members can be declared as part of a LAG using 'bearer-parent-ref'.

'sync-phy-capable':
: Reports whether a synchronization physical (Sync PHY) mechanism is supported for this bearer.

'sync-phy-enabled':
: Indicates whether a Sync PHY mechanism is enabled for a bearer. Only applies when 'sync-phy-capable' is set to 'true'.

'sync-phy-type':
: Specifies the Sync PHY mechanism (e.g., SynchE {{ITU-T-G.781}}) enabled for the bearer.

'provider-location-reference':
: Indicates a location identified by a provider-assigned reference.

'customer-point':
: Specifies the customer terminating point for the bearer. A bearer request can indicate a device, a site, a combination thereof, or a custom information when requesting a bearer. All these schemes are supported in the model.

'type':
: Specifies the bearer type (Ethernet, wireless, LAG, etc.).

'test-only':
: Indicates that a request is only for test and not for setting, even if there are no errors. This is used for feasibility checks. This data node is applicable only when the data model is used with protocols which do not natively support such option. For example, this data node is redundant with the "test-only" value of the `<test-option>` parameter in the NETCONF `<edit-config>` operation ({{Section 7.2 of ?RFC6241}}).

'bearer-reference':
: Returns an internal reference for the service provider to uniquely identify the bearer. This reference can be used when requesting services. {{ex-create-bearer}} provides an example about how this reference can be retrieved by a customer.
: Whether the 'bearer-reference' mirrors the content of the 'name' is deployment-specific. The module does not assume nor preclude such schemes.

'ac-svc-ref':
: Specifies the set of attachment circuits that are bound to the bearer.

'requested-start':
: Specifies the requested date and time when the bearer is expected to be active.

'requested-stop':
: Specifies the requested date and time when the bearer is expected to be disabled.

'actual-start':
: Reports the actual date and time when the bearer actually was enabled.

'actual-stop':
: Reports the actual date and time when the bearer actually was disabled.

'status':
: Used to track the overall status of a given bearer. Both operational and administrative status are maintained together with a timestamp.

: The "admin-status" attribute is typically configured by a network operator to indicate whether the service is enabled, disabled, or subjected to additional testing or pre-deployment checks. These additional options, such as 'admin-testing' and 'admin-pre-deployment', provide the operators the flexibility to conduct additional validations on the bearer before deploying services over that connection.

'oper-status':
: The "oper-status" of a bearer reflects its operational state as observed. As a bearer can contain multiple services, the operational status should only reflect the status of the bearer connection. To obtain network-level service status, specific network models such as those in {{Section 7.3 of !RFC9182}}  or {{Section 7.3 of !RFC9291}} should be consulted.
: It is important to note that the "admin-status" attribute should remain independent of the "oper-status". In other words, the setting of the intended administrative state (e.g., whether "admin-up" or "admin-testing") MUST NOT be influenced by the current operational state. If the bearer is administratively set to 'admin-down', it is expected that the bearer will also be operationally 'op-down' as a result of this administrative decision.

: To assess the service delivery status for a given bearer comprehensively, it is recommended to consider both administrative and operational service status values in conjunction. This holistic approach  allows a network controller or operator to identify anomalies effectively.
: For instance, when a bearer is administratively enabled but the "operational-status" of that bearer is reported as "op-down", it should be expected that the "oper-status" of services transported over that bearer is also down. These status values differing should trigger the detection of an anomaly condition.
: See "A Common YANG Data Model for Layer 2 and Layer 3 VPNs" {{!RFC9181}} for more details.


## The Attachment Circuit Service ("ietf-ac-svc") YANG Module

The full tree diagram of the module can be generated using, e.g., the
"pyang" tool {{PYANG}}.  That tree is not included here because it is
too long ({{Section 3.4 of ?I-D.ietf-netmod-rfc8407bis}}).  Instead, subtrees are provided
for the reader's convenience. The full tree of the 'ac-svc' is provided in {{AC-svc-Tree}}.

### Overall Structure

The overall tree structure of the AC service module is shown in {{o-svc-tree}}.

~~~~
{::include ./yang/subtrees/svc/overall-stree.txt}
~~~~
{: #o-svc-tree title="Overall AC Service Tree Structure" artwork-align="center"}

The rationale for deciding whether a reusable grouping should be maintained in this document or be moved into the AC common module {{!I-D.ietf-opsawg-teas-common-ac}} is as follows:

* Groupings that are reusable among the AC service module, AC network module, other service models, and network models are included in the AC common module.
* Groupings that are reusable only by other service models are maintained in the "ietf-ac-svc" module.

Each AC is identified with a unique name ('../ac/name') within a domain. The mapping between this AC and a local PE that terminates the AC is hidden to the application that makes use of the AC service model. This information is internal to the Network controller. As such, the details about the (node-specific) attachment interfaces are not exposed in this service model.

The AC service model uses groupings and types defined in the AC common model {{!I-D.ietf-opsawg-teas-common-ac}} ('op-instructions', 'dot1q', 'qinq', 'priority-tagged', 'l2-tunnel-service', etc.). Therefore, the description of these nodes are not reiterated in the following subsections.

Features are used to tag conditional protions of the model in order to accomodate various deployments (support of layer 2 ACs, Layer 3 ACs, IPv4, IPv6, routing protocols,  Bidirectional Forwarding Detection (BFD), etc.).

### Service Profiles {#sec-profiles}

#### Description {#sec-profiles-desc}

The 'specific-provisioning-profiles' container ({{gp-svc-tree}}) can be used by a service provider to maintain a set of reusable profiles. The profiles definitions are similar to those defined in {{!RFC9181}}, including: Quality of Service (QoS), BFD, forwarding, and routing profiles. The exact definition of the profiles is local to each service provider. The model only includes an identifier for these profiles in order to facilitate identifying and binding local policies when building an AC.

~~~~
{::include ./yang/subtrees/svc/sp-svc-profiles-stree.txt}
~~~~
{: #gp-svc-tree title="Service Profiles" artwork-align="center"}

As shown in {{gp-svc-tree}}, two profile types can be defined: 'specific-provisioning-profiles' and 'service-provisioning-profiles'. Whether only specific profiles, service profiles, or a combination thereof are used is local to each service provider.

The following specific provisioning profiles can be defined:

'encryption-profile-identifier':
: Refers to a set of policies related to the encryption setup that can be applied when provisioning an AC.

'qos-profile-identifier':
: Refers to a set of policies, such as classification, marking, and actions (e.g., {{?RFC3644}}).

'failure-detection-profile-identifier':
: Refers to a set of failure detection policies (e.g., Bidirectional Forwarding Detection (BFD) policies {{!RFC5880}}) that can be invoked when building an AC.

'forwarding-profile-identifier':
: Refers to the policies that apply to the forwarding of packets conveyed within an AC. Such policies may consist, for example, of applying Access Control Lists (ACLs).

'routing-profile-identifier':
: Refers to a set of routing policies that will be invoked (e.g., BGP policies) when building an AC.

#### Referencing Service/Specific Profiles

All the above mentioned profiles are uniquely identified by the NETCONF/RESTCONF server by an identifier. To ease referencing these profiles by other data models, specific typedefs are defined for each of these profiles. Likewise, an attachment circuit reference typedef is defined when referencing a (global) attachment circuit by its name is required. These typedefs SHOULD be used when other modules need a reference to one of these profiles or attachment circuits.

### Attachment Circuits Profiles {#sec-acp}

The 'ac-group-profile' defines reusable parameters for a set of ACs. Each profile is identified by 'name'. Some of the data nodes can be adjusted at the 'ac' level.
These adjusted values take precedence over the global values.  The structure of 'ac-group-profile' is similar to the one used to model each 'ac' ({{ac-svc-tree}}).

### AC Placement Contraints {#sec-pc}

The 'placement-constraints' specifies the placement constraints of an AC. For example, this container can be used to request avoidance of connecting two ACs to the same PE. The full set of supported constraints is defined in {{!RFC9181}} (see 'placement-diversity', in particular).

The structure of 'placement-constraints' is shown in {{precedence-tree}}.

~~~~
{::include ./yang/subtrees/svc/precedence-stree.txt}
~~~~
{: #precedence-tree title="Placement Constraints Subtree Structure" artwork-align="center"}

### Attachment Circuits

The structure of 'attachment-circuits' is shown in {{ac-svc-tree}}.

~~~~
{::include ./yang/subtrees/svc/overall-ac-stree.txt}
~~~~
{: #ac-svc-tree title="Attachment Circuits Tree Structure" artwork-align="center"}

The description of the data nodes is as follows:

'customer-name':
: Indicates the name of the customer who ordered the AC or a set of ACs.

'description':
: Includes a textual description of the AC.

'test-only':
: Indicates that a request is only for test and not for setting, even if there are no errors. This is used for feasibility checks. This data node is applicable only when the data model is used with protocols which do not natively support such option.

'requested-start':
: Specifies the requested date and time when the attachment circuit is expected to be active.

'requested-stop':
: Specifies the requested date and time when the attachment circuit is expected to be disabled.

'actual-start':
: Reports the actual date and time when the attachment circuit actually was enabled.

'actual-stop':
: Reports the actual date and time when the attachment circuit actually was disabled.

'role':
: Specifies whether an AC is used, e.g., as User-to-Network Interface (UNI) or Network-to-Network Interface (NNI).

'peer-sap-id':
: Includes references to the remote endpoints of an attachment circuit {{!RFC9408}}. 'peer' is drawn here from the perspective of the provider network. That is, a 'peer-sap' will refer to a customer node.

'ac-group-profile-ref':
: Indicates references to one or more profiles that are defined in {{sec-acp}}.

'ac-parent-ref':
: Specifies an AC that is inherited by an attachment circuit.
: In contexts where dynamic terminating points are managed for a given AC,
a parent AC can be defined with a set of stable and common information, while
"child" ACs are defined to track dynamic information. These "child" ACs are bound to the parent AC, which is exposed to services (as a stable reference).
: Whenever a parent AC is deleted, all its "child" ACs MUST be deleted.
: A "child" AC MAY rely upon more than one parent AC (e.g., parent Layer 2 AC and parent Layer 3 AC). In such cases, these ACs MUST NOT be overlapping. An example to illustrate the use of multiple parent ACs is provided in  {{sec-bfd-static}}.

'ac-child-ref':
: Lists one or more references of child ACs that rely upon this attachment circuit as a parent AC.

'group':
: Lists the groups to which an AC belongs {{!RFC9181}}. For example, the 'group-id' is used to associate redundancy or protection constraints of ACs. An example is provided in {{sec-ex-prec}}.

'service-ref':
: Reports the set of services that are bound to the attachment circuit. The services are indexed by their type.

'server-reference':
: Reports the internal reference that is assigned by the provider for this AC. This reference is used to accomodate deployment contexts (e.g., {{Section 9.1.2 of ?RFC8921}}) where an identifier is generated by the provider to identify a service order locally.

'name':
: Associates a name that uniquely identifies an AC within a service provider network.

'service-profile':
: References a set of service-specific profiles.

'l2-connection':
: See {{sec-l2}}.

'ip-connection':
: See {{sec-l3}}.

'routing':
: See {{sec-rtg}}.

'oam':
: See {{sec-oam}}.

'security':
: See {{sec-sec}}.

'service':
: See {{sec-bw}}.

#### Layer 2 Connection Structure {#sec-l2}

The 'l2-connection' container ({{l2-svc-tree}}) is used to configure the relevant Layer 2 properties of an AC including: encapsulation details and tunnel terminations. For the encapsulation details, the model supports the definition of the type as well as the Identifiers (e.g., VLAN-IDs) of each of the encapsulation-type defined. For the second case, attributes for pseudowire, Virtual Private LAN Service (VPLS), and  Virtual eXtensible Local Area Network (VXLAN) tunnel terminations are included.

'bearer-reference' is used to link an AC with a bearer over which the AC is instantiated.

This structure relies upon the common groupings defined in {{!I-D.ietf-opsawg-teas-common-ac}}.

~~~~
{::include ./yang/subtrees/svc/l2-stree.txt}
~~~~
{: #l2-svc-tree title="Layer 2 Connection Tree Structure" artwork-align="center"}



#### IP Connection Structure {#sec-l3}

The 'ip-connection' container is used to configure the relevant IP properties of an AC. The model supports the usage of dynamic and static addressing. This structure relies upon the common groupings defined in {{!I-D.ietf-opsawg-teas-common-ac}}. Both IPv4 and IPv6 parameters are supported.

{{ipv4-svc-tree}} shows the structure of the IPv4 connection.

~~~~
{::include ./yang/subtrees/svc/ipv4-stree.txt}
~~~~
{: #ipv4-svc-tree title="Layer 3 Connection Tree Structure (IPv4)" artwork-align="center"}

{{ipv6-svc-tree}} shows the structure of the IPv6 connection.

~~~~
{::include ./yang/subtrees/svc/ipv6-stree.txt}
~~~~
{: #ipv6-svc-tree title="Layer 3 Connection Tree Structure (IPv6)" artwork-align="center"}

#### Routing {#sec-rtg}

As shown in the tree depicted in {{rtg-svc-tree}}, the 'routing-protocols' container defines the required parameters to enable the desired routing features for an AC. One or more routing protocols can be associated with an AC.  Such routing protocols will be then enabled between a PE and the customer terminating points. Each routing instance is uniquely identified by the combination of the 'id' and 'type' to accommodate scenarios where multiple instances of the same routing protocol have to be configured on the same link.

In addition to static routing ({{sec-static-rtg}}), the module supports BGP ({{sec-bgp-rtg}}), OSPF ({{sec-ospf-rtg}}), IS-IS ({{sec-isis-rtg}}), and RIP ({{sec-rip-rtg}}). It also includes a reference to the 'routing-profile-identifier' defined in {{sec-profiles}}, so that additional constraints can be applied to a specific instance of each routing protocol. Moreover, the module supports VRRP ({{sec-vrrp-rtg}}).

~~~~
{::include ./yang/subtrees/svc/rtg-stree.txt}
~~~~
{: #rtg-svc-tree title="Routing Tree Structure" artwork-align="center"}

##### Static Routing {#sec-static-rtg}

The static tree structure is shown in {{static-rtg-svc-tree}}.

~~~~
{::include ./yang/subtrees/svc/static-rtg-stree.txt}
~~~~
{: #static-rtg-svc-tree title="Static Routing Tree Structure" artwork-align="center"}

As depicted in {{static-rtg-svc-tree}}, the following data nodes can be defined for a given IP prefix:

'lan-tag':
: Indicates a local tag (e.g., "myfavorite-lan") that is used to enforce local policies.

'next-hop':
: Indicates the next hop to be used for the static route.
: It can be identified by an IP address, a predefined next-hop type (e.g., 'discard' or 'local-link'), etc.

'metric':
: Indicates the metric associated with the static route entry. This metric is used when the route is exported into an IGP.

'failure-detection-profile':
: Indicates a failure detection profile (e.g., BFD) that applies for this entry.

'status':
: Used to convey the status of a static route entry. This data node can also be used to control the (de)activation of individual static route entries.

##### BGP {#sec-bgp-rtg}

An AC service request with BGP routing SHOULD include at least the customer's AS Number (ASN) and an address family.
Additional information can be supplied by a customer in a request or exposed by a provider in a response to a query request
in order ease the process of automating the provisioning of BGP sessions (the customer does not use the primary IP address
to establish the underlying BGP session, communicate the provider's IP address used to establish the BGP session,
share authentication parameters, bind the session to a forwarding protection profile, etc.).

The BGP tree structure is shown in {{bgp-rtg-svc-tree}}.

~~~~
{::include ./yang/subtrees/svc/bgp-rtg-stree.txt}
~~~~
{: #bgp-rtg-svc-tree title="BGP Tree Structure" artwork-align="center"}

For deployment cases where an AC service request includes a list of neighors with redundant information,
the ACaaS allows to factorize shared data by means of 'peer-group'. The presence of "peer-groups" in a service request is thus optional.

The following data nodes are supported for each BGP 'peer-group':

'name':
: Defines a name for the peer group.

'local-as':
: Reports the provider's ASN. This information is used at the customer side to configure the BGP session with the provider network.

'peer-as':
: Indicates the customer's ASN. This information is used at the provider side to configure the BGP session with the customer equipment.

'address-family':
: Indicates the address family of the peer. It can be set to 'ipv4', 'ipv6', or 'dual-stack'.
: This address family might be used together with the service type that uses an AC (e.g., 'vpn-type' {{?RFC9182}}) to derive the appropriate Address Family Identifiers (AFIs) / Subsequent Address Family Identifiers (SAFIs) that will be part of the derived device configurations (e.g., unicast IPv4 MPLS L3VPN (AFI,SAFI = 1,128) as defined in {{Section 4.3.4 of !RFC4364}}).

'role':
: Specifies the BGP role in a session. Role values are taken from the list defined in {{Section 4 of ?RFC9234}}. This parameter is useful for interconnection scenarios.
: This is an optional parameter.

'local-address':
: Reports a provider's IP address to use to establish the BGP transport session.

'bgp-max-prefix':
: Indicates the maximum number of BGP prefixes allowed in a session for this group.

'authentication':
: The module adheres to the recommendations in {{Section 13.2 of !RFC4364}}, as it allows enabling the TCP Authentication Option (TCP-AO) {{?RFC5925}} and accommodates the installed base that makes use of MD5. In addition, the module includes a provision for using IPsec.
: Similar to {{?RFC9182}}, this version of the ACaaS assumes that parameters specific to the TCP-AO are preconfigured as part of the key chain that is referenced in the ACaaS. No assumption is made about how such a key chain is preconfigured. However, the structure of the key chain should cover data nodes beyond those in "YANG Data Model for Key Chains" {{!RFC8177}}, mainly SendID and RecvID ({{Section 3.1 of ?RFC5925}}).

For each neighbor, the following data nodes are supported in addition to similar parameters that are provided for a peer group:

'server-reference':
: Reports the internal reference that is assigned by the provider for this BGP session. This is an optional parameter.

'remote-address':
: Specifies the customer's IP address used to establishing this BGP session. If not present, this means that the primary
customer IP address is used as remote IP address.

'requested-start':
: Specifies the requested date and time when the BGP session is expected to be active.

'requested-stop':
: Specifies the requested date and time when the BGP session is expected to be disabled.

'actual-start':
: Reports the actual date and time when the BGP session actually was enabled.

'actual-stop':
: Reports the actual date and time when the BGP session actually was disabled.

'status':
: Indicates the status of the BGP routing instance.

'peer-group':
: Specifies a name of a peer group.
: Parameters that are provided at the 'neighbor' level takes precedence over the ones provided in the peer group.
: This is an optional parameter.

'failure-detection-profile':
: Indicates a failure detection profile (BFD) that applies for a BGP neighbor. This is an optional parameter.

##### OSPF {#sec-ospf-rtg}

The OSPF tree structure is shown in {{ospf-rtg-svc-tree}}.

~~~~
{::include ./yang/subtrees/svc/ospf-rtg-stree.txt}
~~~~
{: #ospf-rtg-svc-tree title="OSPF Tree Structure" artwork-align="center"}

The following OSPF data nodes are supported:

'address-family':
: Indicates whether IPv4, IPv6, or both address families are to be activated.

'area-id':
: Indicates the OSPF Area ID.

'metric':
: Associates a metric with OSPF routes.

'sham-links':
: Used to create OSPF sham links between two ACs sharing the same area and having a backdoor link ({{Section 4.2.7 of !RFC4577}} and {{Section 5 of !RFC6565}}).

'authentication':
: Controls the authentication schemes to be enabled for the OSPF instance. The following options are supported: IPsec for OSPFv3 authentication {{!RFC4552}}, and the Authentication Trailer for OSPFv2 {{!RFC5709}}{{!RFC7474}} and OSPFv3 {{!RFC7166}}.

'status':
: Indicates the status of the OSPF routing instance.


##### IS-IS {#sec-isis-rtg}

The IS-IS tree structure is shown in {{isis-rtg-svc-tree}}.

~~~~
{::include ./yang/subtrees/svc/isis-rtg-stree.txt}
~~~~
{: #isis-rtg-svc-tree title="IS-IS Tree Structure" artwork-align="center"}

The following IS-IS data nodes are supported:

'address-family':
: Indicates whether IPv4, IPv6, or both address families are to be activated.

'area-address':
: Indicates the IS-IS area address.

'authentication':
:  Controls the authentication schemes to be enabled
      for the IS-IS instance.  Both the specification of a key chain
      {{!RFC8177}} and the direct specification of key and authentication
      algorithms are supported.

'status':
: Indicates the status of the IS-IS routing instance.

##### RIP {#sec-rip-rtg}

The RIP tree structure is shown in {{rip-rtg-svc-tree}}.

~~~~
{::include ./yang/subtrees/svc/rip-rtg-stree.txt}
~~~~
{: #rip-rtg-svc-tree title="RIP Tree Structure" artwork-align="center"}

'address-family' indicates whether IPv4, IPv6, or both address families are to be activated. For example, this parameter is used to determine whether RIPv2 {{?RFC2453}}, RIP Next Generation (RIPng) {{?RFC2080}}, or both are to be enabled.

##### VRRP {#sec-vrrp-rtg}

The model supports the Virtual Router Redundancy Protocol (VRRP) {{?RFC9568}} on an AC ({{vrrp-rtg-svc-tree}}).

~~~~
{::include ./yang/subtrees/svc/vrrp-rtg-stree.txt}
~~~~
{: #vrrp-rtg-svc-tree title="VRRP Tree Structure" artwork-align="center"}

The following data nodes are supported:

'address-family':
: Indicates whether IPv4, IPv6, or both address
      families are to be activated.  Note that VRRP version 3 {{!RFC9568}}
      supports both IPv4 and IPv6.

'status':
:  Indicates the status of the VRRP instance.

Note that no authentication data node is included for VRRP, as there
isn't any type of VRRP authentication at this time (see {{Section 9 of !RFC9568}}).


#### Operations, Administration, and Maintenance (OAM) {#sec-oam}

As shown in the tree depicted in {{oam-svc-tree}}, the 'oam' container defines OAM-related parameters of an AC.

~~~~
{::include ./yang/subtrees/svc/oam-stree.txt}
~~~~
{: #oam-svc-tree title="OAM Tree Structure" artwork-align="center"}

This version of the module supports BFD. The following BFD data nodes can be specified:

'id':
: An identifier that uniquely identifies a BFD session.

'local-address':
: Indicates the provider's IP address used for a BFD session.

'remote-address':
: Indicates the customer's IP address used for a BFD session.

'profile':
: Refers to a BFD profile.

'holdtime':
:  Used to indicate the expected BFD holddown time, in milliseconds.

'status':
:  Indicates the status of the BFD session.

#### Security {#sec-sec}

As shown in the tree depicted in {{sec-svc-tree}}, the 'security' container defines a set of AC security parameters.

~~~~
{::include ./yang/subtrees/svc/security-stree.txt}
~~~~
{: #sec-svc-tree title="Security Tree Structure" artwork-align="center"}

The 'security' container specifies a minimum set of encryption-related parameters that can be requested to be applied to traffic for a given AC. Typically, the model can be used to directly control the encryption to be applied (e.g., Layer 2 or Layer 3 encryption) or invoke a local encryption profile (see  {{sec-profiles-desc}}). For example, a service provider may use IPsec when a customer requests Layer 3 encryption for an AC.

#### Service {#sec-bw}

The structure of the 'service' container is depicted in {{bw-tree}}.

~~~~
{::include ./yang/subtrees/svc/bw-stree.txt}
~~~~
{: #bw-tree title="Bandwidth Tree Structure" artwork-align="center"}

The 'service' container defines the following data nodes:

'mtu':
: Specifies the Layer 2 MTU, in bytes, for the AC.

'svc-pe-to-ce-bandwidth' and'svc-ce-to-pe-bandwidth':
   'svc-pe-to-ce-bandwidth':
   : Indicates the inbound bandwidth of the AC (i.e., download bandwidth from the service provider to
     the customer site).

   'svc-ce-to-pe-bandwidth':
   : Indicates the outbound bandwidth of the AC (i.e., upload bandwidth from the customer site to the service
     provider).

   : Both 'svc-pe-to-ce-bandwidth' and 'svc-ce-to-pe-bandwidth' can be represented using the Committed Information Rate (CIR), the Excess
Information Rate (EIR), or the Peak Information Rate (PIR). Both reuse the 'bandwidth-per-type' grouping defined in {{!I-D.ietf-opsawg-teas-common-ac}}.

'qos':
: Specifies a list of QoS profiles to apply for this AC.

'access-control-list':
: Specifies a list of ACL profiles to apply for this AC.


# YANG Modules

## The Bearer Service ("ietf-bearer-svc") YANG Module {#sec-bearer-module}

This module uses types defined in {{!RFC6991}}, {{!RFC9181}}, and {{!I-D.ietf-opsawg-teas-common-ac}}.

~~~~~~~~~~ yang
<CODE BEGINS> file "ietf-bearer-svc@2024-08-06.yang"
{::include-fold ./yang/ietf-bearer-svc.yang}
<CODE ENDS>
~~~~~~~~~~

## The AC Service ("ietf-ac-svc") YANG Module {#sec-ac-module}

This module uses types defined in {{!RFC6991}}, {{!RFC9181}}, {{!RFC8177}}, and {{!I-D.ietf-opsawg-teas-common-ac}}.

~~~~~~~~~~ yang
<CODE BEGINS> file "ietf-ac-svc@2024-08-06.yang"
{::include-fold ./yang/ietf-ac-svc.yang}
<CODE ENDS>
~~~~~~~~~~

# Security Considerations

This section uses the template described in Section 3.7 of {{?I-D.ietf-netmod-rfc8407bis}}.

   The YANG modules specified in this document defines a data model that is
   designed to be accessed via YANG-based management protocols, such as
   NETCONF {{?RFC6241}} and RESTCONF {{?RFC8040}}. These protocols have to
   use a secure transport layer (e.g., SSH {{?RFC4252}}, TLS {{?RFC8446}}, and
   QUIC {{?RFC9000}}) and have to use mutual authentication.

   The Network Configuration Access Control Model (NACM) {{!RFC8341}}
   provides the means to restrict access for particular NETCONF or
   RESTCONF users to a preconfigured subset of all available NETCONF or
   RESTCONF protocol operations and content.

   There are a number of data nodes defined in these YANG modules that are
   writable/creatable/deletable (i.e., config true, which is the
   default).  These data nodes may be considered sensitive or vulnerable
   in some network environments.  Write operations (e.g., edit-config)
   and delete operations to these data nodes without proper protection
   or authentication can have a negative effect on network operations.
   Specifically, the following subtrees and data nodes have particular
sensitivities/vulnerabilities in the "ietf-bearer-svc" module:

   'placement-constraints':
   : An attacker who is able to access this data node can modify the
   attributes to influence how a service is delivered to a customer, and
   this leads to Service Level Agreement (SLA) violations.

   'bearer':
   : An attacker who is able to access this data node can modify
   the attributes of bearer and, thus, hinder how ACs are built.
   : In addition, an attacker could attempt to add a new bearer or
   delete existing ones. An attacker may also change the requested
   type, whether it is for test-only, or the activation scheduling.

   The following subtrees and data nodes have particular
sensitivities/vulnerabilities in the "ietf-ac-svc" module:

   'specific-provisioning-profiles':
   : This container includes a set of sensitive data that influence
      how an AC will be delivered. For example, an attacker who has access
      to these data nodes may be able to manipulate routing policies, QoS
      policies, or encryption properties.
   : These profiles are defined with "nacm:default-deny-write"
      tagging {{!I-D.ietf-opsawg-teas-common-ac}}.

   'service-provisioning-profiles':
   : An attacker who has access to these data nodes may be able
   to manipulate service-specific policies to be applied for an AC.
   : This container is defined with "nacm:default-deny-write" tagging.

   'ac':
   : An attacker who is able to access this data node can modify
   the attributes of an AC (e.g., QoS, bandwidth, routing protocols,
   keying material), leading to malfunctioning of services that will
   be delivered over that AC and therefore to SLA violations.
   In addition, an attacker could attempt to add a new AC.

   Some of the readable data nodes in these YANG modules may be considered
   sensitive or vulnerable in some network environments.  It is thus
   important to control read access (e.g., via get, get-config, or
   notification) to these data nodes. Specifically, the following subtrees and data nodes have particular
sensitivities/vulnerabilities in the "ietf-bearer-svc" module:

   'customer-point' and 'locations':
   : An attacker can retrieve privacy-related information about locations from where
      the customer is connected or can be serviced. Disclosing such information may be used to infer
      the identity of the customer.

   The following subtrees and data nodes have particular
sensitivities/vulnerabilities in the "ietf-ac-svc" module:

   'customer-name', 'l2-connection', and 'ip-connection':
   : An attacker can retrieve privacy-related information, which can be used to track a
      customer.  Disclosing such information may be considered a
      violation of the customer-provider trust relationship.

   'keying-material':
   : An attacker can retrieve the cryptographic keys
      protecting the underlying connectivity services (routing, in
      particular).  These keys could be used to inject spoofed routing
      advertisements.

   Several data nodes ('bgp', 'ospf', 'isis', and 'rip') rely
   upon {{!RFC8177}} for authentication purposes.  As such, the AC service module
   inherits the security considerations discussed in Section 5 of
   {{!RFC8177}}.  Also, these data nodes support supplying explicit keys as
   strings in ASCII format.  The use of keys in hexadecimal string
   format would afford greater key entropy with the same number of
   key-string octets.  However, such a format is not included in this
   version of the AC service model because it is not supported by the underlying
   device modules (e.g., {{?RFC8695}}).


# IANA Considerations

   IANA is requested to register the following URIs in the "ns" subregistry within
   the "IETF XML Registry" {{!RFC3688}}:

~~~~
   URI:  urn:ietf:params:xml:ns:yang:ietf-bearer-svc
   Registrant Contact:  The IESG.
   XML:  N/A; the requested URI is an XML namespace.

   URI:  urn:ietf:params:xml:ns:yang:ietf-ac-svc
   Registrant Contact:  The IESG.
   XML:  N/A; the requested URI is an XML namespace.
~~~~

   IANA is requested to register the following YANG modules in the "YANG Module
   Names" subregistry {{!RFC6020}} within the "YANG Parameters" registry.

~~~~
   Name:  ietf-bearer-svc
   Maintained by IANA?  N
   Namespace:  urn:ietf:params:xml:ns:yang:ietf-bearer-svc
   Prefix:  bearer-svc
   Reference:  RFC xxxx

   Name:  ietf-ac-svc
   Maintained by IANA?  N
   Namespace:  urn:ietf:params:xml:ns:yang:ietf-ac-svc
   Prefix:  ac-svc
   Reference:  RFC xxxx
~~~~

--- back

# Examples {#examples}

This section includes a non-exhaustive list of examples to illustrate the use of the service models defined in this document. An example instance data can also be found at {{Instance-Data}}.

## Create a New Bearer {#ex-create-bearer}

An example of a request message body to create a bearer is shown in {{create-bearer}}.

~~~~ json
{::include-fold ./json-examples/svc/simple-bearer-create.json}
~~~~
{: #create-bearer title="Example of a Message Body to Create a New Bearer"}

A "bearer-reference" is then generated by the controller for this bearer. {{get-bearer}} shows the example of a response message body that is sent by the controller to reply to a GET request:

~~~~ json
{::include-fold ./json-examples/svc/get-bearer-reference.json}
~~~~
{: #get-bearer title="Example of a Response Message Body with the Bearer Reference"}

Note that the response also indicates that Sync Phy mechanism is supported for this bearer.

## Create an AC over an Existing Bearer {#ac-bearer-exist}

An example of a request message body to create a simple AC over an existing bearer is shown in {{ac-b}}. The bearer reference is assumed to be known to both the customer and the network provider. Such a reference can be retrieved, e.g., following the example described in {{ex-create-bearer}} or using other means (including, exchanged out-of-band or via proprietary APIs).

~~~~ json
{::include-dolf ./json-examples/svc/simple-ac-existing-bearer.json}
~~~~
{: #ac-b title="Example of a Message Body to Request an AC over an Existing Bearer"}

{{ac-br}} shows the message body of a GET response received from the controller and which indicates the "cvlan-id" that was assigned for the requested AC.

~~~~ json
{::include-fold ./json-examples/svc/simple-ac-existing-bearer-response.json}
~~~~
{: #ac-br title="Example of a Message Body of a Response to Assign a CVLAN ID"}

## Create an AC for a Known Peer SAP {#ac-no-bearer-peer-sap}

An example of a request to create a simple AC, when the peer SAP is known, is shown in {{ac-known-ps}}. In this example, the peer SAP identifier points to an identifier of an SF. The (topological) location of that SF is assumed to be known to the network controller. For example, this can be determined as part of an on-demand procedure to instantiate an SF in a cloud. That instantiated SF can be granted a connectivity service via the provider network.

~~~~ json
{::include-fold ./json-examples/svc/simple-ac-known-peer-sap.json}
~~~~
{: #ac-known-ps title="Example of a Message Body to Request an AC with a Peer SAP"}

{{ac-known-ps-res}} shows the received GET response with the required informaiton to connect the SF.

~~~~ json
{::include-fold ./json-examples/svc/simple-ac-known-peer-sap-response.json}
~~~~
{: #ac-known-ps-res title="Example of a Message Body of a Response to Create an AC with a Peer SAP"}

## One CE, Two ACs {#sec-ex-one-ce-multi-acs}

Let us consider the example of an eNodeB (CE) that is directly connected to the access routers of the mobile backhaul (see {{enodeb}}). In this example, two ACs are needed to service the eNodeB (e.g., distinct VLANs for Control and User Planes).

~~~~ aasvg
.-------------.                  .------------------.
|             |       ac1        | PE               |
|             |==================|  192.0.2.1       |
|   eNodeB    |          VLAN 1  |  2001:db8::1     |
|             |          VLAN 2  |                  |
|             |==================|                  |
|             |       ac2        |                  |
|             | Direct           |                  |
'-------------' Routing          |                  |
                                 |                  |
                                 |                  |
                                 |                  |
                                 '------------------'
~~~~
{: #enodeb title="Example of a CE-PE ACs"}

An example of a request to create the ACs to service the eNodeB is shown in {{two-acs-same-ce}}. This example assumes that static addressing is used for both ACs.

~~~~ json
{::include-fold ./json-examples/svc/two-acs-same-ce.json}
~~~~
{: #two-acs-same-ce title="Example of a Message Body to Request Two ACs on the Same Link (Not Recommended)"}

{{two-acs-same-ce-res}} shows the message body of a GET response received from the controller.

~~~~ json
{::include-fold ./json-examples/svc/two-acs-same-ce-response.json}
~~~~
{: #two-acs-same-ce-res title="Example of a Message Body of a Response to Create Two ACs on the Same Link (Not Recommended)"}

The example shown {{two-acs-same-ce-res}} is not optimal as it includes many redundant data. {{two-acs-same-ce-node-profile}} shows a more compact request that factorizes all the redundant data.

~~~~ json
{::include-fold ./json-examples/svc/two-acs-same-ce-node-profile.json}
~~~~
{: #two-acs-same-ce-node-profile title="Example of a Message Body to Request Two ACs on the Same Link (Node Profile)"}

A customer may request adding a new AC by simply referring to an existing per-node AC profile as shown in {{add-ac-same-ce-node-profile}}. This AC inherits all the data that was enclosed in the indicated per-node AC profile (IP addressing, routing, etc.).

~~~~ json
{::include-fold ./json-examples/svc/add-ac-same-ce-node-profile.json}
~~~~
{: #add-ac-same-ce-node-profile title="Example of a Message Body to Add a new AC over an existing link (Node Profile)"}


## Control Precedence over Multiple ACs {#sec-ex-prec}

When multiple ACs are requested by the same customer for the same site, the request can tag one of these ACs as "primary" and the other ones as "secondary". An example of such a request is shown in {{ac-precedence}}. In this example, both ACs are bound to the same "group-id", and the "precedence" data node is set as a function of the intended role of each AC (primary or secondary).

~~~~ aasvg
                                 .---.
                 ac1: primary    |   |
            .--------------------+PE1|
.---.       |    bearerX@site1   |   |
|   +-------'                    '---'
|CE |
|   +-------.                    .---.
'---'       |    ac2: secondary  |   |
            '--------------------+PE2|
                 bearerY@site1   |   |
                                 '---'
~~~~
{: #multipleac title="An Example Topology for AC Precedence Enforcement"}

~~~~ json
{::include-fold ./json-examples/svc/ac-precedence.json}
~~~~
{: #ac-precedence title="Example of a Message Body to Associate a Precedence Level with ACs"}

## Create Multiple ACs Bound to Multiple CEs {#sec-multiple-ces}

{{network-example}} shows an example of CEs that are interconnected by a service provider network.

~~~~ aasvg
                   .----------------------------------.
      .----.  ac1  |                                  |  ac3  .----.
      | CE1+-------+                                  +-------+ CE3|
      '----'       |                                  |       '----'
                   |              Network             |
      .----.  ac2  |                                  |  ac4  .----.
      |CE2 +-------+                                  +-------+ CE4|
      '----'       |                                  |       '----'
                   '----------------------------------'
~~~~
{: #network-example title="Network Topology Example" artwork-align="center"}

Let's assume that a request to instantiate various ACs that are shown in {{network-example}} is sent by the customer. {{multiple-sites}} depicts the example of the message body of a GET response that is received from the controller.

~~~~ json
{::include-fold ./json-examples/svc/multiple-ce-with-profile.json}
~~~~
{: #multiple-sites title="Example of a Message Body of a Request to Create Multiple ACs bound to Multiple CEs"}

## Binding Attachment Circuits to an IETF Network Slice {#sec-ex-slice}

This example shows how the AC service model complements the model defined in "A YANG Data Model for the RFC 9543 Network Slice Service" {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}} to connect a site to a Slice Service.

First, {{slice-vlan-1}} describes the end-to-end network topology as well the orchestration scopes:

- The topology is made up of two sites ("site1" and "site2"), interconnected via a Transport Network (e.g., IP/MPLS network). An SF is deployed within each site in a dedicated IP subnet.
- A 5G Service Management and Orchestration (SMO) is responsible for the deployment of SFs and the indirect management of a local Gateway (i.e., CE).
- An IETF Network Slice Controller (NSC) {{?RFC9543}} is responsible for the deployment of IETF Network Slices across the Transport Network.

SFs are deployed within each site.

~~~~ aasvg
{::include ./figures/drawing-slice-1.fig}
~~~~
{: #slice-vlan-1 title="An Example of a Network Topology Used to Deploy Slices"}

{{slice-vlan-2}} describes the logical connectivity enforced thanks to both IETF Network Slice and ACaaS models.

~~~~ aasvg
{::include ./figures/drawing-slice-2.fig}
~~~~
{: #slice-vlan-2 title="Logical Overview"}

{{slice-acs}} shows the message body of the request to create the required ACs using the ACaaS module.

~~~~ json
{::include-fold ./json-examples/svc/acs-for-slices.json}
~~~~
{: #slice-acs title="Message Body of a Request to Create Required ACs"}

{{slice-acs-res}} shows the message body of a response to a GET request received from the controller.

~~~~ json
{::include-fold ./json-examples/svc/acs-for-slices-response.json}
~~~~
{: #slice-acs-res title="Example of a Message Body of a Response Indicating the Creation of the ACs"}


{{slice-prov}} shows the message body of the request to create a Slice Service bound to the ACs created using {{slice-acs}}. Only references to these ACs are included in the Slice Service request.

~~~~ json
{::include-fold ./json-examples/svc/slice-provisionning.json}
~~~~
{: #slice-prov title="Message Body of a Request to Create a Slice Service Referring to the ACs"}

## Connecting a Virtualized Environment Running in a Cloud Provider {#sec-ex-cloud}

This example ({{cloud-provider-1}}) shows how the AC service model can be used to connect a Cloud Infrastructure to a service provider network. This example makes the following assumptions:

1.	A customer (e.g., Mobile Network Team or partner) has a virtualized infrastructure running in a Cloud Provider. A simplistic deployment is represented here with a set of Virtual Machines running in a Virtual Private Environment. The deployment and management of this infrastructure is achieved via private APIs that are supported by the Cloud Provider: this realization is out of the scope of this document.

1.	The connectivity to the Data Center is achieved thanks to a service based on direct attachment (physical connection), which is delivered upon ordering via an API exposed by the Cloud Provider. When ordering that connection, a unique "Connection Identifier" is generated and returned via the API.

1.	The customer provisions the networking logic within the Cloud Provider based on that unique connection identifier (i.e., logical interfaces, IP addressing, and routing).


~~~~ aasvg
{::include ./figures/drawing-cp-1.fig}
~~~~
{: #cloud-provider-1 title="An Example of Realization for Connecting a Cloud Site"}

{{cloud-provider-2}} illustrates the pre-provisioning logic for the physical connection to the Cloud Provider. After this connection is delivered to the service provider, the network inventory is updated with "bearer-reference" set to the value of the "Connection Identifier".

~~~~ aasvg
  Customer                                                       Cloud
Orchestration       DIRECT INTERCONNECTION ORDERING (API)       Provider
                ------------------------------------------------>

               Connection Created with "Connection ID:1234-56789"
               <------------------------------------------------
                                        x
                                        x
                                        x
                                        x

       Physical Connection 1234-56789 is delivered and
                         connected to PE1

       Network Inventory Updated with:
         bearer-reference: 1234-56789 for PE1/Interface "If-A"
~~~~
{: #cloud-provider-2 title="Illustration of Pre-provisioning"}

Next, API workflows can be initiated by:

* The Cloud Provider for the configuration per Step (3) above.
* The Service provider network via the ACaaS model. This request can be used in conjunction with additional requests based on the L3SM (VPN provisioning) or Network Slice Service model (5G hybrid Cloud deployment).

{{cloud-provider-ac}} shows the message body of the request to create the required ACs to connect the Cloud Provider Virtualized (VM) using the Attachment Circuit module.

~~~~ json
{::include-fold ./json-examples/svc/cloud-provider.json}
~~~~
{: #cloud-provider-ac title="Message Body of a Request to Create the ACs for Connecting to the Cloud Provider"}

{{cloud-provider-ac-res}} shows the message body of the response received from the provider as a response to a query message. Note that this Cloud Provider mandates the use of MD5 authentication for establishing BGP connections.

> The module supports MD5 to basically accommodate the installed BGP base (including by some Cloud Providers). Note that MD5 suffers from the security weaknesses discussed in {{Section 2 of ?RFC6151}} and {{Section 2.1 of ?RFC6952}}.

~~~~ json
{::include-fold ./json-examples/svc/cloud-provider-response.json}
~~~~
{: #cloud-provider-ac-res title="Message Body of a Response to the Request to Create ACs for Connecting to the Cloud Provider"}


## Connect Customer Network Through BGP

CE-PE routing using BGP is a common scenario in the context of MPLS VPNs and is widely used in enterprise networks. In the example depicted in {{provider-network}}, the CE routers are customer-owned devices belonging to an AS (ASN 65536). CEs are located at the edge of the provider's network (PE, or Provider Edge) and use point-to-point interfaces to establish BGP sessions. The point-to-point interfaces rely upon a physical bearer ("line-113") to reach the provider network.

~~~~ aasvg
{::include-fold ./figures/ce-to-provider-bgp.txt}
~~~~
{: #provider-network title="Illustration of Provider Network Scenario"}

The attachment circuit in this case use a SAP identifier to refer to the physical interface used for the connection between the PE and the CE. The attachment circuit includes all the additional logical attributes to describe the connection between the two ends, including VLAN information and IP addressing. Also, the configuration details of the BGP session makes use of peer group details instead of defining the entire configuration inside the 'neighbor' data node.

~~~~ json
{::include-fold ./json-examples/svc/provider-network-interas-option-a.json}
~~~~
{: #add-attachment-circuit-bgp-routing title="Message Body of a Request to Create ACs for Connecting CEs to a Provider Network"}

This scenario allows the provider to maintain a list of ACs belonging to the same customer without requiring the full service configuration.

## Interconnection via Internet eXchange Points (IXPs) {#sec-peering}

This section illustrates how to use the AC service model for interconnection purposes. To that aim, the document assumes a simplified Internet eXchange Point (IXP) configuration without zooming into IXP deployment specifics. Let us assume that networks are interconnected via a Layer 2 facility. Let us also assume a deployment context where selective peering is in place between these networks. Networks that are interested in establishing selective BGP peerings expose  a dedicated ACaaS server to the IXP. BGP is used to exchange routing information and reachability announcements between those networks.

This example follows the recursive deployment model depicted in {{u-ex-r}}. Specifically, base bearer/AC service requests are handled locally by the IXP. However, binding BGP sessions to existing ACs involves a recursion step.

~~~~ aasvg
.----------.   Bearer/AC    .----------.       AC      .--------------.
| Customer | Service Models |    IXP   | Service Model  |   Provider    |
| Service  |<-------------->| Operator |<-------------->| Service Order |
| Ordering |                |  B2B C/S |                |    Handling   |
'-----^----'                '-----^----'                '-------^-------'
      |                           |                             |
      |                           |                             |
 Provisioning                Provisioning                  Provisioning
      |                           |                             |
.-----v----.                .-----v----.                .-------v-------.
|   ASBR   |=====Bearer=====|    IXP   |=====Bearer=====|     ASBR      |
|          +----Parent AC---|    SW    +----Parent AC---|               |
|          +------------------BGP Session---------------+               |
|          |=====Bearer=====|          |=====Bearer=====|               |
'----------'                '----------'                '---------------'
~~~~
{: #u-ex-rb title="Recursive Deployment Example" artwork-align="center"}

The following subsections exemplify a deployment flow, but BGP sessions can be managed without having to execute systematically all the steps detailed hereafter.

The bearer/AC service models can be used to establish interconnection between two networks without involving an IXP.

### Retrieve Interconnection Locations {#sec-ret-loc}

{{ex-retrieve-locations}} shows an example a message body of a request to retrieve a list of interconnection locations. The request includes a customer name and an ASN to filter out the locations.

~~~~ json
{::include-fold ./json-examples/svc/get-locations.json}
~~~~
{: #ex-retrieve-locations title="Message Body of a Request to Retrieve Interconnection Locations"}

{{ex-retrieve-locations-res}} provides an example of a response to a query received from the server with a list of available interconnection locations.

~~~~ json
{::include-fold ./json-examples/svc/get-locations-response.json}
~~~~
{: #ex-retrieve-locations-res title="Message Body of a Response to Retrieve Interconnection Locations"}

### Create Bearers and Retrieve Bearer References

A peer can then use the location information and select the ones where it can request new bearers. As shown in {{ex-create-bearer-parent-ref}}, the request includes a location reference which is known to the server (returned in {{ex-retrieve-locations-res}}).

~~~~ json
{::include-fold ./json-examples/svc/simple-bearer-create-with-provider-ref.json}
~~~~
{: #ex-create-bearer-parent-ref title="Message Body of a Request to Create a Bearer using a Provider-Assigned Reference"}

The bearer is then activated by the server as shown in {{ex-create-bearer-parent-ref-res}}. A "bearer-reference" is also returned. That reference can be used for subsequent AC activation requests.

~~~~ json
{::include-fold ./json-examples/svc/simple-bearer-create-with-provider-ref-response.json}
~~~~
{: #ex-create-bearer-parent-ref-res title="Message Body of a Response for a Bearer Created in a Specific Location"}

### Manage ACs and BGP Sessions {#sec-manage-ac-bgp}

As depicted in {{bgp-peer-network}}, each network connects to the IXP switch via a bearer over which an AC is created.

~~~~ aasvg
{::include-fold ./figures/bgp-peering-example.txt}
~~~~
{: #bgp-peer-network title="Simple Interconnection Topology"}

The AC configuration ({{bgp-peer-network-add-attachment-circuit}}) includes parameters such as VLAN configuration, IP addresses, MTU, and any additional settings required for connectivity. The peering location is inferred from the "bearer-reference".

~~~~ json
{::include-fold ./json-examples/svc/bgp-peering-example.json}
~~~~
{: #bgp-peer-network-add-attachment-circuit title="Message Body of a Request to Create an AC to Connect to an IXP"}

{{bgp-peer-network-response}} shows the received response to a query with the required information for the activation of the AC.

~~~~ json
{::include-fold ./json-examples/svc/bgp-peering-example-response.json}
~~~~
{: #bgp-peer-network-response title="Message Body of a Response to an AC Request to Connect to an IXP"}

Once the ACs are established, BGP peering sessions can be configured between routers of the participating networks. BGP sessions can be established via a route server or between two networks. For the sake of illustration, let us assume that BGP sessions are established directly between two network. {{bgp-peer-network-add-bgp-attachment-circuit}} shows an example of a request to add a BGP session to an existing AC. The properties of that AC are not repeated in this request because that information is already communicated during the creation of the AC.

~~~~ json
{::include-fold ./json-examples/svc/bgp-conf-peering-example.json}
~~~~
{: #bgp-peer-network-add-bgp-attachment-circuit title="Message Body of a Request to Create a BGP Session over an AC"}

{{bgp-awaiting-validation}} provides the example of a response which indicates that the request is awaiting validation. The response includes also a server-assigned reference for this BGP session.

~~~~ json
{::include-fold ./json-examples/svc/bgp-conf-peering-awaiting-validation.json}
~~~~
{: #bgp-awaiting-validation title="Message Body of a Response for a BGP Session Awaiting Validation"}

Once validation is accomplished, a status update is communicated back to the requestor. The BGP session can then be established over the AC. The BGP session configuration includes parameters such as neighbor IP addresses, ASNs, authentication settings (if required), etc. The configuration is triggered at each side of the BGP connection (i.e., peer ASBRs).

~~~~ json
{::include-fold ./json-examples/svc/bgp-peering-all-sessions.json}
~~~~
{: #bgp-peering-all-sessions.json title="Message Body of a Response to Report All Active BGP sessions over an AC"}

## Connectivity of Cloud Network Functions {#sec-cloudified-nfs}

### Scope

This section demonstrates how the AC service model permits managing connectivity requirements for complex Network Functions (NFs) - containerized or virtualized -  that are typically deployed in Telco networks. This integration leverages the concept of "parent AC" to decouple physical and logical connectivity so that several ACs can shares Layer 2 and Layer 3 resources. This approach provides flexibility, scalability, and API stability.

The NFs have the following characteristics:

- The NF is distributed on a set of compute nodes with scaled-out and redundant instances.
- The NF has two distinct type of instances: user plane ("nf-up") and routing control plane ("nf-cp").
- The user plane component can be distributed among the first 8 compute nodes ("compute-01" to "compute-08") to achieve high performance.
- The control plane is deployed in a redundant fashion on two instances running on distinct compute nodes ("compute-09" and "compute-10").
- The NF is attached to distinct networks, each making use of a dedicated VLAN. These VLANs are therefore instantiated as separate ACs. From a realization standpoint, the NF interface connectivity is generally provided thanks to MacVLAN or Single Root I/O Virtualization (SR-IOV). For the sake of simplicity only two VLANs are presented in this example, additional VLANs are configured following a similar logic.

### Physical Infrastructure

{{cloud-parent-infra}} describes the physical infrastructure. The compute nodes (customer) are attached to the provider infrastructure thanks to a set of physical links on which attachment circuits are provisioned (i.e., "compute-XX-nicY"). The provider infrastructure can be realized in multiple ways, such as IP Fabric, Layer 2/Layer 3 Edge Routers. This document does not intend to detail these aspects.

~~~~ aasvg
{::include-fold ./figures/cloud-parent-infra.txt}
~~~~
{: #cloud-parent-infra title="Example Physical Topology for Cloud Deployment"}

### NFs Deployment

The NFs are deployed on this infrastructure in the following way:

* Configuration of a parent AC as a centralized attachment for "vlan 100". The parent AC captures Layer 2 and Layer 3 properties for this VLAN: vlan-id, IP default gateway and subnet, IP address pool for NFs endpoints, static routes with BFD to user plane, and BGP configuration to control plane NFs. In addition, the IP addresses of the user plane ("nf-up") instances are protected using BFD.
* Configuration of a parent AC as a centralized attachment for "vlan 200". This vlan is for Layer 2 connectivity between NFs (no IP configuration in the provider network).
* "Child ACs" binding bearers to parent ACs for "vlan 100" and "vlan 200".
* The deployment of the network service to all compute nodes ("compute-01" to "compute-10"), even though the NF is not instantiated on "compute-07"/"compute-08". This approach permits handling compute failures and scale-out scenarios in a reactive and flexible fashion thanks to a pre-provisioned networking logic.


~~~~ aasvg
{::include-fold ./figures/ac-parent-logical.txt}
~~~~
{: #cloud-parent-logical title="Logical Topology of the NFs Deployment"}

For readability the payload is displayed as single JSON file ({{parent-profile}}). In practice, several API calls may take place to initialize these resources (e.g., GET requests from the customer to retrieve the IP address pools for NFs on "vlan 100" thanks to parent configuration and BGP configuration, and POST extra routes for user planes and BFD).

Note that no individual IP address is assigned in the data model for the NF user plane instances (i.e., no "customer-address" in the Child AC). The assignment of IP addresses to the NF endpoints is managed by the Cloud Infrastructure IPAM based on the customer-addresses IP address pool "192.0.2.1-200". Like in any standard LAN-facing scenario, it is assumed that the actual binding of IP endpoints to logical attachments (here Child ACs) relies on a dedicated protocol logic  (typically, ARP or NDP) and is not captured in the data model. Hence, the IP addresses displayed for NF user plane instances are simply examples of a realization approach. Note also that the Control Plane is defined with static IP address assignment on a given AC/bearer to illustrate another deployment alternative.

~~~~ json
{::include-fold ./json-examples/svc/ac-cloud-parent.json}
~~~~
{: #parent-profile title="Message Body for the Configuration of the NF ACs"}

### NF Failure and Scale-Out

Assuming a failure of "compute-01", the instance "nf-up-1" can be redeployed to "compute-07" by the NF/Cloud Orchestration. The NFs can be scaled-out thanks to the creation of an extra instance "nf-up7" on "compute-08". Since connectivity is pre-provisioned, these operations happen without any API calls. In other words, this redeployment is transparent from the perspective of the configuration of the provider network.

~~~~ aasvg
{::include-fold ./figures/ac-parent-nf-lcm.txt}
~~~~
{: #cloud-parent-nf-lcm title="Example of Compute Failure and Scale-out"}

Finally, the addition or deletion of compute nodes in the deployment ("compute-11", "compute-12", etc.) involves merely changes on Child ACs and possible routing on the parent AC. In any case, the parent AC is a stable identifier, which can be consumed as a reference by end-to-end service models for VPN configuration such as {{?I-D.ietf-opsawg-ac-lxsm-lxnm-glue}}, Slice Service {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}}, etc. This decoupling to a stable identifier provides great benefits in terms of scalability and flexibility since once the reference with the parent AC is implemented, no API call involving the VPN model is needed for any modification in the cloud.

## BFD and Static Addressing {#sec-bfd-static}

{{ex-bfd-static}} shows a topology example of a set of CEs connected to a provider network via dedicated bearers. Each of these CE maintains two BFD sessions with the provider network.

~~~~ aasvg
{::include-fold ./figures/ac-parent-nf-static-ips.txt}
~~~~
{: #ex-bfd-static title="Example of Static Addressing with BFD"}

{{ex-json-bfd-static}} shows the message body of the ACaaS configuration to enable the target architecture shown in {{ex-bfd-static}}. This example uses an AC group profile to factorize common data between all involved ACs. It also uses child ACs that inherit the properties of two parent ACs; each terminating in a separate gateway in the provider network.

~~~~ json
{::include-fold ./json-examples/svc/ac-parent-static-ip.json}
~~~~
{: #ex-json-bfd-static title="Message Body for the Configuration of CEs with Static Addressing and BFD Protection"}




# Acknowledgments
{:numbered="false"}

This document leverages {{!RFC9182}} and {{!RFC9291}}. Thanks to Gyan Mishra for the review.

Thanks to Ebben Aries for the YANG Doctors review and for providing {{Instance-Data}}.

Thanks to Donald Eastlake for the careful rtg-dir reviews and Tero Kivinen for the sec-dir review.

Thanks to Luis Miguel Contreras Murillo for the careful Shepherd review.

Thanks to Mahesh for the AD review.
