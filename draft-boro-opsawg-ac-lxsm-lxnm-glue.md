---
title: "A YANG Data Model for Augmenting VPN Service and Network Models with Attachment Circuits"
abbrev: "AC Glue for VPN Models"
category: std

docname: draft-boro-opsawg-ac-lxsm-lxnm-glue-latest
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

author:
 -
    fullname: Mohamed Boucadair
    organization: Orange
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

   The document specifies a module that updates existing service and
   network VPN modules with the required information to bind specific
   services to ACs that are created using the AC service model.

--- middle

# Introduction

   The document specifies a module that updates existing service and
   network VPN modules with the required information to bind specific
   services to ACs that are created using the AC service model, specifically the following modules are augmented:

* The Layer 2 Service Model (L2SM) {{!RFC8466}}
* The Layer 3 Service Model (L3SM) {{!RFC8299}}
* The Layer 2 Network Model (L2NM) {{!RFC9291}}
* The Layer 3 Network Model (L3NM) {{!RFC9182}}

DIVE MORE INTO the use of ac-ref, sap, etc.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

The meanings of the symbols in the YANG tree diagrams are defined in {{?RFC8340}}.

This document uses terms defined in {{!I-D.boro-opsawg-teas-attachment-circuit}}.


# Sample Uses of the Data Models

## ACs Terminated by One or Multiple Customer Edges (CEs)

{{uc}} depicts two target topology flavors that involve ACs. These topologies have the following characteristics:

* A Customer Edges (CEs) can be either a physical device or a logical entity. Such logical entity is typically a software component (e.g., a virtual service function that is hosted within the provider's network or a third-party infrastructure). A CE is seen by the network as a peer SAP.

* An AC service request may include one or multiple ACs, which may be associated to a single CE or multiple CEs.

* CEs may be either dedicated to one single connectivity service or host multiple connectivity services (e.g., CEs with roles of service functions {{?RFC7665}}).

* A network provider may bind a single AC to one or multiple peer SAPs (e.g., CE#1 and CE#2 are tagged as peer SAPs for the same AC). For example, and as discussed in {{!RFC4364}}, multiple CEs can be attached to a PE over the same attachment circuit. This scenario is typically implemented when the layer 2 infrastructure between the CE and the network is a multipoint service.

* A single CE may terminate multiple ACs, which can be associated with the same bearer or distinct bearers.

* Customers may request protection schemes in which the ACs associated with their endpoints are terminated by the same PE (e.g., CE#3), distinct PEs (e.g., CE#34), etc. The network provider uses this request to decide where to terminate the AC in the network provider network and also whether to enable specific capabilities (e.g., Virtual Router Redundancy Protocol (VRRP)).


~~~~
┌───────┐                ┌────────────────────┐           ┌───────┐
│       ├──────┐         │                    ├────AC─────┤       │
│ CE#1  │      │         │                    ├────AC─────┤ CE#3  |
└───────┘      │         │                    │           └───────┘
               ├───AC────┤     Network        │
┌───────┐      │         │                    │
│       │      │         │                    │           ┌───────┐
│ CE#2  ├──────┘         │                    │─────AC────┤ CE#4  │
└───────┘                │                    │           └────+──┘
                         └───────────+────────┘                |
                                     |                         |
                                     └────────────AC───────────┘
~~~~
{: #uc title='Examples of ACs' artwork-align="center"}

## Separate AC Provisioning vs. Actual Service Provisioning

The procedure to provision a service in a service provider network may depend on the practices adopted by a service provider. This includes the flow put in place for the provisioning of advanced network services and how they are bound to an attachment circuit. For example, a single attachment circuit may be used to host multiple connectivity services. In order to avoid service interference and redundant information in various locations, a service provider may expose an interface to manage ACs network-wide. Customers can then request a bearer or an attachment circuit to be put in place, and then refer to that bearer or AC when requesting services that are bound to the bearer or AC.

{{u-ex}} shows the positioning of the AC service model is the overall service delivery process.

~~~~
                          +---------------+
                          |   Customer    |
                          +-------+-------+
          Customer Service Model  |
          e.g., slice-svc, ac-svc,| and bearer-svc
                          +-------+-------+
                          |    Service    |
                          | Orchestration |
                          +-------+-------+
           Network Model          |
  e.g., l3vpn-ntw, sap, and ac-ntw|
                          +-------+-------+
                          |   Network     |
                          | Orchestration |
                          +-------+-------+
    Network Configuration Model   |
                      +-----------+-----------+
                      |                       |
             +--------+------+       +--------+------+
             |    Domain     |       |     Domain    |
             | Orchestration |       | Orchestration |
             +---+-----------+       +--------+------+
  Device         |        |                   |
  Configuration  |        |                   |
  Model          |        |                   |
            +----+----+   |                   |
            | Config  |   |                   |
            | Manager |   |                   |
            +----+----+   |                   |
                 |        |                   |
                 | NETCONF/CLI..................
                 |        |                   |
               +--------------------------------+
 +----+ Bearer |                                | Bearer +----+
 |CE#1+--------+            Network             +--------+CE#2|
 +----+        |                                |        +----+
               +--------------------------------+
  Site A                                                  Site B
~~~~
{: #u-ex title="An Example of AC Model Usage" artwork-align="center"}


## Tree Structure

ACs created using the "ietf-ac-svc" module can be referenced in other
   modules (e.g., L2SM, L3SM, L2NM, L3NM, and Slicing).  Some
   augmentations are required to that aim as shown in {{tree}}.

~~~~~~~~~~
{::include ./yang/full-trees/ac-glue-tree.txt}
~~~~~~~~~~
{: #tree title="AC Glue Tree Structure" artwork-align="center"}

# The AC Glue ("ietf-ac-glue") YANG Module

This module uses types defined in xxxx.

~~~~~~~~~~
<CODE BEGINS> file ietf-ac-svc@2022-11-30.yang
{::include ./yang/example-ac-glue.yang}
<CODE ENDS>
~~~~~~~~~~

# Security Considerations

   The YANG module specified in this document defines schema for data
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
   vulnerability in the "ietf-ac-svc" module:

   * TBC
   * TBC

   Some of the readable data nodes in this YANG module may be considered
   sensitive or vulnerable in some network environments.  It is thus
   important to control read access (e.g., via get, get-config, or
   notification) to these data nodes.  These are the subtrees and data
   nodes and their sensitivity/vulnerability in the "ietf-ac-svc" module:

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
   format would afford greater key entropy with the same number of key-
   string octets.  However, such a format is not included in this
   version of the AC service model, because it is not supported by the underlying
   device modules (e.g., {{?RFC8695}}).


# IANA Considerations

   IANA is requested to register the following URI in the "ns" subregistry within
   the "IETF XML Registry" {{!RFC3688}}:

~~~~
   URI:  urn:ietf:params:xml:ns:yang:ietf-ac-glue
   Registrant Contact:  The IESG.
   XML:  N/A; the requested URI is an XML namespace.
~~~~

   IANA is requested to register the following YANG module in the "YANG Module
   Names" subregistry {{!RFC6020}} within the "YANG Parameters" registry.

~~~~
   Name:  ietf-ac-glue
   Maintained by IANA?  N
   Namespace:  urn:ietf:params:xml:ns:yang:ietf-ac-glue
   Prefix:  ac-glue
   Reference:  RFC xxxx
~~~~

--- back

# Examples {#examples}

This section includes a xx


# Acknowledgments
{:numbered="false"}

Thanks to TBC for the comments.
