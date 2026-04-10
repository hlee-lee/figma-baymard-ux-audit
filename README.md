# Figma Baymard UX Audit

A [Claude Code](https://claude.ai/code) skill that audits live websites and user flows against **Baymard Institute UX research principles**, then posts structured findings directly into Figma.

## What it does

Give it a URL or a screen recording of a user flow, point it at a Figma file with screenshots, and it will:

1. **Query Baymard UX research** via NotebookLM notebooks containing Baymard's guidelines for product pages, cart & checkout, accounts, and site-wide patterns
2. **Evaluate every visible module** against specific Baymard principles with PASS / FAIL / INCONCLUSIVE verdicts
3. **Pin findings as Figma comments** directly on the relevant area of each screenshot — one finding per pin, with the module name, interaction type, principle reference, and evaluation
4. **Generate styled summary frames** in Figma with compliance stats, a page summary, ranked priorities, and flow analysis

### Example output

**Pinned on a screenshot:**
```
FAIL
MODULE: Promo Code Field
INTERACTION: view, type
PRINCIPLE [9, 10]: Hide promo code fields by default to prevent abandonment for coupon hunting
FINDING: Promo code field is fully expanded and visible by default, driving users to abandon checkout to search for codes.
```

**Summary frames** (created as designed Figma elements, not comments):

| Frame | Contents |
|---|---|
| Baymard Compliance Summary | Color-coded verdict counts, pass rate, strongest/weakest areas |
| Page Summary | 2-3 paragraph analysis of what works and what doesn't |
| Top Priorities | 5 ranked recommendations with module, principle, and impact |
| Flow Summary | Cross-step consistency, friction points, drop-off risks |

## Requirements

### MCP Servers
- **Figma MCP** — for reading screenshot metadata and creating summary frames
- **NotebookLM MCP** — for querying Baymard UX research notebooks

### Baymard Notebooks

You need 4 NotebookLM notebooks with Baymard UX research loaded as sources. The skill discovers them by name:

| Notebook | Covers |
|---|---|
| Baymard UX - Product Page, Product Lists & Filtering | PDPs, product lists, filtering, sorting |
| Baymard UX - Cart & Checkout | Cart, checkout flow, payment, order review |
| Baymard UX - Accounts & Self Service | Login, account pages, order tracking |
| Baymard UX - Homepage, Category Taxonomy, Main Navigation, Search... | Homepage, navigation, search, site-wide patterns |

### Other
- **Figma personal access token** — for posting comments via the REST API
- **ffmpeg** (optional) — only needed if auditing a video recording of a user flow

## Usage

```
/figma-baymard-ux-audit https://example.com
```

The skill will walk you through:
1. Providing the Figma file URL with screenshots
2. Confirming the page/flow goal
3. Classifying page types and selecting relevant notebooks
4. Reviewing findings before they're posted

### Input types

- **Single page URL** — audits one live page
- **Video file path** — extracts keyframes via ffmpeg, audits each step of the flow

## How findings are delivered

| Finding type | Delivery method | Location |
|---|---|---|
| Module-level (PASS/FAIL/INCONCLUSIVE) | Figma comment (REST API) | Pinned on screenshot at the module's position |
| Steps without screenshots | Figma comment (REST API) | Grouped per step on the parent frame |
| Compliance Summary | Figma frame (`use_figma`) | To the right of screenshots |
| Page Summary | Figma frame (`use_figma`) | Below Compliance Summary |
| Top Priorities | Figma frame (`use_figma`) | Below Page Summary |
| Flow Summary | Figma frame (`use_figma`) | Below Top Priorities |

## Comment format

Every module-level finding uses this structure:

```
PASS | FAIL | INCONCLUSIVE
MODULE: [component name]
INTERACTION: [view, click, hover, scroll, type, expand, etc.]
PRINCIPLE [#]: [Baymard principle with source reference number]
FINDING: [evaluation — 1-2 sentences]
```

Principle reference numbers (e.g., `[14]`, `[32]`) come directly from the NotebookLM query responses and map to specific Baymard source documents.

## Architecture

```
┌─────────────────┐     ┌──────────────┐     ┌─────────────────┐
│  Live site URL   │     │  NotebookLM  │     │     Figma       │
│  or video file   │     │  (Baymard    │     │  (screenshots   │
│                  │     │   notebooks) │     │   + comments)   │
└────────┬────────┘     └──────┬───────┘     └────────┬────────┘
         │                     │                      │
         ▼                     ▼                      ▼
┌─────────────────────────────────────────────────────────────┐
│                    Claude Code Skill                         │
│                                                              │
│  1. Fetch page content / extract video frames                │
│  2. Identify modules and classify page types                 │
│  3. Query Baymard notebooks per module                       │
│  4. Cross-reference principles against live implementation   │
│  5. Assign PASS / FAIL / INCONCLUSIVE verdicts               │
│  6. Pin findings on Figma screenshots (REST API comments)    │
│  7. Create summary frames in Figma (Plugin API)              │
└─────────────────────────────────────────────────────────────┘
```

## License

This skill is designed for use with Claude Code. Baymard Institute research is proprietary — you must have legitimate access to their guidelines to populate the NotebookLM notebooks.
