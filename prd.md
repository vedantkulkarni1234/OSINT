# Product Requirements Document — Aegis OSINT & Digital Forensics Platform

**Document status:** Product baseline for design and implementation  
**Version:** 1.0  
**Date:** 25 October 2026  
**Audience:** Product, engineering, security, digital-forensics, legal, procurement, and law-enforcement stakeholders  
**Working product name:** Aegis (name is provisional; this is not a branding decision)

> **Product integrity statement:** This system supports authorized investigations; it does not establish guilt, guarantee identification, or replace investigator judgment, forensic examination, legal process, or judicial findings. “100× more powerful” is treated as an aspiration to make authorized investigative work substantially more thorough, timely, reproducible, and well-supported—not as a factual performance claim or permission for unbounded surveillance. The platform must not be designed to make evasion “impossible.” Attribution is probabilistic, incomplete, and subject to human verification.

---

## 1. Executive Summary

Aegis is a secure, investigator-centered OSINT and digital-evidence analysis platform for cybercrime and fraud cases. It helps cybercrime officers and digital-forensics teams turn a victim’s plain-language account and a collection of supplied digital artifacts into a structured, auditable investigation. A case can combine public or properly licensed information, forensic examination of authorized evidence, deterministic analysis utilities, and optional AI agents. The system identifies candidate entities, organizes evidence, proposes leads, records sources and uncertainty, and produces reviewable reports. The investigator remains accountable for investigative scope, approvals, interpretation, disclosure, and every consequential decision.

The product has a deliberately simple intake surface: **one large free-form narrative box and one evidence upload/folder-ingestion area**. The narrative may contain any useful details in the victim’s own words. The system extracts candidate facts into a review queue instead of requiring the victim to understand a complex technical form. The victim or officer can skip unknown details, submit partial information, and add material later. Evidence items are preserved separately from normalized derivatives, and the interface does not require a victim to sort their material before submission.

A **Master Agent** coordinates case work through an explicit, bounded investigation plan. It may propose or invoke vetted specialist agents—such as email-header analysis, URL/domain enrichment, payment-reference normalization, or chat-log review—only when their capabilities are approved for that case and the authorized scope. The system dynamically selects from a reviewed catalog rather than creating arbitrary, unconstrained agents. Agents produce structured observations with citations to exact evidence or source records; they do not make findings of guilt or take enforcement action. A case can also run without generative AI, using deterministic parsers and manual workflows.

**MCP (Model Context Protocol) is a first-class integration boundary.** Administrators can register, verify, configure, test, enable, suspend, and retire MCP servers through a governed management experience. MCP tools are discovered and indexed dynamically, but discovery does not imply trust or permission. Each server and tool is subject to identity, version, capability, data-classification, scope, and execution-policy controls. No server may receive case data or execute a tool merely because it is connected. A protocol adapter layer keeps MCP integrations modular and supports approved non-MCP connectors without coupling the core product to a single provider or server.

Aegis is designed around five non-negotiable principles:

1. **Lawful, scoped investigation:** use information only under a documented case purpose and applicable authority; access only public, licensed, or specifically authorized sources.
2. **Evidence before inference:** preserve originals, distinguish observations from interpretations, and link every material claim to provenance.
3. **Human control:** require investigator review for extracted identifiers, material correlations, report conclusions, and any high-impact action; agents cannot contact subjects, freeze funds, notify providers, or alter external systems.
4. **Privacy and minimization:** protect victim and third-party data, restrict access by role and case, minimize external disclosure, and support retention and deletion obligations.
5. **Verifiable, bounded automation:** every agent task, tool invocation, source access, transformation, and report version is logged; errors and uncertainty are visible rather than hidden.

The repository’s research papers inform product choices on OSINT tradecraft, evidence handling, multi-agent orchestration, MCP interoperability and security, tool-use reliability, adversarial inputs, and human oversight. The papers include peer-reviewed research, preprints, workshop materials, strategy documents, and an industry report; they are treated as design inputs, not as legal authority or proof that a proposed feature is effective in this product. References [R01]–[R36] identify the specific repository PDFs.

---

## 2. Product Context and Problem Statement

### 2.1 The problem

Victims commonly report a sequence of confusing events rather than a normalized incident record: a persuasive message, a call from a changing number, a payment request, a compromised account, a suspicious URL, and later contact from a supposed recovery service. Evidence is dispersed across phones, email inboxes, messaging applications, bank statements, screenshots, browser histories, and third-party services. It may be incomplete, duplicated, out of order, in different languages, or supplied with little technical context. Investigators must manually extract entities, preserve the originals, find lawful sources, compare timelines, track leads, and explain how conclusions were reached.

Common friction includes:

- **Fragmented collection:** different evidence types use different tools and formats, and the same identifier appears in several places with inconsistent formatting.
- **High-volume review:** chat, email, transaction, and media collections can exceed the practical capacity of manual triage. Research on digital forensics describes the need for scalable extraction while emphasizing data quality, integrity, interpretability, and privacy limits [R12, R15, R16].
- **Uncertain attribution:** a reused username, shared device, proxy, wallet cluster, phone number, or domain does not by itself identify a person. Public data can be stale, manipulated, misattributed, or collected under different terms. OSINT must be validated and evaluated in context [R07, R08, R09].
- **Operational silos:** open-source, forensic, financial, and provider data may require distinct expertise and access permissions. A single general-purpose assistant is not a reliable substitute for specialist methods or source-specific validation [R10, R11, R12].
- **Tool proliferation:** integrations have different APIs, schemas, terms, authentication models, availability, and security postures. MCP offers a standard interface but introduces additional tool, server, transport, and composition risks [R26–R30, R36].
- **Weak traceability:** conventional notes and ad hoc exports may not capture source versions, collection time, transformations, hash values, analytical lineage, or uncertainty in a form suitable for review.
- **Victim burden:** a long technical intake form may deter or distress victims, require information they do not have, or cause them to omit a useful narrative. High-stress non-expert interaction benefits from accessible, context-sensitive guidance [R35].
- **AI reliability and security:** language models may hallucinate, miss context, overstate confidence, inherit incorrect assumptions from earlier agents, or be influenced by malicious instructions embedded in evidence or tool output. Multi-agent decomposition can itself bypass safety controls or spread errors [R06, R27, R29, R34].

### 2.2 Why an integrated platform is needed

Aegis combines guided intake, controlled evidence ingestion, case management, specialist analysis, governed external integrations, source-aware correlation, and report generation in one system. The objective is not to automate legal judgments. It is to make authorized investigative work more consistent and efficient by reducing repetitive parsing and cross-referencing, exposing relationships for analyst review, and retaining a reproducible record of how an output was produced.

The product must support both conventional, deterministic forensic processing and AI-assisted interpretation. A model can help extract candidate entities from unstructured text, summarize a long conversation, or suggest a possible correlation. It cannot be the sole authority for a fact, source credibility, identity, legal sufficiency, or a case disposition. Where a source or tool is unavailable, the platform must report the gap and preserve the status as unknown rather than filling it with a plausible answer [R05, R10, R12, R35].

### 2.3 Product positioning and limits

Aegis is an **investigative decision-support and evidence-management platform**, not:

- a mass-surveillance, population-profiling, or general-purpose person-search product;
- a system for bypassing authentication, accessing private accounts, exploiting vulnerabilities, or scraping in violation of law, provider terms, or access controls;
- a substitute for a search warrant, subpoena, consent, authorization, or other required legal process;
- an automated suspect-identification, guilt-scoring, facial-recognition, or predictive-policing system;
- an automated enforcement or intervention system; or
- a guarantee that every scammer can be identified, located, or apprehended.

The phrase “100×” may be used internally as a direction to improve investigative leverage. Any external performance claim must be supported by a pre-registered, independently reviewed evaluation on representative cases and must state its baseline, scope, error rates, and limitations.

---

## 3. Users, Roles, and Stakeholders

### 3.1 Primary users

| User | Primary needs | Product permissions (default; agency policy may narrow) |
|---|---|---|
| **Cybercrime investigator / case officer** | Intake, scope definition, lead review, task approval, case status, collaboration, reporting | Create and manage assigned cases; review entities and findings; approve permitted investigation tasks; generate drafts; cannot administer global connectors unless separately authorized |
| **Digital-forensics examiner** | Forensic preservation, verified processing, artifact examination, chain-of-custody records, reproducible exports | Access assigned evidence; create examination records and derivative artifacts; attest to procedures; cannot alter original evidence or approve their own privileged access |
| **Supervising investigator / team lead** | Case oversight, workload, quality review, escalation, approvals | View team cases; approve elevated scope or sensitive access; review and sign reports according to policy |
| **MCP / integration administrator** | Register and maintain MCP servers, credentials, tool policy, health and audit | Manage integrations and tool allowlists; cannot view case contents by virtue of integration administration alone |
| **Security / privacy / compliance auditor** | Audit access, policy adherence, retention, incident response | Read immutable audit records and approved metadata; case content access only when separately authorized and logged |
| **Victim / reporting party (optional deployment mode)** | Provide a narrative and files with clear consent and expectations | Submit or update own report; see acknowledgment and safe next steps; cannot see investigative leads, other people’s data, or internal notes |
| **Prosecutor / authorized reviewer (optional)** | Review a disclosure-approved package and lineage | Read only the explicitly shared, versioned report and associated evidence references |

### 3.2 Role and access rules

- Access is authenticated, least-privilege, case-scoped, and auditable. Agency roles and separation-of-duty rules are configurable; no role is assumed to have universal access.
- A case assignment does not imply permission to access every evidence item or restricted category. The system supports item- and field-level restrictions for especially sensitive records.
- High-risk access (for example, sealed material, credentials, intimate personal data, or restricted-source information) requires an additional approved purpose and is recorded distinctly.
- Administrators who can configure models, MCP servers, retention, or roles must not gain implicit access to evidence content. Privileged operations require just-in-time elevation where supported and a reason code.
- Service identities and agents are not human users. Each has a unique identity, narrowly scoped permissions, expiry, and attributable audit events.

### 3.3 External stakeholders

External sources may include public-record registries, domain/DNS services, reputation or threat-intelligence feeds, licensed OSINT providers, public blockchain explorers, email/security-analysis services, and MCP servers. Each source is separately assessed for lawful basis, permitted use, coverage, freshness, terms, retention, security, and potential victim-data disclosure. The system must not infer that “publicly reachable” means “lawfully reusable for every purpose” [R07, R09].

---

## 4. Vision, Goals, and Non-Goals

### 4.1 Product vision

Give authorized investigators a reliable, transparent way to convert fragmented scam reports and digital artifacts into organized, source-grounded investigative leads—without imposing technical burden on victims and without surrendering evidentiary judgment to AI.

### 4.2 Goals

1. **Simple intake:** capture a useful report through one plain-language narrative and one evidence area, including incomplete or unstructured submissions.
2. **Preserved evidence:** retain a verifiable original and document every ingest, access, transformation, derivative, and export.
3. **Actionable structure:** extract candidate entities, events, and relationships with links to source locations and a review state.
4. **Adaptive, bounded analysis:** plan only relevant tasks, choose among approved specialist agents/tools, parallelize safe independent work, and stop at policy boundaries.
5. **MCP extensibility:** let administrators add and govern new tool servers without changing core product code, while preventing arbitrary servers from reaching case data.
6. **Analyst efficiency:** reduce time spent on repetitive triage and source collation while preserving investigator control and work-product quality.
7. **Trustworthy reporting:** produce clear reports distinguishing fact, allegation, observation, inference, and unresolved lead; provide provenance and reproducibility metadata.
8. **Responsible operation:** protect victim and third-party privacy; limit collection; detect malicious evidence/tool output; provide accessible, humane interactions.

### 4.3 “100×” operating interpretation and success measures

“100×” is **not a product guarantee** and cannot mean perfect detection or that evasion is impossible. It is operationalized as an aspirational compound of breadth, speed, traceability, and safety. Success is measured against a documented baseline and is accepted only if quality and safety are not degraded.

Proposed pilot measures (targets to validate with representative agency case data before production commitment):

