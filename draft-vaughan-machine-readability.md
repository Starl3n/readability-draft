---
title: "Defining Machine Readability for Usage Preferences and Policy Expression"
abbrev: "Machine Readability"
category: info

docname: draft-vaughan-machine-readability-latest
submissiontype: IETF
ipr: trust200902
number:
date:
v: 3
keyword:
 - machine readable
 - usage preferences
 - policy expression
venue:
  github: "thunderpoot/readability-draft"

author:
 -
    fullname: "Thom Vaughan"
    organization: Common Crawl Foundation
    email: "thom@commoncrawl.org"

informative:
  RFC3444:
  RFC9309:
  I-D.ietf-aipref-vocab:
  I-D.ietf-aipref-attach:

  ODRL:
    target: https://www.w3.org/TR/odrl-model/
    title: "ODRL Information Model 2.2"
    author:
      - name: Renato Iannella
      - name: Serena Villata
    date: 2018-02-15
    seriesinfo:
      W3C: Recommendation

  DPV:
    target: https://www.w3.org/community/reports/dpvcg/CG-FINAL-dpv-20240801/
    title: "Data Privacy Vocabulary (DPV)"
    author:
      - name: Harshvardhan J. Pandit
      - name: Beatriz Esteves
      - name: Georg P. Krog
    date: 2024-08-01
    seriesinfo:
      W3C: Community Group Final Report

  CCREL:
    target: https://www.w3.org/submissions/2008/02/
    title: "ccREL: The Creative Commons Rights Expression Language"
    author:
      - name: Hal Abelson
      - name: Ben Adida
      - name: Mike Linksvayer
      - name: Nathan Yergler
    date: 2008-08-20
    seriesinfo:
      W3C: Member Submission

  TDMREP:
    target: https://www.w3.org/community/reports/tdmrep/CG-FINAL-tdmrep-20240510/
    title: "TDM Reservation Protocol (TDMRep)"
    author:
      - name: Laurent Le Meur
    date: 2024-05-10
    seriesinfo:
      W3C: Community Group Final Report

  SCHEMA-ORG:
    target: https://schema.org/license
    title: "schema.org: license property"
    author:
      - org: Schema.org

  DCTERMS:
    target: https://www.dublincore.org/specifications/dublin-core/dcmi-terms/
    title: "DCMI Metadata Terms"
    author:
      - org: Dublin Core Metadata Initiative
    date: 2020-01-20
    seriesinfo:
      DCMI: Recommendation

  C2PA:
    target: https://c2pa.org/specifications/
    title: "Coalition for Content Provenance and Authenticity (C2PA) Technical Specification"
    author:
      - org: Coalition for Content Provenance and Authenticity

  CAWG-TDM:
    target: https://cawg.io/training-and-data-mining/
    title: "Training and Data Mining Assertion"
    author:
      - org: Creator Assertions Working Group

  FAIR:
    target: https://doi.org/10.1038/sdata.2016.18
    title: "The FAIR Guiding Principles for scientific data management and stewardship"
    author:
      - name: Mark D. Wilkinson
      - name: Michel Dumontier
      - name: IJsbrand Jan Aalbersberg
      - name: Barend Mons
    date: 2016-03-15
    seriesinfo:
      DOI: 10.1038/sdata.2016.18

  DSM:
    target: https://eur-lex.europa.eu/eli/dir/2019/790/oj
    title: "Directive (EU) 2019/790 of the European Parliament and of the Council on copyright and related rights in the Digital Single Market"
    author:
      - org: European Parliament and Council of the European Union
    date: 2019-04-17

--- abstract

The term "machine readable" is widely invoked when content usage
preferences, rights, and legal terms are expressed for automated
consumption, but it is rarely defined with enough precision to be
actionable.  This document resolves the term into two core criteria,
parseability and interpretability, which are properties of an
expression itself, and three further dimensions (discoverability,
actionability, and verifiability) which are orthogonal to the core
criteria and to one another, concerning as they do the expression's
place in a transaction rather than its content alone.  Together these
determine whether an expression of preferences or policy can be
reliably acted upon by a non-human agent without human intervention.
This document applies the framework to usage preferences and legal
terms of service.


