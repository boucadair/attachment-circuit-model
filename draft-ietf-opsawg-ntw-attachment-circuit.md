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
  AC-Ntw-Tree:
    title: Full Network Attachment Circuit Tree Structure
    date: 2023
    target: https://github.com/boucadair/attachment-circuit-model/blob/main/yang/full-trees/ac-ntw-without-groupings.txt

  PYANG:
    title: pyang
    date: 2023
    target: https://github.com/mbj4668/pyang

--- abstract

This document specifies a network model for attachment circuits. The model can be used for the provisioning of attachment circuits prior or during service provisioning (e.g., Network Slice Service). A companion service model is specified in I-D.ietf-opsawg-teas-attachment-circuit.

The module augments the Service Attachment Point (SAP) model with the detailed information for the provisioning of attachment circuits in Provider Edges (PEs).

--- middle

# Introduction

Connectivity services are provided by networks to customers via
   dedicated terminating points, such as Service Functions {{?RFC7665}},
   customer edges (CEs), peer Autonomous System Border Routers (ASBRs),
   data centers gateways, or Internet Exchange Points.

The procedure to provision a service in a service provider network may depend on the practices adopted by a service provider, including the flow put in place for the provisioning of advanced network services and how they are bound to an Attachment Circuit (AC). For example, the same attachment circuit may host multiple services (e.g., Layer 2 Virtual Private Network (VPN), Slice Service, or Layer 3 VPN). In order to avoid service interference and redundant information in various locations, a service provider may expose an interface to manage ACs network-wide. Customers can then request a standalone attachment circuit to be put in place, and then refer to that attachment circuit when requesting services to be bound to that AC. {{!I-D.ietf-opsawg-teas-attachment-circuit}} specifies a data model for managing attachment circuits as a service.

{{sec-module}} specifies a network model for attachment circuits ("ietf-ac-ntw"). The model can be used for the provisioning of ACs prior or during service provisioning.

The document leverages {{!RFC9182}} and {{!RFC9291}} by adopting an AC provisioning structure that uses data nodes that are defined in these RFCs. Some refinements were introduced to cover, not only conventional service provider networks, but also specifics of other target deployments (cloud, for example).

The AC network model is designed as an augmnetation to the Service Attachment Point (SAP) model {{!RFC9408}}. An attachment circuit can be bound to a single or multiple SAPs. Likewise, the model is designed to accomdate deployments where a SAP can be bound to one or multiple ACs (e.g., a parent AC and its child ACs).

