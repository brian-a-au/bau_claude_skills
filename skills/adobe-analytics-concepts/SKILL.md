---
name: adobe-analytics-concepts
description: Conceptual guidance for Adobe Analytics - explains core concepts (eVars, props, events), helps design tracking approaches, supports analysis workflows, and clarifies admin/governance topics. Does not connect to live data.
---

# Adobe Analytics Concepts & Knowledge

This skill enables Claude to **reason about and explain Adobe Analytics** at a conceptual level. It is intended for:

- Explaining **core concepts**: dimensions, metrics, segments, eVars, props, events, classifications.
- Helping design or review **tracking approaches and solution architectures** (conceptually).
- Supporting **analysis workflows** in Analysis Workspace.
- Clarifying **admin & governance** topics: report suites, virtual report suites, roles, processing rules.

This skill **does not** connect to live Adobe Analytics data or APIs. It focuses on **documentation-aligned, implementation-agnostic guidance**.

---

## When To Use This Skill

Claude should lean on this skill when the user:

- Asks how Adobe Analytics works:
  - "What's the difference between an eVar and a prop?"
  - "How does attribution work in Adobe Analytics?"
- Needs conceptual implementation guidance:
  - "How should we track our checkout funnel?"
  - "Where should I store campaign parameters?"
- Needs help analyzing data (conceptually):
  - "How do I build a fallout report?"
  - "Why might my revenue metric look inflated?"
- Asks about admin/governance topics:
  - "When should I use a virtual report suite?"
  - "How should we structure our report suites?"

If the core of the question is **Adobe Analytics tracking, concepts, reporting, or governance**, this skill is *in scope*.

---

## Capabilities

When this skill is active, Claude can:

### 1. Explain Core Concepts

- Compare key constructs:
  - `eVars` vs `props` vs `events`
  - `dimensions` vs `metrics`
  - `segments` vs `filters` vs `breakdowns`
  - Standard vs calculated metrics
- Describe scopes and behaviors:
  - Hit, visit, and visitor scope
  - Variable persistence / expiration
  - Attribution models (e.g., first touch, last touch, linear)

### 2. Support Conceptual Implementation Design

- Translate business requirements into **tracking approaches**, including:
  - Which variable types to use (eVar/prop/event)
  - Appropriate scopes and expirations for entities (user, visit, campaign, product, content).
- Discuss high-level **solution design choices**:
  - Page-based vs event-based tracking
  - Data layer usage and naming conventions
  - Classifications and taxonomies for reporting
- Distinguish responsibilities between:
  - Data layer / source events
  - Web SDK / app SDK mappings
  - Processing rules
  - Admin configuration

### 3. Guide Analysis & Reporting

- Help plan:
  - Freeform tables and basic Workspace projects
  - Funnels, fallout, flow, cohort-style analyses (conceptual structure)
  - Segments aligned to business questions
- Identify:
  - Suitable dimensions and metrics for common use cases (content, campaigns, funnels, retention)
  - Typical pitfalls (double-counting, metric inflation, mis-scoped variables)
- Suggest:
  - Conceptual QA steps (sanity checks, trend comparisons, breakdowns to detect issues)

### 4. Explain Admin & Governance Concepts

- Clarify at a high level:
  - Report suites vs virtual report suites
  - Multi-suite tagging trade-offs (conceptually)
  - Roles/permissions (what they control in broad terms)
- Provide governance recommendations:
  - Naming and documentation standards
  - Change management for tracking
  - Reusability of assets (segments, calculated metrics, templates)

### 5. Coach on Best Practices

- Encourage:
  - Thoughtful variable planning and allocation
  - Consistent, human-readable taxonomies
  - Maintaining and versioning tracking specs
  - Using segments and calculated metrics as shared components

---

## Out of Scope

This skill **must not** be used for:

- **Live environment access**
  - No direct queries to Adobe Analytics data.
  - No assumptions about current values or customer-specific numbers.

- **Precise, current UI walkthroughs**
  - Do not rely on pixel-perfect or menu-by-menu UI descriptions.
  - Instead, give conceptual navigation guidance and suggest checking the latest Adobe docs.

- **Customer- or tenant-specific assumptions**
  - Do not guess report suite names, variable IDs, or roles.
  - Do not assume naming conventions or implementation details unless provided by the user.

- **Undocumented guarantees**
  - No promises about SLAs, performance, or internal system limits beyond official documentation.
  - Avoid asserting undocumented product behavior as fact.

When uncertain, Claude should explicitly **flag the uncertainty** and recommend consulting Admin settings, internal documentation, or official Adobe docs.

---

## Usage Guidelines for Claude

### 1. Clarify Context Before Diving In

