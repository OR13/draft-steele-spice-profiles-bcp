---
title: "Digital Credential Profiles Best Current Practices"
abbrev: "DCP-BCP"
category: info

docname: draft-steele-spice-profiles-bcp-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Security"
workgroup: "Secure Patterns for Internet CrEdentials"
keyword:
 - digitial credentials
 - data model profiles
 - policy guidance
venue:
  group: "Secure Patterns for Internet CrEdentials"
  type: "Working Group"
  mail: "spice@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/spice/"
  github: "OR13/draft-steele-spice-profiles-bcp"
  latest: "https://OR13.github.io/draft-steele-spice-profiles-bcp/draft-steele-spice-profiles-bcp.html"

author:
 -
    fullname: "Orie Steele"
    organization: Transmute
    email: "orie@transmute.industries"
 -
    fullname: "Michael Prorock"
    organization: mesur.io
    email: "mprorock@mesur.io"
 -
    fullname: "Mahmoud Alkhraishi"
    organization: mavennet
    email: "mahmoud@mavennet.com"

normative:
  RFC4949: SEC-GLOSS
  RFC8610: CDDL
  RFC9518: RFC9518
  I-D.draft-ietf-cbor-cde: CDE


informative:
  I-D.draft-petithuguenin-rfc-ontology: RFC-ONT




--- abstract

Digital Credentials are frequently modeled on documents, forms, and messages
that enable a holder to prove they have attributes that are acceptable to a verifier.

This document provides guidance to verifiers enabling them to describe their requirements
such that they can be translated into digital credential profiles.

--- middle

# Introduction

Verifiers have digital credential requirements that reduce their liability,
improve their transaction throughput, comply with local, regional, or international
laws, and support their environmental, social and governance objectives and values.

Requirements are often expressed as "Policy Documents", and furnished to holders,
to enable them to easily comply. For example, sometimes to receive a new
credential, a holder may need to present one or more existing credentials, and
different regional agencies might have unique requirements regarding the quality,
age, and issuing authority of these credentials.

Not all the attributes of a credential may be necessary to disclose, and the details
of the serialization are often less relevant to the verifier than
the authenticity and integrity of the credential attributes.

Verifiers need to update their policies as new credential formats become
available, but still need to ensure that mandatory attributes are disclosed,
even while changing the securing mechanisms and serialization details.

Depending on how a verifier wrote their policy, the process of updating it
to take advantage of new capabilities, safer cryptography,
smaller message sizes, or advances in data minimization, can be difficult.

This document provides guidance to policy writers, enabling them to construct
policies that can be translated into human and machine verifiable profiles,
enabling digital credential formats to evolve with the speed and precision
at which policies can be written.

# Terminology

{::boilerplate bcp14-tagged}

Ontology:
: a set of concepts and categories in a subject area or domain that shows their properties and the relations between them. (oxford dictionary citation fixme)

# Ontologies

Verifiers can share their view of a subject area or domain by acknowledging or leveraging ontologies.
Ontologies can be leveraged to model information requirements, with or without requiring the data
format to explicitly encode the ontology or leverage the ontology directly in the construction of
digital credentials.

For example, the ontology defined in {{-RFC-ONT}} can be used to describe the information
required in a digital credential for RFCs, without requiring the digital credential to contain
the literal strings used to serialize the ontology, for example the string: "ftp://shalmaneser.org/rfc#area",
need not be present, so long as the verifier describes that the string "area" is equivalent in their
profile text.

Policy writers SHOULD distinguish between the information they require,
and the ontologies that can express the concepts needed to understand the information.

# Information vs Data

Information is abstract, we can express the same information in many different ways, including in many different serializations.

The following statement for example, expresses information:

~~~
Alice believes Bob is 42 years old.
~~~

That can also be expressed in different data structures, while preserving the information:

in JSON:

~~~json
{
  "alice": {
    "knows": {
      "bob": {
        "age": {
          "42": "years"
        }
      }
    }
  }
}
~~~


Verifiers can write policies that mandate a single method to encode information in a serialization
and allow only one serialization. This can reduce both the cost to implement and the attack surface
associated with the digital credentials that are acceptable to the verifier.

However, if a new serialization is invented, that is simpler to support, and more directly aligns
with the values of the verifier and their mission objectives, having such a policy could prevent the
verifier from adopting the new and improved serialization, even if it secures the same information
and provides additional benefits, beyond integrity and authenticity, such as compactness, reduced
computation and storage costs, or safer formal modeling capabilities.

Policy writers SHOULD distinguish between the information they require,
and the acceptable serializations that can express this information required.

# Schema vs Definition

Once a verifier has documented their information requirements, and selected data formats
capable of expressing the required information while satisfying their policies and values,
the details of the acceptable data format SHOULD be considered.

There are a number of subtle details regarding octet encodings that can lead to security
or performance issues in digital credential formats.

For example, understanding the allowed data types for expressing information, be it an integer,
a floating point number, a string, or a boolean value.

Policy writers SHOULD describe the allowed data types for the expression of information,
and SHOULD NOT support polymorphic types.

Schema or data definition languages such as {{-CDDL}} SHOULD be used when describing
acceptable expressions of information models, so that validation of data instances
can be automated.

# Designing Data Structures

Although schema or data definition languages can help address some common security issues
such as validation as described in {{-SEC-GLOSS}}, there are still problematic
expressions of information which should generally be avoided even when fully specifying data.

Most commonly deeply nested data structures, or lists of deeply nested data structures containing lists.

Most digital credentials are about asserting attributes of a subject, in a way that is secured by the issuer,
and provable by the holder.

This can naturally be expressed using a simple map data type.

~~~ cddl
subject-attributes = {
  ; identifier
  ? &(id: 1) => int,
  ; age
  ? &(age: 2) => int,
}
~~~

Strings and arbitrary length data structures SHOULD be avoided, whenever possible.

As the issuer secures the data, the interpretation of the data is always in the context of:

1. the definitions published by the issuer.
2. the data the issuer chose to secure that expresses the information.

Policy writers SHOULD leverage tabular data structures (tables, csv) whenever possible.

Policy writers SHOULD externalize definitions of data structures wherever possible, and use those
external definitions to generate relevant sections of the policy document.

Policy writers SHOULD ensure that output documents are computer readable, and that when tabular data is
embedded in a policy document, that it is clearly separated from other sections of tabular data.

Documents SHOULD be sectioned by logical concepts, and document sections dealing with the description of
data structures SHOULD be clearly identifed and not mixed with other data structure descriptions without
clear separation.

Policy writers SHOULD clearly version policy documents, and SHOULD clearly identify the date
of publication, start date of applicabiilty of the policy, and if known, the end date of
applicability.

Policy writers SHOULD clearly define the scope of the policy, and the audience to which the
policy applies to. These scope and audience definitions SHOULD take place in their own sections.

Policy writers SHOULD restrict what data is expressed in a digital credential and how the data is expressed
not just the information that is required to be present.

Policy writers SHOULD avoid making recommendations where the same information may be conveyed in many different,
but equivalent data structures. When leveraging CBOR, {{-CDE}} SHOULD be used.

Policy writers should avoid creating "frameworks" where interoperability is not immediately available {{-RFC9518}}.

# Security Considerations

TODO Security

## Cryptographic Agility

TODO Cryptographic Agility

Registries /  Pick at least 2, use at least N bit security... avoid MUST support.... recommend AT LEAST support.

## Internationalized Names

TODO Internationalized Names
Strings / domain names... Unicode.

## Exploiting Data Validation

Max depth exceeded in JSON, cannonicalization timeout in XML / JSON-LD, similar issues with large CBOR structures.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
