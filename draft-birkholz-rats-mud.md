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

Manufacturer Usage Description (MUD) files and the MUD URIs that point to them are defined in RFC 8520.
This document introduces a new type of MUD file to be delivered in conjunction with a MUD file signature and/or to be referenced via a MUD URI embedded in other documents or messages, such as an IEEE 802.1AR Secure Device Identifier (DevID).
These signed documents can be presented to other entities, e.g., a network management system or network path orchestrator.
If this entity is also a verifier as defined by the IETF Remote ATtestation procedureS (RATS) architecture, this verifier can use the references found in the MUD file specified in this document to discover, for example,  appropriate Reference Value Providers, Endorsement Documents, or Verifier Services.
All theses references to resources and services can be a prerequisite to enable RATS by enabling discovery or improve findability of these required resources or services.

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

1.) Secure Device Identifiers (IEEE 802.1AR DevIDs) as defined by {{-mud}} and
2.) Entity Attestation Tokens (EAT) as defined by {{-eat}}.

DevIDs and EATs (essentially CWTs ) are two very prominent examples of "trustworthy documents" (TDs) with a binary format and the embedding of MUD URIs in theses TDs can be applied to other TD types, for example, Selective Disclosure CWTs {{-sd-cwt}}.
Other TDs are out-of-scope of this specification, though.
The TDs are typically enrolled on Attesters by manufacturers or provisioned by supply chain entities with appropriate authority.
The TDs can be presented to local Network Management Systems, AAA-services (e.g., via IEEE 802.1X), or other points of first contact (POFC), for example, {{RFC8071}}.
These POFC are typically trusted third parties (TTP) that can digest the TDs and then base trust decisions on the associated certification paths and trust anchors.
If a TD presented by the Attester is deemed to be trusted by a local trust authority, the MUD URI embedded is considered to be a trusted source for viable resources and services in support of remote attestation of the Attester.

This specification does not define the shape or format of any resource or service that is referenced by the MUD file.
In support of a unified mechanism to categorize the formats of referenced resources, a conceptual message wrapper (CMW, {{-cmw}} is used for each type of resource.
An example of a referenced resource is a CoRIM tag {{-corim}}.

## Requirements Notation

{::boilerplate bcp14-tagged}

# MUD URIs in Trustworthy Documents

This document does neither modify nor augment the definition about how to compose a MUD URI.
The two TDs covered by this specification are Secure Device Identifiers and Entity Attestation Tokens.

## MUD URIs in DevIDs

{{-mud}} defines the format of how to embed MUD URIs in DevIDs and that specification is used in this document.

## MUD URIs in EATs

To embed a MUD URI in an EAT, the mud-sig claim specified in this document MUST be used.

# MUD File Signatures

As the resources required by a Verifier's appraisal procedures have to be trustworthy, a MUD signature file for a corresponding MUD File MUST be available.
The MUD File MUST also include a reference to its MUD signature file via the 'mud-signature' statement.
A MUD signature MAY be referenced in the TD, but entities consuming this information must be aware that a MUD File can change.
If a MUD file changed (i.e., the MUD signature in the TD does not match any more) or the signature is expired, a reference to a MUD signature file MUST be included the MUD File itself and the MUD File Signature MUST be available.
If both the signature embedded in a TD and any signature referenced by a MUD File do not match, the MUD File MUST NOT be trusted.

## MUD File Signatures in DevIDs

TBD

## MUD File Signatures in EATs

To embed a MUD File Signature in an EAT, the mud-sig claim specified in this document MUST be used and the mud-file claim MUST be present.
The value of the claim is a CBOR byte-wrapped MUD File Signature as specified in {{Section 13.1 of -mud}}.

# Trusting MUD URIs and MUD Files

The level of assurance about the authenticity of a MUD URI embedded in a TD is based on the level of trust put into the corresponding trust anchor associated with the key material that signed the TD.
If it is not possible to establish a level of trust towards the entity that signed a TD, the embedded MUD URI MUST NOT be trusted.

The level of assurance about the integrity of a MUD file is based on the level of trust put into the entity that created the corresponding MUD File Signature.
If is not possible to establish a level of trust into the corresponding trust anchor associated with the MUD signature file, the referenced MUD File MUST NOT be trusted.

## Trusting RATS Resources Referenced by a MUD File

Resources, e.g., RATS Conceptual Messages, that are referenced by a MUD File MUST be signed.
The signing procedures, the format of corresponding identity documents, and the establishment of trust relationships associated with these resources are out-of-scope of this document.

# Specification of RATS MUD Files Referenced by MUD URIs

The MUD URI embedded in a TD presented by an Attester points to a MUD File.
MUD URIs typically point to a piece of data that is a YANG-modeled XML file with a structure specified in the style of a YANG module definition ({{RFC7950}} and corresponding updates: {{RFC8342}}, {{RFC8526}}).
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