~~~~ aasvg
{::include-fold ./figures/ac-ntw-example.txt}
~~~~
{: #sap-ac-ntw title="Attachment Circuits Examples" artwork-align="center"}

The AC network model uses the AC common model defined in {{!I-D.ietf-opsawg-teas-common-ac}}.

The YANG data model in this document conforms to the Network Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

Sample examples are provided in {{sec-examples}}.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

The reader should be familiar with the terms defined in {{Section 2 of !RFC9408}}.

This document uses the term "network model" as defined in {{Section 2.1 of ?RFC8969}}.

The meanings of the symbols in the YANG tree diagrams are defined in {{?RFC8340}}.

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
: A network that is able to provide network services (e.g., L2VPN, L3VPN, or Network Slice Services).

Service provider:
: A service provider that offers network services (e.g., L2VPN, L3VPN, or Network Slice Services).

# Sample Uses of the Attachment Circuit Data Models

{{u-ex}} shows the positioning of the AC network model in the overall service delivery process. The "ietf-ac-ntw" module is a network model which augments the SAP with a comprehensive set of parameters to reflect the attachment circuits that are in place in a network. The model also maintains the mapping with the service references that are used to expose these ACs to customers. Whether the same naming conventions to reference an AC are used in the service and network layers is deployment-specific.

~~~~ aasvg
{::include-fold ./figures/arch.txt}
~~~~
{: #u-ex title="An Example of the Network AC Model Usage" artwork-align="center"}

Similar to {{!RFC9408}}, the "ietf-ac-ntw" module can be used for both User-to-Network Interface (UNI) and
Network-to-Network Interface (NNI). For example, all the ACs shown in {{fig-inter-pn}} have a 'role' set
to "ietf-sap-ntw:nni". Typically, AS Border Routers (ASBRs) of each network is directly
connected to an ASBR of a neighboring network via one or multiple links (bearers). ASBRs of "Network#1" behaves as a PE and treats the other adjacent ASBRs as if it were a CE.

~~~~ aasvg
{::include-fold ./figures//ntw/inter-pn.txt}
~~~~
{: #fig-inter-pn title="An Example of the Network AC Model Usage Between Provider Networks" artwork-align="center"}


# Description of the Attachment Circuit YANG Module

The full tree diagram of the module can be generated using the
"pyang" tool {{PYANG}}.  That tree is not included here because it is
too long ({{Section 3.3 of ?RFC8340}}).  Instead, subtrees are provided in the following subsections
for the reader's convenience.

## Overall Structure of the Module

The overall tree structure of the module is shown in {{o-ntw-tree}}.

~~~~
augment /nw:networks/nw:network:
  +--rw specific-provisioning-profiles
  |  ...
  +--rw ac-profile* [name]
     ...
augment /nw:networks/nw:network/nw:node:
  +--rw ac* [name]
     +--rw name                 string
     +--rw ac-svc-ref?          ac-svc:attachment-circuit-reference
     +--rw ac-profile* [profile-id]
     |  +--rw profile-id    -> /nw:networks/network/ac-profile/name
     +--rw ac-parent-ref?       ac-ntw:attachment-circuit-reference
     +--rw peer-sap-id*         string
     +--rw group* [group-id]
     |  +--rw group-id      string
     |  +--rw precedence?   identityref
     +--rw status
     |  +--rw admin-status
     |  |  +--rw status?        identityref
     |  |  +--rw last-change?   yang:date-and-time
     |  +--ro oper-status
     |     +--ro status?        identityref
     |     +--ro last-change?   yang:date-and-time
     +--rw description?         string
     +--rw l2-connection
     |  ...
     +--rw ip-connection
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
    +--rw ac*   ac-ntw:attachment-circuit-reference
~~~~
{: #o-ntw-tree title="Overall Tree Structure"}

The full tree of the 'ac-ntw' is provided in {{AC-Ntw-Tree}}.

A node can host one or more SAPs. As per {{!RFC9408}}, a SAP is an abstraction of the network
reference points (the PE side of an AC, in the context of this document) where network services can be delivered and/or are delivered to customers. Each SAP terminates one or multiple ACs. Each AC in turn may be terminated by one or more peer SAPs ('peer-sap'). In order to expose such AC/SAP binding information, the SAP model {{!RFC9408}} is augmented with required AC-related information.

Unlike the AC service model {{!I-D.ietf-opsawg-teas-attachment-circuit}}, an AC is uniquely identified by a name within the scope of a node, not a network. A textual description of the AC may be provided ('description').

Also, in order to ease the correlation between the AC exposed at the service layer and the one that is actually provisioned in the network operation, a reference to the AC exposed to the customer ('ac-svc-ref') is stored in the 'ietf-ac-ntw' module.

ACs that are terminated by a SAP are listed in 'ac' under '/nw:networks/nw:network/nw:node/sap:service/sap:sap'. A controller may indicate a filter based on the service type (e.g., Network Slice or L3VPN) to retrieve the list of available SAPs, and thus ACs, for that service.

In order to factorize common data that is provisioned for a group of ACs, a set of profiles ({{sec-profiles}}) can be defined at the network level, and then called under the node level. The information contained in a profile is thus inherited, unless the corresponding data node is refined at the AC level. In such a case, the value provided at the AC level takes precedence over the global one.

In contexts where the same AC is terminated by multiple peer SAPs (e.g., an AC with multiple CEs) but a subset of them have specific information, the module allows operators to:

* Define a parent AC that may list all these CEs as peer SAPs.
* Create individual ACs that are bound to the parent AC using 'ac-parent-ref'.
* Indicate for each individual ACs one or a subset of the CEs as peer SAPs. All these individual ACs will inherit the properties of the parent AC.

Whenever a parent AC is deleted, then all child ACs of that AC MUST be deleted.

An AC may belong to one or multiple groups {{!RFC9181}}. For example, the 'group-id' is used to associate redundancy or protection constraints with ACs.

The status of an AC can be tracked using 'status'. Both operational status and administrative status are maintained. A mismatch between the administrative status vs. the operational status can be used as a trigger to detect anomalies.

An AC can be characterized using Layer 2 connectivity ({{sec-l2}}), Layer 3 connectivity ({{sec-l3}}), routing protocols ({{sec-rtg}}), OAM ({{sec-oam}}), security ({{sec-sec}}), and service ({{sec-svc}}) considerations.

## Provisioning Profiles {#sec-profiles}

The specific provisioning profiles tree structure is shown in {{profiles-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/profiles-tree.txt}
~~~~
{: #profiles-tree title="Profiles Tree Structure"}

The exact definition of these profiles is local to each service provider. The model only includes an identifier for these profiles in order to ease identifying and binding local policies when building an AC. As shown in {{profiles-tree}}, the following identifiers can be included:

'encryption-profile-identifier':
: An encryption profile refers to a set of policies related to the encryption schemes and setup that can be applied on the AC.

'qos-profile-identifier':
: A Quality of Service (QoS) profile refers to a set of policies such as classification, marking, and actions (e.g., {{?RFC3644}}).

'bfd-profile-identifier':
: A Bidirectional Forwarding Detection (BFD) profile refers to a set of BFD policies {{!RFC5880}} that can be invoked when building an AC.

'forwarding-profile-identifier':
: A forwarding profile refers to the policies that apply to the forwarding of packets conveyed over an AC. Such policies may consist of, for example, applying Access Control Lists (ACLs).

'routing-profile-identifier':
: A routing profile refers to a set of routing policies that will be invoked (e.g., BGP policies) for an AC.


## L2 Connection {#sec-l2}

The 'l2-connection' container is used to manage the Layer 2 properties of an AC. The  Layer 2 connection tree structure is shown in {{l2-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/l2-tree.txt}
~~~~
{: #l2-tree title="Layer 2 Connection Tree Structure"}

The 'encapsulation' container specifies the Layer 2 encapsulation to use (if any) and allows the configuration of the relevant tags. Also, the model supports tag manipulation operations (e.g., tag rewrite).

The 'l2-tunnel-service' container is used to specify the required parameters to set a Layer tunneling service (e.g., a Virtual Private LAN Service (VPLS), a Virtual eXtensible Local Area Network (VXLAN), or a pseudowire ({{Section 6.1 of !RFC8077}})). 'l2vpn-id' is used to identify a L2VPN service that is associated with an Integrated Routing and Bridging (IRB) interface.

To accommodate implementations that require internal bridging, a local bridge reference can be specified in 'local-bridge-reference'. Such a reference may be a local bridge domain.

A reference to the bearer is maintained using 'bearer-reference'.

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

In some deployment contexts (e.g., network merging), multiple IP subnets may be used in a transition period. For such deployments, multiple ACs (typically, two) with overlapping information may be maintained during a transition period. The correlation between these ACs may rely upon the same "ac-svc-ref".

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

One or multiple routing profiles ('routing-profiles') can be provided for
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

'bfd-enable':
: Indicates whether BFD is enabled or disabled for this static route entry.

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
: This address family will be used together with the 'vpn-type' to
      derive the appropriate Address Family Identifiers (AFIs) /
      Subsequent Address Family Identifiers (SAFIs) that will be part of
     the derived device configurations (e.g., unicast IPv4 MPLS L3VPN
      (AFI,SAFI = 1,128) as defined in {{Section 4.3.4 of !RFC4364}}).

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

'authentication':
:  The module adheres to the recommendations in
      {{Section 13.2 of !RFC4364}}, as it allows enabling the TCP
      Authentication Option (TCP-AO) {{!RFC5925}} and accommodates the
      installed base that makes use of MD5.  In addition, the module
      includes a provision for using IPsec.
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

'authentication':
:  Controls the authentication schemes to be enabled
      for the OSPF instance.  The following options are supported: IPsec
      for OSPFv3 authentication {{!RFC4552}}, and the Authentication
      Trailer for OSPFv2 {{!RFC5709}} {{!RFC7474}} and OSPFv3 {{!RFC7166}}.

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

'mode':
:  Indicates the IS-IS interface mode type.  It can be set to
      'active' (that is, send or receive IS-IS protocol control packets)
      or 'passive' (that is, suppress the sending of IS-IS updates
      through the interface).

: 'authentication':
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
      whether RIPv2 {{!RFC2453}}, RIP Next Generation (RIPng), or both are
      to be enabled {{!RFC2080}}.

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
      families are to be activated.  Note that VRRP version 3 {{!RFC5798}}
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
isn't any type of VRRP authentication at this time (see {{Section 9 of !RFC5798}}).

## OAM {#sec-oam}

The OAM subtree structure is shown in {{oam-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/oam-tree.txt}
~~~~
{: #oam-tree title="OAM Tree Structure"}

The following OAM data nodes can be specified:

'profile':
: Refers to a BFD profile ({{sec-profiles}}).

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

The security subtree structure is shown in {{sec-tree}}. The 'security' container specifies the authentication and the encryption to be applied to traffic for a given AC. Tthe model can be used to directly control the encryption to be applied (e.g., Layer 2 or Layer 3 encryption) or invoke a local encryption profile.

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
: Specifies the Layer 2 MTU, in bytes, for the VPN network access.

'svc-pe-to-ce-bandwidth' and 'svc-ce-to-pe-bandwidth':
: Specify the service bandwidth for the L2VPN service.

: 'svc-pe-to-ce-bandwidth' indicates the inbound bandwidth of the connection (i.e., download bandwidth from the service provider to the site).

: 'svc-ce-to-pe-bandwidth' indicates the outbound bandwidth of the connection (i.e., upload bandwidth from the site to the service provider).

: 'svc-pe-to-ce-bandwidth' and 'svc-ce-to-pe-bandwidth' can be represented using the Committed Information Rate (CIR), the Excess Information Rate (EIR), or the Peak Information Rate (PIR).

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
<CODE BEGINS> file ietf-ac-ntw@2022-11-30.yang
{::include-fold ./yang/ietf-ac-ntw.yang}
<CODE ENDS>
~~~~


# Security Considerations

   The YANG module specified in this document defines a schema for data
   that is designed to be accessed via network management protocols such
   as NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}}.  The lowest NETCONF layer
   is the secure transport layer, and the mandatory-to-implement secure
   transport is Secure Shell (SSH) {{!RFC6242}}.  The lowest RESTCONF layer
   is HTTPS, and the mandatory-to-implement secure transport is TLS
   {{!RFC8446}}.

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
   These are the subtrees and data nodes and their sensitivity/
   vulnerability in the "ietf-ac-ntw" module:

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
   notification) to these data nodes.  These are the subtrees and data
   nodes and their sensitivity/vulnerability in the "ietf-ac-svc" module:

   'ac':
   : Unauthorized access to this subtree can disclose the identity
      of a customer 'peer-sap-id'.

   'l2-connection' and 'ip-connection':
   :  An attacker can retrieve
      privacy-related information, which can be used to track a
      customer.  Disclosing such information may be considered a
      violation of the customer-provider trust relationship.

   'keying-material':
   :  An attacker can retrieve the cryptographic keys
      protecting an AC (routing, in particular). These keys could
      be used to inject spoofed routing  advertisements.

Several data nodes ('bgp', 'ospf', 'isis', and 'rip') rely upon {{!RFC8177}} for authentication purposes. As such, the AC network module inherits the security considerations discussed in Section 5 of {{!RFC8177}}. Also, these data nodes support supplying explicit keys as strings in ASCII format. The use of keys in hexadecimal string format would afford greater key entropy with the same number of key-string octets. However, such a format is not included in this version of the AC network model, because it is not supported by the underlying device modules (e.g., {{?RFC8695}}).

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

Let's consider the example depicted in {{ex-topo}} with two customer terminating points (CE1 and CE2). Let's also assume that the bearers to attach these CEs to the provider network are already in place. References to the identify these bearers are shown in the figure.

~~~~~~~~~~
{::include ./figures/glue/ex-topo.txt}
~~~~~~~~~~
{: #ex-topo title="Topology Example" artwork-align="center"}

The AC service model {{!I-D.ietf-opsawg-teas-attachment-circuit}} can be used by the provider to manage and expose the ACs over existing bearers as shown in {{ex-ac}}.

~~~~~~~~~~
{::include-fold ./json-examples/glue/example-acsvc-vpls.json}
~~~~~~~~~~
{: #ex-ac title="ACs Created Using ACaaS" artwork-align="center"}

The provisionned AC at PE1 can be retrieved using the AC network model as depicted in {{ex-acntw-query}}. A similar query can be used for the AC at PE2.

~~~~~~~~~~
{::include-fold ./json-examples/glue/example-acntw.json}
~~~~~~~~~~
{: #ex-acntw-query title="Example of AC Network Response (Message Body)" artwork-align="center"}

## Parent AC

In reference to the topology depicted in {{sap-ac-ntw}}, PE2 has a SAP which terminates an AC with two peer SAPs (CE2 and CE5). In order to control data that is specific to each of these peer SAPs over the same AC, child ACs can be instantiated as depicted in {{ex-parent-ac}}.

~~~~~~~~~~
{::include-fold ./json-examples/ntw/multiple-acs-same-sap-2.json}
~~~~~~~~~~
{: #ex-parent-ac title="Example of Child ACs" artwork-align="center"}

{{ex-parent-ac-sap}} shows how to bind the parent AC to a SAP.

~~~~~~~~~~
{::include-fold ./json-examples/ntw/multiple-acs-same-sap.json}
~~~~~~~~~~
{: #ex-parent-ac-sap title="Example of Binding Parent AC to SAPs" artwork-align="center"}

# Acknowledgments
{:numbered="false"}

This document builds on {{!RFC9182}} and {{!RFC9291}}.

Thanks to Moti Morgenstern for the review and comments.
