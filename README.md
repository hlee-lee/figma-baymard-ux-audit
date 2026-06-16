# Figma First-Impression Audit

A [Claude Code](https://claude.ai/code) skill that performs a **first-impression UX and content audit** of a live website, an app flow (screen recording), an existing Figma design, or another digital touchpoint, then posts findings directly into Figma — module-level observations beside a screenshot, plus synthesized page-level summary frames beside it.

## What it does

Give it a Figma file plus **any one source** — a live URL, a screen recording, or screenshots already placed in the file. It detects the touchpoint type (website / app / other), confirms the page's user goal, and then:

1. **Evaluate the page through 7 universal lenses** (+4 experience lenses, applied by touchpoint, when there's a flow or live source) — Brand TOV, Clarity, Audience Relevance, Messaging Hierarchy, CTA Clarity, Trust + Credibility, Content, and more
2. **Pin module-level observations as Figma comments** directly on the screenshot — one focused observation per pin, sentiment-marked (✅ / ⚠️ / ❌) and category-tagged
3. **Generate 8 page-level summary frames** in a styled column beside the screenshot — synthesized deliverables covering brand tone, audience, user needs, messaging, copy consistency, content gaps, page summary, and a prioritized redesign roadmap
4. **(Optional) Build a sitemap frame** for whole-site audits — an org-chart-style hierarchy of every page found, with ✓/✗ markers for Figma coverage

Every observation is anchored to **the page's user goal**. The skill writes like a senior product designer leaving notes for a colleague — not a checklist auditor.

### Example output

**Pinned on a screenshot:**
```
⚠️ [CTA Clarity] Primary and secondary CTAs use the same weight and color,
so users can't tell which action is recommended for their journey stage.
```

**Summary frames** (created as designed Figma elements, not comments):

| Frame | Contents |
|---|---|
| Brand Tone & First Impression | Brand tone read on initial impression, moments of inconsistency |
| Audience & User Value | Primary user types and what they'd find valuable |
| Core User Needs & Use Cases | Top 5 reasons users visit, key tasks the page supports |
| Messaging Clarity | Areas where messaging is vague, confusing, or under-contextualized |
| Copy Consistency & Nomenclature | Voice, terminology, grammar, naming conventions |
| Content Gaps & Category Relevance | Missing content that would support users, SEO, or authority |
| Page Summary | 2-3 paragraphs: what works, what doesn't, biggest structural observation |
| Prioritization & Redesign Roadmap | Recommended fixes grouped High / Mid / Low |

## Requirements

### MCP Servers
- **Figma MCP** — for reading screenshot metadata and creating summary frames via `use_figma`

### Other
- **ffmpeg** — required only when the source is a screen recording; used to extract keyframes that are auto-placed in Figma
- **Playwright MCP** — used to capture screenshots when only a live URL is provided and the Figma file has none yet (web only)
- **Live URL access** (optional) — one of the accepted sources; enables the live-source pass via WebFetch

## Usage

```
/figma-firstimpression-audit https://example.com
```

The skill will walk you through:
1. Confirming the destination Figma file URL and the source (URL, recording, or existing screenshots) — and detecting the touchpoint type
2. Acquiring screenshots (ffmpeg keyframes for a recording, Playwright capture for a URL, or existing Figma screenshots)
3. Defining (or inferring) the page's primary user goal
4. Choosing audit scope — just this page/screen, or the whole flow/site (4–8 representative pages)
5. Opt-in for a sitemap deliverable (whole-site websites only)
6. (Optional) Brand guidelines / TOV doc / design system reference

### Input types

Always provide a Figma file URL (the destination), plus **at least one source**:

- **Live URL** — best for websites. If the Figma file has no screenshots yet, they're captured via Playwright; if it already has them, those are audited.
- **Screen recording** — best for apps and multi-step flows. Keyframes are extracted via ffmpeg and auto-placed in Figma.
- **Existing Figma screenshots** — audited directly (no URL or recording needed).

The 7 universal lenses always run. The 4 experience lenses run when there's a flow or live source, applied by touchpoint — websites get all four; apps/other touchpoints get User Experience, UX Copy + Microcopy, and UI Design, with Search + Discoverability limited to in-touchpoint findability (no SEO items).

## How findings are delivered

| Finding type | Delivery method | Location |
|---|---|---|
| Module-level pin (✅ / ⚠️ / ❌) | Figma comment (REST API) | Pinned on screenshot at the module's position |
| 8 summary frames | Figma frame (`use_figma`) | Vertical column, 80px gutter to the right of the screenshot |
| Sitemap (opt-in) | Figma frame (`use_figma`) | To the right of the summary column, top-aligned |

## Comment format

Every module-level pin uses this structure:

```
<sentiment> [<Category>] <Observation, 1-2 sentences>
```

- Sentiment: `✅` working well, `⚠️` opportunity, `❌` issue
- Category: one of the 11 canonical tags (e.g., `[CTA Clarity]`, `[Brand TOV]`, `[UI Design]`)
- Observation: one focused point, tied back to the page goal — no jargon, no implementation references

## Evaluation lenses

**Universal (always run):**
- Brand TOV
- Clarity + Comprehension
- Audience Relevance
- Messaging Hierarchy
- CTA Clarity
- Trust + Credibility
- Content

**Experience lenses (run when there's a flow or live source; applied by touchpoint):**
- User Experience
- Search + Discoverability *(websites: full; apps/other: in-touchpoint findability only)*
- UX Copy + Microcopy
- UI Design

## Architecture

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│  Figma file URL │     │  Live site URL   │     │     Figma       │
│  (screenshot)   │     │  (optional)      │     │ (comments +     │
│                 │     │                  │     │  summary frames)│
└────────┬────────┘     └────────┬─────────┘     └────────┬────────┘
         │                       │                        │
         ▼                       ▼                        ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Claude Code Skill                           │
│                                                                  │
│  1. Verify Figma MCP + personal access token                     │
│  2. Confirm page user goal (provided or inferred)                │
│  3. Discover screenshot placement via get_metadata               │
│  4. Run lens passes in parallel (WebFetch for URL pages)         │
│  5. Map modules to coordinates on the screenshot                 │
│  6a. Post module pins via Figma REST API (parallel batches)      │
│  6b. Build 8 summary frames in one use_figma call                │
│  6c. (Opt-in) Build org-chart sitemap as separate deliverable    │
│  7. Report counts by sentiment and category                      │
└─────────────────────────────────────────────────────────────────┘
```

## Re-running an audit

Ask the skill to revise or redo, and it will:
1. Delete all prior pin comments via the Figma REST API
2. Delete prior summary frames matching `High-Level Overview — *` via `use_figma`
3. Repost / rebuild with the updated findings

## License

This skill is designed for use with Claude Code.
