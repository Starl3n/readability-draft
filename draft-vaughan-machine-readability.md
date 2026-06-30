---
title: "Defining Machine Readability for Usage Preferences and Policy Expression"
abbrev: "Machine Readability"
category: info

docname: draft-vaughan-machine-readability-latest
submissiontype: independent
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

normative:
  RFC9309:

informative:
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

  C2PA:
    target: https://c2pa.org/specifications/
    title: "Coalition for Content Provenance and Authenticity (C2PA) Technical Specification"
    author:
      - org: Coalition for Content Provenance and Authenticity

--- abstract

The term "machine readable" is widely invoked when content usage
preferences, rights, and legal terms are expressed for automated
consumption, but it is rarely defined with enough precision to be
actionable.  This document distinguishes a series of properties
(discoverability, parseability, interpretability, actionability, and
verifiability) that together determine whether an expression of
preferences or policy can be reliably acted upon by a non-human agent
without human intervention.  It applies these properties to the case of
usage preferences and legal terms of service.


--- middle

# Introduction {#introduction}

Expressions of how content may be used by automated systems are increasingly published in forms described as "machine readable".  Operators of crawlers and other automated Agents are expected to discover these Expressions, determine what they permit or forbid, and act accordingly.  The term "machine readable" is invoked frequently in this context, in standards work, in policy, and in legislation, but it is rarely defined with enough precision to tell an implementer whether a given Expression actually supports automated action.
The difficulty is that "machine readable" names several distinct properties that are commonly conflated. An Expression may be serialised in a structured syntax, and so be straightforward for a program to parse, while still conveying nothing a program can act upon.  A terms-of-service document placed verbatim in a JSON string is structurally parseable but no more actionable than the same text in a web page.  Conversely, an Expression may be richly actionable yet undiscoverable, or trivially discoverable yet unverifiable.  Treating "machine readable" as a single binary property obscures these differences and permits a Declaring Party to claim machine readability on the strength of the least demanding property while failing the ones that matter for automated action.
This document separates "machine readable" into five properties: an Expression may be discoverable, parseable, interpretable, actionable, and verifiable.  These are defined in {{terminology}} and {{ladder}}.  They are presented as a ladder, from least to most demanding, but they do not strictly entail one another, and {{separability}} sets out how they come apart in practice.  The purpose of the framework is diagnostic: it allows a given mechanism to be described in terms of precisely which properties it provides, rather than asserted to be machine readable as an undifferentiated whole.
The framework is general, but this document applies it in particular to two cases: the expression of usage preferences for automated processing, and the expression of legal terms of service.  The latter is treated at length in {{requirements-for-legal-terms}}, because legal text exposes the gap between the lower and higher rungs most sharply.  Terms that are easy to publish in a structured form are frequently impossible to act upon without human interpretation.
This document does not define a vocabulary, a syntax, or a protocol, and it is not a product of any IETF working group.  It does not propose that any existing mechanism be changed.  Its contribution is a set of definitions against which existing and future mechanisms can be assessed.

## Requirements Language

{::boilerplate bcp14-tagged}

# Terminology

Agent:
: A non-human process that acquires, processes, or acts upon content or its associated expressions without real-time human direction.  A web crawler, for instance.

Expression:
: A concrete artefact (for instance: a file, header, embedded block, or metadata record) that conveys preferences, rights, terms, or other policies about a resource.

Resource:
: The content or asset to which an Expression pertains.

Declaring Party:
: The entity asserting an Expression.

# A Ladder of Machine Readability {#ladder}

The properties below are presented as a ladder, from least to most demanding, because each higher rung is most useful when the rungs beneath it also hold.  An Expression whose meaning is fixed (interpretable) is of little utility if an Agent cannot find it (discoverable).  For expository purposes this document treats the rungs as cumulative.  They are not, however, strictly dependent, and the {{separability}} section describes the cases in which they come apart.

An Expression should have the following properties:

## Discoverable

An Expression is discoverable if an Agent can locate it from the Resource, or from the act of acquiring the Resource, without out-of-band knowledge specific to the Declaring Party. A test would be whether the Agent can find the Expression, when given only the Resource's identifier and a general method.  A robots.txt file passes this test (fixed path, fetched first).  A terms page linked only from a human-readable footer fails.  The "without out-of-band knowledge" clause is what excludes something like "email us for our API terms".