--- middle

# Introduction {#introduction}

Expressions of how content may be used by automated systems are increasingly published in forms described as "machine readable".  Operators of crawlers and other automated Agents are expected to discover these Expressions, determine what they permit or forbid, and act accordingly.  The term "machine readable" is invoked frequently in this context, in standards work, in policy, and in legislation, but it is rarely defined with enough precision to tell an implementer whether a given Expression actually supports automated action.

The difficulty is that "machine readable" names several distinct properties that are commonly conflated. An Expression may be serialised in a structured syntax, and so be straightforward for a program to parse, while still conveying nothing a program can act upon.  A terms-of-service document placed verbatim in a JSON string sits inside a parseable envelope, but the terms themselves parse no better than the same text in a web page.  What a conforming Agent recovers is a string of legalese, not a structured representation of anything upon which it can act.  Conversely, an Expression may be richly actionable yet undiscoverable, or trivially discoverable yet unverifiable.  Treating "machine readable" as a single binary property obscures these differences, and permits a Declaring Party to claim machine readability on the strength of whichever property is cheapest to provide, typically parseability, while failing the ones that actually matter for automated action.

This document separates "machine readable" into five named properties, of which two are core and three are orthogonal to them.  The core criteria, parseable and interpretable, are properties of an Expression itself: they can be assessed on the artefact alone, they are defined in {{core-criteria}}, and they are where a claim of machine readability is actually won or lost.  The remaining properties (discoverable, actionable, and verifiable, defined in {{dimensions}}) concern the Expression's place in a transaction: whether an Agent can find it, whether it settles the Agent's pending decision, and whether its origin and integrity can be established.  Each can be present or absent independently of the core criteria and of the others ({{independence}}).  The purpose of the framework is diagnostic: it allows a given mechanism to be described in terms of precisely which of these properties its Expressions provide, rather than asserted to be machine readable as an undifferentiated whole.  The framework defines no conformance threshold, and must not be read as one ({{no-threshold}}).

The framework is general, and nothing in it is specific to any subject matter: an Expression conveying bibliographic records, sensor descriptions, or recipes can be assessed against it as readily as one conveying policy.  This document applies it in particular to two cases: the expression of usage preferences for automated processing, and the expression of legal terms of service.  The latter is treated at length in {{requirements-for-legal-terms}}, because legal text exposes the distance between the core criteria and an actionable outcome most sharply.  Terms that are easy to publish in a structured form are frequently impossible to act upon without human interpretation.

This document does not define a vocabulary, a syntax, or a protocol, and it is not a product of any IETF working group.  It does not propose that any existing mechanism be changed.  Its contribution is a set of definitions against which existing and future mechanisms can be assessed.

# Terminology

Agent:
: A non-human process that acquires, processes, or acts upon content or its associated expressions without real-time human direction.  A web crawler, for instance.

Expression:
: A concrete artefact (for instance: a file, header, embedded block, or metadata record) that conveys structured information.  This document is concerned in particular with Expressions that convey preferences, rights, terms, or other policies about a Resource, but the framework does not depend on that subject matter, and the Expression may even be the Resource itself.  The Expression is the artefact rather than the assertions it carries; a single Expression may convey one statement or many.

Resource:
: The content or asset to which an Expression pertains.

Declaring Party:
: The entity asserting an Expression.

Mechanism:
: A specification or convention (a protocol, vocabulary, or metadata scheme, for instance) under which Expressions are produced.  The properties defined in {{criteria}} apply to individual Expressions; a Mechanism is assessed indirectly, through the properties that Expressions produced under it are able to satisfy.  Statements of the form "TDMRep is parseable" are shorthand for this.

# Criteria for Machine Readability {#criteria}

Machine readability, insofar as it is a property of an Expression at all, rests on the two criteria defined in {{core-criteria}}: whether the Expression is parseable, and whether it is interpretable.  These are intrinsic.  Each can be assessed on the artefact alone, without reference to who consumes it, how it was obtained, or what its consumer intends to do.  Interpretability presupposes parseability, since a term cannot be fixed against a vocabulary before it has been recovered from the artefact.