- Median time from evidence upload to first investigator-reviewable entity/evidence index: **under 10 minutes for a 1 GB text-and-image case on the reference deployment**, excluding external source wait time and OCR queue delays.
- At least **90% of displayed extracted candidate entities** have a source span, page, timestamp, or equivalent artifact locator; no candidate presented as confirmed without human confirmation or deterministic validation.
- At least **95% of external-source-derived findings** include source name, retrieval timestamp, query/response reference or permissible capture, and freshness/status metadata.
- **Zero silent source failures:** unavailable, blocked, rate-limited, stale, or nonresponsive sources are visibly marked and never replaced by generated assertions.
- **100% of agent/tool invocations** are linked to a case, task, actor identity, policy decision, tool/server version, timestamp, and outcome in the audit record.
- **100% of report claims** designated material by the report schema are linked to one or more reviewed evidence/findings records or explicitly marked as uncorroborated allegation/inference.
- On a held-out, independently labeled test set, extraction precision/recall and false-positive rates are reported separately by artifact type and language; release thresholds are set by agency policy and must not be combined into a single opaque “accuracy” score.
- No automated action against a person or third-party service occurs without human review; such actions are outside the baseline scope even with approval until a separate product and legal review authorizes a specific workflow.

A 100× throughput claim is not a launch criterion. Measure investigator minutes saved, time to first useful lead, evidence coverage, report preparation time, error rate, and reviewer correction burden. Compare identical case sets and analyst baselines. Record the method, confidence intervals, case mix, missing-data rate, and the risks of automation bias.

### 4.4 Non-goals (initial product)

- Automated identity resolution as a final conclusion; facial recognition; voice identity matching; or automatic linkage of an online alias to a named individual.
- Covert access, credential stuffing, account takeover, unauthorized network scanning, malware execution, exploitation, or purchasing illegal data.
- Unrestricted dark-web crawling. Any dark-web/threat-intelligence function must use a legally reviewed, contractually permitted, controlled provider or a specifically authorized collection environment and must not facilitate illicit transactions or dissemination.
- Automated direct messaging, baiting, undercover interaction, calling a subject, contacting a suspect, or contacting a financial institution/provider on an investigator’s behalf.
- Automatic blocking, account suspension, fund freezing, geofence creation, surveillance watchlisting, arrest recommendation, or guilt/risk score.
- Training foundation models on case evidence by default. Case data may not be used to train or fine-tune external models unless a separate, explicit, lawful agency authorization and data-governance review permits it.
- Consumer-facing promise of recovery of money or guaranteed case resolution.

---

## 5. Product Principles and Evidence-Informed Decisions

The literature in the repository yields several practical product requirements. These are used as design guidance, with limitations, rather than as proof of the product’s future performance.

- **Dynamic specialization must be economical and task-relevant.** Multi-agent systems can decompose work and specialize, but composition, role design, dependencies, and resource assignment affect both quality and cost. Avoid spawning a fixed swarm for every case; select the smallest approved team adequate for the stated objective [R01–R04].
- **A task graph must be observable and revisable.** The Manager Agent literature emphasizes dependency-aware workflow planning, monitoring, adaptation, constraints, and stakeholder communication. Aegis therefore exposes a reviewable task plan and explicit pause/cancel/replan controls [R04, R05].
- **Do not assume collaboration eliminates errors.** Agent errors can propagate through dependency graphs and become false consensus; maintain source-level claim genealogy, independent validation, conflict detection, and correction propagation [R06].
- **OSINT requires tradecraft, validation, and governance.** Distinguish public and commercially available information from other data; record source context, terms, collection method, limitations, and confidence; do not equate collection with intelligence [R07–R09].
- **Evidence work is multimodal and method-dependent.** Preserve distinct OCR, contextual text, visual, audio, and metadata evidence channels; record modality availability, quality, and extraction method instead of treating model output as native evidence [R12–R18].
- **Tool use is a long-horizon reliability problem.** Tool selection, parameter correctness, dependencies, ordering, parallelism, state, failure recovery, and evaluation all matter. Validate schemas and parameter values, use constrained calls, and test multi-step cases [R19–R25].
- **MCP expands interoperability and attack surface together.** Separate host/client/server responsibilities, register capabilities, synchronize tool changes, pin reviewed versions, apply policy and least privilege, and test transport and response boundaries [R26–R30, R36].
- **Human alignment and adversarial resistance are system properties.** Scope, values, oversight, and escalation should be explicit; handle instructions in evidence and tool responses as untrusted data; check the original investigative intent across decomposed tasks [R32–R34].
- **Accessible interaction is not optional.** Victims should not be required to produce a precise technical query or navigate operational jargon; use plain language, progressive disclosure, and contextual guidance [R35].

---

## 6. Scope and Product Modules

### 6.1 Release scope

The initial release comprises:

1. Secure sign-in, role and case access controls, audit records, and organization-level configuration.
2. Case intake using a narrative box plus file/folder import, consent and authority statements, and a reviewable extraction queue.
3. Evidence storage, hashing, chain-of-custody events, derivatives, artifact parsers, and integrity verification.
4. Case workspace with entities, source records, findings, leads, timeline, relationship view, agent/task status, and notes.
5. Master Agent planning, approved specialist-agent execution, tool registry, human approvals, cancellation, and traceability.
6. MCP server management with capability inventory, trust review, policy assignment, safe connection tests, version integrity, and health status.
7. Controlled OSINT and forensic connector framework; launch with only agency-approved sources configured.
8. Report drafting, review, export, evidence manifest, and signed/versioned audit trail.
9. Testing, operational monitoring, retention configuration, backup/restore, and security incident procedures.

### 6.2 Later capabilities requiring separate authorization and validation

- Additional jurisdictions and language packs; organization-managed translation models.
- Special-purpose social-platform or commercial OSINT connectors after terms, legal basis, and privacy review.
- Additional forensic parsers and licensed financial data connectors.
- Interagency exchange or CTI export formats (e.g. STIX-compatible packages) with redaction, disclosure review, and recipient authorization.
- On-premises/local model options for restricted environments.

No future capability is implicitly approved by appearing in this roadmap.

---

## 7. Detailed Functional Requirements

Priority meanings: **P0** required for a safe initial release; **P1** high value after P0; **P2** optional / later. “Must” indicates a testable product requirement.

### 7.1 Identity, authentication, and access (P0)

- **AUTH-01:** Support agency-configured identity provider integration where available, with MFA for privileged roles. Local accounts, if enabled, require strong credential policy, MFA, secure recovery, and an administrator-controlled lifecycle.
- **AUTH-02:** Enforce role-based access control and case assignment checks on every API and background job, not only in the UI. Deny by default.
- **AUTH-03:** Bind every user, service, agent, and connector action to a unique identity. Shared accounts are prohibited for operational use.
- **AUTH-04:** Support session expiry, revocation, device/session review, and step-up authentication for exports, sensitive evidence, permission changes, credential updates, and destructive actions.
- **AUTH-05:** Allow admins to configure separation of duties (e.g., the report author cannot be the sole final approver; connector administrators cannot access evidence contents by default).
- **AUTH-06:** Show access denied and unavailable states without leaking case existence, evidence names, or sensitive metadata to unauthorized users.

### 7.2 Case creation and lifecycle (P0)

A case is the authorization, workflow, and retention boundary for an investigation.

- **CASE-01 Create:** An authorized user creates a case using a short case name or generated reference, a plain-language incident narrative, and an evidence upload/folder import. Narrative and files are the primary intake controls; remaining information is captured through optional, contextual questions or policy/authority confirmations, not a long mandatory form.
- **CASE-02 Minimal required metadata:** Require only what the agency needs to establish ownership and lawful purpose: unique case identifier, responsible unit/owner, case purpose/category, applicable jurisdiction or policy profile, access classification, retention profile, and declaration of authority/consent status according to agency rules. A victim can file a report without knowing these internal fields; an officer completes or confirms them before investigative collection begins.
- **CASE-03 Status:** Support Draft, Intake Review, Authorized, Active, Paused, Awaiting External Information, Review, Closed, Reopened, and Archived. State changes record actor, time, prior/new state, reason, and any required approval.
- **CASE-04 Scope statement:** Before external collection or agent execution, capture the authorized investigative purpose, included identifier(s), source categories, date/time range if known, data restrictions, geographic/jurisdictional limits, expiry/review date, and approval identity. Unknown scope elements must be explicitly marked unknown or not applicable, never silently inferred.
- **CASE-05 Update:** Narrative, scope, evidence, owners, priority, and tags may be updated with revision history. A scope expansion requires a documented reason and additional approval where policy demands it; already completed work remains linked to the scope version under which it ran.
- **CASE-06 Pause/cancel:** Authorized investigators can pause queued/running work. Cancellation propagates to agents and connector jobs where feasible, records completed/partially completed operations, and prevents new calls. Any non-cancelable operation is clearly identified and monitored until termination.
- **CASE-07 Close/reopen:** Closure requires an authorized user, disposition category, evidence/report checks, and retention/legal-hold choice. Reopening requires a reason and renewed scope/authority review if required by policy.
- **CASE-08 Data subject/victim communication:** In victim-facing mode, show an acknowledgment and case-report reference, safe contact guidance configured by agency, and clear privacy expectations. Never expose operational hypotheses or another person’s data through the intake portal.
- **CASE-09 Duplicate/related cases:** Suggest possible duplicates or related case IDs only from authorized records and explain the match basis. A human confirms linkage; merging preserves original identifiers, access restrictions, and audit histories.

### 7.3 Victim narrative intake (P0)

The intake screen contains one prominent free-text box. It accepts a story in ordinary language, including incomplete, emotional, or non-chronological descriptions. It must not require the reporter to know technical labels.

- **INTAKE-01 Guidance:** Placeholder/helper text says, in reassuring language, that the person may describe what happened in their own words, include any details they remember, skip anything unknown, and avoid sending passwords, PINs, one-time codes, full payment-card security codes, or unrelated private material. Suggested hints are optional, collapsible, and phrased as examples—not fields.
- **INTAKE-02 Example hints:** “What happened and when; how the person contacted you; phone numbers, email addresses, usernames, links or website names; payment instructions, UPI ID or bank/payment reference; wallet address or transaction ID; messages, email files, screenshots, receipts, call logs, or other files; what you have already reported.” Explain that a partial report is useful.
- **INTAKE-03 Accessibility and distress:** Save a draft where feasible; state maximum size and privacy notice before submission; provide a “I need help” route configured by the agency. Do not force the reporter to repeat traumatic content or answer an AI-generated interrogation. Optional follow-up questions are limited to material gaps and can be skipped.
- **INTAKE-04 Parsing:** Extract candidate names/aliases, contact points, account identifiers, URLs/domains, payment references, currencies/amounts, dates/times, platforms, claimed organization/person, event sequence, communication channels, and narrative assertions. Every extraction includes a verbatim text span, position, extraction method/model/version, confidence or uncertainty band, and status `unreviewed` until a human confirms it or a deterministic rule validates it.
- **INTAKE-05 Ambiguity:** Preserve alternatives (e.g., two possible dates, uncertain digit in a phone number, conflicting name spelling) rather than choosing one. Ask a narrow, plain-language clarification only if needed; allow “not sure.”
- **INTAKE-06 Language:** Detect likely language; preserve original wording; optional translation is a derivative, with source and translated text linked. Machine translation is visibly labeled and cannot replace the original.
- **INTAKE-07 Prompt injection:** Narrative text and attachments are evidence, not instructions to the system. Embedded directives, URLs, macros, code, or requests to reveal hidden system information are treated as untrusted content and cannot alter system policy, permissions, agent roles, or case scope [R27, R34].
- **INTAKE-08 No outcome promise:** Do not claim the platform can trace, catch, recover funds from, or identify a person. Explain that leads require review and outcomes depend on available evidence and lawful access.
- **INTAKE-09 Victim data separation:** Keep victim contact and identity information separately permissioned from investigative entities when feasible. Do not automatically treat victim-provided third-party allegations as verified facts.

### 7.4 Digital evidence upload and local folder connection (P0)

The evidence area supports drag/drop, file selection, and a local folder picker in supported desktop/browser environments. The product must not claim it can read an arbitrary local path from a browser; if directory selection is unavailable, use multi-file selection or an agency-managed evidence acquisition client. Upload supports resumable transfer and progress state.

