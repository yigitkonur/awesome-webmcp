# Declarative API: From HTML Forms to Agent Tools

Issues: #22, #63, #66, #67, #68, #69, #71, #8 | PRs: #26, #76

---

## Origins & Motivation

**Rob Eisenberg** (Issue #22, Sep 2025) proposed the concept early:

```html
<form tool-name="add-todo" tool-description="Add a new todo item">
  <input name="title" tool-description="The title of the todo" required>
  <button type="submit">Add Todo</button>
</form>
```

This sparked one of the longest-running discussions in the repo.

**Key motivations** for declarative (from Issue #8 resolution and Issue #22):
- Discovery/indexing: Crawlers can find tools without executing JS
- Accessibility: Screen readers and assistive tech can benefit
- Simpler adoption: No JS needed for basic forms-as-tools
- Progressive enhancement: Works alongside imperative API
- TAG feedback: "Particularly useful as MCP might need to interface with the OS statically (without the web app running)"

---

## MiguelsPizza's Full Declarative Proposal (PR #26)

Alex Nahas opened PR #26 in September 2025 with a comprehensive HTML-based approach using `tool-*` attributes. The PR accumulated 14 comments over 5 months and was a crucible for key design decisions.

### ARIA Reuse Debate

**Brandon Walderman** initially suggested reusing ARIA attributes (`aria-label`, `aria-description`) instead of new `tool-*` attributes.

**Sven Schultze** (VOIX project, joined after Anssi's outreach) pushed back:
> "I think it is important not to just reuse ARIA since this could lead to conflicts of interest between optimizing for accessibility or agents."

**Brandon later reversed** after TPAC discussion:
> "ARIA attributes for browsing agents was discussed at TPAC and the consensus is this is not actually a good idea. One of the concerns was that reusing ARIA could lead to conflicts of interest and possibly incentivize web developers to optimize for machines instead of people using a11y tools."

### Content Negotiation Concerns

**Dominic Farolino** raised a significant issue with the `Accept: application/json` approach:

> "Do we really expect server authors to supply JSON/agent-readable responses alongside traditional HTML responses, at the same endpoint? Historically, this is pretty rare."

> "I wonder if it makes sense to use something like `tool-url` that overrides `action`..."

### Mozilla Engagement

**Ben vanderSloot (Mozilla)** asked the fundamental question:
> "Why have the invocation be anything different than the user action? If the tool is tied to an element that the user may otherwise interact with, it seems you have the agent do the same thing as the user."

### Closure & Succession

PR #26 was closed Feb 12, 2026 in favor of **PR #76** -- Dominic Farolino's formal Declarative API Explainer (still open, Agenda+).

**RESOLUTION (Jan 22, 2026)**: Pursue declarative approach that complements imperative WebMCP API. Chrome began prototyping.

---

## Anders Ruud's Declarative Deep Dive (Feb 2026)

Anders Ruud (Chrome) filed a rapid series of declarative-specific issues:

### Issue #63: Multiple Submit Buttons
How to convert forms with multiple `<button type=submit>` into tool schemas:

```html
<form>
  <input type=radio name=something value=value_A>
  <input type=radio name=something value=value_B>
  <button type=submit name=action value=action_A>Action A</button>
  <button type=submit name=action value=action_B>Action B</button>
</form>
```

Maps to `enum` in JSON Schema -- shared `name` attributes produce enum arrays.

### Issue #66: Multiple Labels
What happens when a form control has multiple `<label>` elements pointing at it?

### Issue #67: Should Labels Determine 'title'?
Whether `<label>` text should automatically become the tool parameter's `title` in the schema.

### Issue #68: Date Min/Max Schema
`<input type=date min="2026-01-01" max="2026-01-31">` -- JSON Schema has no native date constraint. Options:
- Append text to `description`: "min value is 2026-01-01 and max value is 2026-01-31"
- Anders: "That amounts to a micro-format within the description field, which ... isn't great"

### Issue #69: tool* Attributes on Subordinate Elements
Whether `tool-description` etc. should be allowed on individual `<input>` elements within a form, not just on the `<form>` itself.

### Issue #71: Radio Buttons as Single Parameter
How to describe a group of radio buttons as a single parameter with enum values.

---

## Discovery as a First-Class Goal

**RESOLUTION (Oct 2, 2025, Issue #8)**: "The group wants to make tools part of the discovery mechanism and continues to explore API shapes that satisfy this requirement."

Khushal Sagar summarized offline feedback:
> "Sites will add capabilities that a human can't use directly but can via an Agent. The discovery mechanism for Agents will need a way to crawl and index these capabilities."

The declarative API is seen as essential for this -- imperative JS-only tools can't be crawled without executing the page.