Three further properties matter in practice and are commonly folded into the same term: whether an Expression is discoverable, actionable, and verifiable.  These are defined in {{dimensions}} as dimensions orthogonal to the core criteria and to one another.  None of them is intrinsic to the artefact.  Discoverability concerns the path by which an Agent comes to hold the Expression; actionability concerns the relation between the interpreted Expression and a particular consumer's pending decision; verifiability concerns whether the Expression's origin and integrity can be established.  An Expression may possess any of them, in any combination, at any state of the core criteria, and {{independence}} illustrates how they come apart in deployed mechanisms.

Assessment against this framework therefore yields a profile rather than a score: a statement of which properties an Expression provides, and for which purposes.  {{no-threshold}} makes explicit that no particular profile constitutes a passing grade.

## Core Criteria {#core-criteria}

### Parseable {#parseable}

An Expression is parseable if its syntax is defined such that a conforming Agent recovers the same structured representation from it that any other conforming Agent would.  A test for this would be whether there is a grammar or schema against which the Expression is valid or invalid, deterministically.  Parseability is a property of the Expression and its grammar alone, and can be assessed with no knowledge of the Expression's consumers.  A terms-of-service document carried in a JSON string is a useful case about which to be precise: the JSON envelope is valid against a grammar and every conforming Agent recovers the same envelope, but the terms inside it recover only as an opaque string, with no grammar of their own.  Wrapping prose in a parseable container does not make the prose parseable, and this is the characteristic most often mistaken for machine readability as a whole.

### Interpretable {#interpretable}

An Expression is interpretable if the meaning of its parsed elements is fixed against a shared, identified vocabulary, such that two conforming Agents assign the same meaning to the same element.  A test for this is whether each term is resolvable to a definition that is itself machine-identified (like a URI or a registry entry) rather than relying on the Agent's own natural-language understanding.  This characteristic separates something like a JSON field called "may_train" (whose meaning is whatever a reader guesses) from AIPREF's train-ai, whose meaning is pinned to a specification.  Like parseability, interpretability is intrinsic: it is a property of the binding between the Expression's terms and their published definitions, not of any particular consumer.  It neither requires nor licenses interpretation by the Agent in the natural-language sense; where meaning has to be guessed, inferred, or modelled, the criterion is not met.

The division of labour between the two core criteria is an old one under new names.  Parseability and interpretability stand to one another much as structural interoperability stands to semantic interoperability: the first is agreement on form, the second agreement on meaning, and systems have always been able to exchange well-formed data that they do not understand alike.  {{RFC3444}} draws the same line between a data model, which fixes how information is encoded concretely enough to implement against, and an information model, which fixes what the modelled things are and what they mean independently of any encoding.  In those terms, an Expression that is parseable but not interpretable is one whose data model is shared while its information model remains private.

## Orthogonal Dimensions {#dimensions}

The three dimensions below are not further degrees of the core criteria, and they are not ordered among themselves.  Each concerns the Expression's place in a transaction between a Declaring Party and an Agent, and each can hold or fail independently of the others and of the core criteria.

### Discoverable {#discoverable}

An Expression is discoverable if an Agent can locate it from the Resource, or from the act of acquiring the Resource, without out-of-band knowledge specific to the Declaring Party. A test would be whether the Agent can find the Expression, when given only the Resource's identifier and a general method.  A robots.txt file passes this test (fixed path, fetched first).  A terms page linked only from a human-readable footer fails.  The "without out-of-band knowledge" clause is what excludes something like "email us for our API terms".  Discoverability is a property of deployment rather than of the artefact: the same file is discoverable at a well-known location and undiscoverable behind an unadvertised path, without a byte of it changing.

### Actionable {#actionable}

An Expression is actionable, for a given Agent and a given pending action, if the interpreted Expression determines a definite outcome for that action (like "permitted", "forbidden", or something like "conditional-based-on-a-checkable-condition") without recourse to a human.  A test for this could be whether the Agent can map the interpreted Expression onto its specific pending action and get an answer.

