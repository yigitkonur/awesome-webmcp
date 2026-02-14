# Spec Evolution: From Explainer to Formal Draft

PRs: #1, #3, #14, #4, #12, #13, #18, #33, #42, #46, #37, #36, #64, #72, #75, #76, #77, #78, #79, #80, #61

---

## Phase 1: Initial Explainer (Aug 2025)

### PR #1 -- The Beginning (Aug 5-6, 2025)
- **Author**: Brandon Walderman (Microsoft/Edge)
- **Content**: Initial explainer for what was then called "Script Tools API"
- 345 additions, 0 deletions
- Merged in 1 day after Khushal Sagar (Google) acknowledged

### PR #3 -- Merge Explainers (Aug 7-13, 2025)
- Merged Script Tools API and WebMCP explainers
- 751 additions, 223 deletions

### PR #4 -- Name Update (Aug 8, 2025)
- Anssi updated README to reflect "WebMCP" name
- 2 additions, 2 deletions

### PR #14 -- Consolidation (Aug 22-25, 2025)
- Moved all explainer content into README.md
- 616 additions, 622 deletions (net zero: pure reorganization)

---

## Phase 2: Community Polish (Sep-Oct 2025)

| PR | Author | What | Size |
|----|--------|------|------|
| #12 | Brandon | Add authors & date | +16 |
| #13 | Leo Lee (Google) | Fix typos | +6/-7 |
| #18 | Brandon | SVG to PNG for dark mode | +2/-4 |
| #33 | Minko Gechev (Angular) | README formatting | +10/-9 |
| #34 | Martin Joneš | Typo fix -- CLOSED (original was correct) | n/a |
| #42 | Rick Viscomi | Minor formatting | +6/-9 |

### Notable: PR #34 Rejection
Martin Joneš submitted a typo fix. Anssi asked GitHub Copilot, which disagreed. After investigation, the original form was grammatically correct. Anssi closed it graciously:
> "It sounds like the original form was grammatically correct, so I'm inclined to close this. Thanks for the suggestions regardless!"

---

## Phase 3: API Resolution Implementation (Oct-Nov 2025)

### PR #36 -- MCP Intersection Documentation (Oct 9, 2025)
- Khushal added a section explaining how WebMCP relates to MCP
- +18/-1
- Khushal: "Do let me know if there's a better place to add this since the question has come up a few times."

### PR #37 -- API Syntax Update (Oct 9, 2025)
- Updated syntax to match group resolutions
- +25/-2

### PR #46 -- Full Proposal Update (Nov 2, 2025)
- Updated proposal.md based on all resolved API decisions
- +7/-15

---

## Phase 4: Formal Spec Draft (Jan-Feb 2026)

### PR #64 -- The Big One: Spec Draft + CI/CD (Jan 21-26, 2026)
- **Author**: Anssi Kostiainen
- +110 additions: Bikeshed source, CI/CD pipeline, PR Preview setup
- Seeded with content from explainer.md and proposal.md
- Editors appointed: Brandon Walderman, Dominic Farolino, Khushal Sagar

Key comments during review:
> "I have seeded `index.bs` with some content from explainer.md and proposal.md to help avoid the Blank Page Syndrome." -- Anssi
> "I will also appoint @khushalsagar as an editor for this spec." -- Anssi

### PR #72 -- Acknowledgements (Jan 30 - Feb 2, 2026)
- Anssi added acknowledgements section
- Noted a random server deployment issue that self-resolved on re-run

### PR #75 -- WebIDL + Descriptions (Feb 5-12, 2026)
- **Author**: Brandon Walderman
- +175/-3: WebIDL interface definitions and descriptions added to spec
- Dominic rebased and pushed a conflict-resolving commit
- Merged Feb 12

### PR #76 -- Declarative API Explainer (Feb 5, 2026 - OPEN)
- **Author**: Dominic Farolino
- +120 additions
- Agenda+ label
- This is the active document defining the declarative approach

### PRs #77-80 -- Makefile Saga (Feb 5-10, 2026)
- Mark Foltz added a Makefile for Bikeshed quality of life (#77)
- Then reverted it (#78), updated (#79), and cherry-picked (#80)
- Minor build tooling churn

---

## Phase 5: Security & Privacy (Nov-Dec 2025)

### PR #55 -- Security & Privacy Living Document (Nov 14 - Dec 5, 2025)
- **Author**: Victor Huang (Google)
- +317 additions
- Based on Issue #45 discussions
- Khushal: "LGTM! Left a comment for one attack vector that deserves more thinking and can be deferred to another PR."

### PR #59 -- Tool Implementation as Attack Targets (Dec 8-12, 2025)
- **Author**: Victor Huang
- +38 additions to the security document
- Added section on how tool execute functions themselves can be exploited

---

## PR #61 -- Fix JS Examples (Jan 6-20, 2026)
- **Author**: Francois Beaufort (Google Chrome)
- Fixed JavaScript examples in proposal document
- +8/-8
- Required gentle pings to get review