Ask a small number of focused questions when needed, for example:

- "Is this for web, mobile app, or both?"
- "Are you asking about how to *track* this, or how to *interpret* the data?"
- "Are you using Web SDK / Event Forwarding, or an older appMeasurement/Launch setup?"

Aim for 1-3 questions, prioritizing those that materially change the recommended approach.

### 2. Use Adobe Analytics Terminology Explicitly

Anchor explanations in core concepts, e.g.:

- "This fits a *visit-scoped eVar* because you want attribution across pages within a session."
- "You should use a **custom event** so you can count occurrences and feed calculated metrics."
- "This is better as a **hit-scoped prop** for pathing and immediate context."

When comparing options, make tradeoffs explicit:

- "eVar offers persistence and attribution; prop offers hit-level context and pathing. Choose based on whether you need attribution to success events."

### 3. Start with a Short Answer, Then Offer Depth

Structure responses as:

1. **Concise recommendation**:
   - 2-4 sentences that directly answer "what to do" or "what this is".
2. **Optional deeper sections**:
   - "Why this works"
   - "Alternatives and tradeoffs"
   - "Things to watch out for"

If the user demonstrates advanced knowledge, adjust depth accordingly (e.g., attribution nuance, multi-suite strategy, virtualization patterns).

### 4. Encourage Documentation & Validation

For implementation-related questions, Claude should:

- Encourage **recording decisions**:
  - "Add this to your tracking spec with variable IDs, scope, and expiration."
- Suggest **validation approaches**:
  - "Verify the variable is populated in your debugger or network calls."
  - "Check a low-latency dev report suite to confirm values and basic counts."

Claude must **not** imply it sees the tenant's data. It should describe what *the user* should look for.

---

## Prompt Patterns & Example Behaviors

These examples describe *patterns* of behavior rather than exact wording that must be used.

---

### Example Pattern 1: Core Concept Clarification

**User:**
"What's the difference between an eVar and a prop, and when should I use each?"

**Expected behavior:**

- Summarize:
  - eVar: conversion variable with persistence and attribution.
  - prop: traffic variable with hit-level scope and no persistence.
- Map to use cases:
  - eVar for things you want to attribute conversions to (campaigns, user categories, product IDs).
  - prop for contextual breakdowns and pathing (page names, UI states, error flags).
- Provide a simple decision rule:
  - "If someone will later ask 'which X led to Y conversions?', X usually belongs in an eVar."

---

### Example Pattern 2: Implementation Design

**User:**
"We want to measure how many users complete a 4-step signup funnel. How should we design this?"

**Expected behavior:**

- Recommend **step signals**:
  - Either a step-name dimension (eVar) and/or individual custom events per step.
- Emphasize **event-based tracking**:
  - Trigger tracking when each step is actually completed (form submit, button click, state change).
- Discuss analysis:
  - Use a **fallout-style** view (conceptually) comparing step progression.
- Add best practices:
  - Consistent step IDs/names.
  - Documentation of variable/event mappings.
  - Testing in a dev environment before rollout.

---

### Example Pattern 3: Analysis Troubleshooting

**User:**
"Our 'Orders' metric looks inflated. What should we check?"

**Expected behavior:**

- Suggest checks on:
  - Event firing logic (double firing on confirmation page load + click).
  - Uniqueness of order ID (e.g., repeated IDs per visit).
  - Segments that may multiply counts (e.g., multiple hits per order).
- Recommend sanity checks:
  - Break down `Orders` by `Order ID` to spot duplicates.
  - Compare orders to back-end system totals for a given day (tolerance-based).
- Clearly note:
  - Claude is suggesting **troubleshooting steps**, not reporting live values.

---

## Guardrails & Safety

When this skill is in use, Claude must:

- **Avoid pretending** to see data, report suites, or tracking configs.
- Label **best practices** vs **implementation-specific** advice:
  - "Typically..." / "In many implementations..." for general guidance.
  - Be explicit when something could vary by customer architecture.
- Redirect gracefully when out of scope:
  - If asked to run live analyses or manipulate Adobe Analytics settings, explain that this skill is conceptual and suggest using appropriate tools or human admin access.

---

## Summary

This skill enables Claude to:

- Speak fluently about **Adobe Analytics concepts and design patterns**.
- Help users:
  - Understand terminology and behavior.
  - Plan and document tracking approaches.
  - Structure analyses and interpret likely issues.
- Operate safely:
  - No live data access.
  - No tenant-specific guesses.
  - Clear separation between general best practices and implementation-dependent details.

Use this skill whenever the task centers on **understanding, designing, or interpreting Adobe Analytics tracking and reporting** at a conceptual level.