Unlike the core criteria, actionability is not intrinsic to the Expression: it is a relation between the Expression and a consumer's circumstances, and the same Expression may be actionable for one Agent and not for another.  "Training forbidden" is actionable against the action "train" and silent against the action "index".  The scope of application is part of the same relation: an Expression may determine an outcome for a Resource as a whole while determining none for an excerpt or a derivative of it.  An Expression that says "use must be fair" is interpretable but actionable for no Agent, because 'fair' is not a checkable condition.

"Copyright in the US has run out" is an example of something that is interpretable but not necessarily actionable, depending on what you are trying to do.  This ties to verifiability, since one would need to verify such a statement in order to take any action at all.

### Verifiable {#verifiable}

An Expression is verifiable if an Agent can establish that it genuinely originates from a party authorised to make assertions about the Resource, and that it has not been altered.  A test for this could be whether there is a mechanism binding the Expression to an authorised Declaring Party and detecting tampering.  Verifiability is independent of the rest: an Expression may be signed and bound to its Declaring Party while remaining prose no machine can act on, and a perfectly interpretable Expression may carry no evidence of origin at all.

There is a weak, implicit form of provenance short of this.  A Mechanism served from a well-known location, like robots.txt or TDMRep's `/.well-known/tdmrep.json`, carries some evidence of origin in the act of publication itself, since placing a file at that path is something only a party controlling the origin can do.  This is an assurance about who is speaking, to the extent that control of a domain identifies a party; it says nothing about the clarity or validity of what is said.  It also attaches to the act of serving rather than to the Expression, and so it neither survives redistribution of the Resource nor detects alteration.

## No Conformance Threshold {#no-threshold}

This framework defines no conformance threshold, and it must not be read as one.  There is no number of satisfied properties at which an Expression becomes "machine readable", and none whose absence makes it not so.  The properties describe what an Expression provides; what an Agent requires depends on the decision before it.

The threshold reading can be abused from both sides.  A Declaring Party may claim machine readability on the strength of the cheapest property, typically parseability, while failing the ones that matter for the consumer's decision; the survey in {{existing}} and the discussion in {{requirements-for-legal-terms}} are largely about that direction.  In the other direction, an Agent may disregard an Expression on the ground that some property is unsatisfied even though the properties its decision actually depends upon are met.  An unverifiable reservation that is discoverable, parseable, interpretable, and actionable for the pending action still communicates the Declaring Party's position; the absence of verifiability changes what the Agent can prove about the Expression, not what was asserted.  {{security-considerations}} discusses both hazards.

Whether an Expression satisfies a legal requirement of machine readability, such as the reservation "in an appropriate manner, such as machine-readable means" contemplated by Article 4(3) of the EU's Digital Single Market copyright directive {{DSM}}, is a question of law on which this document takes no position.

# Independence in Practice {#independence}

Real Expressions satisfy the properties of {{criteria}} in patches, and the patchwork is the point of assessing them separately.

The Robots Exclusion Protocol {{RFC9309}} is both highly discoverable and parseable.  It is fetched from a fixed location before any other resource, and its grammar is defined in ABNF.  It is deliberately not interpretable in the rich sense used here, because its vocabulary is confined to access control (allow and disallow against paths) and carries no shared semantics for what an Agent may do with content once fetched.  It provides no verifiability whatsoever and trust derives entirely from the authority of the server.

Conversely, a cryptographic provenance Expression may be verifiable and parseable while saying nothing about usage permissions at all, and so contribute nothing towards actionability for a usage decision.

The value of the framework is diagnostic in that it lets one state precisely which properties a given mechanism's Expressions provide and, more importantly, which they do not, rather than asserting that a mechanism is or is not "machine readable" as an undifferentiated whole.

# Requirements for Legal Terms {#requirements-for-legal-terms}

The properties of {{criteria}} are general, but legal terms of service are where the distance between the core criteria and an actionable outcome is widest.  Terms are easy to publish in a parseable form, and may even reach {{interpretable}}, but in most cases they are actionable for no Agent: they cannot be acted upon without a human to interpret them.  A document may be structurally sound, valid against a schema, and served from a location an Agent can discover, and still offer that Agent no machine-determinable answer to the one question it actually has, which is whether the action it is about to take is permitted.

