# Other Contributors

> Additional participants who have shaped WebMCP through code, discussion, and expertise.

---

## Spec Co-Authors

### David Bokan — Google Chrome

**GitHub**: [bokand](https://github.com/bokand)

David Bokan is listed as a **co-author of the initial WebMCP proposal** alongside Brandon Walderman and Hannah Van Opstal. His contributions were foundational to the spec's genesis in August 2025, though his public activity in the repository has been less visible than the named editors.

David works on Chrome's rendering and input teams, bringing deep knowledge of how browser APIs interact with page content — important context for how tools are registered and invoked during page lifecycle events.

### Leo Lee — Microsoft

**GitHub**: Active early contributor

Leo Lee contributed early fixes (typo corrections in [PR #13](https://github.com/webmachinelearning/webmcp/pull/13), Aug 2025) and is listed among the initial contributors. His Microsoft affiliation alongside Brandon Walderman shows cross-team Microsoft investment in WebMCP.

### Andrew Nolan — Microsoft

Andrew Nolan represents additional Microsoft Edge investment in the specification, contributing to early review and alignment discussions.

---

## Google Chrome Contributors

### Hannah Van Opstal — Google

Listed as a **co-author** of the initial proposal. Hannah's involvement in the genesis of WebMCP helped establish Google Chrome's commitment to the specification from day one.

### Anders Ruud — Google Chrome

**GitHub**: [andruud](https://github.com/andruud)

Anders filed a **rapid series of declarative-specific issues** in February 2026, performing the deepest technical analysis of how HTML forms map to tool schemas:

| Issue | Topic | Key Question |
|-------|-------|-------------|
| [#63](https://github.com/webmachinelearning/webmcp/issues/63) | Multiple submit buttons | How to map multiple `<button type=submit>` to tool schemas |
| [#66](https://github.com/webmachinelearning/webmcp/issues/66) | Multiple labels | What happens with multiple `<label>` elements |
| [#67](https://github.com/webmachinelearning/webmcp/issues/67) | Labels as titles | Should `<label>` text become parameter `title` |
| [#68](https://github.com/webmachinelearning/webmcp/issues/68) | Date min/max schema | JSON Schema has no native date constraints |
| [#69](https://github.com/webmachinelearning/webmcp/issues/69) | `tool*` attributes on subordinates | Whether `tool-description` is allowed on individual `<input>` |
| [#71](https://github.com/webmachinelearning/webmcp/issues/71) | Radio buttons as single parameter | Mapping radio groups to enum values |

On date constraints:
> "That amounts to a micro-format within the description field, which ... isn't great" — on appending min/max text to descriptions.

### Francois Beaufort — Google Chrome

**GitHub**: [beaufortfrancois](https://github.com/beaufortfrancois)

Francois contributed to code quality and raised the **user gesture activation** question:

| Action | Reference |
|--------|-----------|
| Fixed JS examples in proposal | [PR #61](https://github.com/webmachinelearning/webmcp/pull/61), Jan 6, 2026 |
| Raised user gesture activation for PiP | [Issue #62](https://github.com/webmachinelearning/webmcp/issues/62) |
| Revealed `navigator.modelContextTesting` API | [Issue #74](https://github.com/webmachinelearning/webmcp/issues/74) comment |

His user gesture question ([Issue #62](https://github.com/webmachinelearning/webmcp/issues/62)) — whether agent-invoked tools should receive transient user activation — remains open and has no resolution. This is particularly relevant for APIs like `requestPictureInPicture()` that require user gestures.

### Andre Bandarra — Google

Andre Bandarra works on **Chrome Early Preview** and created the WebMCP **travel demo** — one of the first public demonstrations of WebMCP's capabilities. His demo showed an AI agent booking travel through a website's WebMCP tools, illustrating the end-to-end user experience.

### Johann Hofmann — Google Chrome

**GitHub**: [johannhof](https://github.com/johannhof)

Johann provided critical **security analysis**, particularly around:

- **Threat model clarity**: Pushed for clearer attacker definitions and goals
- **User gesture activation**: Cautioned against blanket granting of user activation to agent scripts
- **Input length restrictions** ([Issue #73](https://github.com/webmachinelearning/webmcp/issues/73)): Proposed restricting maximum input lengths
- **Passkey-based proof of intent**: Suggested for sensitive operations like purchases

Key quote on user activation:
> "I'm not sure just blanket granting scripts run by agents transient activation is the right solution. Most often activation is required because something will happen in the UI, and most agents won't have the ability to control it, e.g., accept permission prompts."

Three realistic threats he identified:
1. Origins trying to enforce malicious action on **other origins**
2. Third-party content attacking **first-party origin** through tools
3. Origins using prompt injection to attack **the agent/model** directly

---

## Community Contributors

### 43081j — Independent

One of the most vocal community contributors, **43081j** advocates for a **minimal web API** approach. Their contributions include:

- **CustomElementRegistry analogy** for tool registration ([Issue #15](https://github.com/webmachinelearning/webmcp/issues/15))
- **Strong pushback** on Chrome's `createClient` proposal ([Issue #51](https://github.com/webmachinelearning/webmcp/issues/51))
- **Scope clarity requests** (Issues #43, #56)
- **Concurrency perspective** from MCP experience ([Issue #47](https://github.com/webmachinelearning/webmcp/issues/47))

Key quotes:
> "Chrome has implemented this in a way that means ONLY Chrome can execute registered tools (internally). That obviously doesn't fly."
> "Much of this reads more complicated than it needs to be."
> "In our implementation we have a CustomElementsRegistry-style interface: `window.agent.tools.define(name, def)`"

### Sven Schultze — VOIX Paper Author

Joined the community in November 2025 after Anssi Kostiainen's outreach about his arxiv paper on the VOIX project. Contributed important insights on the declarative API:

> "I think it is important not to just reuse ARIA since this could lead to conflicts of interest between optimizing for accessibility or agents."

> "We established a more explicit interface where MCP tools are separated from standard UI HTML elements."

> "Is there an equivalent idea for declarative context/resources? We found that it was really helpful to explicitly set agent-only text elements."

### Minko Gechev — Angular / Google

**GitHub**: [mgechev](https://github.com/mgechev)

Submitted formatting corrections to the README ([PR #33](https://github.com/webmachinelearning/webmcp/pull/33), Oct 2025). His engagement from the Angular team signals the JavaScript framework ecosystem's interest in WebMCP.

### Ben vanderSloot — Mozilla

Asked the fundamental question in [PR #26](https://github.com/webmachinelearning/webmcp/pull/26) (Jan 2026):

> "Why have the invocation be anything different than the user action? If the tool is tied to an element that the user may otherwise interact with, it seems you have the agent do the same thing as the user."

This represents Mozilla's first public engagement with WebMCP. The question challenges whether WebMCP is needed at all if agents can simply simulate user interactions — a position that the spec's proponents counter with arguments about structured data, discovery, and security.

---

## W3C TAG Members

### Xiaocheng — W3C TAG

**GitHub**: [xiaochengh](https://github.com/xiaochengh)

TAG reviewer who proposed the **"lower-level primitives" approach** — building WebMCP from foundational capabilities (agent connection, permissions) rather than directly introducing a high-level API.

### Marcos Caceres — W3C TAG

**GitHub**: [marcoscaceres](https://github.com/marcoscaceres)

TAG member who expressed interest in reviewing WebMCP. His engagement signals broader TAG attention to the agentic web platform.

---

## Other Notable Participants

| Person | Affiliation | Contribution |
|--------|-------------|-------------|
| **Leonie Watson** | TPGi | Accessibility expert; analyzed how WebMCP intersects with screen readers and assistive tech ([Issue #65](https://github.com/webmachinelearning/webmcp/issues/65)) |
| **David Pang** | Anthropic / MCP | MCP alignment; asked about resources & prompts support |
| **Rick Viscomi** | — | Formatting corrections ([PR #42](https://github.com/webmachinelearning/webmcp/pull/42)) |
| **Tom Nicholson** | Google | SVG dark mode tips, Prompt API alignment for media types |
| **SuperPauly** | Community | Extensive enterprise proposal for concurrency and RBAC |
| **stalgiag** | Community | Observable lifecycle signals for `requestUserInteraction` abuse prevention |
| **KenjiBaheux** | Google | Per-agent tool visibility argument ([Issue #51](https://github.com/webmachinelearning/webmcp/issues/51)) |
| **Sushanth Rajasankar** | Microsoft | Warning about showing website-controlled content in privileged UI; listed in spec acknowledgements as "Sushanth Rajasankar" (GitHub: Sushraja) |
| **Rob Eisenberg** | Community | Early declarative concept proposal ([Issue #22](https://github.com/webmachinelearning/webmcp/issues/22)) |
| **Jason Mayes** | Google | Created WebMCP channel in Web AI Discord server |
| **Adam Sobieski** | Community | DevTools integration (Issue #10), MCP client capabilities (Issue #7) |
| **am2222** | Community | WebLLM / LangGraph integration question ([Issue #39](https://github.com/webmachinelearning/webmcp/issues/39)) |

---

## Accessibility Expert: Leonie Watson

**Affiliation**: TPGi · **GitHub**: [LJWatson](https://github.com/LJWatson)

Leonie Watson's analysis in [Issue #65](https://github.com/webmachinelearning/webmcp/issues/65) is one of the most forward-looking contributions:

> "Screen readers depend on platform accessibility API for deterministic information about the UI. AI agents also use the DOM and the accessibility API, and so both screen readers and agents are vulnerable when sufficient information is unavailable."

> "AI agents also use computer vision to analyze screenshots of the UI. Screen readers are increasingly using AI for this purpose."

> "It's possible that screen readers could benefit from WebMCP in the same way that agents are intended to."

Her insight — that WebMCP tools could serve assistive technologies, not just AI agents — opens a significant opportunity for the spec.

---

## See Also

- [Anssi Kostiainen](anssi-kostiainen.md) — CG Chair, community outreach
- [Khushal Sagar](khushal-sagar.md) — Spec Editor, API design lead
- [Brandon Walderman](brandon-walderman.md) — Spec Editor, initial author
- [Alex Nahas](alex-nahas.md) — Community contributor, MCP-B
- [Community Group](../10-w3c-process/community-group.md) — How to participate
- [TAG Review](../10-w3c-process/tag-review.md) — Xiaocheng and Marcos Caceres engagement
- [Core concepts and overview](../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Ecosystem overview](../01-overview/ecosystem-overview.md) — Ecosystem overview
- [Community feature requests](../13-spec-discussions/community-external-requests.md) — Community feature requests
- [Developer sentiment](../14-industry/developer-sentiment.md) — Developer sentiment
- [Miguel's Pizza polyfill](../08-ecosystem/polyfills/miguels-pizza-webmcp.md) — Miguel's Pizza polyfill
- [Ripulio polyfill](../08-ecosystem/polyfills/ripulio-web-mcp.md) — Ripulio polyfill
- [WebMCP Bridge CLI](../08-ecosystem/bridges/webmcp-bridge-cli.md) — WebMCP Bridge CLI
- [Getting started tutorial](../12-tutorials/getting-started.md) — Getting started tutorial
