---
title: "A YANG Data Model for Augmenting VPN Service and Network Models with Attachment Circuits"
abbrev: "AC Glue for VPN Models"
category: std

docname: draft-ietf-opsawg-ac-lxsm-lxnm-glue-latest
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
    email: rroberts@juniper.net

 -
    fullname: Samier Barguil Giraldo
    organization: Nokia
    email: samier.barguil_giraldo@nokia.com

 -
    fullname: Oscar Gonzalez de Dios
    organization: Telefonica
    email: oscar.gonzalezdedios@telefonica.com


contributor:

normative:

informative:


--- abstract

This document defines a YANG data model, referred to as the "AC Glue" model, to
augment the Layer 2/3 Service Model (LxSM) and Layer 2/3 Network Model (LxNM) with references to attachment circuits (ACs). The
AC Glue model enables a provider to associate Layer 2/3
VPN services (LxVPNs) with the underlying AC infrastructure, thereby
facilitating consistent provisioning and management of new or existing ACs in
conjunction with LxVPN services. Specifically, by introducing an integrated approach to AC
and LxVPN management, this model supports Attachment Circuit-as-a-Service
(ACaaS) and provides a standardized mechanism for aligning AC/VPN requests
with the network configurations required to deliver them.

--- middle

# Introduction

To facilitate data transfer within the provider network, it is assumed that the appropriate setup
   is provisioned over the links that connect customer termination
   points and a provider network (usually via a Provider Edge (PE)),
   allowing successfully data exchanged over these links.  The required
   setup is referred to in this document as an (((attachment circuit (AC), (((attachment circuit (AC)))),
   while the underlying link is referred to as "bearer".

The document specifies a YANG module ("ietf-ac-glue", {{sec-glue}}) that updates existing service and
network (((Virtual Private Network (VPN)))) modules with the required information to bind specific
services to ACs that are created using the AC service model {{!I-D.ietf-opsawg-teas-attachment-circuit}}. Specifically, the following modules are augmented:

* The Layer 2 Service Model (L2SM) {{!RFC8466}}
* The Layer 3 Service Model (L3SM) {{!RFC8299}}
* The Layer 2 Network Model (L2NM) {{!RFC9291}}
* The Layer 3 Network Model (L3NM) {{!RFC9182}}

Likewise, the document augments the (((L2NM))) and (((L3NM))) with references to the ACs that are managed using the AC network model {{!I-D.ietf-opsawg-ntw-attachment-circuit}}.

This approach allows operators to separate AC provisioning from actual (((VPN))) service provisioning. Refer to {{sep}} for more discussion.

The YANG data model in this document conforms to the Network
Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

Examples to illustrate the use of the "ietf-ac-glue" model are provided in {{sec-example}}.

## Editorial Note (To be removed by RFC Editor)

Note to the RFC Editor: This section is to be removed prior to publication.

This document contains placeholder values that need to be replaced with finalized values at the time of publication. This note summarizes all of the substitutions that are needed.

Please apply the following replacements:

  * XXXX --> the assigned RFC number for this I-D
  * SSSS --> the assigned RFC number for {{!I-D.ietf-opsawg-teas-attachment-circuit}}
  * NNNN --> the assigned RFC number for {{!I-D.ietf-opsawg-ntw-attachment-circuit}}
  * 2025-01-07 --> the actual date of the publication of this document

# Conventions and Definitions

The meanings of the symbols in the YANG tree diagrams are defined in {{?RFC8340}}.

This document uses terms defined in {{!I-D.ietf-opsawg-teas-attachment-circuit}}.

LxSM refers to both the (((L2SM))) and the (((L3SM))).

LxNM refers to both the L2NM and the L3NM.

The following terms are used in the modules prefixes:

ac:
: Attachment circuit

ntw:
: Network

ref:
: Reference

svc:
: Service

The names of data nodes are prefixed using the prefix associated with the corresponding imported YANG module as shown in {{pref}}:

|Prefix|	Module| Reference |
| ac-svc|ietf-ac-svc|Section 5.2 of RFC SSSS|
| ac-ntw|ietf-ac-ntw|RFC NNNN|
| l2nm|ietf-l3vpn-ntw| {{!RFC9291}}|
| l2vpn-svc|ietf-l2vpn-svc| {{!RFC8466}}|
| l3nm|ietf-l3vpn-ntw| {{!RFC9182}}|
| l3vpn-svc|ietf-l3vpn-svc| {{!RFC8299}}
{: #pref title="Modules and Their Associated Prefixes"}

# Relationship to Other AC Data Models

{{ac-overview}} depicts the relationship between the various AC data models:

* "ietf-ac-common" ({{?I-D.ietf-opsawg-teas-common-ac}})
* "ietf-bearer-svc" ({{Section 5.1 of !I-D.ietf-opsawg-teas-attachment-circuit}})
* "ietf-ac-svc" ({{Section 5.2 of !I-D.ietf-opsawg-teas-attachment-circuit}})
* "ietf-ac-ntw" ({{!I-D.ietf-opsawg-ntw-attachment-circuit}})
* "ietf-ac-glue" ({{sec-glue}})

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

The "ietf-ac-common" module is imported by the "ietf-bearer-svc", "ietf-ac-svc", and "ietf-ac-ntw" modules. Bearers managed using the "ietf-bearer-svc" module may be referenced by service ACs managed using the "ietf-ac-svc" module. Similarly, a bearer managed using the "ietf-bearer-svc" module may list the set of ACs that use that bearer. To facilitate correlation between an AC service request and the actual AC provisioned in the network, "ietf-ac-ntw" leverages the AC references exposed by the "ietf-ac-svc" module. Furthermore, to bind Layer 2 VPN or Layer 3 VPN services with ACs, the "ietf-ac-glue" module augments the LxSM and LxNM with (((AC))) service references exposed by the "ietf-ac-svc" module and AC network references exposed by the "ietf-ac-ntw" module.

# Sample Uses of the Data Models

## ACs Terminated by One or Multiple Customer Edges (CEs)

{{uc}} depicts two target topology flavors that involve ACs. These topologies have the following characteristics:

* A Customer Edge (CE) can be either a physical device or a logical entity. Such logical entity is typically a software component (e.g., a virtual service function that is hosted within the provider's network or a third-party infrastructure). A CE is seen by the network as a peer Service Attachment Point (SAP) {{?RFC9408}}.

* CEs may be either dedicated to one single connectivity service or host multiple connectivity services (e.g., CEs with roles of service functions {{?RFC7665}}).

* A network provider may bind a single AC to one or multiple peer SAPs (e.g., CE1 and CE2 are tagged as peer SAPs for the same AC). For example, and as discussed in {{?RFC4364}}, multiple CEs can be attached to a PE over the same attachment circuit. This scenario is typically implemented when the Layer 2 infrastructure between the CE and the network is a multipoint service.

* A single CE may terminate multiple ACs, which can be associated with the same bearer or distinct bearers (e.g., CE4).

* Customers may request protection schemes in which the ACs associated with their endpoints are terminated by the same PE (e.g., CE3), distinct PEs (e.g., CE4), etc. The network provider uses this request to decide where to terminate the AC in the service provider network and also whether to enable specific capabilities (e.g., Virtual Router Redundancy Protocol (VRRP)).

~~~~ aasvg
{::include ./figures/acs-examples.txt}
~~~~
{: #uc title='Examples of ACs' artwork-align="center"}

These ACs can be referenced when creating VPN services. Refer to the examples provided in {{sec-example}} to illustrate how VPN services can be bound to ACs.

## Separate AC Provisioning From Actual VPN Service Provisioning {#sep}

The procedure to provision a service in a service provider network may depend on the practices adopted by a service provider. This includes the flow put in place for the provisioning of advanced network services and how they are bound to an attachment circuit. For example, a single attachment circuit may be used to host multiple connectivity services (e.g., Layer 2 VPN ("ietf-l2vpn-svc"), Layer 3 VPN ("ietf-l3vpn-svc"), Network Slice Service ("ietf-network-slice-service")). In order to avoid service interference and redundant information in various locations, a service provider may expose an interface to manage ACs network-wide using {{!I-D.ietf-opsawg-teas-attachment-circuit}}. Customers can request an attachment circuit ("ietf-ac-svc") to be put in place, and then refer to that AC when requesting VPN services that are bound to the AC ("ietf-ac-glue").

Also, internal references ("ietf-ac-ntw") used within a service provider network to implement ACs can be used by network controllers to glue the L2NM ("ietf-l2vpn-ntw") or the L3NM ("ietf-l3vpn-ntw") services with relevant ACs.

{{u-ex}} shows the positioning of the AC models in the overall service delivery process.

~~~~ aasvg
{::include ./figures/arch.txt}
~~~~
{: #u-ex title="An Example of AC Models Usage" artwork-align="center"}


# Module Tree Structure

{{!RFC8299}} specifies that a 'site-network-access' attachment is achieved through a
'bearer' with an 'ip-connection' on top. From that standpoint, a 'site-network-access' is mapped to an attachment circuit with both Layers 2 and 3 properties per {{!I-D.ietf-opsawg-teas-attachment-circuit}}. {{!RFC8466}} specifies that a 'site-network-access' represents a logical Layer 2 connection to a site. A 'site-network-access' can thus be mapped to an attachment circuit with  Layer 2 properties {{!I-D.ietf-opsawg-teas-attachment-circuit}}. Similarly, 'vpn-network-access' defined in both {{!RFC9182}} and {{!RFC9291}} is mapped to an attachment circuit per {{!I-D.ietf-opsawg-teas-attachment-circuit}} or {{!I-D.ietf-opsawg-ntw-attachment-circuit}}.

As such, ACs created using the "ietf-ac-svc" module {{!I-D.ietf-opsawg-teas-attachment-circuit}} can be referenced in other
VPN-related modules (e.g., LxSM and LxNM). Also, ACs managed using the "ietf-ac-ntw" module {{!I-D.ietf-opsawg-ntw-attachment-circuit}} can be referenced in VPN-related network modules (mainly, the LxNM). The required augmentations to that aim are shown in {{tree}}.

~~~~~~~~~~
{::include ./yang/full-trees/ac-glue-tree.txt}
~~~~~~~~~~
{: #tree title="AC Glue Tree Structure" artwork-align="center"}

When an AC is referenced within a specific network access, then that AC information takes precedence over any overlapping information that is also enclosed for this network access.

> This approach is consistent with the design in {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}} where an AC service reference, called 'ac-svc-name', is used to indicate the names of AC services. As per {{?I-D.ietf-teas-ietf-network-slice-nbi-yang}}, when both 'ac-svc-name' and the attributes of 'attachment-circuits' are defined, the 'ac-svc-name' takes precedence.

The "ietf-ac-glue" module includes provisions to reference ACs within or outside a VPN network access to accommodate deployment contexts where an AC reference may be created before or after a VPN instance is created. {{ref-within-access}} illustrates how an AC reference can be included as part of a specific VPN network access, while {{ref-outside-access}} shows how AC references can be indicated outside individual VPN network access entries.

# The AC Glue ("ietf-ac-glue") YANG Module {#sec-glue}

This modules augments the L2SM {{!RFC8466}}, the L3SM {{!RFC8299}}, the L2NM {{!RFC9291}}, and the L3NM {{!RFC9182}}.

This module uses references defined in {{!I-D.ietf-opsawg-teas-attachment-circuit}} and {{!I-D.ietf-opsawg-ntw-attachment-circuit}}.

~~~~~~~~~~ yang
<CODE BEGINS> file "ietf-ac-glue@2025-01-07.yang"
{::include-fold ./yang/ietf-ac-glue.yang}
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

   There are a number of data nodes defined in this YANG module that are
   writable/creatable/deletable (i.e., config true, which is the
   default).  These data nodes may be considered sensitive or vulnerable
   in some network environments.  Write operations (e.g., edit-config)
   and delete operations to these data nodes without proper protection
   or authentication can have a negative effect on network operations.
   Specifically, the following subtrees and data nodes have particular
   sensitivities/vulnerabilities:

   'ac-svc-ref' and 'ac-ntw-ref':
   :  An attacker who is able to access network nodes can
      undertake various attacks, such as deleting a running VPN
      service, interrupting all the traffic of a client. Specifically,
      an attacker may modify (including delete) the ACs that are bound to a running service, leading to
      malfunctioning of the service and therefore to Service Level
      Agreement (SLA) violations.
    : Such activity can be detected by adequately monitoring and tracking
      network configuration changes.

   Some of the readable data nodes in this YANG module may be considered
   sensitive or vulnerable in some network environments.  It is thus
   important to control read access (e.g., via get, get-config, or
   notification) to these data nodes.  Specifically, the following
subtrees and data nodes have particular sensitivities/vulnerabilities:

   'ac-svc-ref' and 'ac-ntw-ref':
   :  These references do not expose per se
      privacy-related information, however 'ac-svc-ref' may be used to track
      the set of VPN instances in which a given customer is involved.
   : Note that, unlike 'ac-svc-ref', 'ac-ntw-ref' is unique within the scope of
   a node and may multiplex many peer CEs.

# IANA Considerations

   IANA is requested to register the following URI in the "ns" subregistry within
   the "IETF XML Registry" {{!RFC3688}}:

~~~~
   URI:  urn:ietf:params:xml:ns:yang:ietf-ac-glue
   Registrant Contact:  The IESG.
   XML:  N/A; the requested URI is an XML namespace.
~~~~

   IANA is requested to register the following YANG module in the "YANG Module
   Names" registry {{!RFC6020}} within the "YANG Parameters" registry group:

~~~~
   Name:  ietf-ac-glue
   Namespace:  urn:ietf:params:xml:ns:yang:ietf-ac-glue
   Prefix:  ac-glue
   Maintained by IANA?  N
   Reference:  RFC XXXX
~~~~

--- back

# Examples {#sec-example}

## A Service AC Reference within The VPN Network Access {#ref-within-access}

Let us consider the example depicted in {{ex-vpws}} which is inspired from {{Section 2.1 of ?RFC4664}}. Each PE is servicing two CEs. Let us also assume that the service references to identify attachment circuits with these CEs are shown in the figure.

~~~~~~~~~~ aasvg
{::include ./figures/glue/rfc4664-vpws-example}
~~~~~~~~~~
{: #ex-vpws title="VPWS Topology Example" artwork-align="center"}

As shown in {{ex-vpws-query}}, the service AC references can be explicitly indicated in the L2NM query for the realization of the Virtual Private Wire Service (VPWS) ({{Section 3.1.1 of ?RFC4664}}).

~~~~~~~~~~ json
{::include-fold ./json-examples/glue/example-vpws.json}
~~~~~~~~~~
{: #ex-vpws-query title="Example of VPWS Creation with AC Service References"}

## Network and Service AC References {#ref-outside-access}

Let us consider the example depicted in {{ex-topo}} with two customer termination points (CE1 and CE2). Let us also assume that the bearers to attach these CEs to the service provider network are already in place. References to identify these bearers are shown in the figure.

~~~~~~~~~~ aasvg
{::include ./figures/glue/ex-topo.txt}
~~~~~~~~~~
{: #ex-topo title="Topology Example" artwork-align="center"}

The AC service model {{!I-D.ietf-opsawg-teas-attachment-circuit}} can be used by the provider to manage and expose the ACs over existing bearers as shown in {{ex-ac}}.

~~~~~~~~~~ json
{::include-fold ./json-examples/glue/example-acsvc-vpls.json}
~~~~~~~~~~
{: #ex-ac title="ACs Created Using ACaaS"}

Let us now consider that the customer wants to request a VPLS instance between the sites as shown in {{ex-vpls}}.

~~~~~~~~~~ aasvg
{::include ./figures/glue/ex-vpls.txt}
~~~~~~~~~~
{: #ex-vpls title="Example of VPLS" artwork-align="center"}

To that aim, existing ACs are referenced during the creation of the VPLS instance using the L2NM {{!RFC9291}} and the "ietf-ac-glue" as shown in {{ex-vpls-req}}.

~~~~~~~~~~ json
{::include-fold ./json-examples/glue/example-vpls.json}
~~~~~~~~~~
{: #ex-vpls-req title="Example of a VPLS Request Using L2NM and AC Glue (Message Body)"}

Note that before implementing the VPLS instance creation request, the provider service orchestrator may first check if the VPLS service can be provided to the customer using the target delivery locations. The orchestrator uses the SAP model {{?RFC9408}} as exemplified in {{ex-sap-query}}. This example assumes that the query concerns only PE1. A similar query can be issued for PE2.

~~~~~~~~~~ json
{::include-fold ./json-examples/glue/example-sap-query.json}
~~~~~~~~~~
{: #ex-sap-query title="Example of SAP Response (Message Body)"}

The response in {{ex-sap-query}} indicates that the VPLS service can be delivered to CE1. {{!I-D.ietf-opsawg-ntw-attachment-circuit}} can be also used to access AC-related details that are bound to the target SAP ({{ex-acntw-query-2}}).

~~~~~~~~~~ json
{::include-fold ./json-examples/glue/example-acntw.json}
~~~~~~~~~~
{: #ex-acntw-query-2 title="Example of AC Network Response with SAP (Message Body)"}

The provisioned AC at PE1 can be retrieved using the AC network model {{!I-D.ietf-opsawg-ntw-attachment-circuit}} as depicted in {{ex-acntw-query}}.

~~~~~~~~~~ json
{::include-fold ./json-examples/glue/example-acntw-2.json}
~~~~~~~~~~
{: #ex-acntw-query title="Example of AC Network Response (Message Body)"}

# Acknowledgments
{:numbered="false"}

Thanks to Bo Wu and Qin Wu for the review and comments.

Thanks to Martin Björklund for the yangdoctors review, Gyan Mishra for the rtg-dir review, Ron Bonica for the opsdir review,
Reese Enghardt for the genart review, and Prachi Jain for the sec-dir review.

Thanks to Mahesh Jethanandani for the AD review.

Thanks to Gunter Van de Velde for the IESG review.