At crawl scale this ceases to be a nuisance and becomes a barrier.  An Agent operating across the web encounters resources in numbers that make per-resource interpretation of natural-language terms infeasible as part of fetching them.  No stage of a crawl pipeline can read a terms-of-service document, written for human readers and varying from one site to the next, quickly enough, reliably enough, and cheaply enough to decide the fetch.  A mechanism that demands this does not describe a workable system.  It describes a human reading legalese, repeated some billions of times.  What works at that scale is low-context communication: an Expression that carries everything the Agent needs, fixed against shared definitions, rather than one whose meaning must be recovered from surrounding context at the moment of consumption.

It is sometimes proposed that a large language model (LLM) could close the gap, reading the terms at scale and reporting what they permit.  Such an approach must not be relied upon for usage decisions.  A language model does not determine what a document permits.  It produces text resembling that determination, and it does so with a well-documented tendency to hallucinate or fabricate.  A crawler that fetches or declines a Resource on the strength of a model's reading of prose is making an access decision from output that may be confidently wrong, and cannot be checked against the source without the very human reading it was meant to replace.  Wrapping the prose in a structured form changes none of this.  The wrapper is parseable, but it does not render the terms inside it actionable.

# Relationship to Existing Work {#existing}

The mechanisms surveyed below each address some part of the problem this document frames, and none addresses all of it.  They are described here in terms of what they do and which of the properties of {{criteria}} their Expressions provide, not to rank them, but to show that the framework describes existing work rather than displacing it.

## Robots Exclusion Protocol

The Robots Exclusion Protocol {{RFC9309}}, standardised in 2022 from the convention Martijn Koster introduced in 1994, lets a service state which URI paths a named crawler may fetch.  Its grammar is defined in ABNF and a crawler retrieves it from a fixed location before fetching anything else, so it is highly discoverable and parseable.  It is, by its own terms, access control and not authorisation, and it says nothing about what may be done with content once fetched.  It carries no provenance or integrity mechanism, and a crawler's trust in it rests entirely on the server's authority over the domain.  It is the clearest example in this document of a mechanism whose Expressions provide discoverability and parseability completely, and the other properties not at all, by design.

## AIPREF

The IETF's AI Preferences working group (AIPREF) is developing a means for content owners to express how their material may be used by automated systems, in particular for AI training.  Its work is split between a vocabulary ({{I-D.ietf-aipref-vocab}}), which defines preference categories such as train-ai and search and fixes their meaning against a specification, and an attachment mechanism ({{I-D.ietf-aipref-attach}}), which conveys those preferences through an HTTP header and through an extension to robots.txt.  The vocabulary is what carries AIPREF past parseability to interpretability, where most usage signals stop short: a crawler encountering `train-ai=n` recovers not merely a string but a term whose meaning is pinned, as discussed at {{interpretable}}.  AIPREF is explicit that preferences are not a security mechanism, and it provides no verifiability.  It establishes neither who set a preference nor that it is unaltered.  It is (at the time of writing) a work in progress.

## ODRL

The Open Digital Rights Language (ODRL) {{ODRL}}, a W3C Recommendation since 2018, is a formal language for expressing policies over digital assets.  Permissions, prohibitions, and duties are attached to actions drawn from an identified vocabulary and narrowed by constraints such as time, place, or purpose.  Of the mechanisms surveyed here its Expressions go furthest on interpretability, and furthest towards actionable outcomes, because its terms are pinned to a published vocabulary and its rules are structured precisely enough that an evaluator can decide whether a given action is permitted.  That expressive power is also its difficulty.  ODRL says nothing in itself about where a policy is to be found or whether it is genuine.  Discovery is left to other mechanisms that link an asset to its policy, and integrity is left to metadata borrowed from outside vocabularies, since ODRL 2.2 carries no native means to establish that a policy is authentic or unaltered.  It is a rich answer to interpretability and actionability that leaves discoverability and verifiability to others.

## DPV