#### 7.4.1 Ingest behavior

- **EVID-01:** Before upload, show accepted categories, size limits, malware-safety notice, privacy/retention summary, and whether files may be processed locally or by approved services. Show the planned destination and case identifier.
- **EVID-02:** Preserve uploaded bytes as an immutable original object. Calculate SHA-256 at ingest; optionally calculate SHA-512 as a second integrity value. Record original filename, byte size, MIME claim, detected type, uploader, case, upload time, source device/client where available, and ingest result. A mismatch or scanner/parser failure never blocks retention of the original unless policy explicitly quarantines it; it does block analysis until safe.
- **EVID-03:** Never execute uploaded code, scripts, macros, active content, or embedded links. Process in isolated, resource-limited workers. Parse archives with path traversal, decompression bomb, recursion, file count, and size safeguards. Quarantine suspicious content and record the reason.
- **EVID-04:** Produce derivatives (OCR text, previews, normalized headers, extracted metadata, transcodes, parser outputs) as separate versioned evidence objects linked to the original by a transformation record. A derivative never overwrites or replaces its source.
- **EVID-05:** Support duplicate detection by cryptographic hash within a permitted scope. Detecting a duplicate does not discard the submitted copy; record its source and link it to the existing identical object only if policy permits.
- **EVID-06:** Record failed or interrupted uploads, retry safely, and ensure retries do not create ambiguous originals. Verify the final hash after transfer.
- **EVID-07:** Label source provenance as `victim-provided`, `officer-provided`, `agency-acquired`, `external-source-capture`, or other governed source class. Record how an external capture was obtained, when, from which authorized source, and any permitted raw response/screenshot reference.
- **EVID-08:** Detect file type using content signatures as well as extension; show extension/type mismatches. Do not trust embedded creation times as authoritative system time.
- **EVID-09:** Provide explicit review states: Uploaded, Quarantined, Processing, Processed, Processing Failed, Integrity Check Failed, Restricted, and Deleted/Retention Expired (tombstone only when required).
- **EVID-10:** Provide access to chain-of-custody history, hash verification, all derivative lineage, and export manifest. Reverification mismatch raises a security incident and prevents the item being labeled integrity-verified.

#### 7.4.2 Initial format support

The product must detect and catalog at least the following formats, with a clearly scoped parser matrix and versioned support status. “Supported” means safe identification/cataloging plus the listed extraction path where applicable; it does not mean every encoding or proprietary format can be decoded.

- **Text and documents:** TXT, CSV, TSV, JSON, XML, HTML, PDF (text and image/scanned OCR), DOCX, XLSX, PPTX, RTF; legacy/unsupported formats are preserved and flagged for an approved examiner tool.
- **Email and messaging:** EML, MSG where licensed parser is available, MBOX, PST/OST only through approved forensic tooling; exported chat formats such as JSON/HTML/TXT/CSV from supported platforms; SMS/call-log exports when supplied in a documented format. Preserve headers, identifiers, ordering, timezone context, and original message text.
- **Images:** JPEG/JPG, PNG, TIFF, WebP, HEIC where decoder is approved, BMP, GIF; preserve originals and EXIF/XMP/IPTC metadata where present. OCR outputs include page/region coordinates and OCR confidence where available.
- **Audio/video:** WAV, MP3, M4A, AAC, MP4, MOV, AVI, WEBM and common codecs subject to safe transcoding. Initial release may catalog and securely preview only; transcription requires a separately approved model/engine and creates a labeled derivative. No voice identification or speaker attribution by model.
- **Archives:** ZIP, 7z, TAR, GZ and other formats only in sandboxed, bounded extraction. Preserve archive and member relationships; encrypted archive is cataloged as encrypted and is not brute-forced.
- **Network and system artifacts:** PCAP/PCAPNG, EVTX, selected browser history/bookmark exports, structured DNS/proxy/firewall logs, and forensic tool reports as approved. Native disk images, mobile device images, and memory dumps may be preserved as large restricted objects, but initial release does not promise in-product deep parsing or recovery of deleted data.
- **Financial/transaction records:** PDFs, images, CSV exports, bank/payment receipts, statements, UPI/payment app screenshots, blockchain transaction data supplied by the user; extraction is candidate-only and sensitive fields are masked in ordinary views.
- **Other files:** preserve and catalog any file type subject to configured size policy; do not analyze unsupported or potentially executable formats automatically.

#### 7.4.3 Ingestion outcome and user communication

For every file, show name, size, content-type result, hash status, ingest progress, processing status, warnings, and permitted actions. Explain an unreadable, encrypted, unsupported, or corrupted file in plain language and allow an investigator to record a note. No content is silently omitted. An upload containing malware-like content is not executed; access and handling follow the agency’s forensic policy.

### 7.5 Entity extraction, normalization, and review (P0)

The entity catalog stores both the observed raw value and normalized representation. It must not destroy distinctions (such as `+91` phone notation, leading zeros, original punctuation, or case-sensitive handles).

- **ENT-01:** Extract entities using deterministic parsers first where appropriate and ML/LLM-assisted extraction as optional candidate generation. This hybrid approach limits unnecessary model calls and improves reproducibility [R12, R19, R21].
- **ENT-02:** Each candidate includes type, raw value, normalized value if applicable, source evidence ID and exact locator, observed time vs. event time distinction, detection method/version, confidence/uncertainty, sensitivity class, and review state.
- **ENT-03:** Human can Confirm, Reject, Correct, Mark Ambiguous, or Restrict an entity. Corrections are append-only with prior value, author, time, and reason retained. Rejected candidates remain auditable but are excluded from ordinary graph analysis.
- **ENT-04:** Normalizers are versioned and locale-aware. Preserve original timezone and offset; dates without timezone remain timezone-unknown. Currency values retain original symbol/string and use a separate ISO currency normalization only when defensible.
- **ENT-05:** Deduplication creates a possible-equivalence suggestion, not an automatic merge, unless values match by a safe deterministic rule and policy. Similar names, images, aliases, and partial identifiers are never automatically merged.
- **ENT-06:** Support multilingual scripts and Unicode normalization while preserving the exact original bytes/text span and script. Transliteration is a separate, explicitly labeled representation.
- **ENT-07:** Candidate identification is not an identity assertion. A human-reviewed record must still distinguish `observed`, `claimed`, `reported`, `source-attributed`, `correlated`, and `confirmed by independent evidence`.

### 7.6 Master Agent, child agents, and workflow orchestration (P0)

#### 7.6.1 Master Agent responsibilities

The Master Agent is a case-specific coordinator, not an autonomous investigator with unrestricted access. It receives the authorized scope version, reviewed/allowed case context, available evidence metadata, approved tool catalog, and case policy. It generates a typed task graph that includes rationale, task dependencies, candidate agents/tools, required data, expected outputs, privacy classification, estimated cost/time, and approval requirements. Task planning and role/team construction should be adaptive and task-relevant, rather than a fixed agent set for all matters [R02–R05].

- **AGENT-01:** Before execution, display the plan in understandable terms: what will be examined, why it is relevant, which data may be sent outside the agency boundary, which tools may be invoked, and what remains uncertain.
- **AGENT-02:** The planner may propose tasks only within case purpose, scope, connector allowlist, data policy, and user role. It cannot expand legal authority or infer authorization from a victim narrative.
- **AGENT-03:** Run safe, independent, read-only tasks in parallel subject to per-case and per-provider concurrency budgets. Tasks with dependencies execute only after prerequisites complete and their outputs pass validation. Avoid race conditions and make retries idempotent [R19, R24].
- **AGENT-04:** Agent count is bounded by deployment policy; each case has maximum depth, total tool calls, token budget, runtime, external-source quota, and cost budget. The UI shows budgets and stops with an explicit reason when a limit is reached.
- **AGENT-05:** Use least-capable model and minimal context appropriate to the task. Sensitive case data is routed only to models/providers approved for that classification. Where no approved model exists, skip AI or use a local approved route.
- **AGENT-06:** The Master Agent cannot direct a child agent to perform an unapproved operation by hiding or relabeling a task. A central policy engine validates every task and invocation independent of model output.
- **AGENT-07:** The Master Agent aggregates findings without treating repeated copies of the same source as independent corroboration. It detects conflicting findings, missing evidence, source dependence, stale results, and unsupported conclusions. It may request a verification task but must not erase the original disagreement [R06].
- **AGENT-08:** The system maintains a claim genealogy: source observation → parsed artifact → entity/finding → dependent task → report claim. Corrections, tool version changes, and invalidated data mark downstream items for review rather than silently changing them [R06].
- **AGENT-09:** Agents cannot make autonomous suspect guilt judgments, determine probable cause, rank individuals by criminality, contact subjects, publish results, or execute enforcement actions.
- **AGENT-10:** Investigators can inspect, pause, cancel, approve, reject, retry, or request a new plan. Every plan revision is versioned and reasoned.

#### 7.6.2 Child-agent registry and lifecycle

Child agents are templates in an administrator-reviewed registry, not freely generated executable code. A template defines agent ID/version, task classes, model eligibility, input/output schema, allowed sources/tools, data classes, permissions, limits, validation rules, safety tests, owner, and retirement state. LLM-generated role text cannot grant new permissions.

Lifecycle: `Proposed → Reviewed → Enabled → Instantiated for task → Running → Completed / Failed / Cancelled / Timed out → Retained as audit record → Template version retired`. An instance has an ephemeral context, task-specific minimum inputs, task ID, deadline, resource budget, and explicit teardown policy. No agent retains cross-case memory by default. Any opt-in shared knowledge base must exclude case-specific personal data unless separately approved and governed.

#### 7.6.3 Initial specialist-agent catalog

The initial product may ship templates for the tasks below; deployment administrators choose which are enabled. Each output is a candidate analytic artifact, not an attribution verdict.

1. **Narrative and entity extraction:** structured candidate facts and timeline with exact text spans.
2. **Email artifact analysis:** parse supplied EML/MBOX/header data; identify routing/authentication fields and inconsistencies; report interpretation limits; do not claim a sender’s real-world identity from a header alone.
3. **URL/domain/DNS enrichment:** safely normalize a submitted URL, derive registrable domain, query approved passive DNS/WHOIS/TLS/reputation sources, and preserve retrieval times. Never visit a live target using investigator credentials or run active scans by default.
4. **Phone/contact normalization:** parse, format, and compare supplied phone numbers/email addresses against approved public/licensed sources. Do not reveal protected subscriber information or bypass provider controls.
5. **Social/public-web lead review:** search only approved public endpoints or licensed data; record query, source, terms/version, capture time, result context, and limitations. No private-account access, credential use, evasion of access controls, or broad personal profiling.
6. **Chat and communication analysis:** segment conversations, extract keyphrases/entities/events, preserve speaker labels as supplied, and surface candidate coercion/payment patterns with false-positive warning. Research on high-volume chat extraction informs efficient triage, not behavioral guilt classification [R13, R16].
7. **Image/document metadata and OCR:** extract metadata and visible text as derivatives; retain region/page references and OCR uncertainty. No facial recognition or identity matching.
8. **Payment/financial normalization:** structure user-supplied bank, card, UPI, payment-app, remittance, and transaction data; mask sensitive fields; no account access or bank-system queries absent a distinct authorized integration.
9. **Public-blockchain transaction analysis:** query approved public-chain data sources for a supplied transaction or wallet; report observed on-chain paths and provider labels with timestamps and caveats. Do not infer the controller’s identity from wallet linkage or present clustering as definitive. No access to private exchange/KYC records absent legal authority and a separate connector.
10. **Timeline and contradiction review:** normalize events to a timeline with original timezone/source, distinguish reported time from artifact metadata, and identify conflicts for investigator review.
11. **Cross-source corroboration:** test whether independently sourced records support or conflict with a candidate entity. Independence is defined by source lineage, not by number of search results.
12. **Report drafting:** create a structured, citation-linked draft from reviewed findings only; label unreviewed data and uncertainty; never invent missing facts.

Prohibited initial templates: face recognition, biometric speaker identification, private-account scraping, credential discovery, exploit/reconnaissance against live targets, person-location prediction, and automated suspect scoring.

#### 7.6.4 Inter-agent communication and output contract

