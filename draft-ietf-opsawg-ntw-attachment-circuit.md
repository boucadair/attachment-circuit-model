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

The procedure to provision a service in a service provider network may depend on the practices adopted by a service provider, including the flow put in place for the provisioning of advanced network services and how they are bound to an Attachment Circuit (AC). For example, the same AC may host multiple services (e.g., Layer 2 VPN, Slice Service, or Layer 3 VPN). In order to avoid service interference and redundant information in various locations, a service provider may expose an interface to manage ACs network-wide. Customers can then request a base AC to be put in place, and then refer to that AC when requesting services to be bound to that AC. {{!I-D.ietf-opsawg-teas-attachment-circuit}} specifies a data model for managing attachment circuits as a service.

This document specifies a network model for ACs ("ietf-ac-ntw"). The model can be used for the provisioning of ACs prior or during service provisioning.

The document leverages {{!RFC9182}} and {{!RFC9291}} by adopting an AC provisioning structure that uses data nodes that are defined in these RFCs. Some refinements were introduced to cover, not only conventional service provider networks, but also specifics of other target deployments (cloud, for example).

The AC network model is designed as an augmnetation to the Service Attachment Point (SAP) model {{!RFC9408}}. An AC can be bound to a single or multiple SAPs. Likewise, the model is designed to accomdate deployments where a SAP can be bound to one or multiple ACs.

