# UI/UX Design Document — Aegis OSINT & Digital Forensics Platform

**Document status:** Design baseline for implementation  
**Version:** 1.0  
**Date:** 25 October 2026  
**Audience:** Product design, frontend engineering, accessibility, security, forensics, and agency stakeholders  
**Product name:** Aegis (working name)  
**Requirements parent:** [`prd.md`](prd.md)

> **Design boundary:** This is a professional investigative decision-support interface, not a suspect-tracking or automated enforcement interface. It must communicate evidence, uncertainty, source provenance, approvals, and limits clearly. No screen should imply that AI has identified a criminal or that a correlation proves identity. The design follows PRD principles: simple and humane intake, evidence-first workflows, bounded agents, governed MCP, human review, and case-scoped privacy.

---

## 1. Purpose and Design Outcomes

This document defines the visual system, information architecture, screens, interaction patterns, accessibility rules, and textual wireframes needed to design Aegis consistently. It is intended to be specific enough that product and frontend teams can implement a coherent first release without inventing behavior for high-risk states.

### 1.1 Primary design outcomes

1. **Support sound judgment.** Surface provenance, status, confidence limits, contradictions, and missing data at the point of decision—not in a buried tooltip.
2. **Reduce avoidable workload.** Let investigators find evidence, review candidate extractions, understand agent work, and draft reports without manually reconciling scattered systems.
3. **Keep the victim experience humane.** Lead with a single narrative box and a single evidence area; use plain language and optional guidance. Never require technical categorization to submit a report.
4. **Prevent mistaken certainty.** Differentiate observed, reported, derived, inferred, and human-confirmed information visually and semantically. Never use a “suspect score” or a confident red/green AI verdict.
5. **Make governance visible.** Show when work requires approval, what data leaves the organization, which MCP server/tool is involved, and why an action is blocked.
6. **Remain calm under stress.** Favor restrained visual hierarchy, predictable controls, clear status language, and recoverable errors over novelty, gamification, or excessive animation.
7. **Meet accessibility requirements.** Use color-independent state communication, keyboard operation, readable typography, screen-reader semantics, and equivalent table views for visualizations.

### 1.2 Research-informed design basis

The repository research informs these design choices:

- Victim and non-expert workflows benefit from context-sensitive, plain-language interaction rather than requiring precise technical queries [PRD §5, §7.3; R35].
- Multi-agent task graphs, task status, replanning, and stakeholder communication should be observable [R02–R05].
- Agent collaboration can amplify errors; source lineage, conflicts, and review states must remain visible [R06].
- OSINT collection and source validation require context and limitations, not just a list of hits [R07–R10].
- Forensic evidence can be text, image, audio, logs, or metadata; a UI must show modality, method, quality, and provenance [R12–R18].
- MCP capability discovery and live changes create trust and boundary risks, so administration, version, identity, and policy must be visible [R26–R30, R36].
- Tool-call failure and parameter uncertainty should be clear instead of silently hidden, and the interface should support multi-step workflows [R19–R25].
- Decomposition attacks and untrusted tool content require the interface to distinguish evidence content from system instruction and make blocked behavior inspectable [R27, R29, R34].