## Parseable

An Expression is parseable if its syntax is defined such that a conforming Agent recovers the same structured representation from it that any other conforming Agent would.  A test for this would be whether there is a grammar or schema against which the Expression is valid or invalid, deterministically.  ToS-in-JSON passes this test but, crucially, usually no higher one.  This characteristic is the one most often mistaken for machine readability as a whole.

## Interpretable

An Expression is interpretable if the meaning of its parsed elements is fixed against a shared, identified vocabulary, such that two conforming Agents assign the same meaning to the same element.  A test for this is whether each term is resolvable to a definition that is itself machine-identified, (like a URI or a registry entry) rather than relying on the Agent's own natural-language understanding.  This characteristic separates something like a JSON field called "may_train" (whose meaning is whatever a reader guesses) from AIPREF's train-ai, whose meaning is pinned to a specification.

## Actionable

An Expression is actionable if an interpretable Expression additionally determines, for the Agent's purposes, a definite outcome (like "permitted", "forbidden", or something like "conditional-based-on-a-checkable-condition") without recourse to a human.  A test for this could be whether the Agent can map the interpreted Expression onto its specific pending action and get an answer.  An Expression that says "use must be fair" is interpretable but admits no definite outcome, because 'fair' is not a checkable condition.  One that says "training forbidden" is actionable against the action "train".

## Verifiable {#verifiable}

An Expression is verifiable if an Agent can establish that it genuinely originates from a party authorised to make assertions about the Resource, and that it has not been altered.  A test for this could be whether there is a mechanism binding the Expression to an authorised Declaring Party and detecting tampering.

# Separability

The ladder metaphor is an expository convenience, and not a claim that each characteristic actually strictly entails the ones below it.  Real Expressions satisfy these properties in patches.
The Robots Exclusion Protocol {{RFC9309}} is both highly discoverable and parseable.  It is fetched from a fixed location before any other resource, and its grammar is defined in ABNF.  It is deliberately not interpretable in the rich sense used here, because its vocabulary is confined to access control (allow and disallow against paths) and carries no shared semantics for what an Agent may do with content once fetched.  It provides no verifiability whatsoever and trust derives entirely from the authority of the server.
Conversely, a cryptographic provenance Expression may be verifiable and parseable while saying nothing about usage permissions at all, and so contribute nothing on the actionable rung for a usage decision.
An Expression may, therefore, occupy a high rung while failing a lower one, or satisfy a lower rung richly while being absent higher up.  The value of the ladder is diagnostic in that it lets one state precisely which property a given mechanism provides and, more importantly, which it does not, rather than asserting that a mechanism is or is not "machine readable" as an undifferentiated whole.

# Requirements for Legal Terms

The properties of {{ladder}} are general, but legal terms of service are where the gap between the lower and higher rungs is widest.  Terms are easy to publish in a parseable form, but in most cases they cannot be acted upon without a human to interpret them.  A document may be structurally sound, valid against a schema, and served from a location an Agent can discover, and still offer that Agent no machine-determinable answer to the one question it actually has, which is whether the action it is about to take is permitted.
At crawl scale this ceases to be a nuisance and becomes a barrier.  An Agent operating across the web encounters resources in numbers that make per-resource interpretation of natural-language terms infeasible as part of fetching them.  No stage of a crawl pipeline can read a terms-of-service document, written for human readers and varying from one site to the next, quickly enough, reliably enough, and cheaply enough to decide the fetch.  A mechanism that demands this does not describe a workable system.  It describes a human reading legalese, repeated some billions of times.
It is sometimes proposed that a large language model (LLM) could close the gap, reading the terms at scale and reporting what they permit.  Such an approach must not be relied upon for usage decisions.  A language model does not determine what a document permits.  It produces text resembling that determination, and it does so with a well-documented tendency to hallucinate or fabricate.  A crawler that fetches or declines a Resource on the strength of a model's reading of prose is making an access decision from output that may be confidently wrong, and cannot be checked against the source without the very human reading it was meant to replace.  Wrapping the prose in a structured form changes none of this.  The wrapper is parseable, but it does not render the terms inside it actionable.