The Data Privacy Vocabulary (DPV) {{DPV}}, a W3C Community Group specification rather than a Recommendation, is a large taxonomy of terms for describing how personal data is processed: purposes, legal bases, processing operations, technical measures, and, through its extensions, concepts drawn from regulations such as the GDPR and the EU AI Act.  It is included here not because a crawler would discover and act on a DPV document in the way it discovers robots.txt, but because of the role it plays in interpretability.  DPV is designed to be used inside other mechanisms.  Its terms can supply the meaning of a condition in an ODRL policy, so that a constraint refers to a defined concept of purpose or jurisdiction rather than to a bare string.  In the vocabulary of {{criteria}} it is an interpretability resource, a source of identified, shared meaning that other Expressions can draw on, rather than a discoverable, self-standing signal in its own right.  Like the others surveyed here it provides provenance metadata but no cryptographic integrity, and it establishes nothing about who asserted a given statement.

## CC REL

Creative Commons Rights Expression Language (CC REL) {{CCREL}}, published as a W3C Member Submission in 2008, is the method Creative Commons recommends for attaching licence information to a work in machine-readable form.  It is RDF-based and designed to be embedded where the work lives: as RDFa attributes in HTML, or as XMP metadata inside a media file so that the licence travels with the file when it is copied.  Its terms are interpretable in the sense {{interpretable}} requires, since a CC licence is identified by a URI that resolves to a defined set of permissions, requirements, and prohibitions rather than to free text, and it is the most widely deployed of the mechanisms surveyed here by virtue of the licences themselves.  Its profile against the other properties is mixed: discovery depends on the work carrying the embedded markup, which an Agent can read where it is present but cannot rely on in general; and while CC REL records attribution and source, giving a measure of provenance, the specification itself provides no cryptographic means to confirm that a licence statement is authentic or unaltered.  It is also worth noting that a Member Submission is not a W3C standard; W3C received the work but did not place it on the standards track.

## TDMRep

The TDM Reservation Protocol (TDMRep) {{TDMREP}}, a W3C Community Group Final Report, is a deliberately simple mechanism by which a rights holder can reserve text-and-data-mining rights over web content or other kinds of assets, developed as a technical response to the reservation contemplated by Article 4 of the EU's Digital Single Market copyright directive {{DSM}}.  Its core is a single property, `tdm-reservation`, set to 1 or 0, optionally accompanied by a URL pointing to a fuller policy.  A crawler finds it in a file at a well-known location that the specification directs an Agent to fetch before "mining" anything on the server, with HTTP headers and in-document metadata as alternatives.  Of everything surveyed here, TDMRep answers the crawler's question most cleanly: its Expressions are discoverable by fixed convention, parseable, interpretable because `tdm-reservation` is a defined term rather than a guessable field, and actionable for the Agent's purpose because the value yields a definite outcome against the act of "mining".  Where it stops, like the others, is verifiability.  The specification provides no cryptographic means to establish who set a reservation or that it is unaltered, and the schemes that add this by binding declarations to content fingerprints actually sit outside TDMRep.  Its narrowness is the point of its success.  By asking only whether mining is reserved, it essentially stays answerable.

## Schema.org

Schema.org {{SCHEMA-ORG}} is a vocabulary of structured-data terms embedded in web pages, maintained by a community backed by the major search engines rather than published as a standard.  Among its terms are several concerned with rights: `license`, `usageInfo`, `acquireLicensePage`, and others, attached to a described work.  These are the closest thing to a usage signal that a great many sites already publish, which is what makes schema.org the sharpest illustration of the distinction that this document draws.  The license property is defined as "a license document that applies to this content, typically indicated by URL", and that phrase is the whole difficulty in miniature.  A schema.org licence annotation is reliably discoverable and parseable, and where the URL it carries is a recognised licence with fixed meaning, for instance a specific Creative Commons licence, it is interpretable too.  But where the URL points to a publisher's own licence page written in prose, the structured part of the chain ends at that link, and what waits on the other side is exactly the human-readable document that {{requirements-for-legal-terms}} describes.  It is parseable to reach, not actionable once reached.  Schema.org is structured data that can carry an actionable signal or a mere pointer to prose with equal ease, and nothing in the markup itself tells an Agent which it has been handed.  It provides, like the others, no means to verify who published the annotation.  It is the case that most tempts the conflation mentioned above in {{introduction}}, where being structured is mistaken for being machine-readable in the sense that actually matters.

