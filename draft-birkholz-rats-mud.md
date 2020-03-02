---
title: MUD-Based RATS Service Discovery
abbrev: Muddy Rats
docname: draft-birkholz-rats-mud-latest
stand_alone: true
ipr: trust200902
area: Security
wg: SACM Working Group
kw: Internet-Draft
cat: std
pi:
  toc: yes
  sortrefs: yes
  symrefs: yes

author:
- ins: H. Birkholz
  name: Henk Birkholz
  org: Fraunhofer SIT
  abbrev: Fraunhofer SIT
  email: henk.birkholz@sit.fraunhofer.de
  street: Rheinstrasse 75
  code: '64295'
  city: Darmstadt
- ins: E. Lear
  name: Eliot Lear
  org: Cisco Systems
  email: lear@cisco.com
  street: Richtistrasse 7
  code: 'CH-8304'
  city: Wallisellen
  country: Switzerland
  phone: +41 44 878 9200
  country: Germany

normative:
  BCP26: RFC8126
  BCP178: RFC6648
  RFC2119:
  RFC8288:
  RFC8520:
  RFC7950:
  RFC8071:
  RFC8342:
  RFC8510:
  RFC8526:

informative:
  RFC4949:
  I-D.ietf-rats-architecture: rats-arch
  I-D.ietf-rats-eat: eat
  
--- abstract

Manufacturer Usage Description (MUD) files and the MUD URI that point to them are defined in RFC 8520. This document introduces a new type of MUD file to be delivered in composition with a MUD file signature or to be embedded in an IEEE 802.1AR Secure Device Identifier (DevID). A DevID is a device specific pub-key identity document that can be presented to other entities, e.g. a network management system (NMS). If this entity is also a verifier as defined by the IETF Remote ATtestation procedureS (RATS) architecture, this verifier can use the reference in the MUD file specified in this document in order to discover appropriate Reference Integrity Measurements (RIM), Endorsement Documents, or even globally suitable Remote Attestation Services (RAS). All three types of theses resources are required to conduct RATS. Hence, the MUD file defined in this document enables remote attestation procedures by supporting the discovery of required resources or services.

--- middle

# Introduction

Verifiers, Endorsers, and Attesters are roles defined in the RATS Architecture {{-rats-arch}}. In the RATS architecture, the Attester role relies on the Verifier and Endorser roles to enable the appraisal of Attestation Evidence produced by said Attesters; and to create corresponding Attestation Results. Attestation Results compose a believable chunk of information that can be digested by Relying Parities in order to assess an Attester's trustworthiness. The assessment of a remote peer's trustworthiness is vital to steer any future protocol interaction between the Attester and the remote Relying Party. To create these Attestation Results to be consumed by Relying Parties, the Attestation Evidence an Attester creates has to be processed by one or more appropriate Verifiers.

This document defines a procedure that enables the discovery of viable resources for RATS services in a local scope:
1.) Reference Integrity Measurements, and
2.) Endorsement documents.

Additionally, a third option is provided: this document defines the option to enable the discovery of remote Verfiers:
3.) Remote Attestation Services (RAS) in a global scope (if no local trusted authorities are available).

Attestation Policies and Endorsements are required to enable an appropriate appraisal of Attestation Evidence in a fashion that helps Relying Parties to digest the corresponding Attestation Results. This document defines the use of MUD URIs embedded in Secure Device Identifiers (IEEE 802.1AR DevIDs) as defined by {{RFC8520}}. These DevIDs are enrolled on the Attester by manufacturers or a related supply chain entities with appropriate authority. The DevIDs can be presented to local Network Management Systems, AAA-services (e.g. via IEEE 802.1X), or other points of first contact (e.g. {{RFC8071}}) in a local scope. These local entities of authority can digest the DevID and conduct trust decisions based on the DevID by tracing associated certification paths and trust anchors {{RFC4949}}. If the DevID presented by the Attester is deemed to be trusted by the local trust authority, the MUD URI embedded is considered to be a trusted source of viable (and if their Identity Documents are also to be trusted - believable) Attestation Policies, Endorsements, and even globally available RAS.

In essence, the MUD file that is referenced by the DevID presented refers to sources of Attestation Policies and Endorsements recommended by the manufacturer or related supply chain entities with appropriate authority. These sources are required by appraisal procedures conducted by Verifiers in order to create well-founded Attestation Results. For example, trusted Endorses can enrich Attestation Results by vouching for the quality of hardware components, the composition of a Composite Attester, or other security characteristics of an Attester.

This specification does not define the format of Attestation Policies and Endorsements. A type of Attestation Policies and their representation is defined in [I-D tbd]. A specific format of Endorsements for hardware components based on {{-eat}} is defined in [I-D tbd].

## Requirements Notation

{::boilerplate bcp14}

# MUD URIs in DevIDs

The MUD URI embedded in a DevID presented by an Attester points to a MUD file. {{RFC8510}} defines the format of how to embed MUD URIs in Secure Device Identifiers. This document uses this specification and does neither modify nor augment it.

# Specification of RATS MUD files referenced by MUD URIs

The MUD URI embedded in a DevID presented by an Attester points to a MUD file.
At the time of writing this -00 I-D, MUD URIs always point to a piece of data that is a YANG-modeled XML file with a structure specified in the style of a YANG module definition ({{RFC7950}} and corresponding updates: {{RFC8342}}, {{RFC8526}}). This document specified the YANG module definition for a RATS MUD file. The following definition MUST be used, if a MUD URI points to a RATS MUD file.

~~~~ CDDL
<CODE BEGINS>
{::include preliminary-rats-mud-file-module.yang}
<CODE ENDS>
~~~~

--- back