Agents communicate through a typed internal task/event interface with correlation ID, case/scope version, task ID, sender/recipient identity, timestamp, data classification, schema version, and content hash. Free-form agent-to-agent chat is not the source of record. Each finding uses a structured contract:

- finding ID and type;
- concise observation, separated from interpretation;
- evidence/source references and exact locators;
- method, tool/model/server versions, retrieval/analysis time;
- source status (primary/secondary, public/licensed/user-supplied/unknown), provenance and freshness;
- confidence semantics and known limitations (no uncalibrated “probability of guilt”);
- dependencies and upstream finding IDs;
- corroboration/conflict links;
- privacy/data classification and any redaction;
- review status and proposed next step, if any.

Agent output is schema-validated, length-limited, escaped, and treated as untrusted data. Invalid output is rejected or held for review; it is not silently coerced into a valid finding. Tool output containing instructions is never executed as instruction. Cross-agent context is minimized and only sent under an explicit need-to-know policy [R06, R27, R29, R34].

### 7.7 OSINT and forensic capability catalog (P0 framework; source enablement is jurisdiction- and agency-dependent)

The following techniques describe supported capability classes, not permission to access every source. Each connector requires a documented source profile, approved purpose, terms and legal review, data retention rule, permitted fields, authentication, rate limits, and a kill switch.

#### 7.7.1 Email and communication

- Parse supplied email headers, MIME structure, attachment names/types, message IDs, dates, routing fields, and authentication results where present.
- Extract candidate sender/recipient addresses, domains, URLs, payment instructions, claimed organizations, and timestamps from message bodies and attached receipts.
- Explain that SPF/DKIM/DMARC signals and `Received` chains have context-specific limitations and may be affected by forwarding, gateways, spoofing, or incomplete data.
- Analyze supplied chat/SMS exports for entities, event sequences, repeated asks, and key phrases. Preserve exact message order, platform export metadata, and supplied speaker mapping; uncertain diarization/translation remains uncertain.
- No inbox login, mailbox access, message sending, contact discovery beyond approved sources, or inference that a displayed sender name proves the sender’s identity.

#### 7.7.2 Phone, email, username, and public social identity

- Normalize user-supplied phone numbers, email addresses, handles, public profile URLs, and organization names; record country/locale assumptions.
- Search approved public or licensed indexes for reuse, account links, official contact details, business registrations, or relevant public posts, with query and source logs.
- Distinguish exact identifier match, display-name match, fuzzy similarity, and analyst interpretation. Do not automatically fuse profiles with similar names or profile images.
- No private profile access, account enumeration using deceptive means, scraping behind login, circumvention of rate limits, stalking, doxxing, or continuous monitoring of an individual.

#### 7.7.3 Domains, URLs, IP addresses, and network indicators

- Safely parse URLs, punycode/Unicode domain representation, redirect chains only through a controlled resolver/browser sandbox when approved, and domain registration/DNS/TLS/reputation records from authorized providers.
- Record query time, provider, record type, historical/current status, resolution context, and geographic precision. IP geolocation is an approximate network attribute; the platform must not display it as a person’s location or home address.
- Passive DNS and domain history results are leads and can be shared infrastructure. Label shared hosting, VPN, proxy, CDN, NAT, Tor exit, mobile carrier, and cloud infrastructure caveats where known.
- No default port scanning, vulnerability scanning, exploitation, credential testing, or direct interaction with suspected criminal infrastructure. Any narrowly authorized active collection requires a separately reviewed tool, explicit scope, investigator approval, rate controls, and an auditable execution plan.

#### 7.7.4 Images, video, metadata, and multimodal evidence

- Extract available EXIF/XMP/IPTC metadata, create visual hashes for duplicate or near-duplicate detection within authorized evidence, and perform OCR with coordinates, language, quality, and confidence.
- Optional reverse-image similarity search may use user-supplied images against approved public/contracted endpoints, with a record of the image sent, provider, purpose, response, retention terms, and notice/approval. Prefer local perceptual-hash matching against case evidence or an approved reference corpus.
- No face identification, biometric identification, or identity assertion from visual similarity in the initial product. No bulk image search of a population or sensitive-class inference.
- Optional speech transcription is a labeled derivative only. No speaker identity recognition. Preserve source media, transcription engine/version, language, timestamps, confidence/quality notes, and any omissions.
- The multimodal workflow records whether information came from embedded OCR text, user-provided contextual text, or image-only semantic analysis; it must not imply that an absent modality was examined [R14].

#### 7.7.5 Cryptocurrency and payment-related evidence

- Parse public-chain addresses, transaction hashes, chain/network, token, amounts, block time, confirmations, and supplied exchange/deposit references. Validate checksum/format where applicable.
- Use only approved public-chain or licensed analytics connectors. Keep raw on-chain observation separate from provider labels, heuristics, cluster attribution, and investigator interpretation.
- Show that wallet control, beneficial ownership, and real-world identity are unknown unless independently established through authorized evidence. A hop or cluster is not proof that a person received or controlled funds.
- Normalize bank account, payment handle, UPI ID, merchant, card, and remittance data only from user-supplied/authorized evidence. Mask complete account/card numbers and secrets in routine displays and logs; use field-level access for unmasked values.
- No direct bank queries, account access, fund tracing into non-public systems, freezing, recovery, or transaction initiation by Aegis. Lawful process and institution-specific procedures remain external to this initial product.

#### 7.7.6 Dark-web and threat-intelligence sources (P1 and explicitly controlled)

- Only use an approved commercial/intelligence feed, legally reviewed archive, or specifically authorized collection environment. Each source profile must state collection provenance, terms, data minimization, known reliability, and whether illicit or personal data may appear.
- Do not facilitate purchases, downloads, credential use, forum participation, undercover engagement, or distribution of illicit material. Do not expose an investigator to illegal content by default; use safe indicators/metadata and escalation procedures.
- Results are unverified leads, time-sensitive, and potentially planted or fabricated. Preserve source reliability notes and require human review before report inclusion.

#### 7.7.7 Browser and device artifacts

- Analyze browser histories, cookies, caches, local storage, download records, and device/system artifacts only when supplied under authorized forensic acquisition and within case scope. Cookie/session secrets are restricted and excluded from agent context by default.
- Browser fingerprinting is limited to review of authorized, already-collected forensic artifacts or aggregate/consented telemetry; it is not used to secretly fingerprint, track, or deanonymize a person. Any derived device/browser correlation is a lead with alternative explanations and a human-review requirement.
- No covert tracking pixels, malware, remote agents, spyware, or unauthorized endpoint collection.

### 7.8 MCP server management and integration (P0)

MCP is a modular capability integration system, not an implicit trust channel. The MCP host/client/server relationship, capability discovery, transport, server lifecycle, and risk boundaries inform the controls below [R26–R30, R36].

#### 7.8.1 Add and review a server

- **MCP-01 Registration:** An administrator can add a server using an approved transport/configuration form or import a validated configuration file. Supported transport types are deployment-approved MCP transports (e.g., local stdio for isolated services and remote Streamable HTTP where supported); legacy transport modes are disabled unless explicitly reviewed. No arbitrary user can register a server.
- **MCP-02 Metadata:** Capture display name, stable server ID, owner/vendor, purpose, endpoint/launch command, transport, environment, organization/trust domain, data classifications accepted, source categories provided, documented capabilities, software/version/commit, license/terms, security contact, dependency/image provenance where available, approved regions, data retention behavior, and expiry/review date.
- **MCP-03 Identity and trust:** Verify server identity and endpoint/launch provenance using configured credentials, certificates, signatures/attestation where available, allowlisted network destinations, and reviewed version/hash. A successful connection test is not a security approval.
- **MCP-04 Review states:** `Draft → Security Review → Privacy/Legal Review → Approved → Enabled → Suspended / Disabled → Retired`. Only `Approved` and `Enabled` servers can be offered to cases. Reviewers, decisions, conditions, and expiry are recorded.
- **MCP-05 Capability discovery:** Discover tools/resources/prompts and schemas in an isolated test context. Store a versioned inventory and cryptographic hash of the approved definitions. Changed capability names, descriptions, schemas, dependencies, endpoint, or version trigger re-review; detect “rug pull” changes and block invocation until resolved [R28, R29].
- **MCP-06 Tool review:** For every discovered tool record function, inputs/outputs, side effects (read/write/external communication), data sent, destination, required privileges, classifications, rate/cost limits, timeout, and allow/deny status. Unknown side effects default to deny.
- **MCP-07 Credentials:** Store secrets in an approved secret manager; never in case notes, prompts, source files, plain configuration exports, or audit event bodies. Support rotation, revocation, scope restriction, and access audit. Do not share one broad credential across unrelated servers/cases when finer-grained credentials are available.
- **MCP-08 Test:** Provide connection and schema tests using synthetic/non-sensitive inputs only. Tests must show handshake, discovery, schema validation, expected response shape, timeout, error behavior, and data egress. Never send live victim data during onboarding tests.
- **MCP-09 Enable:** Activation requires named approver, intended case groups, allowed data classes, permitted tools, resource limits, and a default-off data-sharing policy. An organization-level policy may impose stricter limits.
- **MCP-10 Runtime policy:** Before each tool call, enforce case authorization, task authorization, user/agent identity, tool allowlist, data classification, destination, purpose, consent/approval, parameter schema, data minimization, and rate/cost budget. Deny if any required check is missing.
- **MCP-11 Output validation:** Enforce size/depth limits, content type, schema, timeout, serialization safety, and structured error handling. Treat return text/resources as untrusted; scan for secrets or cross-case data; do not follow instructions embedded in tool responses [R30].
- **MCP-12 Data flow:** Show which case fields/data are sent to a server before first use, by category and classification. Default to sending only the minimum relevant identifier, not the narrative, full evidence, victim identity, or entire case file. Require an approved egress policy and explicit case approval for external case data.
- **MCP-13 Runtime isolation:** Run local MCP servers in least-privileged sandboxes with restricted filesystem/network access; remote servers are segmented by trust domain. Prevent tool-composition chains from bypassing a permission boundary. Enforce capability-based access and track information flow [R29].
- **MCP-14 Monitoring:** Show health, latency, error rate, last successful handshake, capability hash, version, credential expiry, and recent policy denials. Alert on unexpected capability changes, repeated failures, unusual data volume, new destinations, or suspected compromise.
- **MCP-15 Control:** Admins can suspend/disable/retire a server immediately. Suspension prevents new calls, cancels safe in-flight work where possible, records outstanding operations, preserves audit records, and flags affected findings for review.
- **MCP-16 Import/export:** Export configuration without secrets. Import revalidates schema and policy and cannot auto-enable an imported server.
- **MCP-17 Availability:** Server failure must be visible and must not be silently substituted with another source/tool. Retry is bounded, safe, and idempotent; results include missing-source status.
- **MCP-18 Feature parity:** Non-MCP services can use adapters into the same tool registry, policy engine, audit format, and task interface. MCP is first-class but does not become a single point of architectural lock-in [R20, R23].

### 7.9 Tool registry and source governance (P0)

A centralized tool registry holds all first-party, MCP, and approved external capabilities. It records canonical schema, name/description, owner, version/hash, source, capability tags, sensitivity, side effects, cost, latency, availability, jurisdiction, terms, and permitted case scopes. The registry dynamically exposes only relevant, permitted tools to an agent; retrieval can use current task context and dependencies, not only the initial prompt [R22, R28].

- Tool descriptions are treated as untrusted content until reviewed; a server cannot promote itself or overwrite a reviewed definition.
- Schema and parameter validation occurs before invocation; values derived from prior tool output retain their lineage and require validation at each boundary [R25].
- Tool selection quality is measured separately from call-parameter validity, result correctness, and downstream analyst acceptance [R19, R25].
- Fallback behavior is explicit, approved, equivalent in data classification, and recorded. “Fallback” cannot mean switching to a broader data source or sending more data to a different provider.

### 7.10 Findings, leads, and evidence relationships (P0)