## Dublin Core

The Dublin Core metadata terms {{DCTERMS}}, maintained by the Dublin Core Metadata Initiative and standardised as ISO 15836, are a general-purpose vocabulary for describing resources of any kind.  Among the terms are two concerned with rights: `dcterms:rights`, defined as information about rights held in and over the resource, and `dcterms:license`, a sub-property defined as a legal document giving official permission to do something with the resource.  Like schema.org and CC REL, Dublin Core is a way of attaching a rights value to a described work rather than a policy language in its own right, and its profile is much the same.  Where the value of `dcterms:license` is a URI resolving to a licence of fixed meaning, the annotation is interpretable; where it is a free-text statement of rights, as `dcterms:rights` commonly is, it is parseable and nothing more, and a consuming Agent is left stuck with a string it can't resolve.  Dublin Core's own guidance is very open about this, noting that a bare URI recorded as a value string gives a consuming application no reliable way to tell it apart from any other string and so to treat it as an identifier.  It provides, like the other embedded-metadata schemes, provenance (in the weak sense of recorded attribution) but no means to verify that a rights statement is authentic or unaltered.

## C2PA

The Coalition for Content Provenance and Authenticity (C2PA) {{C2PA}} is unlike everything else surveyed here, because it is not a usage-preference or rights mechanism at all but a provenance one, and it is the only mechanism in the survey that answers the verifiable dimension.  C2PA binds a cryptographically signed manifest to a digital asset, recording assertions about the asset's origin and editing history; the signature and a hash binding make the manifest tamper-evident, so that any later alteration of the content or the manifest can be detected.  This is genuine verifiability of a kind none of the others provide.  Nor is a manifest confined to provenance history: the Creator Assertions Working Group defines a Training and Data Mining assertion {{CAWG-TDM}} through which a manifest can carry usage preferences, stating whether an asset may be used for data mining or for AI training, including generative training, inside the signed envelope, and this brings C2PA's verifiability into direct contact with the kind of usage signal the rest of this survey describes.  It is worth being precise, though, about which part of verifiability it delivers.  Verifiability ({{verifiable}}) as defined here requires that an Expression has not been altered, and that it genuinely originates from a party authorised to make the assertion.  C2PA answers the first part cleanly.  The second is only answered halfway, in that it establishes that a particular signer made the assertion and that the assertion is unchanged since, but not that the assertion is true, nor that the signer had any standing to make it.  As C2PA's own materials put it, it proves who signed a claim, not whether the claim is true.  The integrity limb is solved; the authority limb, who is entitled to speak for the Resource, is left where the other mechanisms leave it.

## The FAIR Principles

The FAIR principles {{FAIR}}, under which research data should be findable, accessible, interoperable, and reusable, are sometimes offered as a neighbouring frame for this document, and the correspondence of names (findable and discoverable, interoperable and interpretable) invites the comparison.  The two are not functionally equivalent.  FAIR states stewardship goals for data and for the infrastructure that holds it, directed at making datasets reusable over time; its principles are aspirations for an ecosystem rather than tests of an artefact.  The framework here asks a narrower and more mechanical question, whether a specific Expression supports a specific automated decision, and it can be applied to a single artefact in isolation.  A repository can be exemplary against FAIR while every rights statement in it stops at parseable, and a single TDMRep file can satisfy both core criteria and be discoverable and actionable while participating in no FAIR ecosystem at all.

# Security Considerations {#security-considerations}

This document defines terminology and a diagnostic framework.  It specifies no protocol, syntax, or wire format, and so introduces no new protocol elements to secure.  The security considerations that arise concern how the properties it defines are relied upon by Agents making usage decisions.

