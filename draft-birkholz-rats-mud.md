---
title: MUD-Based RATS Resources Discovery
abbrev: Muddy Rats
docname: draft-birkholz-rats-mud-latest
stand_alone: true
ipr: trust200902
area: Security
wg: RATS Working Group
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
  email: henk.birkholz@ietf.contact
  street: Rheinstrasse 75
  code: '64295'
  city: Darmstadt

normative:
  RFC2119:
  RFC6991:
  RFC7950:
  RFC8071:
  RFC8340:
  RFC8342:
  RFC8520: mud
  RFC8526:
  RFC9334: rats-arch
  I-D.ietf-rats-eat: eat
  I-D.ietf-rats-msg-wrap: cmw
  I-D.ietf-rats-corim: corim

informative:
  RFC4949:
  I-D.ietf-spice-sd-cwt: sd-cwt

--- abstract

Manufacturer Usage Description (MUD) files and the MUD URI that point to them are defined in RFC 8520. This document introduces a new type of MUD file to be delivered in conjunction with a MUD file signature and/or to be referenced via a MUD URI embedded in an IEEE 802.1AR Secure Device Identifier (DevID).
A DevID is a device specific pub-key identity document that can be presented to other entities, e.g. a network management system. If this entity is also a verifier as defined by the IETF Remote ATtestation procedureS (RATS) architecture, this verifier can use the references found in the MUD file specified in this document in order to discover appropriate Reference Integrity Measurements (RIM), Endorsement Documents, or even globally suitable Remote Attestation Services (RAS).
All three types of theses resources are required to conduct RATS. Hence, the MUD file defined in this document enables remote attestation procedures by supporting the discovery of these required resources or services.

--- middle

# Introduction

Verifiers, Endorsers, and Attesters are roles defined in the RATS Architecture {{-rats-arch}}.
In the RATS architecture, the Relying Party roles rely on the Verifier to carry the burden of Evidence appraisal and to create corresponding Attestation Results for them.
Attestation Results compose a believable chunk of information that can be digested by Relying Parities in order to assess an Attester's trustworthiness.
The assessment of a remote peer's trustworthiness is vital to determine whether any future protocol interaction between a Relying Party and a remote Attester can be considered secure.
To create these Attestation Results to be consumed by Relying Parties, Attestation Evidence an Attester generates must to be processed by one or more appropriate Verifiers.

This document defines a procedure that enables the discovery of resources or services in support of RATS, including:
1.) Reference Values,
3.) Trust Anchors,
2.) Endorsements and Endorsement Distribution APIs,
4.) Verifier APIs, or
5.) Appraisal Policies.

MUD URIs can be embedded in any data item that was signed with trusted key material.
One common way to establish trust in a signed data item is to associate the signing key material with a trust anchor via a certification path (see {{RFC4949}} for trust anchor and certification path).
This document defines the use of MUD URIs embedded in two types of signed data items that typically are trusted via certification paths:

Attestation Policies and Endorsements are required to enable an appropriate appraisal of Attestation Evidence in a fashion that helps Relying Parties to digest the corresponding Attestation Results.
This document defines the use of MUD URIs embedded in Secure Device Identifiers (IEEE 802.1AR DevIDs) as defined by {{RFC8520}}.
These DevIDs are enrolled on the Attester by manufacturers or related supply chain entities with appropriate authority.
The DevIDs can be presented to local Network Management Systems, AAA-services (e.g. via IEEE 802.1X), or other points of first contact (e.g. {{RFC8071}}) in a local scope.
These local entities of authority can digest the DevID and conduct trust decisions based on the DevID by tracing associated certification paths and trust anchors {{RFC4949}}.
If the DevID presented by the Attester is deemed to be trusted by the local trust authority, the MUD URI embedded is considered to be a trusted source of viable (and if their Identity Documents are also to be trusted - believable) Attestation Policies, Endorsements, and even globally available RAS.

In essence, the MUD file that is referenced by the DevID presented refers to sources of Attestation Policies and Endorsements recommended by the manufacturer or related supply chain entities with appropriate authority.
These sources are required by appraisal procedures conducted by Verifiers in order to create well-founded Attestation Results.
For example, trusted Endorses can enrich Attestation Results by vouching for the quality of hardware components, the composition of a Composite Attester, or other security characteristics of an Attester.

This specification does not define the format of Attestation Policies and Endorsement documents.
A type of Attestation Policies and their representation is defined in [I-D tbd].
A specific format of Endorsements for hardware components based on {{-eat}} is defined in [I-D tbd].

## Requirements Notation

{::boilerplate bcp14-tagged}

# MUD URIs in Trustworthy Documents

The MUD URI embedded in a DevID presented by an Attester points to a MUD file.
{{RFC8520}} defines the format of how to embed MUD URIs in Secure Device Identifiers.
This document uses this specification and does neither modify nor augment the definition about how to compose a MUD URI.

# MUD File Signatures

As the resources required by a Verifier's appraisal procedures have to be trustworthy, a MUD signature file for a corresponding MUD File MUST be available.
The MUD File MUST also include a reference to its MUD signature file via the 'mud-signature' statement.
A MUD signature MAY be referenced in the DevID, but entities consuming this information must be aware that a MUD File can change. If MUD file changed (the MUD signature in the DevID does not match any more), a MUD signature file referenced in the MUD File itself MUST exist and MUST be available.
If both the signature embedded in a DevID and referenced by a MUD File do not match, the MUD File SHOULD NOT be trusted.

# Trusting MUD URIs and MUD Files

The level of assurance about the integrity of a MUD URI embedded in a DevID is based on the level of trust put into the corresponding trust anchor associated with the DevID.
If you cannot establish a level of trust towards the entity that signed a DevID (the signer), the embedded MUD URI SHOULD NOT be trusted.

The level of assurance about the integrity of a MUD file is based on the level of trust put into the entity that created the corresponding MUD File Signature.
If you cannot establish a level of trust put into the corresponding trust anchor associated with the MUD signature file, the referenced MUD File SHOULD NOT be trusted.

## Trusting RATS Resources Referenced by a MUD File

Reference Integrity Measurements and Endorsement documents that are referenced by a MUD File MUST be signed.
The signing procedures, the format of corresponding Identity documents, and the establishment of trust relationships associated with these resources are out-of-scope of this document.

# Specification of RATS MUD Files Referenced by MUD URIs

The MUD URI embedded in a DevID presented by an Attester points to a MUD File.
At the time of writing this -00 I-D, MUD URIs always point to a piece of data that is a YANG-modeled XML file with a structure specified in the style of a YANG module definition ({{RFC7950}} and corresponding updates: {{RFC8342}}, {{RFC8526}}).
This document specifies a YANG module augment definition for generic MUD files to create RATS MUD files.
The following definition MUST be used, if a MUD URI points to a RATS MUD file.

## Tree Diagram

The following tree diagram {{RFC8340}} provides an overview of the data model for the "ietf-mud-rats" module augment.

~~~~
<CODE BEGINS>
{::include ietf-mud-rats.tree}
<CODE ENDS>
~~~~

## YANG Module

This YANG module has normative references to {{RFC6991}} and augments {{-mud}}.

~~~~ YANG
<CODE BEGINS> file ietf-mud-rats@2025-02-09.yang
{::include ietf-mud-rats.yang}
<CODE ENDS>
~~~~

# Privacy Considerations

The privacy considerations of RFC 9334 apply.

# Security Considerations

The trust model and Security Considerations of RFC 8520 and RFC 9334 apply.

--- back