- **FIND-01:** Store observations, analytical findings, investigative leads, and hypotheses as distinct record types.
- **FIND-02:** Each record has provenance, evidence references, method, actor, time, source quality/status, confidence semantics, scope version, and review state.
- **FIND-03:** A relationship graph can show evidence, artifacts, entities, events, and findings. Relationship types include `observed-in`, `reported-by`, `sent-to`, `resolved-to`, `paid-to`, `same-as-candidate`, `temporally-near`, `supports`, `contradicts`, and `derived-from`. Inferred relationships are visually differentiated from observed relationships.
- **FIND-04:** Every graph edge states why it exists, its provenance, and whether it is human-confirmed, deterministic, or model-suggested. No proximity-based relationship is displayed as fact.
- **FIND-05:** Analyst can annotate, confirm, reject, restrict, or mark a link as unresolved. Contradictions remain visible and can be resolved only through an auditable action.
- **FIND-06:** Confidence is expressed using calibrated source/method-specific labels only when calibration has been tested. Otherwise use qualitative labels (e.g. `unassessed`, `low/medium/high analytic confidence`) with definition, and do not use a confidence score as an identity/guilt probability.
- **FIND-07:** Corroboration requires source-independence analysis; copies or syndicated republications of one source count as one lineage.
- **FIND-08:** Support lead queue with status `New`, `Needs Review`, `Approved for Follow-up`, `Rejected`, `Awaiting Legal Process`, `Completed`, `Stale`, or `Closed`. Lead approval records owner, purpose, due/review date, and any required authority. No external action is triggered by lead status.
- **FIND-09:** Staleness rules are source-specific. Re-query is a separate collection event, not a silent update to prior observations.
- **FIND-10:** Deleted/invalidated findings retain a tombstone and reason in the audit trail where required; downstream outputs are flagged for review.

### 7.11 Case workspace, dashboard, and visualization (P0)

The operational dashboard supports case workload and investigative review, not surveillance of people.

- Case overview: case reference, classification, owner, state, scope review date, last update, outstanding approvals, and evidence-processing warnings.
- Investigator work queue: assigned review tasks, unreviewed candidates, conflicts, external-source failures, MCP server warnings, expiring approvals, and report review status.
- Agent/task status: queued/running/waiting for approval/completed/failed/cancelled, step and elapsed time, source/tool, budget used, and safe next actions. Do not expose hidden chain-of-thought; display concise action summaries and evidence references.
- Evidence summary: file count by type/state, integrity status, duplicate indicators, processing failures, total data size, and access restrictions.
- Timeline: event date/time, original timezone, time certainty, source, event description, reported-vs-observed-vs-metadata distinction, and analyst note. Conflicting times are shown rather than normalized away.
- Relationship graph: filter by entity type, evidence, source, time, relationship provenance, confidence status, and review state; provide accessible tabular alternative. Graph layout must not imply a causal link merely through visual proximity.
- Map view: only display location data with source and precision. IP geolocation must be visibly approximate and cannot be zoomed to a residential address based on IP alone. Exact location is restricted to properly authorized, evidence-backed data with a review state.
- Filters and search: case-scoped, permission-aware search with explicit source/date/status filters. Search results must not reveal restricted metadata.
- All visualizations provide textual/keyboard-accessible alternatives and exportable, provenance-preserving tabular data.

### 7.12 Notifications and collaboration (P0)

- Notify assigned users of approvals, processing completion/failure, new conflicts, expiring scope, new evidence, source outages, and report review requests.
- Notifications reveal minimum necessary information and link to the authorized case. Email/SMS notifications must not include sensitive case content unless agency policy explicitly permits it.
- Internal notes are authored, time-stamped, versioned, and categorized as operational, analytic, or legal review. The UI distinguishes notes from evidence and findings.
- Comments and task assignments do not alter evidence or agent outputs. New versions preserve the original.

### 7.13 Reports and evidence packages (P0)

Reports are generated from structured, reviewed records. Aegis produces drafts and reproducible exports, not court-certified conclusions.

- **REPORT-01:** Report templates are agency- and jurisdiction-configurable; all content carries case ID, report version, generation time, author, reviewer/approver, classification, and disclosure marking.
- **REPORT-02:** Distinguish allegation (what a reporter says), observed fact (what an artifact/source shows), analytical assessment (interpretation), and unresolved hypothesis/lead.
- **REPORT-03:** Each material statement links to reviewed evidence/findings, citations, source snapshots or references where lawful, exact locator, and method. The report generator may not fill gaps from model memory.
- **REPORT-04:** Include methodology, scope/authority reference (not restricted legal text unless allowed), tools/models/versions used, collection times, timezones, known limitations, source freshness, confidence definitions, conflict handling, and unavailable sources.
- **REPORT-05:** Include evidence manifest with evidence IDs, original filenames, hashes, sizes, acquisition/ingest information, derivative lineage, and integrity verification status. Redact sensitive fields under policy and preserve a restricted source version when appropriate.
- **REPORT-06:** Human reviewer can accept, edit, remove, or mark each draft sentence; edits retain audit history. A final export requires authorized review/signoff according to policy.
- **REPORT-07:** Export at minimum PDF and machine-readable structured data (e.g., JSON/CSV manifest); accessible HTML or DOCX may be supported. Export preserves citation IDs, version, signatures, and a verification manifest. PDF rendering must not imply cryptographic signing unless actually signed.
- **REPORT-08:** The system records exact report input snapshot, evidence/finding IDs and versions, template version, generator/model version, reviewer decisions, output hash, export destination, and time. A corrected report is a new version with a change summary; previous versions remain retrievable under policy.
- **REPORT-09:** Sharing/export requires purpose, recipient, legal/disclosure basis, sensitivity review, and approval. Apply least-disclosure redaction and record what was withheld/altered and why. Never export hidden system prompts, credentials, full model logs, or irrelevant personal data.
- **REPORT-10:** Generated report has an explicit disclaimer that AI-assisted content requires human review and that an association is not proof of identity or guilt.

### 7.14 Retention, legal hold, and deletion (P0)

- Configure retention by evidence type, case class, jurisdiction, and disposition. Defaults are agency-defined; product defaults must not silently decide legal retention.
- Support legal hold with issuer, scope, start/release time, reason, and audit trail; prevent routine deletion for held objects.
- A deletion workflow requires authority, second approval when configured, and a record of affected evidence, derivatives, indexes, backups, exports where controllable, and residual limitations.
- Deletion from search indexes and caches follows the governing policy. Backup expiration and restoration behavior are documented; a restored deleted item must be re-deleted or re-held according to an auditable reconciliation process.
- Retention expiry never silently removes an object needed by an active case or legal hold; flag and route to an authorized reviewer.

---

## 8. Complete Input Data Taxonomy

This taxonomy is a guidance and extraction catalog—not a checklist a victim must fill out. The intake UI shows a short helper with examples and keeps the complete taxonomy in an optional “What may help?” section. Any category can be unknown or absent. Collect only what is relevant, authorized, and necessary.

### 8.1 Narrative and event context

- Description of events in the reporter’s own words; initial contact; how the story changed; requests, threats, promises, urgency, impersonated organization/person, and recovery attempts.
- Approximate or exact dates/times; time zone; order of events; duration; gaps; conflicting recollections; when evidence was created, received, captured, or submitted.
- Scam type as perceived by reporter (phishing, romance, investment/crypto, job/task, marketplace, advance-fee, tech support, impersonation, loan, extortion, charity, account takeover, recovery scam, or unknown).
- Loss or attempted loss; amount and currency; disputed/returned/frozen amounts if known; non-financial harm; impact and immediate safety concerns.
- Reporter’s prior actions and reports to a bank/payment provider/platform/telecom/provider/agency; reference IDs and dates.

### 8.2 People, organizations, and aliases

- Names, display names, claimed titles, business names, aliases, spellings, transliterations, languages, profile handles, public profile URLs, business registration/merchant names, claimed affiliations, and contact-card details.
- Source of each name (self-claimed, displayed, reported by another person, public registry, provider record, or investigator inference); do not assume a name uniquely identifies an individual.
- Known relationships or roles stated in evidence (victim, sender, intermediary, business, beneficiary, witness, unknown); role is case-specific and reviewable.

### 8.3 Contact and account identifiers

- Phone numbers (including country/area code, extensions, caller ID as displayed, messaging-app number, SIM/eSIM/voicemail details if lawfully supplied).
- Email addresses, sender/from/reply-to/return-path values, aliases, mail headers, mailbox/provider identifiers.
- Usernames, handles, account IDs, channel/group IDs, profile URLs, marketplace seller IDs, merchant IDs, support ticket IDs, app IDs, advertising or referral IDs where present.
- Messaging, calling, conferencing, social, gaming, marketplace, email, and collaboration platform names; conversation IDs, message IDs, invite links, and account creation/reset notices.
- Account recovery notices, sign-in alerts, MFA prompts, password-change events, and device/session notices; redact actual passwords, one-time codes, API keys, cookies, and recovery secrets.

### 8.4 Web, domain, and network indicators

- Full URLs (including scheme/path/query where necessary), shortened links, redirect destinations, QR-code contents, domain/subdomain, hostname, registrar or registration data, DNS records, nameservers, certificates, hosting/provider details, IP addresses, ports/protocols if present, and source time.
- Browser warnings, certificate errors, page titles, copied page text, screenshots, downloaded file names, and observed redirects.
- Proxy/VPN/Tor/CDN/shared hosting indications and network logs where lawfully supplied; distinguish public IP from internal/private IP and preserve collection environment.
- User’s device/browser/app and time of observation where known; do not treat a web page’s own claim of legitimacy as verification.

### 8.5 Financial and transaction data

- Bank/branch/account holder as displayed, account number or masked number, routing/IFSC/SWIFT/IBAN where applicable, UPI ID/VPA, payment-app handle, merchant/payment gateway, card transaction descriptor (mask card), wallet/exchange name, remittance service, gift card code reference, and transaction reference/UTR/RRN/authorization number.
- Amount, currency, fees, date/time, transaction status, recipient/payee, memo/narrative, payment method, invoice/receipt, refund/reversal/dispute, chargeback or complaint reference.
- Cryptocurrency network/chain, asset/token, wallet address, transaction hash, block height/time, contract address, memo/tag/destination tag, exchange/deposit reference, and source of the record.
- Never request full card security codes, passwords, PINs, seed phrases, private keys, one-time codes, or login credentials. If accidentally supplied, redact from routine views and apply incident handling; do not use them.

### 8.6 Messages, calls, and communication content

- Email files, headers, attachments, chat logs, SMS, direct messages, call logs, voicemail, call recordings where lawful, transcripts, conversation exports, contact names as displayed, delivery/read receipts, message edits/deletions indicated by platform export, and communication metadata.
- Exact wording, requested actions, links, payment demands, threats, promises, scripts, impersonation cues, and references to other people/accounts.
- Platform export format/version, timezone, sequence numbers, participant identifiers, and any gaps. Distinguish user-entered contact labels from verified account identifiers.

### 8.7 Media, documents, and physical-world context

- Screenshots, screen recordings, scanned papers, receipts, invoices, contracts, advertisements, profile images, logos, QR codes, photos, videos, audio, and any annotations/crops/edits.
- Document metadata (author field, creation/modification timestamps, software, revision history where available), image metadata (EXIF/XMP/IPTC), hashes, dimensions, geotags if present, OCR text/coordinates, and provenance.
- Physical delivery/meeting context only when the reporter supplies it and it is relevant; precise location is sensitive and needs scoped handling.

### 8.8 Device, browser, and application artifacts

- Device make/model, OS/app version, device identifiers where authorized and necessary, browser history/bookmarks/downloads, cache/local storage, app logs, system event logs, notification history, account sessions, network/DNS records, and acquisition reports.
- Volatile/deleted/forensic artifacts only when acquired under authorized procedures by qualified personnel; record method and limitations. The product does not imply deleted-data recovery from a normal file upload.
- Device/browser fingerprint components only when present in an authorized forensic capture or consented telemetry; do not solicit covert tracking.

### 8.9 Public-source and provider records

- Public filings, business registry entries, official organization contact pages, archived public web pages, public posts, public notices, public domain-registration or DNS data, public blockchain data, licensed reputation data, and legally obtained provider responses.
- For each: exact source/provider, query, retrieval time, URL or response reference, terms/version, capture method, data freshness, confidence/limitations, and whether the record is independently sourced.
- Provider, bank, telecom, platform, hosting, exchange, or law-enforcement records are restricted to authorized submissions/processes; the free text box must not imply the system can obtain them automatically.