~~~~ aasvg
{::include ./figures/ac-ntw-example.txt}
~~~~
{: #sap-ac-ntw title="Attachment Circuits Examples" artwork-align="center"}

The AC network model uses the AC common model defined in {{!I-D.ietf-opsawg-teas-common-ac}}.

 The YANG data model in this document conforms to the Network Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

The reader should be familiar with the terms defined in {{Section 2 of !RFC9408}}.

This document uses the term "network model" as defined in {{Section 2.1 of ?RFC8969}}.

The meanings of the symbols in the YANG tree diagrams are defined in {{?RFC8340}}.

This document uses the following terms:

Bearer:
: A physical or logical link that connects a customer node (or site) to a provider network. A bearer can be a wireless or wired link. One or multiple technologies can be used to build a bearer. The bearer type can be specified by a customer.
: The operator allocates a unique bearer reference to identify a bearer within its network (e.g., customer line identifier). Such a reference can be retrieved by a customer and used in subsequent service placement requests to unambiguously identify where a service is to be bound.
: The concept of bearer can be generalized to refer to the required underlying connection for the provisioning of an attachment circuit. One or multiple attachment circuits may be hosted over the same bearer (e.g., multiple VLANs on the same bearer that is provided by a physical link).

Network controller:
: Denotes a functional entity responsible for the management of the service provider network.

Service orchestrator:
: Refers to a functional entity that interacts with the customer of a network service. The service orchestrator is typically responsible for the attachment circuits, the Provider Edge (PE) selection, and requesting the activation of the requested service to a network controller.

Service provider network:
: A network that is able to provide network services (e.g., Network Slice Services).

Service provider:
: A service provider that offers network services (e.g., Network Slice Services).

# Sample Uses of the Attachment Circuit Data Models

{{u-ex}} shows the positioning of the AC network model in the overall service delivery process.

~~~~ aasvg
{::include ./figures/arch.txt}
~~~~
{: #u-ex title="An Example of the Network AC Model Usage" artwork-align="center"}

# Description of the Attachment Circuit YANG Module

The full tree diagram of the module can be generated using the
"pyang" tool {{PYANG}}.  That tree is not included here because it is
too long ({{Section 3.3 of ?RFC8340}}).  Instead, subtrees are provided
for the reader's convenience.

## Overall Structure of the Module

The overall tree structure of the module is shown in {{o-ntw-tree}}. A node can host one or more SAPs. As per {{!RFC9408}}, a SAP is an abstraction of the network
reference points (the PE side of an AC, in the context of this document) where network services can be delivered and/or are delivered to customers. Each SAP terminates one or multiple ACs. Each AC in turn may be terminated by one or more peer SAPs. In order to expose such AC/SAP binding information, the SAP model {{!RFC9408}} is augmented with required AC-related information. Also, in order to ease the correlation between the AC exposed at the service layer and the one that is actually provisioned in the network operation, a reference to the AC exposed to the customer ('ac-svc-ref') is stored in the 'ac-ntw' module. A controller may, for example, indicate a filter based on the service type (e.g., Network Slice or L3VPN) to retrieve the list of available ACs for that service.

Unlike the AC service model {{!I-D.ietf-opsawg-teas-attachment-circuit}}, an AC is uniquely identified within the scope of a node, not a network. An AC can be characterized using Layer 2 connectivity, Layer 3 connectivity, routing protocols, OAM, and security considerations. In order to factorize a set of data that is provisioned for a set of ACs, a set of profiles can be defined at the network level, and then called under the node level. The information contained in a profile is thus inherited, unless the corresponding data node is refined at the AC level. In such a case, the value provided at the AC level takes precedence over the global one.

In contexts where the same AC is terminated by multiple CEs but a subset of them have specific information, the module allows operators to define:

* A parent AC that may list all these CEs as peer SAPs.
* Individual ACs that are bound to the parent AC using "ac-parent-ref".
* Individual ACs will list one or a subset of the CEs as peer SAP.


~~~~
  augment /nw:networks/nw:network:
    +--rw specific-provisioning-profiles
    |  ...
    +--rw ac-profile* [name]
       ...
  augment /nw:networks/nw:network/nw:node/sap:service/sap:sap:
    +--rw ac* [name]
       +--rw name                 string
       +--rw ac-svc-ref?              ac-svc:attachment-circuit-reference
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
          ...
~~~~
{: #o-ntw-tree title="Overall Tree Structure"}

The full tree of the 'ac-ntw' is provided in {{AC-Ntw-Tree}}.

## Provisioning Profiles

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


## L2 Connection

The  Layer 2 connection tree structure is shown in {{l2-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/l2-tree.txt}
~~~~
{: #l2-tree title="Layer 2 Connection Tree Structure"}

## IP Connection

The  Layer 3 connection tree structure is shown in {{l3-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/l3-tree.txt}
~~~~
{: #l3-tree title="IP Connection Tree Structure"}

In some deployment contexts (e.g., network merging), multiple IP subnets may be used in a transition period. For such deployments, multiple ACs (typically, two) with overlapping information may be maintained during a transition period. The correlation between these ACs may rely upon the same "ac-svc-ref".

## Routing

The routing tree structure is shown in {{rtg-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/rtg-tree.txt}
~~~~
{: #rtg-tree title="Rotuing Tree Structure"}

### Static Routing {#sec-static-rtg}

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
    :  Indicates which action to execute when the
         maximum number of BGP prefixes is reached.  Examples of such
         actions include sending a warning message, discarding extra
         paths from the peer, or restarting the session.

    'restart-timer':
    :  Indicates, in seconds, the time interval after
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
: Parameters that are provided at the 'neighbor' level takes precedence over the one provided in the peer group.


### OSPF {#sec-ospf-rtg}

 The following OSPF data nodes are supported:

'address-family':
:  Indicates whether IPv4, IPv6, or both address
      families are to be activated.
: When the IPv4 or dual-stack address family is requested, it is up
      to the implementation (e.g., network orchestrator) to decide
      whether OSPFv2 {{!RFC4577}} or OSPFv3 {{!RFC6565}} is used to announce
      IPv4 routes.  Such a decision will typically be reflected in the
      device configurations that will be derived to implement the L3VPN.

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

## OAM

The OAM tree structure is shown in {{oam-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/oam-tree.txt}
~~~~
{: #oam-tree title="OAM Tree Structure"}

## Security

The security tree structure is shown in {{sec-tree}}.

~~~~
{::include ./yang/subtrees/ac-ntw/security-tree.txt}
~~~~
{: #sec-tree title="Security Tree Structure"}

## Service

The service tree structure is shown in {{svc-tree}}.

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

#  YANG Module

This module uses types defined in {{!RFC6991}}, {{!RFC8177}}, {{!RFC8294}}, {{!RFC8343}}, {{!RFC9067}}, {{!RFC9181}}, {{!I-D.ietf-opsawg-teas-common-ac}}, and IEEE Std 802.1Qcp.

~~~~ yang
<CODE BEGINS> file "ietf-ac-ntw@2022-11-30.yang"
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

   * TBC
   * TBC

   Some of the readable data nodes in this YANG module may be considered
   sensitive or vulnerable in some network environments.  It is thus
   important to control read access (e.g., via get, get-config, or
   notification) to these data nodes.  These are the subtrees and data
   nodes and their sensitivity/vulnerability in the "ietf-ac-svc" module:

   * TBC
   * TBC

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

# Acknowledgments
{:numbered="false"}

This document builds on {{!RFC9182}} and {{!RFC9291}}.

Thanks to Moti Morgenstern for the review and comments.