An Expression that is not verifiable ({{verifiable}}) can be forged, altered in transit or at rest, or published by a party with no authority over the Resource.  An Agent that acts on unverifiable Expressions is trusting, at most, the channel over which it obtained them: for a robots.txt file, the server's authority over its own domain; for embedded metadata, whoever last wrote the file.  Every mechanism surveyed in {{existing}} except C2PA has this property, and Agents should treat the assertions such Expressions carry accordingly.

The absence of integrity protection also permits downgrade by removal.  Where an Expression is conveyed separately from the Resource it governs, an intermediary that strips or weakens the Expression leaves the Agent with no indication that a preference was ever asserted.  An Agent cannot tell the difference between "no Expression published" and "Expression removed in transit" unless the mechanism binds the Expression to the Resource in some tamper-evident way.

Conflation of the properties with one another is itself a hazard.  An Agent that treats a parseable Expression as actionable is acting on content it has not, in the sense of {{criteria}}, actually understood, and a Declaring Party or third party can exploit that gap by presenting structurally valid Expressions whose free-text content does not mean what the Agent's heuristics assume it means.

The converse abuse is dismissal by threshold.  Because the framework defines no conformance level ({{no-threshold}}), an assessment that an Expression lacks some property is not a finding that the Expression may be ignored.  A party wishing to disregard preferences could otherwise hold that nothing is machine readable unless it provides all five properties, a bar that no deployed mechanism meets.  An Agent that declines to honour any Expression short of, say, verifiability is not applying this framework but exploiting it.

Finally, the use of language models to interpret natural-language terms, discussed in {{requirements-for-legal-terms}}, adds an attack surface beyond the reliability problem described there.  Text under the control of a Declaring Party or of a third party is processed by a model whose output steers the Agent's behaviour; adversarially crafted terms may induce a permissive reading that the document does not support, or otherwise manipulate the consuming system.  This is a further reason such output must not be the basis of usage decisions.

# IANA Considerations

This document has no IANA actions.

# Acknowledgments
{:numbered="false"}

The author thanks Pedro Ortiz Suarez, Erin Simon, Christopher Flammang, Alissa Cooper, Henk Birkholz, and Carsten Bormann for their reviews of this document and their suggested additions, and the participants in the discussion of this document at IETF 126, whose observations shaped the reframing in this version.

--- back

# Change Log
{:numbered="false" removeinrfc="true"}

## Since draft-vaughan-machine-readability-00
{:numbered="false"}

- Replaced the ladder framing.  Parseability and interpretability are now defined as the two core, intrinsic criteria ({{core-criteria}}), and discoverability, actionability, and verifiability as dimensions orthogonal to them and to one another ({{dimensions}}).  The former Separability section is now {{independence}}.  (IETF 126 discussion.)
- Added {{no-threshold}}: the framework defines no conformance threshold, Agents should not dismiss Expressions whose relevant properties are met, and legal machine-readability requirements such as DSM Article 4(3) are out of scope.  Added the corresponding dismissal-by-threshold hazard to {{security-considerations}}.  (Issue #3.)
- Made actionability explicitly relative to a particular Agent, action, and context, including the scope of application.  (IETF 126 discussion.)
- Generalised the definition of Expression to structured information in general, keeping usage preferences and legal terms as this document's applied focus, and noted that an Expression may itself be the Resource.  (Issue #2.)
- Added Mechanism to the terminology: the framework assesses Expressions, and mechanisms are assessed through the Expressions they yield.  (IETF 126 discussion.)
- Added {{FAIR}} to the survey, distinguishing this framework from the FAIR principles.  (IETF 126 discussion.)
- Corrected the TDMRep description to cover assets other than web content, and added a reference to the DSM directive.  (Issue #1.)
- Noted that crawl-scale consumption requires low-context communication.  (IETF 126 discussion.)
- Noted that the core criteria correspond to the structural/semantic interoperability distinction and to the data-model/information-model distinction of {{RFC3444}}.
- Acknowledged the weak provenance implicit in publication at a well-known location ({{verifiable}}), and tied the Robots Exclusion Protocol's trust model to it.  (Issue #5.)
- Added the CAWG Training and Data Mining assertion to the C2PA section.  (Issue #7.)