### 8.10 Investigative and procedural context

- Case reference, reporting agency, authorized purpose, jurisdiction, scope and limits, applicable time window, legal-process/consent reference status, assigned investigator, evidence custodian, related case references, disclosure/retention requirements, and chain-of-custody transfers.
- Suspect or target labels are investigator-assigned hypotheses and must not be populated automatically from weak matches.

### 8.11 Data quality, uncertainty, and sensitivity annotations

Every input may carry source reliability, completeness, possible manipulation, OCR/transcription quality, time uncertainty, language/translation status, duplication status, personal/special-category sensitivity, encryption status, and restriction reason. These are separate annotations and do not alter original evidence. The system must represent `unknown`, `not provided`, `not examined`, `unavailable`, `not applicable`, and `withheld/restricted` as distinct states.

---

## 9. Core User Journeys and Use Cases

Each scenario below describes candidate product behavior. It is not a guaranteed investigative result. In all cases, external lookups occur only within approved sources and scope, and a human reviews consequential findings.

### 9.1 Phishing email with payment diversion

1. Officer opens an authorized case. Victim provides a narrative and uploads the original email (`.eml`), screenshots, invoice, and payment receipt.
2. Ingestion preserves originals, hashes them, parses headers/MIME in an isolated worker, extracts OCR/text derivatives, and records chain-of-custody events.
3. The extraction queue proposes sender/reply-to addresses, domains, URLs, claimed vendor, invoice amount, beneficiary, bank/UPI details, transaction reference, and event times with exact source spans.
4. Investigator reviews candidates and confirms only supported values. The plan proposes email-header, URL/domain, payment-normalization, and timeline tasks; shows data sent to each source and requests approval for external lookups.
5. Agents retrieve only approved passive/public or licensed source data. The tool reports unavailable sources and distinguishes a shared hosting IP or similar domain from a confirmed actor identity.
6. A graph shows the email, URL, domain, payment instruction, transaction receipt, and related evidence with observed vs. inferred edges. Investigator reviews contradictions (e.g., reply-to differs from claimed organization).
7. The report draft cites the original email and receipt, states what the headers do and do not establish, marks the source attribution as unresolved, and is reviewed by an authorized human.

### 9.2 Romance / investment scam with reused accounts

1. Victim submits narrative, chat export, profile URLs, screenshots, bank/crypto receipts, and approximate timeline. Victim can skip exact identity or sensitive questions.
2. Chat parser extracts requested transfers, claimed identity/organization, communication shifts, handles, wallet addresses, and event references while preserving original text and speaker mapping as supplied.
3. Investigator determines which public or licensed sources are authorized. A social/public-web agent may identify a matching public profile, but the product labels name/photo similarity as a lead and does not equate it with the person controlling the account.
4. A public-chain agent may document transactions and service labels. It cannot claim wallet ownership or access exchange KYC records. If provider records are required, a human records the separate legal-process workflow outside automated source calls.
5. The timeline separates the victim’s recollection from transaction records and platform export timestamps. The graph records candidate relationships and source independence.
6. The report explains uncertainty, preserves the chat and transaction artifacts, and avoids “romance scammer identified” language unless the evidence and authorized review support a carefully qualified statement.

### 9.3 Job/task scam or advance-fee fraud

1. Narrative and evidence include recruitment ad, recruiter contact details, task platform, payment requests, fake balance screenshots, and withdrawal fee messages.
2. The system extracts organization/role claims, public ad URLs, contacts, payment instructions, dates, and platform identifiers. The analyst can flag multiple related reports only if access permits.
3. Public business-registration and domain sources may be queried under policy. Similar organization names or reused templates are presented as possible links, not proof of common control.
4. A report differentiates the platform’s displayed balance from independently verified funds and records any source outage or non-public provider data that was unavailable.

### 9.4 Tech-support impersonation / remote-access scam

1. Supplied evidence includes call log, caller number, invoice, remote-support app name, chat, payment, and screenshots.
2. The product parses supplied call records and screenshots; it does not connect to the victim’s computer or inspect a remote-access session unless evidence is separately acquired under an authorized forensic procedure.
3. Candidate support brand, domain, phone, payment account, and application artifacts are reviewed. Any installed remote-access software is documented as an observation, not evidence of who operated it.
4. The investigator controls any follow-up and separately follows agency-approved victim protection and provider-reporting processes.

### 9.5 Business email compromise / executive impersonation

1. Authorized business user uploads email thread, headers, invoices, payment instructions, and relevant audit/export logs. Sensitive employee data is classified and minimized.
2. Aegis compares sender/reply-to/routing and payment changes, surfaces lookalike domains, and builds an event timeline. It distinguishes an email account compromise hypothesis from spoofing or display-name impersonation.
3. Any exchange with identity provider, mail provider, or bank requires a separately approved connector or lawful process. No account reset or payment cancellation is performed by Aegis.

### 9.6 Marketplace scam

1. Evidence contains listing URL, seller handle, messages, shipment/receipt data, and payment reference.
2. Public listing captures are timestamped and attributed; platform content changes are represented as separate observations. Similar listing images may be compared within the case or permitted reference corpus with no identity inference.
3. Analyst reviews whether a seller ID, payment recipient, and domain are independently linked. Reused photos alone are not enough to attribute a seller.

### 9.7 Account takeover with recovery scam

1. Victim narrative includes login alerts, messages, contact details, and alleged recovery fees. Aegis warns not to provide passwords, codes, or seed phrases.
2. The extraction workflow links supplied notification time, URL, account identifier, and recovery payment. It does not attempt to log in or reset the account.
3. Recovery-scam communications are recorded separately from the original compromise so that one attacker’s claim about another is not treated as fact.

### 9.8 Multilingual, low-quality, or incomplete evidence

1. Evidence arrives as blurry screenshots, mixed-script messages, voice notes, or approximate dates.
2. The system records OCR/transcription quality and language; original media remains primary. Optional translation is linked as a derivative and marked as machine-generated.
3. Ambiguous names/numbers/timestamps remain multiple candidates. Investigator can request a narrow clarification or continue with unknown values.
4. Any source/model not approved for that language/classification is not used; the system explicitly states the limitation.

### 9.9 Source compromise, prompt injection, or malicious MCP result

1. A supplied HTML page or MCP result contains text instructing the AI to ignore policy, disclose data, or call another tool.
2. The system treats the text as data, applies input/output scanning and policy checks, and blocks any instruction-driven scope or permission change.
3. The result is quarantined or labeled; the investigator and security administrator see an alert. The MCP server may be suspended and all dependent findings flagged for review.
4. The audit trail retains the event and affected task IDs without copying secrets into notification channels [R27, R29, R30, R34].

---

## 10. Evidence Integrity, Provenance, and Legal Readiness

Aegis supports defensible process; it does not certify admissibility. Admissibility and lawful use depend on the jurisdiction, acquisition method, evidence type, agency procedures, and court decisions.

### 10.1 Chain-of-custody event model

For each original and relevant derivative, record append-only events:

- unique evidence ID, case ID, source class, and restriction/classification;
- acquisition/submission/ingest event type, actor/service identity, date/time in UTC plus source-local time if known, time source/system clock status, and reason/purpose;
- original name, size, detected format, hash algorithm and value, ingest validation, storage object ID, and encryption/key reference (not the key itself);
- custodian/handling location or logical storage location where applicable; each transfer/access/export/verification event and acknowledgment;
- processing tool, version, configuration, input hash, output hash, time, operator, parameters, and error/warning state for each derivative;
- access, redaction, report inclusion, disclosure/export, retention hold, deletion, and integrity recheck events.

Events are append-only at the application layer and protected against unauthorized modification. Any correction is a new event referencing the original; never rewrite the event history. Use trusted, synchronized clocks and record known clock uncertainty. Hashing demonstrates byte consistency from a recorded point, not authenticity, source identity, truth, or lawful acquisition.

### 10.2 Evidence states and roles

- **Original evidence:** bytes as received/acquired; immutable to normal users.
- **Working copy:** a controlled copy used by a forensic tool; linked to original by hash and copy event.
- **Derivative:** OCR, transcript, extracted header, normalized CSV, screenshot, or analysis output; always linked to input and method.
- **Observation:** directly recorded content/state from a source or artifact.
- **Finding:** analysis based on observations and method.
- **Lead/hypothesis:** proposition requiring further verification.

The UI and report must never collapse these distinctions. AI-generated summaries are derivatives/analysis artifacts, not originals.

### 10.3 Auditability and reproducibility

Record user and service actions, authorization decisions, policy version, scope version, prompt/template/model versions where applicable, tool invocation parameters (with secret redaction), response/result hashes or lawful references, model output, validation decisions, retries, reviewer edits, and report versions. Hidden chain-of-thought is not collected or displayed as an evidentiary rationale; instead capture concise task rationale, cited inputs, tool events, and reviewer decisions. Reproducibility means rerunning a versioned deterministic process when possible; external sources may change and LLM outputs may vary, so the system preserves the actual observed result rather than promising identical future output.

---

## 11. Non-Functional Requirements

### 11.1 Security

- Encrypt network traffic using currently approved TLS; use encryption at rest for evidence, databases, backups, indexes, and secrets. Support agency key-management integration and key rotation.
- Isolate tenants/organizations and cases; enforce authorization at data access and background-job boundaries. Use sandboxing, egress controls, anti-malware scanning, secure parsing, and output encoding.
- Maintain secure software supply chain practices for models, parsers, MCP servers, containers, dependencies, and plugins: provenance, version pins, vulnerability scanning, signed artifacts where available, and rollback.
- Protect against prompt injection, tool poisoning, server mutation, credential leakage, cross-case context bleed, replay/session abuse, privilege escalation, excessive agency, unsafe output rendering, and malicious files [R27, R29, R30, R33, R34].
- Provide tamper-evident audit storage, log retention controls, alerting, backup/restore testing, and incident response. Audit systems must not log passwords, private keys, unmasked payment credentials, or full sensitive evidence unnecessarily.
- Support vulnerability disclosure and security testing; perform threat modeling at product, connector, agent, and MCP-server boundaries.

### 11.2 Privacy and data protection

- Purpose limitation, data minimization, role/case restriction, configurable retention, correction workflows, legal holds, and subject-rights workflows where applicable.
- Identify and classify personal, financial, authentication, health, intimate, child-related, and other specially protected information. Apply masking and field-level access where appropriate.
- External model/provider transfer is off by default for sensitive evidence. For each enabled provider, document data residency, training/retention terms, subcontractors, deletion commitments, and incident notification terms.
- Redact before sharing; do not de-identify by merely removing names if combinations of indirect identifiers can reidentify a person. CTI sharing or interagency export requires a separate codification/redaction review [R10].
- Provide local/on-premises deployment options where justified by threat model and data policy; model choice alone does not guarantee privacy.

### 11.3 Performance and scale (initial reference targets; validate in pilot)

- Case intake interface remains responsive for a narrative of up to 20,000 characters and up to 500 selected files; large-file upload is asynchronous and resumable.
- For a reference deployment of 100 concurrent investigators, ordinary case-list, case-detail, and evidence metadata queries should meet **p95 ≤ 2 seconds** under representative load, excluding external-source calls and large graph rendering.
- Safe document parsing begins within 30 seconds of successful upload for typical supported files (<50 MB); actual completion is shown per artifact and is dependent on size, OCR, encryption, and queue load.
- Queue and agent scale horizontally subject to per-case/source budgets; no requirement to run every agent simultaneously. Bulk inputs are back-pressure controlled to avoid unbounded cost or service degradation.
- Large evidence files are streamed or object-stored; avoid loading entire files into application memory. Graph views support pagination/filtering and must degrade to a table if too large.
- Performance targets are operational targets, not forensic validation thresholds. Public-source latency is outside product SLA unless contractually covered.

### 11.4 Reliability and availability