# Relationship to Existing Work

The mechanisms surveyed below each address some part of the problem this document frames, and none addresses all of it.  They are described here in terms of what they do and where they sit against the properties of {{ladder}}, not to rank them, but to show that the framework describes existing work rather than displacing it.

## Robots Exclusion Protocol

The Robots Exclusion Protocol {{RFC9309}}, standardised in 2022 from the convention Martijn Koster introduced in 1994, lets a service state which URI paths a named crawler may fetch.  Its grammar is defined in ABNF and a crawler retrieves it from a fixed location before fetching anything else, so it is highly discoverable and parseable.  It is, by its own terms, access control and not authorisation, and it says nothing about what may be done with content once fetched.  It carries no provenance or integrity mechanism, and a crawler's trust in it rests entirely on the server's authority over the domain.  It is the clearest example in this document of a mechanism that sits firmly on the lower rungs and makes no claim to the higher ones.

## AIPREF

The IETF's AI Preferences working group (AIPREF) is developing a means for content owners to express how their material may be used by automated systems, in particular for AI training.  Its work is split between a vocabulary ({{I-D.ietf-aipref-vocab}}), which defines preference categories such as train-ai and search and fixes their meaning against a specification, and an attachment mechanism ({{I-D.ietf-aipref-attach}}), which conveys those preferences through an HTTP header and through an extension to robots.txt.  The vocabulary is what lets AIPREF reach the interpretable rung where most usage signals stop short: a crawler encountering `train-ai=n` recovers not merely a string but a term whose meaning is pinned, as discussed at {{ladder}}.  AIPREF is explicit that preferences are not a security mechanism, and it provides no verifiability.  It establishes neither who set a preference nor that it is unaltered.  It is (at the time of writing) a work in progress.

## ODRL

The Open Digital Rights Language (ODRL) {{ODRL}}, a W3C Recommendation since 2018, is a formal language for expressing policies over digital assets.  Permissions, prohibitions, and duties are attached to actions drawn from an identified vocabulary and narrowed by constraints such as time, place, or purpose.  Of the mechanisms surveyed here it reaches highest on the interpretable and actionable rungs, because its terms are pinned to a published vocabulary and its rules are structured precisely enough that an evaluator can decide whether a given action is permitted.  That expressive power is also its difficulty.  ODRL says nothing in itself about where a policy is to be found or whether it is genuine.  Discovery is left to other mechanisms that link an asset to its policy, and integrity is left to metadata borrowed from outside vocabularies, since ODRL 2.2 carries no native means to establish that a policy is authentic or unaltered.  It is a rich answer to interpretability and actionability that leaves discoverability and verifiability to others.

## DPV

The Data Privacy Vocabulary (DPV) {{DPV}}, a W3C Community Group specification rather than a Recommendation, is a large taxonomy of terms for describing how personal data is processed: purposes, legal bases, processing operations, technical measures, and, through its extensions, concepts drawn from regulations such as the GDPR and the EU AI Act.  It is included here not because a crawler would discover and act on a DPV document in the way it discovers robots.txt, but because of the role it plays on the interpretable rung.  DPV is designed to be used inside other mechanisms.  Its terms can supply the meaning of a condition in an ODRL policy, so that a constraint refers to a defined concept of purpose or jurisdiction rather than to a bare string.  In the vocabulary of {{ladder}} it is an interpretability resource, a source of identified, shared meaning that other Expressions can draw on, rather than a discoverable, self-standing signal in its own right.  Like the others surveyed here it provides provenance metadata but no cryptographic integrity, and it establishes nothing about who asserted a given statement.

## CC REL

Creative Commons Rights Expression Language (CC REL) {{CCREL}}, published as a W3C Member Submission in 2008, is the method Creative Commons recommends for attaching licence information to a work in machine-readable form.  It is RDF-based and designed to be embedded where the work lives: as RDFa attributes in HTML, or as XMP metadata inside a media file so that the licence travels with the file when it is copied.  Its terms are interpretable in the sense {{ladder}} requires, since a CC licence is identified by a URI that resolves to a defined set of permissions, requirements, and prohibitions rather than to free text, and it is the most widely deployed of the mechanisms surveyed here by virtue of the licences themselves.  Its placement on the ladder is otherwise mixed: discovery depends on the work carrying the embedded markup, which an Agent can read where it is present but cannot rely on in general; and while CC REL records attribution and source, giving a measure of provenance, the specification itself provides no cryptographic means to confirm that a licence statement is authentic or unaltered.  It is also worth noting that a Member Submission is not a W3C standard; W3C received the work but did not place it on the standards track.