Reference IDs are defined in [`prd.md` §15](prd.md#15-research-references-in-the-repository). The papers are design inputs, not legal authority or proof of Aegis performance.

---

## 2. Design Philosophy

### 2.1 Visual character

Aegis should feel like a serious, carefully engineered forensic workspace: quiet, precise, authoritative, and modern. The interface should evoke a well-organized evidence room and an analyst’s workbench—not a consumer finance dashboard, hacker movie, or generic cyber-security console.

**Use:**
- Strong typographic hierarchy, controlled density, consistent alignment, and clear labels.
- Compact data tables with comfortable row height, sticky headers, thoughtful column sizing, and persistent provenance cues.
- Restrained brand color used for navigation, selected states, and high-value actions rather than decoration.
- Flat surfaces with subtle borders and low-contrast elevation; use depth only to distinguish overlays and panels.
- Data visualization that encodes evidence status and relationship type, not threat drama.
- Clear text labels alongside icons, status colors, graphs, and badges.

**Avoid:**
- Generic blue/white technology branding, bright neon, glowing gradients, animated network backgrounds, hacker iconography, “radar” motifs, or gratuitous encryption/hex decorations.
- Alarm-red screens for ordinary states, fake live activity, animated “AI thinking,” or invented ETAs.
- Large hero metrics that imply investigative effectiveness or guilt probabilities.
- A graph that positions an individual at the center of criminal activity without a directly inspectable basis.
- Hidden critical actions behind icon-only menus, color-only statuses, or hover-only disclosure.

### 2.2 Tone and language

Language is neutral, specific, and non-accusatory. Prefer **“possible match,” “candidate,” “observed in supplied file,” “source unavailable,” “not yet reviewed,”** and **“requires verification.”** Avoid “criminal,” “scammer identified,” “trace complete,” “AI confirmed,” “high-risk person,” and “caught.”

Victim-facing text is warm but not overly familiar, never blaming, and does not promise recovery or identification. Investigator-facing text is concise and operational. Error content explains what happened, whether evidence was preserved, what was not done, and what action is safe to take.

### 2.3 Visual hierarchy and cognitive load

Every screen should answer three questions at a glance:

1. **Where am I?** Persistent page title, case reference, and breadcrumb or section navigation.
2. **What needs attention?** One prioritized callout or task state—not multiple competing alerts.
3. **What can I safely do next?** A small set of clear primary/secondary actions with approval and scope context.

Use progressive disclosure for technical details: summary first, then source details, raw values, metadata, tool logs, and forensic processing information. Do not make essential uncertainty or source identity progressive; those remain visible at summary level.

---

## 3. Color System

### 3.1 Brand palette: three chromatic anchors

The brand palette is a distinctive **ink-plum + oxidized jade + aged brass** combination. It avoids standard blue/white and common saturated cyber palettes while remaining restrained and professional.

| Token | Color | Hex | Use |
|---|---|---:|---|
| **Primary — Ink Plum** | Deep aubergine charcoal | `#211B2A` | Primary navigation, top-level shell, headings on light surfaces, selected dark surfaces |
| **Secondary — Oxidized Jade** | Muted verdigris | `#2E8076` | Primary actions, active navigation indicators, links, confirmed neutral system states |
| **Accent — Aged Brass** | Warm antique brass | `#C7913B` | Focused highlights, review-needed emphasis, selected graph edge, important date/attention marker; not a default warning color |

The three anchor colors provide identity, not the entire interface. Neutral surfaces and semantic status colors are necessary for legibility, contrast, and meaning. They must remain visually subordinate to the three anchors.

### 3.2 Neutral surfaces

| Token | Hex | Purpose |
|---|---:|---|
| `surface-canvas` | `#F3F1EC` | Warm pale stone page background; less clinical than pure white |
| `surface-card` | `#FFFDF9` | Primary cards, modal interiors, report preview |
| `surface-muted` | `#E9E6DF` | Secondary panel, table header, disabled background |
| `surface-subtle` | `#F8F6F1` | Nested details and alternate table rows |
| `text-primary` | `#24212A` | Main body text on light surfaces |
| `text-secondary` | `#5F5A64` | Supporting text, metadata labels |
| `text-muted` | `#77727B` | Secondary metadata; never use for essential text below contrast requirements |
| `border-default` | `#D6D1C8` | Dividers and low-emphasis outlines |
| `border-strong` | `#9B949D` | Input borders, table boundaries, focus-adjacent structure |
| `surface-dark` | `#2A2432` | Deep plum-adjacent panel background |
| `text-on-dark` | `#F7F4EF` | Body text on dark shell |
| `text-on-jade` | `#FFFFFF` | Button/label text on jade where verified contrast permits |

### 3.3 Dark shell and dark mode

The investigator shell may use a dark primary sidebar/top rail with Ink Plum (`#211B2A`) and warm off-white text. Most reading surfaces remain light by default to support long forensic review sessions and print fidelity. Optional dark mode retains the same three chromatic anchors using contrast-safe variants; dark mode must not remap evidence statuses or change meaning. Do not offer a separate “matrix” or high-glow theme.

### 3.4 Semantic status colors

Status is always communicated with icon/label/shape plus color; status colors are not additional brand anchors. Proposed tokens:

| Semantic status | Token | Hex | Label example |
|---|---|---:|---|
| Success / verified system check | Deep evergreen | `#28664F` | “Integrity verified” |
| Warning / review required | Dark ochre | `#8A5A12` | “Needs review” |
| Error / blocked / integrity issue | Deep garnet | `#9B3F43` | “Integrity check failed” |
| Informational / neutral | Slate | `#52616B` | “Awaiting source” |
| Unknown / not assessed | Neutral gray | `#6D6871` | “Not yet reviewed” |

Semantic color is used sparingly: small icon, border, or badge background rather than full-card fill. A status must remain understandable in grayscale and in common color-vision deficiencies.

### 3.5 Contrast and usage constraints

- All standard text/background combinations meet WCAG 2.2 AA contrast; large text and UI boundaries also meet applicable contrast criteria.
- Brass is not used as small text on light stone; use dark text on a pale brass tint or use brass as a small icon/border.
- Jade is tested for text contrast on both light and dark contexts. Use a darker jade token for small text on light surfaces when needed.
- Never use red alone to mean “suspect” or guilt. Red is reserved for error, unsafe condition, blocked operation, or integrity alert.
- Focus rings use a two-layer high-contrast treatment (white/near-white inner keyline plus jade outer ring on dark backgrounds; dark/jade ring on light surfaces) and are visible against adjacent controls.

---

## 4. Typography and Content Density

### 4.1 Font families

- **UI and prose:** `Inter` or an agency-approved equivalent with strong Unicode and Devanagari coverage. Use system fallbacks: `Inter, "Noto Sans", "Noto Sans Devanagari", "Segoe UI", sans-serif`.
- **Monospace / identifiers:** `IBM Plex Mono` or `JetBrains Mono`, with fallback `ui-monospace, SFMono-Regular, Consolas, monospace`. Use for hashes, IPs, URLs, transaction IDs, IDs, raw technical fields, and source keys. Wrap or horizontally scroll long values without clipping.
- **Report body:** same readable UI family unless an agency report template specifies otherwise. Avoid decorative serif typography in operational screens.

Font files are self-hosted or served from an agency-approved source; no font request should disclose user activity to an unapproved third party. Include system fallback if custom fonts are unavailable.

### 4.2 Type scale

Use a 4 px base spacing grid and a compact but comfortable type scale:

| Style | Size / line height | Weight | Typical use |
|---|---|---|---|
| Display / page hero | 32 / 40 px | 600 | Rare; dashboard welcome or auth title only |
| H1 | 28 / 36 px | 600 | Main page title |
| H2 | 22 / 30 px | 600 | Major panel/section title |
| H3 | 18 / 26 px | 600 | Card group or evidence section |
| Body large | 16 / 24 px | 400–500 | Narrative, explanatory copy |
| Body | 14 / 22 px | 400 | Standard controls and tables |
| Label | 13 / 18 px | 600 | Field labels, table headers, status labels |
| Caption | 12 / 18 px | 400–500 | Metadata, timestamp context; not essential instructions alone |
| Technical | 13 / 20 px | 450 | IDs, hashes, addresses, source values |

Text zoom to 200% must preserve functionality without clipped controls. Long-form evidence text uses a readable line length (approximately 65–90 characters) and selectable content.

### 4.3 Number and time formatting

- Store and display an explicit UTC timestamp plus original/source timezone when known. In investigator views, the local display timezone is visible (e.g. `25 Oct 2026, 14:10 IST · source UTC+05:30`).
- Distinguish event time, capture time, upload time, source retrieval time, and processing time with labels.
- Use locale-aware dates/numbers and preserve the original string for financial amounts and timestamps. Do not infer an omitted timezone.
- Currency amounts show currency code where ambiguity is possible. Mask account/card identifiers by default.

---

## 5. Information Architecture and Navigation

### 5.1 Investigator application shell

Desktop app shell:

- **Left navigation rail/sidebar:** persistent, collapsible; Overview, Cases, Evidence (permission-aware), Agents & Tasks, MCP & Integrations (admin-only), Reports, Audit (authorized roles), Settings (admin/role dependent).
- **Top utility bar:** organization/unit context, global case-scoped search entry, notifications, help, current user/profile, and environment label (`Production`, `Training`, `Test`). Environment label is always visible and visually distinct to prevent mistaken production/test actions.
- **Main content region:** breadcrumb, title, case context, page actions, content canvas.
- **Optional right inspector panel:** selected evidence/entity/task detail; can be closed and is not necessary to access core information.

Sidebar bottom shows the signed-in identity and current role; no presence of one user implies a shared session. A case context banner remains visible on all case pages with reference, classification, status, and scope expiry/review warning.

### 5.2 Navigation rules

- Case-level pages use a tab row: **Summary · Evidence · Entities & Findings · Timeline · Activity · Reports**. The selected tab is reflected in URL and browser history.
- “Start analysis” is available only when case status and scope permit it. It is not the same control as “Create case.”
- MCP management is separated from case investigation. The fact that a server is enabled globally does not automatically select it for a case.
- Cross-case search is disabled by default and requires role/policy entitlement. A search always states its scope: “This case” or “Authorized cases.”
- The browser back button returns to the prior filtered view without losing unsaved content where safely possible.

### 5.3 Global interaction conventions

- Primary action is a single jade button per region; secondary actions are outlined/quiet; destructive or high-impact actions are separated and require confirmation.
- Buttons state the action, e.g. “Review investigation plan,” “Pause run,” “Export reviewed report.” Avoid vague “Go” or “Proceed.”
- Menus are keyboard-operable; icon buttons include accessible names and visible tooltips on focus/hover.
- Modals are reserved for decisions requiring focused attention, not routine detail. Side panels support inspection without losing place.
- Unsaved changes are indicated with a clear banner and explicit Save/Discard controls. Never silently discard notes or edits.

---

## 6. Component System

### 6.1 Buttons

- **Primary:** solid jade; one principal action per visual group.
- **Secondary:** transparent with strong border and primary text.
- **Tertiary:** text button, for reversible low-risk actions.
- **Destructive:** garnet outline or muted fill, with explicit object/action wording (`Disable server`, `Delete draft`); never use as a default red primary button.
- **Loading:** preserves button width, announces “Working,” prevents duplicate submission, and exposes cancellation only if safe.
- **Disabled:** includes a nearby explanation when disabled because of permission, scope, approval, or missing data.

### 6.2 Inputs and form controls

- Persistent visible labels; placeholder is an example, not the label.
- Errors appear inline after the field, announce to screen readers, and explain correction.
- Sensitive fields use masking, reveal control with accessible label, and audit on reveal where policy requires.
- Never prepopulate a sensitive field from an LLM extraction without showing it as a candidate and requiring acceptance.
- Search inputs show active scope and filters. A clear action empties the field without erasing saved case data.

### 6.3 Status chips and evidence-state labels

A chip has icon, concise text, semantic color, and optional tooltip with definition. Examples: `Unreviewed`, `Human-confirmed`, `Source-derived`, `Inference`, `Conflict`, `Stale`, `Restricted`, `Integrity verified`. “Verified” always names what was verified (e.g. file hash at ingest), never an overall case truth.

### 6.4 Tables

- Use sticky header, sortable columns, keyboard-focusable rows, row-selection checkbox only when bulk actions are available, visible sort direction, and pagination/virtualization for large results.
- Every data table has a clear empty state, loading skeleton, partial-data notice, error state, and accessible table semantics.
- Technical values use monospace, can be copied with a named button, and remain visible via wrap or deliberate horizontal scroll. Copy operations do not place values in unapproved telemetry.
- Table row background never alone indicates evidence state. Use a labeled chip/icon.

### 6.5 Provenance / source card

A reusable source card appears in entity, finding, timeline, and report contexts. It includes:

- Source type and name/provider;
- observed/retrieved/captured time and timezone context;
- source reliability/freshness state, not a misleading universal score;
- evidence ID or source record link;
- exact locator (page, message, header field, row, timestamp, OCR region);
- method and parser/model/tool version;
- source independence/corroboration status;
- limitations and permitted-use/retention marker where relevant.

### 6.6 Approval summary card

Displays action requested, why it is needed, case scope version, data classes involved, destination/source, likely external egress, approving role, expiry, and what happens if denied. The user can inspect exact parameters and choose `Approve once`, `Reject`, or `Edit plan` where allowed. Never default a checkbox to approval.

### 6.7 Evidence provenance strip

A compact always-available strip on evidence details: `Original / Derivative`, hash verification status, parent/child lineage, custody event count, last verification, and restriction. Expands to the full custody table.

### 6.8 Notifications

- Toasts are for low-risk transient confirmations and non-blocking completion only; no sensitive content in toast text.
- High-risk issues use persistent alert banners in the relevant case/server context.
- Notification center includes severity, case reference (if permitted), event time, action link, read state, and role-specific visibility.
- No push notification content includes evidence text, personal identifiers, or report conclusions.

### 6.9 Agent run header and stage rail

A reusable **AgentRunHeader** anchors every prompt analysis and agent run. It shows the run ID, case reference, current truthful stage label, status, elapsed time, last event time, and safe controls (`Pause`, `Cancel`, or `Review plan`, depending on state). It never displays a person-level risk score, a fake completion percentage, or a stream of hidden model reasoning.

A compact **AgentStageRail** visualizes the broad workflow:

`Received → Validate → Analyze → Plan → Approval → Execute → Synthesize → Review-ready`

- The rail is event-driven by backend state, not timed animation. Completed stages show a check icon and timestamp; the current stage has an active marker and concise label; future stages are muted; blocked stages show a warning icon and reason; skipped stages are explicitly marked `Not run` with reason.
- Because agent workflows branch and run in parallel, the rail is a high-level summary only. A task list/graph remains the source for individual task state and dependencies; the rail must not imply that parallel tasks ran sequentially.
- `Analyze` is the user-facing equivalent of “thinking”: it indicates actual request/evidence analysis, but never exposes hidden chain-of-thought. Supporting text describes observable work (e.g., “Checking the narrative and selected evidence for candidate details”), not speculative internal model activity.
- `Approval` is a real gate, not an animated countdown. Until required approval is granted, execution and external tool calls remain visibly pending.

### 6.10 Agent task row and tool-call card

**AgentTaskRow** is a compact, accessible row/card with task name, approved agent template/version, status icon and text, dependency count, data class, source/tool badges, start/last-update time, and an expand control. The expanded view provides concise purpose, input references, output links, data-flow summary, warnings, and safe actions. Do not use a blinking dot as the only indication of activity.

**ToolCallCard** appears only when an actual tool call is scheduled, running, or completed. It identifies the tool/server and approved version, purpose, destination/source, exact data categories sent, request status, response validation state, and result/evidence link. Sensitive arguments are redacted in the ordinary view. An external call is visually distinct from local parsing. A policy-denied call reads `Blocked — no request sent`; it must never animate as though a call occurred.

### 6.11 Agent activity motion and feedback

Motion is purposeful and restrained. It confirms an input was accepted, shows an actual state transition, guides attention to a new review item, or indicates a live task. It must not simulate thought, imply certainty, or conceal system latency.

**Motion tokens (default):**

| Token | Duration | Easing | Use |
|---|---:|---|---|
| `motion-control` | 120 ms | `cubic-bezier(0.2, 0, 0, 1)` | Hover, pressed, focus-adjacent, button feedback |
| `motion-state` | 180 ms | `cubic-bezier(0.2, 0, 0, 1)` | Status badge/step state change, check icon reveal |
| `motion-panel` | 240 ms | `cubic-bezier(0.2, 0, 0, 1)` | Expand/collapse, inspector drawer, details disclosure |
| `motion-emphasis` | 320 ms max | `cubic-bezier(0.2, 0, 0, 1)` | One-time arrival highlight for a completed task or review item |
| `motion-live-cycle` | 1,400 ms | linear, low contrast | Indeterminate active-task shimmer/pulse; only while work is actually active |

- Use opacity and small translation/scale changes (typically ≤4 px or ≤1.02 scale); no large movement, spring bounce, parallax, flashing, or full-screen takeover.
- Stage transitions use a brief fade plus a thin progress/step marker moving to the new stage only after a backend event. The marker must not race ahead of actual work.
- Completed work may animate a checkmark once (120–180 ms) and then remain still. A new finding row may receive one low-contrast 320 ms outline/fade; do not repeatedly pulse findings or draw attention to a person/entity.
- Indeterminate spinners or subtle shimmer are used only to show that a task is active when progress cannot be measured. They have an accessible text label and a stable container to avoid layout shifts.
- Determinate progress is shown only from measured work units (e.g., bytes transferred, files processed, tasks completed). Always label the unit and numerator/denominator when available; never infer a percent from elapsed time or model tokens.
- No sounds, haptics, typewriter simulation, animated chat bubbles, fake typing dots, or “AI brain”/orb animation are used in the default law-enforcement workspace. A restrained activity marker may pulse; it is decorative only when accompanied by a textual status and is disabled under reduced-motion preference.
- Animation cannot delay an approval, cancellation, emergency access, or navigation control. Controls remain operable throughout transitions.
- The `prefers-reduced-motion: reduce` setting removes nonessential transforms, shimmer, pulses, and sliding; state changes become immediate with a subtle static icon/border update. Provide an in-app motion setting only if it respects the operating-system preference as the minimum.
- When offline or event streaming is stale, stop the live pulse and show `Updates paused` with the timestamp of the last confirmed event. Never leave a spinner running indefinitely without a stale/error state.

---

## 7. Screen Specifications

## 7.1 Login and authentication

### Purpose
Provide a calm, clearly branded entrance that identifies the organization and environment, supports approved authentication, and does not reveal account/case data before authorization.

### Layout
- Desktop: centered login panel on warm stone background; small Ink Plum brand mark and product name; organization name; environment label (`Training`/`Production`); SSO button or agency login; MFA/recovery flow; support/legal/privacy links.
- No decorative background image or security theater.
- On SSO handoff, show the identity provider name and return path status.
- If local login is configured, keep username/password fields visible with show/hide toggle and password manager support; MFA follows.

### Wireframe
```text
┌─────────────────────────────────────────────────────────────┐
│ AEGIS                                              TRAINING  │
│                                                             │
│             Secure investigative workspace                  │
│       Sign in using your agency credentials                 │
│                                                             │
│       [ Continue with agency sign-in ]                      │
│       ───────────── or ─────────────                        │
│       [ Work email / user ID                         ]       │
│       [ Password                                  ◉ ]       │
│       [ Sign in ]                                            │
│       MFA / account recovery                                 │
│                                                             │
│ Privacy notice · Accessibility · Support                    │
└─────────────────────────────────────────────────────────────┘
```

### States
- Invalid credentials: generic message that does not confirm account existence.
- MFA challenge: clear step count, accessible code entry, paste allowed if policy permits, resend timer, help route.
- Session expired: preserve safe draft state, ask to reauthenticate, do not resubmit a sensitive action automatically.
- Maintenance: state service impact and safe contact path; no sensitive operational details.

---

## 7.2 Main dashboard / investigator overview

### Purpose
Show workload and required decisions—not a crime map or guilt analytics. The dashboard prioritizes unreviewed or blocked work and makes case state visible.

### Layout (desktop)
- Header: `Overview`, short period filter if needed, `New case` action for authorized roles.
- At top: 3–4 modest summary cards (My active cases, Needs my review, Evidence processing, Awaiting approval). Each number is a navigable count with a defined scope and timestamp, not a success metric.
- Main left column: `My work queue` with high-priority approval, evidence exceptions, conflicts, unreviewed entities, report review, and expiring scopes.
- Main right column: `Recent cases` table with reference, short title, owner, state, last activity, classification; no victim names on a shared dashboard by default.
- Lower panel: `System notices` for connector/MCP outages, maintenance, and policy alerts. Admin-only health details are segregated.
- Optional small `Recent activity` feed with actor/action/time and case reference; no AI “thought stream.”

### Wireframe
```text
┌ Sidebar ┐ ┌──────────────────────────────────────────────────────────────┐
│Overview│ │ Overview                                      [ + New case ] │
│Cases   │ │                                                              │
│Evidence│ │ [ My active 12 ] [ Review 5 ] [ Processing 3 ] [ Approvals 2 ]│
│Agents  │ │                                                              │
│Reports │ │ My work queue                     Recent cases                │
│...     │ │ ┌───────────────────────────┐    ┌─────────────────────────┐ │
│        │ │ │ Scope approval · Case ... │    │ Case | State | Activity │ │
│        │ │ │ 4 candidates to review    │    │ ...                     │ │
│        │ │ │ MCP source unavailable    │    └─────────────────────────┘ │
│        │ │ │ Report needs sign-off    │                                  │
│        │ │ └───────────────────────────┘    System notices                │
│        │ │                                      Connector status ...     │
└────────┘ └──────────────────────────────────────────────────────────────┘
```

### Interaction and edge cases
- Empty state offers `Create a case` or explains role restrictions; do not show fabricated sample cases in production.
- Counts link to the corresponding filtered queue. The filter chips remain visible after navigation.
- Data loading uses skeleton cards and table rows; if one data source fails, render available parts and label the missing panel.
- Stale dashboard data shows `Last updated at ...` and a refresh control. Do not imply live updates if data is polled.
- Any priority label is based on operational deadlines or policy-defined work state, never predicted likelihood of criminality.

---

## 7.3 New case creation and victim narrative intake

### Purpose
Capture the report with the least necessary cognitive burden and let an investigator confirm operational/legal context separately. In victim mode, the reporter must not see staff-only classifications, agent controls, or technical entity forms.

### Victim-facing layout
1. Short heading: `Tell us what happened`.
2. One sentence: “Share what you remember in your own words. It is okay if some details are missing.”
3. One large textarea (minimum 8–10 visible lines on desktop and 6 on mobile) with plain-language, optional examples in an expandable “Details that may help” area.
4. One evidence section: drag/drop area plus `Choose files` and supported folder selection when browser/platform permits. State that folder access is limited to files the user selects; Aegis cannot scan the whole device.
5. Privacy and safety notice above submission: do not enter passwords, PINs, one-time codes, seed phrases, or card security codes; files may contain sensitive personal data; who can access; how to contact the agency.
6. Optional contact preference/report callback fields may appear only when needed and be separate from the narrative; not a technical form.
7. `Save draft` / `Submit report` actions. Confirmation page states reference and safe expectations, not an outcome promise.

### Example placeholder and help copy

**Textarea placeholder:** “Describe what happened, in your own words. You can include when and how you were contacted, what they asked you to do, and anything you remember about a payment or website. You can skip details you do not know.”

**Optional helper drawer:** “Details that may help: phone numbers, email addresses, usernames, links or website names, payment or UPI references, wallet addresses or transaction IDs, approximate dates, and the names of apps or services involved. Screenshots, messages, email files, receipts, and call records can be added below. A partial report is okay.”

### Investigator-facing creation layout
- The same two primary controls at top: narrative and evidence area.
- A secondary `Case details` accordion holds case reference, unit/owner, category, jurisdiction/policy, classification, retention profile, and purpose/authority declarations. These may be required by agency workflow but must not clutter victim intake.
- Show a clearly separated `Authorization and scope` step before enabling collection or agent run. The system may accept/store a report draft before the case is authorized; external lookups remain unavailable.
- After submitting, navigate to `Intake review`, not directly into an executing investigation.

### Wireframe (victim intake)
```text
┌───────────────────────────────────────────────────────────────┐
│ Report online fraud                                            │
│ Share what you remember. You can skip anything you do not know.│
│                                                               │
│ What happened?                                                 │
│ ┌───────────────────────────────────────────────────────────┐ │
│ │ Describe the events in your own words...                   │ │
│ │                                                           │ │
│ │                                                           │ │
│ └───────────────────────────────────────────────────────────┘ │
│ [ Details that may help ▾ ]                                   │
│                                                               │
│ Add screenshots, messages, emails, receipts or other files    │
│ ┌───────────────────────────────────────────────────────────┐ │
│ │ Drag files here or [ Choose files ] [ Select a folder ]    │ │
│ │ Your device is not scanned; only selected items are added. │ │
│ └───────────────────────────────────────────────────────────┘ │
│                                                               │
│ Safety & privacy: Do not include passwords, PINs or codes.     │
│ [ Save draft ]                                     [ Submit ] │
└───────────────────────────────────────────────────────────────┘
```

### Required interaction details
- Drag/drop and file picker are equivalent. Selected items appear in a queue with filename, size, upload state, remove-before-submit control, and warning if type unsupported. Removing an item before upload does not create an evidence record.
- During upload, allow the user to continue writing; maintain draft autosave with a visible saved timestamp. Sensitive narrative is not stored in browser local storage unless encrypted and explicitly approved; prefer server-side draft storage after authentication or a secure ephemeral client strategy.
- For an upload failure, identify the affected item and whether its bytes were received; do not ask the victim to repeatedly submit a file if it was already retained.
- For a file too large or unsupported, offer a clear alternative configured by the agency and avoid suggesting use of unapproved file-sharing services.
- Submission confirmation uses a short reference; do not expose internal case ID if policy distinguishes it from the reporter-facing reference.
- Consent/privacy notice must be accessible before upload/submit and available in the language of the intake.

### Prompt submission and agent-activity experience

Submitting the narrative is an explicit handoff to the intake service; it is **not** consent to conduct external searches or start every agent. The interface communicates the real lifecycle in short, observable stages and keeps the reporter/investigator oriented. The same stage component is used for staff-started analysis, with `Approval` and task/source details visible to staff.

1. **Receiving the prompt:** On activation of `Submit report` or `Analyze supplied information`, prevent duplicate submission, retain button dimensions, and display a one-time pressed/accepted state. Show `Receiving your report` with a subtle status icon and indeterminate indicator only while the server is accepting the request. When the server acknowledges, show the report reference and exact received time. If acknowledgement is lost, show `Checking submission status` and allow safe status refresh; do not create duplicate reports on retry.
2. **Processing the request:** Transition to `Checking the report and selected files`. Show discrete, real tasks such as `Text received`, `4 files received`, `Checking file types`, or `Preparing safe previews`. File rows report their own upload/scan/parse status. Determinate progress is used only when the upload or parser provides measured progress; otherwise use labeled indeterminate status. Malformed/unsupported items appear as exceptions without hiding successfully received files.
3. **Analyzing (the user-facing “thinking” stage):** Show `Reviewing the information you provided` with supporting text such as `Identifying possible dates, contact details, links, and payment references`. This is an honest description of the processing task, not a display of hidden reasoning. The stage may use the low-contrast live marker only while an analysis job is active. It must not show fabricated thought text, chat-like typing, a changing confidence score, or animated guesses about a person.
4. **Planning:** For a staff-authorized case, show `Preparing a reviewable investigation plan` and a list of proposed work items, their purpose, and relevant inputs. If the plan includes external sources, explain the destination and data categories before approval. In victim-facing intake, do not expose internal task details or request an approval from the victim; the flow stops at report receipt/intake processing.
5. **Approval gate:** When authorization or investigator approval is required, the stage becomes `Waiting for investigator review` and remains stationary—no pulsing countdown. Show `No external searches have started` where true. The primary action is `Review plan`; external tools cannot execute before the required approval.
6. **Executing tasks:** After approval, show actual tasks in `Queued`, `Running`, `Waiting`, `Complete`, `Blocked`, `Failed`, or `Cancelled` states. Parallel work is shown in task rows/graph with dependencies; a coarse stage rail does not imply a sequential path. Only the currently active task gets a subtle live indicator.
7. **Using tools:** On each actual tool invocation, display a ToolCallCard with source/server, purpose, data sent, call status, and response validation. Differentiate local processing from external lookup. A denied or unavailable tool shows the precise outcome (`Blocked — no request sent`, `Unavailable — no response received`); never animate a simulated query or imply a result exists.
8. **Generating a reviewable response:** After task outputs pass validation, show `Preparing a source-linked summary` or `Drafting findings for review`. Do not stream an unverified narrative as if it were final. The user sees a concise, structured draft with citations, uncertainty and conflict labels; each item remains `Needs review` until a person accepts or corrects it.
9. **Ready state:** Show `Ready for investigator review`, the completion time, completed/partial tasks, source failures, and a button such as `Review extracted details` or `Open findings`. Do not use celebratory confetti, “case solved,” or success language that implies identification or resolution.

**Stage timing and failure rules:** Each stage begins and ends based on an authoritative server event. A stage may be skipped or repeated; the UI represents `Skipped`, `Retrying`, or `Partial` explicitly. If an event does not arrive within the configured heartbeat window, replace active animation with `Status not updated` and a timestamp; offer a refresh/reconnect action. If a job fails, retain the prompt and submitted files, report whether receipt succeeded, and offer only a safe retry. Pausing/cancelling is available for staff runs where supported; it is never hidden behind the animation.

### Intake review screen (staff)
- Left panel: narrative, original-language text, author/source, submitted time; translation in a separate tab with label.
- Right panel: extracted candidate queue by type with source-highlighted phrase, raw value, proposed normalization, confidence/uncertainty, action buttons `Confirm`, `Correct`, `Reject`, `Mark unclear`.
- Footer: `Continue with unknowns`, `Ask a clarification` (only where appropriate), `Save review`, and `Authorization & scope` status. No automatic source lookup begins from this screen.

---

## 7.4 Case detail / investigation workspace

### Purpose
Provide a persistent place to understand the case, review evidence, see findings and workflow state, and move between investigative views without losing scope context.

### Shared case header
- Case reference and title; classification badge; current state; assigned owner; `Scope valid until / review due`.
- If scope/authority is missing, expired, paused, or needs approval, a persistent amber/garnet banner explains exactly what is unavailable. `Start analysis` is disabled with reason.
- Primary buttons depend on state: `Review plan`, `Add evidence`, `Assign task`, `Generate report draft`. Only one primary action at a time.
- Tabs: `Summary`, `Evidence`, `Entities & findings`, `Timeline`, `Activity`, `Reports`.

### Summary tab
- Top strip: case purpose summary, scope status, evidence integrity/process status, active tasks, unresolved conflicts.
- Main: `Investigation brief` from investigator-approved content, not an AI-generated unreviewed conclusion; `What is known / unverified / missing` three-column section.
- `Evidence at a glance`: counts by type and processing state.
- `Current task plan`: concise task graph or list, dependencies, approvals, status, cost/time budget.
- `Recent case activity`: uploads, approvals, finding changes, connector outages, report revisions.
- `Next review` due date and owner.

### Wireframe
```text
┌ Sidebar ┐ ┌──────────────────────────────────────────────────────────────┐
│ ...     │ │ Case CR-2026-0412 · Investment fraud     ACTIVE · Restricted │
│         │ │ Owner: A. Investigator   Scope review: 12 Nov 2026            │
│         │ │ [Review plan] [Add evidence] [Generate report draft]          │
│         │ ├ Summary | Evidence | Entities & findings | Timeline | ...     │
│         │ │                                                              │
│         │ │ KNOWN / REVIEWED        UNVERIFIED             MISSING        │
│         │ │ 2 transfers documented  Wallet ownership      Provider data  │
│         │ │ 1 email supplied        Profile link is lead  Not requested  │
│         │ │                                                              │
│         │ │ Evidence status          Current plan                         │
│         │ │ 5 originals · 2 derived  Email parse      Complete           │
│         │ │ All hashes verified      Domain lookup    Needs approval     │
│         │ │                          Timeline review  Ready              │
│         │ └──────────────────────────────────────────────────────────────┘
└────────┘
```

### Entity and findings tab
- Split view: filterable entity/finding list on left; evidence-backed detail inspector on right.
- Detail begins with raw value and normalized value clearly distinguished; data class, review state, candidate type, first/last observed dates; `How this was found` source cards; `Related evidence` list; `Related entities` with relationship provenance.
- User actions: confirm/correct/reject/mark ambiguous, create note, link/unlink with reason, open source evidence, flag for report. Actions are audited.
- Similarity suggestions show the basis and alternatives; never merge automatically based on fuzzy match.

### Timeline tab
- Chronological list with filters by source/type/status and optional graph view.
- Each event card labels the time basis: `Reported by victim`, `Message timestamp`, `File metadata`, `External source retrieval`, or `Investigator-entered`.
- Unknown timezone and conflicting timestamps are prominent badges; sort order uses a stated rule (UTC where known, then unknown-time section) and allows alternative display.
- Event details link to exact evidence location. A “correct time” operation stores a corrected analyst assertion without changing original timestamp.

### Activity tab
- Audit-like case activity feed appropriate to the investigator’s access level. User-facing activity is not identical to the restricted security audit log.
- Agent/tool events display task summary, server/tool, time, purpose, outcome, data classes sent, and reviewer decision. Restricted raw payload is separately permissioned.

---

## 7.5 Evidence library and evidence detail

### Evidence library layout
- Case filter always scoped and visible.
- Table columns: evidence ID, original filename, type, source class, size, ingest time, hash status, processing status, restriction, uploader/custodian.
- Filters: type, processing state, source class, integrity, date range, restriction, duplicate candidate.
- Bulk actions are limited to safe administrative operations and require permission; no bulk deletion or public-source lookup.
- Upload button opens the same file-area component with case context and preflight notice.

### Evidence detail layout
- Header: evidence ID, source filename, original/derivative state, restriction, integrity state; actions `Open viewer`, `Verify hash`, `Export reference` subject to permission.
- Left/center content viewer: safe text/document/image/media preview; no active content; visible original-vs-derivative banner.
- Right inspector: metadata, hash values, ingest facts, source/custody history, parser outputs, parent/child lineage, linked entities, analyst notes, access restrictions.
- Evidence comparison supports side-by-side original/derivative where safe (e.g., image and OCR text). OCR highlight maps text back to page/region when available.
- Redaction view makes applied masks visible and preserves a restricted original according to policy. A redacted export has a distinct file ID/hash and redaction record.

### Integrity and unsupported states
- `Integrity verified` means hash recomputation matches the recorded hash; tooltip explains exact meaning and does not claim authenticity.
- `Integrity check failed` is persistent, garnet, and blocks “verified” report use until examiner review; provide incident link and preserve the object.
- If preview cannot load, offer safe download only if user permission permits; state that the original is preserved. Never render scripts/macros.
- If encrypted/password protected, label as such and leave decryption to authorized procedures; no brute-force affordance.

---

## 7.6 Agent monitoring and control panel

### Purpose
Give investigators transparent control of plans and executions without showing misleading private reasoning traces or overwhelming them with token-level internals. The AgentRunHeader, AgentStageRail, AgentTaskRow, and ToolCallCard defined in §6.9–§6.11 are reused here so live activity has the same labels, motion, and event semantics as prompt submission.

### Case plan review
Before a run, present:
- Case goal as written by investigator and active scope version;
- task list/graph with each task’s purpose, input evidence/entities, planned agent, tools/sources, dependencies, output type, data class, and estimated cost/time band;
- parallel tasks indicated with a dependency diagram and text explanation;
- external data disclosures highlighted in a separate section, including destination and exact data categories;
- approval requirements and any unresolved policy block;
- total budget, call cap, runtime limit, and available cancellation controls.

Actions: `Approve selected tasks`, `Edit plan`, `Reject plan`, `Save as draft`. If a task is out of scope, show a specific reason and a safe alternative such as manual review; do not ask an LLM to override policy.

### Live run screen
- Status header with run ID, case reference, state, start time, elapsed time, budget consumed, pause/cancel buttons.
- Workflow list/graph: task name, agent template/version, status, dependencies, source/tool badge, last update, output link. Use text-readable list as primary; graph is optional enhancement.
- Activity feed: concise events such as `Email parser completed: 4 candidate addresses`, `Passive DNS lookup blocked: source not approved`, `Waiting for investigator approval`. Never expose raw private chain-of-thought.
- “What data was sent?” expandable audit summary for each external call, subject to role permissions.
- `Needs review` queue lists generated findings and conflicts. Completion does not imply case success.

### Wireframe
```text
┌ Case CR-2026-0412 · Run RUN-018         RUNNING · 6m 12s          [Pause] [Cancel] ┐
│ Budget 14 / 30 calls · 2 / 4 tasks complete · 1 waiting for approval             │
├───────────────────────────────┬──────────────────────────────────────────────────┤
│ TASK PLAN                     │ ACTIVITY                                         │
│ ✓ Parse supplied email        │ 14:10:22 Email parser completed                  │
│ ✓ Extract payment references  │ 4 candidate values · awaiting review            │
│ ◷ Passive DNS lookup          │ 14:11:03 Passive DNS blocked                     │
│   └ depends on reviewed URL   │ Source not approved for this case                │
│ ! Public profile search       │                                                  │
│   Awaiting investigator       │ FINDINGS TO REVIEW                               │
│                               │ 4 candidate addresses · 1 timeline conflict     │
└───────────────────────────────┴──────────────────────────────────────────────────┘
```

### Control behavior
- Pause stops scheduling new tasks and requests safe cancellation of eligible tasks; explain that an already-sent external query cannot be recalled.
- Cancel ends queued work and cancels supported in-flight tasks; persist partial results with `partial/cancelled` state.
- Retry shows previous attempt and whether it may repeat a side effect. Non-idempotent action cannot be retried without explicit confirmation and policy approval.
- Replan creates a new version and requires review where scope/data flow changes. The prior plan and results remain intact.
- A failed agent can be retried, skipped, or assigned for manual work, but the system never fabricates a successful outcome.

---

## 7.7 MCP server management panel (administrator)

### Purpose
Make MCP lifecycle, trust, capabilities, data access, and runtime health legible to authorized administrators. Integrations administration is separate from case data access.

### Server list
Columns: display name, owner/trust domain, environment/transport, review state, enabled tools count, definition version/hash state, last health check, last activity, approval expiry, warning status. Filters by state, environment, transport, data classification, owner.

Primary action: `Add server`. Each row opens a detailed server view. No “connect all” or automatic discovery-to-enable shortcut.

### Add server flow (wizard)
1. **Connection details:** server name, transport, endpoint or local launch configuration, environment, owner, purpose.
2. **Data and permissions:** accepted data classes, destination, access/network scope, credentials/secret reference, side-effect declaration, retention/processing behavior.
3. **Discover safely:** connect in isolated test environment using synthetic data; display discovered tools/resources/prompts, schemas, and warnings.
4. **Review:** capability-by-capability allow/deny; compare version/hash; privacy/legal/security checks; test responses and boundary cases.
5. **Approve:** named reviewers, permitted case groups, egress rules, rate/cost/time budgets, expiry/review date. Activation remains a deliberate separate action.

### Server detail tabs
- **Overview:** status, owner, approval conditions, health, review dates.
- **Capabilities:** discovered tools/resources/prompts, schema, descriptions, side effects, allow/deny, version/hash, last change.
- **Policy & data flow:** permitted classifications, fields sent, destinations, retention notes, case constraints.
- **Credentials:** secret-manager reference, scope/expiry/rotation state; values never displayed after initial entry.
- **Health & diagnostics:** handshake, discovery, error categories, latency, response validation; diagnostics are redacted.
- **Audit:** changes, approval, calls, denials, suspension, definition mutations.

### Wireframe
```text
┌ MCP & Integrations                                  [ + Add server ] ┐
│ Enabled 6 · In review 2 · Suspended 1 · Approval expiry: 1           │
│ Filters: [Status ▾] [Transport ▾] [Classification ▾] [Search]       │
├──────────────────────────────────────────────────────────────────────┤
│ Server      Trust domain   Transport   Review   Tools  Health   ▸    │
│ Domain Intel Agency        HTTPS       Enabled  4/6    Healthy  ▸    │
│ Registry X  Vendor         stdio       Review   8/8    Not run  ▸    │
│ Blockchain  Approved feed  HTTPS       Suspended 3/3   Changed  ▸    │
└──────────────────────────────────────────────────────────────────────┘
```

### High-risk states
- Capability hash changed: persistent warning `Server definition changed since approval`; tools unavailable until reviewed. Show old/new diff and approver.
- Endpoint identity mismatch: block connection; no override in the ordinary page. Security administrator escalation only.
- Server unreachable: show last known state and outage; no silent fallback.
- Credential near expiry: notify administrator, never expose credential value.
- Suspended: display the reason and affected case findings/tasks; allow export of diagnostics only to authorized admins.
- Test connection must identify synthetic payload. Live case content is not available in test mode.

---

## 7.8 Report generation, review, and export

### Draft creation screen
- Select template and scope (case summary, evidence register, analysis report, disclosure package) from agency-approved options.
- Show included reviewed findings and excluded/unreviewed items. Defaults include only reviewed findings; investigator can include an allegation/unverified lead only with explicit label and approval.
- Options for date range, source classes, redaction profile, recipient/disclosure purpose, and output format appear in a review panel, not a long wizard.
- Summary of sensitive data included, hidden, or redacted before generation.

### Report editor layout
- Main document preview/editor in center; outline/navigation on left; citation/evidence inspector on right.
- Each paragraph has `Evidence-backed`, `Analyst-authored`, `AI draft`, `Needs review`, or `Uncited` status. Uncited material cannot be finalized as material factual assertion.
- Clicking a citation opens source evidence at exact page/message/field, without losing editor state.
- Side panel shows the claim and its source lineage; contradictory sources are displayed together.
- Toolbar: Save draft, Compare versions, Validate citations, Preview redactions, Request review, Export after approval.
- Track user edits; AI generation is not allowed to overwrite accepted human edits without confirmation.

### Report verification checklist
Before export, show:
- Material claims cited or explicitly marked as allegation/inference;
- unreviewed candidates excluded or specifically labeled;
- source freshness/availability and known gaps;
- scope and methods included;
- evidence manifest and integrity status;
- redaction/disclosure check;
- reviewer and approver requirements;
- output format, recipient, destination, and export audit.

### Export confirmation
Use a modal with a concise summary and explicit `Export reviewed report` action. Do not use a generic “Download” if the action creates an externally shareable package. User confirms recipient/purpose; system records hash/version and produces a download or approved transfer. If any approval is missing, provide `Save draft` and state the missing condition.

---

## 7.9 Settings and configuration

Settings navigation is permission-filtered and separated into:

- **Profile & accessibility:** display name, locale, timezone, text size, theme, keyboard shortcuts, notification preference.
- **Organization:** units, default case templates, classification labels, date/time display, report templates.
- **Access & roles:** role mapping, case assignment, MFA/SSO, privileged access, separation-of-duty rules.
- **AI & agent policies:** approved model/provider, data classes, logging/redaction, budgets, enabled agent templates, retention and no-training commitments.
- **Connectors & source policies:** tool registry, approved source profiles, jurisdiction/terms, rate limits, data egress and provider status; links to MCP admin panel.
- **Retention & legal hold:** retention profiles, hold process, deletion approvals, backup behavior.
- **Audit & export:** audit storage/retention, export permissions, diagnostic redaction.
- **System health:** administrator-only service/dependency status; no evidence contents displayed.

Every policy change includes current value, proposed value, impact summary, requester, reviewer, effective date, and rollback/previous version. High-impact changes require reauthentication and approval.

---

## 8. Interaction Patterns and State Models

### 8.1 Loading and progressive processing

- Initial page loads show skeletons shaped like the final content; do not show fake percentages.
- Progress bar is used only when total work is estimable from known files/steps. Otherwise state `Queued`, `Processing`, or `Waiting for source` with a last-updated time.
- Evidence parsing is per-file and asynchronous. A completed case intake does not imply all files have been processed.
- Partial results are labeled. A failed file does not block unrelated safe files, but the case summary shows the exception.
- Agent activity uses the event-bound motion tokens in §6.11: the stage rail, task state, and subtle live indicator change only after the corresponding service event. Animation confirms the event; it never predicts it.
- At submission, first acknowledge receipt, then show file/request processing, analysis, plan review, approval wait, execution/tool use, synthesis, and review-ready states as applicable. A victim submission may stop after intake processing; it must not imply that an investigation has been authorized or started.
- Show determinate progress only for measured units (bytes, files, completed tasks). LLM token generation or elapsed wall time is not a valid proxy for investigative completion. If no measurable denominator exists, use a labeled indeterminate activity state and expose elapsed time separately.
- Respect `prefers-reduced-motion`; transitions become immediate/static as specified in §6.11. A screen-reader user receives concise status changes, not repeated announcements for every animation frame.

### 8.2 Real-time agent and system updates

- Live updates use a persistent event stream where available; each event includes server time, event sequence, run/task ID, and canonical state. If connection is lost, UI indicates `Updates paused` and offers refresh/reconnect.
- Stage changes and completion indicators are driven by canonical server events. Client-side timers may animate a progress affordance, but cannot change workflow state or show progress claims.
- New events do not steal focus or reorder a list while the user is reading. Show a `New activity` marker and allow the user to jump.
- “Agent activity” is factual task-state reporting, not chain-of-thought, anthropomorphic dialogue, or a dramatized transcript. `Analyzing` names a processing stage; it does not reveal private reasoning.
- Tool-call visualization appears only after a real call is scheduled or initiated. A blocked, failed, or not-yet-approved operation never receives a running animation.
- If WebSocket/SSE connection fails, core case data remains accessible; status may be stale and is timestamped. Stop active pulses after the configured heartbeat threshold and show the last confirmed event time.
- When updates resume, reconcile by event sequence and refresh authoritative state before animating a transition; do not replay a misleading backlog of “live” activity.

### 8.3 Approval and consent

- Approval explains the purpose, the requested capability, the exact data category/destination, authority/scope reference, and approval duration.
- Approval is per action or per narrowly defined policy; no broad default approval for all future tools.
- Refusal is a valid, non-punitive option. Denial produces a blocked state and suggested manual/approved alternative where available.
- Approval cannot be preselected, inferred from a previous unrelated action, or obtained through a misleading button.

### 8.4 Error states

Every error has a cause category, affected object, consequence, recovery path, and whether the original evidence remains safe. Examples:

- **Network/source error:** “The passive DNS service did not respond. No result was recorded. The case evidence is unchanged.”
- **Policy denial:** “This source is not approved for this case’s data class. No request was sent.”
- **Schema error:** “The MCP server returned data in an unexpected format. The result is quarantined; no finding was created.”
- **Integrity error:** “The stored file no longer matches the hash recorded at intake. Access is restricted and the evidence custodian was notified.”
- **Parser failure:** “This file is preserved, but this version could not extract its contents. No content was omitted from the original.”
- **Authorization error:** “You do not have permission for this evidence. Ask the case owner or supervisor.” Do not reveal hidden object metadata.

### 8.5 Confirmations and destructive actions

- `Remove from draft` vs. `Delete evidence` are distinct and never share the same copy.
- Deleting a case/evidence item requires a reason, authority, and any configured second approval; show retention/legal-hold constraints.
- Disabling an MCP server is reversible but may interrupt tasks; confirmation names affected runs and findings.
- Copying a technical identifier is low-risk but should not trigger external requests. It may use a brief local toast `Copied` without echoing the identifier.

### 8.6 Notifications and alert prioritization

Prioritize alerts by immediate operational impact:
1. Evidence integrity/security incident or unauthorized data egress;
2. Scope/authority expired or task blocked by authorization;
3. Required investigator approval;
4. Processing failure or source outage;
5. Routine completion and non-urgent review.

Do not use a general red banner for a minor parser warning. Alerts remain until acknowledged only when policy requires; acknowledgement does not resolve the underlying issue.

---

## 9. Responsive Design

### 9.1 Investigator application

Primary target is desktop/laptop at 1280 px and above because investigators compare evidence and complex tables. Support tablet down to 768 px for review and approval. Under 768 px, the shell collapses to a top menu and workflows become single-column; evidence graph is replaced by an accessible list/table by default. Do not support high-impact bulk actions on a narrow viewport if the confirmation context cannot be shown clearly.

- At widths ≥1440 px, show sidebar + main + optional inspector.
- At 1024–1439 px, use collapsible sidebar and hide optional inspector until requested.
- At 768–1023 px, use a compact rail and single content column; tables preserve essential fields and can reveal details in a row inspector.
- Below 768 px, investigator use is review-only for initial release: see cases, approve/deny bounded plan, review a small number of findings, and acknowledge notices. File ingestion, report sign-off, bulk changes, and MCP administration are disabled or redirected to desktop if safe.

### 9.2 Victim-facing intake

Must work on mobile first:
- Single-column controls, large touch targets (minimum 44 × 44 CSS px), no horizontal scrolling, plain-language file selection, upload resume where supported.
- Narration text remains editable during file upload. Keep primary `Submit` action reachable but not sticky if it obscures content or assistive technology focus.
- Provide explicit camera/gallery selection only as an OS-mediated file picker; no automatic camera/microphone activation.
- Save-draft behavior is clearly described; on shared devices avoid persistent sensitive drafts without consent. Offer a safe “clear this draft from this device” control where applicable.

---

## 10. Accessibility and High-Stress Use

### 10.1 Standards target

Target **WCAG 2.2 AA** for public/victim surfaces and investigator application, subject to stricter agency/local accessibility requirements. Accessibility acceptance is part of release—not a later polish pass.

### 10.2 Keyboard and screen reader

- All functions available without pointer; logical tab order; skip links; focus visible; no keyboard traps except properly managed modal dialogs.
- Semantic landmarks, headings in order, labels/descriptions, table headers, status announcements, and meaningful link names.
- Async updates use polite live regions; urgent integrity/security alerts use assertive announcements only when necessary. Avoid announcing every graph/data update.
- Dialog open returns focus to trigger on close; errors move focus to the first relevant issue or summary.
- Charts/graphs have a title, summary, text/table representation, and accessible relationship descriptions.

### 10.3 Visual accessibility

- Test contrast, zoom, text spacing, focus state, reduced motion, and color vision variants.
- Do not use color alone for evidence states, relationship types, errors, or confidence.
- Support OS/browser reduced-motion preference; default to restrained, nonessential motion only. Under `prefers-reduced-motion: reduce`, remove pulses, shimmer, sliding, and transforms, and communicate state changes through text, icons, and static borders.
- Dynamic status is announced through a polite live region with concise messages (e.g. “Email parsing started”, “Waiting for investigator approval”, “Tool call blocked; no request sent”). Do not announce animation frames, token-level output, or every low-level event.
- Provide stable layout and no flashing/rapid activity indicators. Motion must never be the sole carrier of an update or delay access to pause/cancel controls.

### 10.4 Cognitive and trauma-aware accessibility

- Use short instructions and one task per screen where feasible. Preserve the victim’s narrative; do not break it into mandatory technical questions.
- Define unavoidable acronyms inline; “MCP,” “hash,” “DNS,” and “wallet” appear in admin/investigator contexts with help text, not in victim copy unless the reporter used them.
- Avoid blame framing (“Why did you…?”). Use neutral prompts (“What happened next, if you remember?”).
- Allow partial completion, “I don’t know,” “not sure,” and “skip.” Never signal that a report is low quality because details are missing.
- Provide a clear route to human help and the relevant agency contact channel; do not present an AI as emergency response.

---

## 11. Wireframe Index

Detailed ASCII wireframes are provided within each screen section. The required screens and their primary layout anchors are:

| Screen | Primary anchor | Core interaction |
|---|---|---|
| Login/authentication | Single centered identity panel | Agency sign-in + MFA |
| Main dashboard | Work queue + case list | Open/review cases and action queue |
| New case intake | Narrative + evidence drop zone | Submit partial report safely |
| Intake review | Narrative/source on left, candidates on right | Confirm/correct/reject extractions |
| Case detail | Case banner + tabs + status summary | Review evidence/findings/timeline/tasks |
| Evidence detail | Safe viewer + provenance inspector | Inspect original/derivative and custody |
| Agent monitor | Task plan/status + activity | Approve, pause, cancel, inspect data flow |
| MCP management | Server inventory and lifecycle | Add, review, test, approve, suspend |
| Report editor | Document + outline + citation inspector | Correct, verify, review, export |
| Settings | Permission-filtered category navigation | Change governed policy with audit |

These wireframes establish information priority, not pixel-level implementation. Final component sizing must be validated at 1280 × 800 desktop, 1024 × 768 tablet, and 390 × 844 mobile, plus 200% zoom and screen-reader use.

---

## 12. Frontend Framework and Implementation Recommendation

### 12.1 Recommended stack

- **React + TypeScript** for typed UI components and state-safe handling of complex case/evidence records.
- **Vite** as the frontend build tool for a fast, static client application suitable for agency-hosted or on-premises deployment.
- **React Router** for URL-based navigation and case tabs with browser history support.
- **TanStack Query** for API state, request caching, invalidation, retry policy, and background refresh. Cache policy must be case-aware and clear sensitive records on logout/session expiry.
- **Accessible component primitives** (e.g., Radix UI or an agency-approved equivalent) wrapped in Aegis design tokens; no unreviewed component library is permitted to render evidence or privileged operations without accessibility/security review.
- **CSS variables/design tokens** for palette, typography, spacing, contrast modes, agency theming, and the motion tokens in §6.11; avoid coupling colors or durations directly into component code.
- **CSS transitions and keyframes** for standard UI feedback and status indicators. Use the Web Animations API only for a small number of one-shot state transitions when CSS alone is insufficient; do not add a heavy motion dependency solely for spinners or stepper effects. Any added animation library requires bundle, accessibility, and security review.
- **WebSocket or Server-Sent Events client** for task/evidence status; backend remains authoritative and events are replayable or refreshable after reconnect.
- **Visx/D3 or equivalent visualization layer** only for graph/timeline/map components, with a table/list alternative and a vetted data boundary. Avoid using a chart library as a substitute for accessible interaction design.

### 12.2 Why React + Vite

The primary product is an authenticated, data-dense investigator application with a separately governed backend API. Server-rendering public content has little value for private case records and could complicate handling of sensitive data in caches or rendering layers. React’s component model and TypeScript’s discriminated unions are a good fit for explicit states such as `unreviewed`, `blocked`, `integrity-failed`, `partial`, and `approved`. Vite produces a lean static bundle that agencies can host behind their own identity and network controls, and it keeps the frontend deployment boundary independent from backend and agent services.

This choice does not remove the need for secure API authorization, content security policy, safe HTML rendering, session protections, or strict cache clearing. Frontend state is not a security boundary; all permission and scope enforcement is server-side as required by [`prd.md` §7.1, §11].

### 12.3 Frontend engineering constraints

- Use strict TypeScript; no `any` for case/evidence/agent/MCP/report domain objects except isolated third-party adapters with runtime validation.
- Validate API and MCP-derived data at runtime; never trust TypeScript types for untrusted network payloads.
- Separate public/victim intake bundle from investigator/admin bundle where the deployment requires distinct exposure and access control.
- No third-party analytics, session replay, advertising, remote fonts, or client-side telemetry that captures case content.
- Apply Content Security Policy, trusted types where supported, secure cookie/session strategy managed by backend, and output escaping.
- Scrub sensitive query-cache and in-memory state at logout, permission downgrade, case switch where required, session expiry, or suspected compromise.
- Use route-level permission guards for usability, with backend authorization as the actual control.
- Keep user-visible errors accessible and do not log raw case data to browser console or telemetry.

---

## 13. Content and Localization Rules

- Initial investigator locale is configurable; default date display for an India deployment may use `en-IN` and `Asia/Kolkata`, while preserving UTC and original source timezone.
- Victim intake should support localization and right-to-left scripts when an agency requires them. Translation copy is reviewed by human translators, not solely generated at runtime.
- Keep identifiers, URL, hash, and numeric strings in a stable direction using bidirectional isolation; do not corrupt mixed-script or RTL identifiers.
- Localized copy must preserve distinctions between “reported,” “observed,” “derived,” and “confirmed.”
- Provide a language/source indicator on original and translated content. Do not overwrite original text with translation.
- Acronyms, legal terms, and technical labels have glossary/help content for investigators and are excluded from victim-facing copy unless necessary.

---

## 14. Design Acceptance Checklist

A screen is ready for implementation when it meets all applicable items below:

### Visual and interaction
- [ ] Uses the three-anchor palette and approved neutral/semantic tokens.
- [ ] Has one clear title, purpose, and primary action; high-risk action has explicit explanation and confirmation.
- [ ] Displays case scope/classification context where case data is shown.
- [ ] Communicates observed/reported/inferred/reviewed/unknown distinctions without color alone.
- [ ] Includes loading, empty, partial, stale, error, permission denied, and success states.
- [ ] Uses only restrained, event-bound animation from §6.11; no fake progress, simulated typing/thought, or hidden chain-of-thought transcript.
- [ ] Prompt submission states and agent stages are observable, truthful, and clearly distinguish intake processing from authorized investigation execution.
- [ ] Reduced-motion mode removes nonessential motion while preserving equivalent textual/status feedback; active animation stops when events become stale.

### Evidence, privacy, and safety
- [ ] Material claim/candidate can be traced to source and exact locator.
- [ ] External source/data disclosure is visible before authorization and auditable afterward.
- [ ] Sensitive values are masked or permission-restricted where required.
- [ ] Unsupported or failed processing is visible and confirms whether original evidence remains preserved.
- [ ] The screen does not imply identification, guilt, legal sufficiency, or guaranteed recovery.
- [ ] Instructions embedded in evidence/tool output cannot be mistaken for system/user controls.

### Accessibility and responsive behavior
- [ ] Keyboard-only completion and visible focus are verified.
- [ ] Screen-reader labels, headings, tables, dialog focus, and async announcements are verified.
- [ ] Color contrast and grayscale/color-vision states are verified.
- [ ] 200% zoom and narrow viewport preserve essential function.
- [ ] Graph/map/chart has a textual/table alternative.
- [ ] Victim-facing copy is plain language, non-blaming, skippable, and does not overpromise.

### Quality assurance
- [ ] User testing includes investigators, forensic examiners, and non-technical participants for intake.
- [ ] Usability tests include incomplete evidence, conflicting data, malicious content, slow source, upload interruption, and low-bandwidth states.
- [ ] Screenshot/review captures contain synthetic or approved de-identified data only.

---

## 15. Traceability to PRD and Research

| Design area | PRD reference | Research references |
|---|---|---|
| Victim intake / low cognitive load | §7.3, §8, §11.5 | [R35], [R12] |
| Evidence upload / provenance / integrity | §7.4, §10 | [R12], [R14–R18] |
| Case workspace / graph / timeline | §7.2, §7.10–§7.11 | [R06], [R12], [R31] |
| Agent plan and monitoring | §7.6, §7.11–§7.12 | [R02–R06], [R19], [R24], [R34] |
| MCP admin lifecycle | §7.8–§7.9 | [R26–R30], [R36] |
| Report drafting and disclosure | §7.13, §10, §12 | [R06], [R10], [R12], [R32], [R35] |
| Security/accessibility | §7.1, §11–§14 | [R18], [R27], [R29], [R30], [R32], [R34] |
| OSINT source presentation | §7.7, §7.9, §8 | [R07–R11], [R19–R25] |

---

## 16. Open Design Decisions Resolved by This Baseline

The choices below are deliberate defaults so implementation can proceed. A deployment can refine them through change control, but may not weaken the PRD’s safety constraints without formal approval.

1. **Investigator app is desktop-first; victim intake is mobile-first.** Desktop is optimized for evidence comparison and dense review; mobile investigator capability is limited to safe review/approval in the first release.
2. **Light reading surfaces with a dark plum navigation shell are default.** This supports long-form evidence review and report consistency; an optional dark mode may follow accessibility testing.
3. **Three chromatic brand anchors are Ink Plum, Oxidized Jade, and Aged Brass.** Neutrals and semantic states remain necessary, subdued support colors.
4. **The front end is React + TypeScript + Vite.** The backend/API remains responsible for authorization, case data, evidence, audit, orchestration, MCP, and policy enforcement.
5. **Task graph is visible as a list first and graph second.** A readable ordered task list is the robust accessible representation; graph layout is an optional aid, never the only way to understand dependencies.
6. **There is no live model chain-of-thought display.** User-visible logs show concise task rationale, inputs, data flow, tool results, citations, and decisions instead.
7. **Victim intake accepts partial information before case authorization but cannot initiate external lookups.** Authorization and scope are an investigator-side gate.
8. **MCP connections are disabled until reviewed and explicitly activated.** Discovery is not approval; changed definitions trigger re-review.
9. **Evidence originals are never edited in place.** Annotation, translation, OCR, redaction, normalization, and summaries are derivatives or separate records.
10. **No person-level confidence/guilt score exists.** The design uses source- and method-specific uncertainty and analyst review states.

---

## 17. Change Control

Any design change that alters the victim intake model, evidence visualization, source disclosure, agent approval flow, MCP lifecycle, report sign-off, uncertainty language, accessibility, or privacy behavior must be checked against [`prd.md`](prd.md), especially §§7, 10–14. Changes that enable broader collection, autonomous actions, biometric identification, or new external data use require formal product, legal/privacy, security, and forensic-method review. Design token, copy, and component changes must remain consistent with the three-anchor palette and the language rules in §2.2.