- Target **99.9% monthly availability** for the core case-management service in a production agency deployment, excluding planned maintenance and explicitly declared external provider failures. A deployment may choose a higher on-premises objective.
- Core case access, evidence retrieval, and audit remain usable when AI or external sources are unavailable. AI failure cannot corrupt evidence or prevent manual case work.
- Background jobs are idempotent where possible, retry-bounded, observable, and recoverable. Duplicate tool calls with side effects are denied unless the integration contract provides safe idempotency and policy approval.
- Data backup objectives are agency-configurable; baseline recommendation: encrypted daily backups, documented restore tests, and a recovery plan with explicit RPO/RTO selected by agency risk owners.
- Fail closed for authorization, integrity, credential, and policy checks; fail visibly (not silently) for optional sources.

### 11.5 Usability and accessibility

- Victim intake targets plain language and low cognitive load. One narrative prompt and one evidence area dominate the screen; optional help is progressive and skippable.
- Investigator interfaces support keyboard-only operation, screen readers, scalable text, sufficient contrast, status not encoded by color alone, focus management, clear destructive-action confirmation, and accessible graph alternatives. Aim for WCAG 2.2 AA subject to applicable agency requirements.
- Distinguish AI activity from system operations. Use concise progress states, not simulated “thinking” or misleading animation. Show the current step, evidence being processed, approvals needed, ETA only when supportable, and stop controls.
- High-stress copy is calm, non-blaming, non-promissory, and avoids implying the victim caused the fraud. Error messages state what happened, what was preserved, and safe next steps.

### 11.6 Interoperability and portability

- Use versioned, documented APIs and stable internal schemas for case, evidence, entity, finding, audit, and report records.
- Tool interfaces are protocol-agnostic behind adapters; MCP servers remain replaceable. Export/import supports open formats where possible, with manifests and lineage.
- Support agency-managed deployment, identity, secrets, storage, key management, monitoring, and approved model endpoints. No cloud-only dependency may be assumed for restricted deployments.

### 11.7 Observability and support

- Metrics include queue depth, processing latency, failures by parser/tool, MCP health, denied calls, policy violations, model usage/cost, and system availability. Metrics must avoid case content and direct identifiers unless expressly approved.
- Operational logs, audit logs, and analytic records are separated by purpose and access. Diagnostic export uses redaction and requires authorization.
- Every background task has a correlation ID and traceable parent task; agents cannot create unbounded orphan work.

---

## 12. Legal, Ethical, and Governance Requirements

1. **Jurisdiction-aware deployment:** the customer’s legal/privacy authority defines permitted purposes, public/commercial data rules, evidence handling, retention, disclosure, and provider process. Aegis provides policy hooks and records; it does not claim compliance by default.
2. **India deployment considerations:** configure agency policy to address applicable Indian criminal procedure, evidence, cybercrime, data protection, and electronic-record requirements as determined by qualified agency counsel. Do not encode a single legal conclusion or US-only assumption into the product. Timezone defaults should support Asia/Kolkata while retaining UTC and source-local times. Indian payment types (including UPI/VPA, UTR/RRN, IFSC) are supported as data formats, not direct bank-access permissions.
3. **Human accountability:** investigators own scope, collection, interpretation, and decisions. Human review must be meaningful: reviewers see source evidence and alternatives, not merely an AI confidence badge. Training should address automation bias, uncertainty, source dependency, and limitations [R11, R32].
4. **Fairness and harm minimization:** do not infer protected traits or criminal propensity; test error rates by language, format, and relevant evidence conditions; monitor false association and overcollection. Provide correction and contest mechanisms for analyst-entered records.
5. **Sensitive subject matter:** child-safety, sexual, intimate, self-harm, and other graphic content requires restricted access and specialized organizational protocols. Automatic classification is not a substitute for trained review. Do not store or expose such material beyond authorized need.
6. **Source legality and terms:** each connector has a documented legal/contractual basis and permitted scope. No connector may circumvent access controls, robots/rate policies where applicable, or platform security measures.
7. **Explainability:** provide evidence-grounded rationale, method, version, source, and uncertainty. Do not reveal hidden model reasoning as though it were an objective forensic method.
8. **Independent validation:** pilot agents and parsers on representative, lawfully held data; publish known limitations internally; validate any external performance claim. Re-test after model, prompt, parser, MCP, or source changes.
9. **Governance board:** production customers should designate accountable product owner, security owner, privacy/legal reviewer, forensic-method owner, and operational administrator. High-risk connectors and agents require recorded approval and periodic review.
10. **Research status caveat:** repository research has varying rigor and scope. Product teams must independently evaluate claims and follow agency standards and current law; references are not legal advice.

---

## 13. Constraints, Assumptions, and Dependencies

### 13.1 Constraints

- The application cannot access local files except through explicit browser folder/file selection or a separately installed, agency-approved acquisition client.
- Public-source availability, terms, rate limits, historical data coverage, and accuracy vary. No public database can be assumed complete or authoritative.
- Many consequential records require legal process, provider cooperation, or an examiner; the system cannot manufacture missing data.
- Large/complex device images, encrypted data, deleted data, proprietary formats, and certain media require specialist tools and training outside initial release.
- The system must function usefully with no MCP servers or external AI model configured.
- Agent outputs and OSINT sources can be adversarial, stale, incomplete, or wrong; audits, source provenance, and human review are not optional overhead.
- Cloud use may be prohibited for some agencies or data classifications; deployment architecture must support approved local/on-premises options.
- No demographic, jurisdiction, language, or scam-type dataset is guaranteed representative; claims and thresholds require local validation.

### 13.2 Assumptions

- A deploying organization has authority to operate the platform and will designate case owners, policies, retention rules, and approval roles.
- Investigators and examiners receive training on chain of custody, OSINT validation, AI limitations, privacy, source terms, and evidence disclosure.
- Evidence is supplied voluntarily by victims or acquired by authorized agency procedures; the product records source and authority status but cannot independently validate legal authority.
- External connectors and models are contracted/configured and approved by the customer; API access, pricing, data residency, and service levels may change.
- A pilot is conducted with synthetic or lawfully de-identified data first, then with approved real cases under oversight.

### 13.3 Dependencies

- Identity provider, MFA, key management, secrets management, encrypted object storage, database/search, immutable or tamper-evident audit store, malware scanning, and monitoring.
- Approved document/image/audio parsers and OCR/transcription components for each enabled format.
- Approved model runtime(s) and per-classification data handling agreement where generative AI is enabled.
- Approved public/licensed OSINT or forensic data sources, with current terms and credentials.
- Agency policy and legal review for collection scope, retention, disclosure, victim-facing intake, MCP connectors, data exports, and local/cloud deployment.

---

## 14. Acceptance Criteria and Product Release Gates

### 14.1 P0 release acceptance

Aegis may enter controlled pilot only when all of the following are true:

- Victim/intake flow contains the narrative box and evidence area as the primary interactions; required internal authorization checks are enforced before investigation tasks start.
- No unauthenticated or unauthorized user can access case content, evidence, derived entities, agent tasks, or restricted metadata through UI, API, search, background job, or export.
- Original evidence is immutable to ordinary users; SHA-256 ingest hashing, post-upload verification, derivative lineage, and audit event history pass tamper and recovery tests.
- Supported parsers have a published test matrix including benign, malformed, encrypted, oversized, archive-bomb, and mismatched-extension samples; unsafe execution is prevented.
- Every extracted candidate is linked to a source locator and marked unreviewed; the system distinguishes raw allegation, observed artifact, inference, and confirmed analyst record.
- A task plan cannot run before policy and scope checks. An investigator can preview, approve, pause, cancel, and inspect it. Agent and tool budgets are enforced server-side.
- All agent outputs are schema-validated and source-linked; repeated/derived source claims are not misrepresented as independent corroboration; conflict and propagation tests pass.
- MCP servers cannot be enabled without required review; tool definition changes are detected; unknown tools are denied; a server response cannot change scope or trigger another capability without policy validation.
- Sensitive data egress is off by default; pre-call policy denies prohibited classification/destination combinations; secrets do not appear in logs or exports.
- Report generation uses reviewed data only, includes citations and evidence manifest, requires review/signoff, versions corrections, and applies approved redaction.
- Retention, legal hold, export, deletion, access, and incident/audit workflows are tested and documented.
- Threat model covers prompt injection, malicious tool response, malicious uploads, cross-case leakage, supply-chain change, MCP transport/configuration faults, error cascade, excessive agency, and service compromise.
- Accessibility review and representative investigator/victim usability test are complete; critical issues are remediated or explicitly accepted by accountable owners.
- Pilot evaluation has documented metrics, labeled test data provenance, false-positive/false-negative analyses, model/source versions, limitations, and human sign-off.

### 14.2 Release blockers

Any of the following blocks production launch: an unresolved critical/high security vulnerability; unbounded agent/tool execution; unlogged external access; inability to verify evidence integrity; unreviewed MCP capability changes; unauthorized case data egress; missing human review for report conclusions; undisclosed data retention/training by a model vendor; a product claim promising identification/capture/fund recovery; or a material legal/privacy issue without an approved mitigation.

### 14.3 Success review after pilot

After a defined pilot period, review time saved, extraction quality by format/language, lead acceptance and rejection reasons, report corrections, source failure rate, MCP and parser incidents, user satisfaction, privacy/security events, and unexpected disparate error patterns. Expand only if the system adds demonstrable value without compromising evidence integrity, lawful scope, or investigator accountability.

---

## 15. Research References in the Repository

The following identifiers are used in this PRD. Paths are relative to the repository root. The listed paper supports a design concern or method area; it is not an endorsement of Aegis or a substitute for product-specific testing. The repository includes peer-reviewed papers, preprints, reports, workshop materials, and strategy documents. Bibliographic details below are taken from the supplied files and filenames; where a file’s publication status or full citation is unclear, the repository filename is retained as the stable reference.

### Multi-agent orchestration

- **[R01]** Yang et al., *LLM-based Multi-Agent Systems: Techniques and Business Perspectives* (2024). `01_Multi_Agent_LLM_Orchestration/02_LLM_Based_Multi_Agent_Systems_Techniques_Business_2024.pdf` — dynamic decomposition, specialization, modularity, privacy considerations.
- **[R02]** Chen et al., *COLLATOR: Compositional Multi-Agent Orchestration with Counterfactual Reinforcement Learning* (2025). `01_Multi_Agent_LLM_Orchestration/03_LEMON_Executable_Multi_Agent_Orchestration_RL_2025.pdf` — joint role/capacity/dependency choices and quality–cost trade-offs.
- **[R03]** Tian et al., *AgentInit: Initializing LLM-based Multi-Agent Systems via Diversity and Expertise Orchestration for Effective and Efficient Collaboration* (EMNLP 2025). `01_Multi_Agent_LLM_Orchestration/04_AgentInit_Decomposition_Role_Assignment_2025.pdf` — standardized role definitions, relevance/diversity, avoiding redundant teams.
- **[R04]** Masters et al., *Orchestrating Human-AI Teams: The Manager Agent as a Unifying Research Challenge* (2024 filename; paper metadata in PDF). `01_Multi_Agent_LLM_Orchestration/05_Orchestrating_Human_AI_Teams_Manager_Agent_2024.pdf` — dynamic task graphs, monitoring, replanning, constraints, human communication.
- **[R05]** Alexandrou & Pramatari, *A Multi-Agent Orchestration Framework for Venture Capital Due Diligence* (2025 filename; PDF dated 2026). `01_Multi_Agent_LLM_Orchestration/06_Multi_Agent_Orchestration_Venture_Capital_Due_Diligence_2025.pdf` — event-driven retrieval, structured extraction, explicit gaps instead of fabricated data.
- **[R06]** Xie et al., *From Spark to Fire: Modeling and Mitigating Error Cascades in LLM-Based Multi-Agent Collaboration* (2025). `01_Multi_Agent_LLM_Orchestration/07_Error_Cascades_LLM_Multi_Agent_Collaboration_2025.pdf` — dependency-aware error governance and traceable message/finding lineage.

### OSINT and cyber threat intelligence