## TDMRep

The TDM Reservation Protocol (TDMRep) {{TDMREP}}, a W3C Community Group Final Report, is a deliberately simple mechanism by which a rights holder can reserve text-and-data-mining rights over web content, developed as a technical response to the reservation contemplated by Article 4 of the EU's Digital Single Market copyright directive.  Its core is a single property, `tdm-reservation`, set to 1 or 0, optionally accompanied by a URL pointing to a fuller policy.  A crawler finds it in a file at a well-known location that the specification directs an Agent to fetch before "mining" anything on the server, with HTTP headers and in-document metadata as alternatives.  Of everything surveyed here, TDMRep climbs the crawler-relevant rungs most cleanly for its narrow question: it is discoverable by fixed convention, parseable, interpretable because `tdm-reservation` is a defined term rather than a guessable field, and actionable because its value yields a definite outcome against the act of "mining".  Where it stops, like the others, is verifiability.  The specification provides no cryptographic means to establish who set a reservation or that it is unaltered, and the schemes that add this by binding declarations to content fingerprints actually sit outside TDMRep.  Its narrowness is the point of its success.  By asking only whether mining is reserved, it essentially stays answerable.

## Schema.org

Schema.org {{SCHEMA-ORG}} is a vocabulary of structured-data terms embedded in web pages, maintained by a community backed by the major search engines rather than published as a standard.  Among its terms are several concerned with rights: `license`, `usageInfo`, `acquireLicensePage`, and others, attached to a described work.  These are the closest thing to a usage signal that a great many sites already publish, which is what makes schema.org the sharpest illustration of the distinction that this document draws.  The license property is defined as "a license document that applies to this content, typically indicated by URL", and that phrase is the whole difficulty in miniature.  A schema.org licence annotation is reliably discoverable and parseable, and where the URL it carries is a recognised licence with fixed meaning, for instance a specific Creative Commons licence, it is interpretable too.  But where the URL points to a publisher's own licence page written in prose, the structured part of the chain ends at that link, and what waits on the other side is exactly the human-readable document that {{requirements-for-legal-terms}} describes.  It is parseable to reach, not actionable once reached.  Schema.org is structured data that can carry an actionable signal or a mere pointer to prose with equal ease, and nothing in the markup itself tells an Agent which it has been handed.  It provides, like the others, no means to verify who published the annotation.  It is the case that most tempts the conflation mentioned above in {{introduction}}, where being structured is mistaken for being machine-readable in the sense that actually matters.

## C2PA

The Coalition for Content Provenance and Authenticity (C2PA) {{C2PA}} is unlike everything else surveyed here, because it is not a usage-preference or rights mechanism at all but a provenance one, and it is the only mechanism in the survey that answers the verifiability rung.  C2PA binds a cryptographically signed manifest to a digital asset, recording assertions about the asset's origin and editing history; the signature and a hash binding make the manifest tamper-evident, so that any later alteration of the content or the manifest can be detected.  This is genuine verifiability of a kind none of the others provide.  It is worth being precise, though, about which part of verifiability it delivers.  The {{verifiable}} rung as defined here asserts that an Expression has not been altered, and that it genuinely originates from a party authorised to make the assertion.  C2PA answers the first part cleanly.  The second is only answered halfway, in that it establishes that a particular signer made the assertion and that the assertion is unchanged since, but not that the assertion is true, nor that the signer had any standing to make it.  As C2PA's own materials put it, it proves who signed a claim, not whether the claim is true.  The integrity limb is solved; the authority limb, who is entitled to speak for the Resource, is left where the other mechanisms leave it.

# Security Considerations

TODO Spoofing, provenance, downgrade, etc.  BCP47 stuff maybe.

# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