- **[R07]** Hoffman & Fuller, *Rebranding second-generation open-source intelligence: “It’s not your father’s OSINT”* (2025). `02_OSINT_Automation_Reconnaissance/08_AI_and_OSINT_Reshaping_Intelligence_Collection_2025.pdf` — evolving OSINT tradecraft, validation, risk, and limits.
- **[R08]** Sindiramutty, *Autonomous Threat Hunting: A Future Paradigm for AI-Driven Threat Intelligence* (2024). `02_OSINT_Automation_Reconnaissance/09_Autonomous_Threat_Hunting_AI_Threat_Intelligence_2024.pdf` — automation opportunities and scalability, interpretability, and ethics concerns.
- **[R09]** U.S. Department of Defense, *OSINT Strategy 2024–2028*. `02_OSINT_Automation_Reconnaissance/10_DoD_Defense_OSINT_Strategy_2024_2028.pdf` — governance, workforce, integration, and intelligence-value principles; not a substitute for local policy.
- **[R10]** Vasan et al., *A SoK for SoCs: Reading the TI Leaves on AI for Cyber Threat Intelligence Generation and Sharing* (2025). `02_OSINT_Automation_Reconnaissance/11_SoK_AI_Cyber_Threat_Intelligence_CTI_Generation_2025.pdf` — extraction, normalization/enrichment, codification, redaction, distribution, and practitioner concerns.
- **[R11]** Malatji, *A cybersecurity AI agent selection and decision support framework* (2025). `02_OSINT_Automation_Reconnaissance/12_Cybersecurity_AI_Agent_Selection_Decision_Framework_2025.pdf` — matching agent autonomy/architecture to security tasks and governance.

### Digital forensics and NLP

- **[R12]** Yin et al., *Digital Forensics in the Age of Large Language Models* (2025). `03_Digital_Forensics_ML_NLP/13_Digital_Forensics_Age_of_Large_Language_Models_2025.pdf` — evidence categories, relation analysis, LLM capabilities, limitations, transparency and accountability.
- **[R13]** Alves et al., *Detecting Relevant Information in High-Volume Chat Logs: Keyphrase Extraction for Grooming and Drug Dealing Forensic Analysis* (2023). `03_Digital_Forensics_ML_NLP/14_Keyphrase_Extraction_Chat_Logs_Grooming_Drug_Forensics_2023.pdf` — high-volume chat triage and keyphrase extraction, with domain/data limitations.
- **[R14]** Shill, *Detection of Hate and Threat in Digital Forensics: A Case-Driven Multimodal Approach* (2025). `03_Digital_Forensics_ML_NLP/16_Hate_Threat_Detection_Multimodal_Digital_Forensics_2025.pdf` — modality-aware analysis, OCR, explicit evidence availability, traceability.
- **[R15]** Dunsin et al., *A Comprehensive Analysis of the Role of Artificial Intelligence and Machine Learning in Modern Digital Forensics and Incident Response* (2023). `03_Digital_Forensics_ML_NLP/17_AI_ML_Modern_Digital_Forensics_Incident_Response_2023.pdf` — collection, recovery, timelines, big data, chain of custody, integrity and privacy concerns.
- **[R16]** Imran et al., *Natural Language Processing (NLP) for Digital Forensics* (DFRWS workshop material, 2023). `03_Digital_Forensics_ML_NLP/18_NLP_for_Digital_Forensics_DFRWS_Workshop_2023.pdf` — textual evidence, forensic NLP, email/chat/social artifacts, authorship-analysis context.
- **[R17]** Jeong et al., *LangurTrace: Forensic analysis of local LLM applications* (DFRWS APAC 2025). `03_Digital_Forensics_ML_NLP/19_LangurTrace_Forensic_Analysis_Local_LLM_Applications_2025.pdf` — structured application environments and reproducible artifact collection.
- **[R18]** Dhiman & Singh, *Cybercrime and Computer Forensics in Epoch of Artificial Intelligence in India* (2024). `03_Digital_Forensics_ML_NLP/20_Cybercrime_Computer_Forensics_AI_India_2024.pdf` — India-oriented legal/ethical, privacy, human-rights, and governance concerns.

### Tool use and function calling

- **[R19]** Xu et al., *The Evolution of Tool Use in LLM Agents: From Single-Tool Call to Multi-Tool Orchestration* (2025 filename; PDF publication metadata later). `04_LLM_Tool_Use_Function_Calling/21_Evolution_Tool_Use_LLM_Agents_Single_to_Multi_Tool_2025.pdf` — long-horizon orchestration, dependencies, scheduling, safety, cost, verifiability.
- **[R20]** Ding & Stevens, *Unified Tool Integration for LLMs: A Protocol-Agnostic Approach to Function Calling* (2025). `04_LLM_Tool_Use_Function_Calling/22_Unified_Tool_Integration_Protocol_Agnostic_Function_Calling_2025.pdf` — adapter-based integration and schema generation.
- **[R21]** Liao et al., *HyFunc: Accelerating LLM-based Function Calls for Agentic AI through Hybrid-Model Cascade and Dynamic Templating* (2025 filename; PDF metadata later). `04_LLM_Tool_Use_Function_Calling/23_HyFunc_Hybrid_Model_Cascade_Dynamic_Templating_2025.pdf` — efficient function retrieval and separating intent selection from structured execution.
- **[R22]** Patel et al., *Dynamic Tool Dependency Retrieval for Lightweight Function Calling* (2024). `04_LLM_Tool_Use_Function_Calling/24_Dynamic_Tool_Dependency_Retrieval_Lightweight_Function_Calling_2024.pdf` — retrieving tools based on evolving plan and dependencies.
- **[R23]** *ToolRegistry: Protocol-Agnostic Tool Management for LLMs* (2025 preprint). `04_LLM_Tool_Use_Function_Calling/25_ToolRegistry_Protocol_Agnostic_Tool_Management_LLMs_2025.pdf` — unified tool registry, MCP/OpenAPI/native adapters, lifecycle and administration.
- **[R24]** Yu et al., *Multi-Mission Tool Bench: Assessing the Robustness of LLM based Agents through Related and Dynamic Missions* (2025). `04_LLM_Tool_Use_Function_Calling/26_Multi_Mission_Tool_Bench_LLM_Agent_Robustness_2025.pdf` — related-task transitions and workflow robustness evaluation.
- **[R25]** Yu et al., *Getting the Parameters Right: A Difficulty-Graded Benchmark and Probe-Guided Training for LLM Tool Calls* (title from the PDF front matter). `04_LLM_Tool_Use_Function_Calling/27_Berkeley_Function_Calling_Leaderboard_BFCL_ICML_2025.pdf` — schema-validity is not value correctness; parameter and dependency evaluation. **Repository metadata note:** this PDF’s contents/title do not match the “Berkeley Function Calling Leaderboard” filename, so the PRD identifies it by the title printed in the document and preserves the repository path for reproducibility.

### MCP, adversarial security, and modular applications

- **[R26]** Sarkar & Sarkar, *Survey of LLM Agent Communication with MCP: A Software Design Pattern Centric Review* (2025). `05_Model_Context_Protocol_MCP/28_Survey_LLM_Agent_Communication_MCP_2025.pdf` — mediator/broker/pub-sub patterns, MCP communication and auditability.
- **[R27]** Hou et al., *Model Context Protocol (MCP): Landscape, Security Threats, and Future Research Directions* (2025). `05_Model_Context_Protocol_MCP/29_MCP_Landscape_Security_Threats_Future_Directions_2025.pdf` — MCP lifecycle, security/privacy threat model and safeguards.
- **[R28]** Lumer et al., *ScaleMCP: Dynamic and Auto-Synchronizing Model Context Protocol Tools for LLM Agents* (2025). `05_Model_Context_Protocol_MCP/30_ScaleMCP_Dynamic_Auto_Synchronizing_MCP_Tools_2025.pdf` — dynamic discovery/index synchronization and tool version consistency.
- **[R29]** Acharya & Gupta, *A Formal Security Framework for MCP-Based AI Agents: Threat Taxonomy, Verification Models, and Defense Mechanisms* (2025). `05_Model_Context_Protocol_MCP/31_Formal_Security_Framework_MCP_Based_AI_Agents_2025.pdf` — capability controls, tool integrity, data confinement, context isolation, runtime policy.
- **[R30]** Taraghi et al., *Real Faults in Model Context Protocol (MCP) Software: a Comprehensive Taxonomy* (2025). `05_Model_Context_Protocol_MCP/32_Real_Faults_Model_Context_Protocol_Software_Taxonomy_2025.pdf` — host/server/tool boundary faults, response validation, config diagnostics and reproducible failures.
- **[R31]** Wattamwar & Akwafuo, *ARIES: A Scalable Multi-Agent Orchestration Framework for Real-Time Epidemiological Surveillance and Outbreak Monitoring* (2025). `05_Model_Context_Protocol_MCP/34_ARIES_Multi_Agent_MCP_Epidemiological_Surveillance_2025.pdf` — hierarchical agent retrieval, cross-source synthesis, visible source-backed output; domain-specific example only.
- **[R32]** Pitkäranta & Pitkäranta, *HADA: Human-AI Agent Decision Alignment Architecture* (2025). `05_Model_Context_Protocol_MCP/35_HADA_Human_AI_Decision_Alignment_Architecture_2025.pdf` — policy/values alignment, stakeholder contestability, layered strategy/coordination/execution.
- **[R33]** KELA, *2025 AI Threat Report: How cybercriminals are weaponizing AI technology*. `06_Cyber_Fraud_Adversarial_AI/36_KELA_2025_AI_Threat_Report_Cybercriminals_Weaponizing_AI.pdf` — current adversarial AI, phishing, impersonation, jailbreak and defensive security context; industry threat report, not an academic trial.
- **[R34]** Srivastav & Zhang, *Safe in Isolation, Dangerous Together: Agent-Driven Multi-Turn Decomposition Jailbreaks on LLMs* (REALM 2025). `06_Cyber_Fraud_Adversarial_AI/37_Multi_Agent_Jailbreak_Task_Decomposition_LLMs_ACL_2025.pdf` — decomposition can bypass safety filters; need holistic policy over full task lineage.
- **[R35]** Jiao et al., *SafeMate: A Modular RAG-Based Agent for Context-Aware Emergency Guidance* (2025). `07_Autonomous_Investigation_MCP_Applications/38_SafeMate_Modular_RAG_Agent_MCP_Emergency_Guidance_2025.pdf` — accessible non-expert interaction, trusted retrieval, grounding and secondary verification.
- **[R36]** Heimig-Elschner et al., *A Modular Reference Architecture for MCP-Servers Enabling Agentic BIM Interaction* (2025). `07_Autonomous_Investigation_MCP_Applications/39_Modular_MCP_Server_Architecture_BIM_Reference_2025.pdf` — isolated server/adapters, modularity, reproducibility, and API decoupling.

---

## 16. Glossary

- **Artifact / evidence item:** A digital object submitted or acquired for possible evidentiary examination, including its original, derivatives, and provenance records.
- **Candidate entity:** A possible identifier or person/organization-related value extracted from content but not yet verified by an investigator.
- **Finding:** A structured analytical observation or conclusion supported by identified sources and a stated method.
- **Lead:** A reviewable investigative possibility requiring additional verification; not a fact or conclusion.
- **Master Agent:** Bounded coordinator that creates and monitors a case task graph under human-approved scope and policy.
- **MCP server:** A service exposing tools/resources/prompts through Model Context Protocol; connection alone does not confer trust or authority.
- **OSINT:** Intelligence derived from publicly or commercially available information that has been processed and evaluated for a defined requirement; not synonymous with any unrestricted data collection [R07, R09].
- **Original:** The exact bytes received or acquired, retained without ordinary-user modification.
- **Provenance:** The source, acquisition/collection method, timestamps, custody, transformations, and lineage of an item or claim.
- **Scope:** Documented case purpose, authority status, identifiers, source classes, time/territorial limits, data restrictions, and approvals governing work.
- **Source independence:** Whether two records derive from genuinely distinct collection origins rather than copies or republications of one origin.
- **Tool registry:** Reviewed, versioned catalog and policy boundary for native tools, APIs, and MCP capabilities.

---

## 17. Change Control

This PRD is the product requirements baseline. A material change to collection scope, source types, biometric capability, agent autonomy, data sharing, retention, external model handling, MCP permissions, or user population requires review by the product owner, security owner, privacy/legal reviewer, and forensic-method owner. Changes to acceptance criteria or performance claims must cite supporting evaluation evidence. The subsequent design, architecture, and implementation task documents must trace requirements back to this PRD and maintain a record of any approved deviations.
