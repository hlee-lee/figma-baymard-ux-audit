---
name: figma-firstimpression-audit
description: First-impression audit of a live website, an app flow (screen recording), an existing Figma design, or any other digital touchpoint. Creates module-level observation cards (sticky-note style frames) placed next to each screenshot, plus page-level summary frames in a column beside the screenshots. Use when the user says "audit", "site audit", "app audit", "page audit", "first impression audit", "UX audit", "review this page/flow", or provides a URL, a screen recording, or a Figma link for review purposes.
disable-model-invocation: true
argument-hint: "[figma URL] [optional site URL or recording path]"
---

# Figma First-Impression Audit

Audit a digital touchpoint — a live website, an app flow captured as a screen recording, an existing Figma design, or another touchpoint — through multiple evaluation lenses and produce two outputs in Figma:

1. **Module-level observation cards** — small colored frames placed beside each module on the screenshot. One focused observation each, sentiment-color-coded and lens-tagged. (Replaces older pin-comment workflow — pin comments require users to click each pin one at a time; in-canvas cards are always visible.)
2. **Page-level summary frames** — synthesized first-impression deliverables placed in a column beside the rightmost screenshot.

---

## Core Principle

Every observation must tie back to the **page's user goal**. Don't describe what a module is — evaluate whether it helps or hinders the goal. Write like a senior product designer leaving notes for a colleague.

---

## Audit Categories

The audit produces two kinds of output:

- **Module-level observation cards** — discrete observations from 7 evaluation lenses (+4 website-only lenses). Each is a small Figma frame placed beside the relevant module on a screenshot.
- **Page-level summary frames** — 8 synthesized outputs placed beside the rightmost screenshot as Figma frames.

### Module-Level Evaluation Lenses (drive observation cards)

#### Brand TOV
- Measure brand TOV consistency
- Identify shifts in tone across pages, modules, CTAs, or error states
- Highlight brand assets that do not reflect defined art direction principles (including logo/logotype usage)
- Track usage of brand voice characteristics based on guidelines provided
- Identify copy that reads as internal business-speak, salesy, generic, or off-TOV

#### Clarity + Comprehension
- Does the page clearly communicate what this product, service, or experience is?
- Highlight confusing messaging and complex directional copy
- Are headlines overly generic?

#### Audience Relevance
- Identify the target audience
- Highlight copy that is deep into category knowledge (assumes expertise)
- Does the language identify the priority audience or customer segments?

#### Messaging Hierarchy
- Define the most important key message(s)
- Does language lean toward useful function or persuasive information?
- Is there a clear copy structure from headline → body → CTA?
- Does supporting copy reinforce the primary message, or distract from it?

#### CTA Clarity
- Are CTAs specific and action-oriented?
- Are primary and secondary CTAs clearly differentiated?
- Does the user know what will happen after clicking?
- Are there too many CTAs competing for attention?
- Does the CTA match the user's likely mindset at that point in the journey?

#### Trust + Credibility
- Identify broken links and errors on the site
- Identify low-res assets, misspelled words, inconsistent size and scale

#### Content
- Identify the breakdown between educational, promotional, inspirational, and support content
- Identify AI-generated assets — do they feel accurate, brand-appropriate, high-quality, and legally usable?
- Track consistency of product descriptions, value propositions, and brand story
- Are key details, proof points, FAQs, comparisons, or how-tos missing or buried?
- Are product, category, branded, or technical terms easy to understand?

### Experience Lenses (run when there is a captured flow or live source to evaluate — i.e. a live URL, a screen recording, or app/touchpoint screenshots; skip when auditing a single static Figma frame with no flow)

These four lenses evaluate the live/interactive experience. Apply them by touchpoint:
- **Website** — run all four against the live URL (via WebFetch) and any captured screenshots.
- **App / other digital touchpoint** — run **User Experience**, **UX Copy + Microcopy**, and **UI Design** against the recording keyframes and placed screenshots. For **Search + Discoverability**, evaluate only the parts that apply to the touchpoint (in-app search, naming, findability of features); skip the SEO/answer-engine items, which are web-specific.

#### User Experience
- Summarize Baymard best practices relevant to the page
- Identify or sketch the site map; highlight interior pages, user flows, hidden pages, and linked URLs not represented in main nav or footer *(checkpoint: does the level of depth match the SOW?)*
- Highlight primary and secondary user flows
- UX affordance and visual cues — identify loaders, confirmations, error states, tooltips *(checkpoint: how does this compare to the design system's depth?)*
- Highlight variant use cases (e.g., pricing, promotional item vs. suites and grouping)

#### Search + Discoverability
- Identify copy using jargon, brand-specific language, or clever phrasing in place of common natural language terms users search for
- Flag missing product, category, use-case, occasion, ingredient, feature, or benefit terms users would expect on this page type
- Note misalignment between page titles, headings, and body copy that would make the primary topic ambiguous to search engines or users
- Surface common questions related to this page's topic that are not directly addressed in the content
- Document inconsistencies in how key terms are used across navigation, headers, filters, product cards, PDPs, FAQs, and CTAs
- Call out instances where clever, playful, or branded copy obscures meaning or replaces searchable terms
- Assess whether the page provides sufficient context (descriptive copy, structured content, supporting detail) for search engines, AI answer engines, and users to clearly understand what the page is about and who it is for

#### UX Copy + Microcopy
- Inventory navigation labels, filters, CTAs, form labels, helper text, tooltips, error messages, confirmation messages, empty states, and loaders for clarity gaps and inconsistencies in terminology or tone
- Identify microcopy that fails to help users make a decision, complete a task, or understand what is expected of them at that moment in the flow
- Flag confirmation messages that are generic, vague, or missing information the user needs to feel confident the action was completed successfully
- Document error states that do not explain what went wrong, why it happened, or what the user should do to resolve it
- Call out CTAs where the label does not accurately reflect what will happen after clicking, or where the action, destination, or outcome is ambiguous
- Note any instructions, labels, or support messages that use technical language, internal terminology, or phrasing that assumes more user knowledge than is reasonable
- Identify specific moments where copy creates friction, hesitation, confusion, or causes the user to pause, second-guess, or take an unnecessary action

#### UI Design
- Measure brand system consistency vs. current design principles — what are the outliers?
  - Color hex values
  - Typography styles: fonts, sizes, line spacing
  - Grid usage and spacers (margins, gutters, etc.)
  - ADA compliance: highlight areas below AA
- Responsive design: identify design deviations from desktop to mobile
- Key components and core modules: is usage and styling consistent? (e.g., controllers, product cards, hero)

### Page-Level Deliverable Frames

Each becomes one summary frame placed in a column beside the screenshot.

1. **Brand Tone & First Impression** — Define the brand tone based on initial impression. Identify moments where tone feels inconsistent, unclear, or misaligned.
2. **Audience & User Value** — Identify the primary user types who would find value in the site (based on content, messaging, overall experience).
3. **Core User Needs & Use Cases** — Define the top 5 reasons users may visit. Identify the key tasks, questions, or needs the site appears designed to support.
4. **Messaging Clarity** — Identify areas where messaging may create confusion, feel overly vague, or require additional context to understand the brand, offering, or next step.
5. **Copy Consistency & Nomenclature** — Assess copy for consistency in voice, terminology, grammar, and naming conventions. Highlight contradictions, confusing language, or opportunities to clarify copy standards.
6. **Content Gaps & Category Relevance** — Determine whether key content types are missing that could better meet user expectations, support SEO, or strengthen authority within the brand's category.
7. **Page Summary** — 2-3 short paragraphs: what the page does well, where it falls short, and the biggest structural observation.
8. **Prioritization & Redesign Roadmap** — Recommended fixes organized into **High / Mid / Low** priorities based on impact on conversion, user clarity, and overall experience.

---

## Canonical Category Tag Names

Observation card titles tag their lens with the exact strings below — never abbreviate or rephrase. Consistent tags let re-runs group and re-post cleanly.

```
[Brand TOV]
[Clarity + Comprehension]
[Audience Relevance]
[Messaging Hierarchy]
[CTA Clarity]
[Trust + Credibility]
[Content]
[User Experience]
[Search + Discoverability]
[UX Copy + Microcopy]
[UI Design]
```

---

## Output Delivery

Both outputs use the Figma Plugin API via the `figma-use` skill.

- **Observation cards** → `use_figma` (Plugin API). Created as small colored frames placed beside the relevant module on a screenshot.
- **Page summary frames** → `use_figma` (Plugin API). Created as larger frames in a vertical column beside the rightmost screenshot.

**MANDATORY:** invoke the `figma-use` skill BEFORE every `use_figma` call. Never call `use_figma` directly. No Figma personal access token or REST API is required — everything happens through the Plugin API.

---

## Step 0 — Preflight: Verify Figma MCP Connection (Blocking)

Call Figma `whoami`.

- If it succeeds, the connection is live — proceed.
- If Figma MCP fails, guide the user:
  - **Terminal/CLI:** "Figma MCP is not connected. Check your MCP configuration and try again."
  - **Claude Code desktop or web app:** "Figma MCP is not connected. Open Settings > MCP Servers and ensure the Figma server is enabled and authenticated."

Do NOT proceed until Figma MCP is confirmed working.

---

## Step 1 — Gather Inputs (Blocking)

Required always:
1. **Figma file URL** — the destination board (extract `fileKey` and `nodeId`). This is where observation cards and summary frames are created, and where screenshots live (either already placed by the user, or auto-placed by this skill).

Required — at least ONE source to audit (the user may provide more than one):
2. **Site URL** — a live page/site to audit. Enables the live-source pass via WebFetch and Playwright capture. Best for websites.
3. **Screen recording** — a local video file (MP4, MOV, WebM) of a flow on a website OR a native/web app. The skill extracts keyframes via ffmpeg and auto-places them in Figma (see Step 1b). Best for apps and multi-step flows.
4. **Existing Figma screenshots** — screenshots the user has already placed in the submitted Figma file. The skill detects and audits these directly.

Video is **not** the primary input — any one of (2), (3), or (4) is sufficient. If none of them is present (only a bare Figma file with no screenshots, no URL, no recording), ask the user for a source. Do NOT proceed without a Figma file URL and at least one source.

### Detect the touchpoint type

Classify what is being audited — it gates the Experience Lenses and shapes the summary frames:
- **Website** — a live URL is provided, or the screenshots/recording are clearly of a website.
- **App** — a recording or screenshots of a native or web app (no public per-screen URL). Sign-up, onboarding, rewards/loyalty flows, etc.
- **Other digital touchpoint** — kiosk, email, in-product surface, embedded widget, etc.

Infer the type from the inputs and content; if genuinely ambiguous, ask the user one short question to confirm. Carry the detected type through Steps 4–6.

Always ask:
5. **Audit scope** — "Should I audit just this one page/screen, or the whole flow/site?" For a website: single page vs. homepage + key interior pages (PDP, PLP, About, etc.). For an app/flow: a single screen vs. the full captured flow. Default to single-page/single-screen if the user has no strong preference. For "whole site," crawl 4–8 representative pages and treat the Figma screenshot as the anchor; cross-page findings feed the summary frames.
6. **Sitemap deliverable** (only ask if scope = whole-site, website touchpoint) — "Want a sitemap frame included as the final deliverable (hierarchical tree of every page found, with ✓/✗ markers for Figma coverage)?" If yes, build the Sitemap frame as part of Step 6b. If no, skip it. Ask this **upfront in Step 1** — not after the audit is finished.

Optional but sharpens output:
7. **Brand guidelines / TOV doc / design system reference** — referenced by the Brand TOV and UI Design lenses

If any required input is missing, ask. Do NOT proceed until a Figma file URL and at least one source are present.

---

## Step 1b — Acquire Screenshots (Blocking)

Get the screens to be audited **directly inside the node the user linked** (the `nodeId` from their Figma URL — a frame or section). The user's node IS the destination. The goal is a set of placed screenshot image nodes that observation cards sit beside.

**Placement rules (apply to every path that places images — shared with the Baymard audit):**
- Place screenshots as **direct children of the user's node** — do NOT wrap them in a new container/"audit" frame (that creates a confusing double-frame), and do NOT place them as a sibling beside the node. *(Fallback: if the URL has no `nodeId`, create ONE holding frame/section to act as the destination.)*
- Children of a SECTION/FRAME use coordinates **relative to that node's origin**, not page coordinates.
- Lay screenshots in a horizontal row with a **wide ~400px gap** (`x = 120 + i*(W+400)`, `y = 120`) so a 260px observation-card column fits to the right of each screen. Name each `Screen N — <short label>` (1-indexed).
- **Grow the node** with `resizeWithoutConstraints` to fit the row plus a right-hand lane (past the last screen's card column) for summary frames.
- **Image fills — reliable pipeline, used from the start (do NOT inline base64):** the `use_figma` `code` field caps at ~50k chars, so `figma.createImage(<inlined bytes>)` is not viable. Instead: create gray placeholder rectangles → `upload_assets` (count ≤5/call) for submit URLs → `POST` each PNG's raw bytes (`curl --data-binary @f.png -H "Content-Type: image/png"`) → **capture each `imageHash` from the POST response** → set `fills = [{type:"IMAGE", scaleMode:"FILL", imageHash}]` explicitly per rectangle. Do NOT rely on `upload_assets`' `nodeId` auto-fill (it often leaves a gray placeholder). **Verify** with a screenshot that no rectangle is gray before continuing.

### Path A — A recording was provided → extract keyframes via ffmpeg and place inside the node

This mirrors the Baymard audit's frame pipeline.

1. **Verify ffmpeg:** run `which ffmpeg`. If missing, guide the user to install it:
   - **Terminal/CLI:** "ffmpeg is required to extract frames from the recording. Run `brew install ffmpeg` and let me know when it's done."
   - **Desktop/web app:** "ffmpeg is required to process the recording. Open a terminal (Spotlight → Terminal, or Applications → Utilities), paste `brew install ffmpeg`, and wait for it to finish. If you don't have Homebrew, first run `/bin/bash -c \"$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)\"`. Say 'done' when complete."
   - Do NOT proceed until ffmpeg is confirmed.
2. **Extract keyframes** via scene-change detection into a clean working directory:
   ```
   rm -rf /tmp/firstimpression_frames && mkdir -p /tmp/firstimpression_frames
   ffmpeg -i "<video_path>" -vf "select='gt(scene,0.3)'" -fps_mode vfr /tmp/firstimpression_frames/frame_%03d.png
   ```
   Use `-fps_mode vfr` (not the deprecated `-vsync vfr`).
3. **Confirm step frames:** read the extracted frames as images, present them, and ask the user to confirm which represent distinct screens (drop duplicates and transition frames). Each confirmed frame becomes one audited screen.
   - **Re-extract at a lower threshold when screens are missing.** The `0.3` scene threshold reliably catches screens the user dwelt on, but **misses screens navigated through quickly** (e.g., reward-detail pages skimmed briefly, modals, intermediate states). If the user mentions a screen, stage, or "hesitation" not in the set, re-extract the whole video at a lower threshold (`select='gt(scene,0.1)'`) into a separate dir — this typically yields many more frames. Locate the relevant stretch (the missing screens cluster near where they sit in the flow), build a small contact sheet of just that region to identify the distinct screens, and confirm the additions with the user before placing. This is a normal second pass, not a failure of the first.
4. **Place confirmed frames inside the user's node** following the **Placement rules** above (load the `figma-use` skill first; always pass `skillNames: "figma-use"`): get the frames' pixel dimensions (`sips`), create named gray placeholder rectangles as direct children of the node in a ~400px-spaced row, upload via `upload_assets` + POST, capture each `imageHash`, and set the IMAGE fills explicitly. Capture the rectangle node IDs in step order (these are the screenshot image nodes for Step 3). Verify no gray placeholders, then tell the user: "Placed N screenshots directly inside your '<node name>' frame. Take a quick look and confirm before I continue." Wait for confirmation.

### Path B — Only a URL was provided (no recording) → detect existing, else capture via Playwright

1. **First, detect existing screenshots in the submitted Figma file:** call `get_metadata` on the provided `nodeId` (or the file root). If the file already contains placed page screenshots, use those — do NOT re-capture. Tell the user which screenshots you found and that you'll audit them.
2. **If no usable screenshots exist, capture via Playwright** (live web only): use the Playwright MCP to navigate the URL (and, for whole-site scope, each representative page), screenshot each full page, then place the captures inside the user's node using the same **Placement rules** above (direct children of the node, ~400px-spaced row, `upload_assets` + explicit `imageHash` fills — no wrapper container). Confirm placement with the user before continuing.
   - If Playwright isn't available, tell the user (and, per the Playwright-MCP note, suggest checking `which node`) and offer to proceed by auditing the live URL via WebFetch with whatever screenshots already exist in Figma.

### Path C — Only existing Figma screenshots (no URL, no recording)

Audit the screenshots already placed in the file (current Figma-only behavior). Proceed to Step 3 directly.

> If both a recording and a URL are provided, run Path A for the screenshots and still use the live URL via WebFetch in Step 4.

---

## Step 2 — Identify Page Goal (Blocking)

Ask:

"What is the primary goal of this page? For example: build trust and drive purchase, generate leads, educate and retain users, etc. — or I can infer it from a first-impression scan of the page."

### Rules
- This goal anchors every observation in the audit
- If the user provides it upfront, confirm it back
- If the user asks you to infer it, do a first-impression scan (screenshot + live URL hero/nav/CTAs) and propose a one-sentence goal, then confirm
- If unclear, suggest a goal based on the page type (homepage, PDP, landing page, etc.) and confirm
- Do NOT proceed until the goal is agreed upon (an inferred goal counts as agreed once the user confirms or proceeds without objecting)

---

## Step 3 — Discover the Figma Layout

Before auditing, understand the screenshot placement. If Step 1b auto-placed frames (Path A) or captures (Path B), reuse the image rectangle node IDs it returned instead of re-discovering them.

1. Call `get_metadata` on the provided `nodeId` to find the screenshot image node(s) — IDs, positions, dimensions
2. Call `get_screenshot` on each image node to visually identify modules and estimate their Y-positions within the image. For whole-site audits, render multiple key screenshots individually at higher resolution to identify which page each represents

Record per screenshot:
- **Image node ID** — the parent for observation cards (cards live as siblings, positioned beside the image)
- **Image x, y, width, height** (absolute or relative to the section/board)
- **Page identity** — which live URL this screenshot corresponds to (so observation cards can be matched to the right screenshot)
- **Parent section/board ID** — for placing cards and summary frames as children of the same container

---

## Step 4 — Audit the Page

Run multiple lens passes in parallel. Use the best available source for the touchpoint: a live URL via `WebFetch`; recording keyframes / placed screenshots via `get_screenshot` plus visual inspection. When both a URL and screenshots exist, cross-reference them.

Run these 4 fetches in parallel always:

1. **Voice & Tone** — covers Brand TOV; feeds the Brand Tone & First Impression summary
2. **Messaging & Clarity** — covers Clarity + Comprehension, Audience Relevance, Messaging Hierarchy; feeds Messaging Clarity, Audience & User Value summaries
3. **CTA & Trust** — covers CTA Clarity, Trust + Credibility
4. **Content** — covers Content lens; feeds Content Gaps & Category Relevance, Core User Needs & Use Cases summaries

Run this 5th pass when there is a flow or live source to evaluate (a live URL, a recording, or app/touchpoint screenshots) — skip it only for a single static Figma frame with no flow:

5. **Experience** — covers User Experience, Search + Discoverability, UX Copy + Microcopy, UI Design. Apply by touchpoint per the Experience Lenses section: for a **website**, run all four against the live URL and screenshots; for an **app / other touchpoint**, run User Experience, UX Copy + Microcopy, and UI Design against the keyframes/screenshots, and limit Search + Discoverability to in-touchpoint findability (skip SEO/answer-engine items).

Each pass should be loaded with the specific questions from that lens (above), not just the lens name. Frame every finding through: "Does this help or hinder the user goal?"

---

## Step 5 — Map Observations to Screenshot Positions

For each finding, decide:

1. **Which screenshot** does this observation belong to? Match to the page screenshot whose live URL the observation came from. For cross-page observations, attach to the homepage/anchor screenshot.
2. **Where vertically** within that screenshot? Estimate the module's Y-position (hero, product grid, footer, etc.).
3. **Sentiment** — ✅ working well, ⚠️ opportunity, ❌ issue. This drives the card's header color.

### Missing pages (whole-site audits)
If you discover a page on the live site that has NO matching screenshot in the Figma file, still create an observation card. Anchor it to the nearest related screenshot (e.g., a missing PDP → card beside the PLP screenshot) and prefix the card body with `[MISSING PAGE: <url or page name>]` so the user knows the screenshot is absent. Also list all missing pages in the Content Gaps & Category Relevance summary frame.

---

## Step 6a — Create Module-Level Observation Cards

**Invoke the `figma-use` skill BEFORE the `use_figma` call.** This is mandatory.

### Card content format

Each card has a colored header band + white body:

- **Header (bold, small):** `<emoji> [<Category>] <Short label>` — e.g., `⚠️ [CTA Clarity] Competing CTAs in hero`
- **Body (regular):** the 1-2 sentence observation — direct, specific, tied to the page goal

### Sentiment markers + header colors

| Sentiment | Marker | Header fill | Header text |
|---|---|---|---|
| Working well, supports goal | `✅` | `#D9F2DB` (sage green) | `#1F4F2A` |
| Opportunity, could better serve goal | `⚠️` | `#FBEFBC` (cream yellow) | `#5C4A0E` |
| Issue, actively hinders goal | `❌` | `#FBD4D4` (peach pink) | `#6B1F1F` |

If the user supplies a brand palette, swap these hex values to match while preserving the sentiment-by-hue convention (green = positive, yellow = caution, red/pink = problem).

### Card visual specs

- **Width:** 260px (fixed)
- **Height:** auto (grows with content)
- **Auto-layout:** vertical, 0px gap (header sits flush against body)
- **Corner radius:** 8px
- **Border:** 1px solid `#D9D9D9`
- **Drop shadow:** subtle — `0 2 6 rgba(0,0,0,0.08)` (optional but improves canvas legibility)
- **Header band:** auto-layout horizontal, 10px vertical padding, 12px horizontal padding, header fill from table above, text 12px bold, line-height 1.4, color from table above
- **Body band:** white fill, auto-layout vertical, 12px padding on all sides, text 12px regular, line-height 1.5, color `#1A1A1A`

**Construction order — avoid collapsed (1px-tall) cards:** `node.resize()` resets a frame's sizing modes to FIXED, so order matters. Create the card frame and set `layoutMode = "VERTICAL"` → `appendChild` to the node → `resize(260, 1)` → **THEN** set `counterAxisSizingMode = "FIXED"` (lock width) and `primaryAxisSizingMode = "AUTO"` (height hugs). Setting AUTO *before* `resize()` makes the resize re-fix the height at 1px and every card collapses to a sliver. For header/body child frames: `appendChild` first, then `layoutSizingHorizontal = "FILL"`, `layoutSizingVertical = "HUG"`. For text: `appendChild`, then `layoutSizingHorizontal = "FILL"` + `textAutoResize = "HEIGHT"`. After a batch, verify card heights are ~150–250px (not 1px); if any are 1px, re-set `primaryAxisSizingMode = "AUTO"`.

### Card placement

- **X position:** placed to the right of the relevant screenshot, with an 16px gap from the screenshot's right edge — `card.x = screenshot.x + screenshot.width + 16`
- **Y position:** aligned to the module's Y-position within the screenshot — `card.y = screenshot.y + module_y_offset`
- **Stacking:** if multiple cards target the same module, stack them vertically with 8px between cards
- **Overlap protection:** if cards would collide with the next screenshot to the right, shift the card further right (e.g., place above or below the screenshot grid) — never overlap two screenshots
- If the inter-screenshot gap is too narrow (< 280px), place cards in a "card lane" directly below the screenshot column instead

### Frame naming

Name each card frame: `Audit Card — <Sentiment> — <Lens> — <Screenshot identifier>` — e.g., `Audit Card — Opportunity — CTA Clarity — Homepage`. This enables re-run cleanup via name pattern matching.

### Writing rules
- One observation per card — never combine multiple points
- Use the **canonical category tag** verbatim (see list above)
- Header label is a short 3-7 word handle; body carries the full thought
- Tie every note back to the page goal and the user's experience
- Don't describe what the module is — evaluate what it does for the user
- Don't make assumptions about the industry, competitors, or time period
- Don't reference technical implementation (HTML, CSS, ARIA, tabindex, schema)
- Write in plain, direct language — no jargon, no hedging
- Suggest specific improvements where relevant (e.g., "a benefit line like 'our #1 cream for dry skin' would help")

### Batching

Create cards in batches of 8-12 per `use_figma` call to keep payloads manageable. Group by screenshot so related cards are placed together.

---

## Step 6b — Build Page-Level Summary Frames

**Invoke the `figma-use` skill BEFORE calling `use_figma`.** This is a hard requirement of the Figma Plugin API workflow.

### Frame layout

- Placed **immediately to the right of the rightmost screenshot** in the section/board — not beyond all screenshots at the section's far edge. If the section contains 11 page screenshots stacked horizontally, frames sit 80px to the right of screenshot #11, not 80px to the right of the section bounding box.
- **80px gutter** between the rightmost screenshot (and any cards attached to it) and the first frame
- Frames are **children of the section/board**, not siblings of it. If the section isn't wide enough, resize the section's width so frames + 80px right margin fit
- Stacked **vertically**, **24px gap** between frames
- Top frame's Y-position should align with the **top of the rightmost screenshot** (so the column reads as paired with it)

### Per-frame specs

- **Width:** 480px (fixed)
- **Height:** auto (grows with content)
- **Auto-layout:** vertical, 24px padding on all sides, 16px gap between title and body
- **Background:** white (`#FFFFFF`)
- **Border:** 1px solid neutral (`#E5E5E5`)
- **Corner radius:** 8px
- **Font:** system default (Inter if available)
- **Title:** 20px, bold, line-height 1.3
- **Body:** 14px, regular, line-height 1.5

### Frame names (exact strings — used for re-run cleanup)

```
High-Level Overview — Brand Tone & First Impression
High-Level Overview — Audience & User Value
High-Level Overview — Core User Needs & Use Cases
High-Level Overview — Messaging Clarity
High-Level Overview — Copy Consistency & Nomenclature
High-Level Overview — Content Gaps & Category Relevance
High-Level Overview — Site Summary
High-Level Overview — Prioritization & Redesign Roadmap
```

### Sitemap — separate visual deliverable (whole-site audits only, opt-in)

The Sitemap is **not** part of the High-Level Overview column. It is a separate visual deliverable produced only when the user opts in via Step 1 question 4.

**Format:** a traditional org-chart-style hierarchy — boxes per page, elbow connectors, tree from left → right, with `✓` (Figma screenshot present), `✗` (referenced or live, no screenshot), and `▣` (category container, not a page) markers. Do **not** use an ASCII tree.

**Construction:**
1. Wrapper frame named `Audit Sitemap — <Site or brand>` with 8px corner radius, 1px neutral border, white fill. Width and height grow to fit the tree.
2. Title text `Audit: Sitemap` (Inter Bold 24) + legend row at the top.
3. Recursively lay out the IA: x by depth (column = `depth × (BOX_W + COL_GAP)`), y by leaf order (leaves stacked vertically, parents centered between their first and last child).
4. Connectors: orthogonal 3-segment "elbow" lines (parent right → mid-X → child left), drawn before boxes so boxes overlay them. Each segment is a 1px-thick rectangle, neutral gray fill.
5. Boxes:
   - **Pages with screenshot (`✓`)**: white fill, solid 1px medium-gray border, page name (Inter Bold 12) + path (Inter Regular 10, muted).
   - **Pages without screenshot (`✗`)**: very light gray fill, **dashed** 1px border (`dashPattern: [4, 3]`), same typography.
   - **Category containers (`▣`)**: pale beige fill, solid 1px warm border, name only (no path).

**Placement:** to the **right of the High-Level Overview column** at all times, top-aligned with the first overview frame, with an **80px gutter** between the rightmost overview frame and the sitemap. Expand the section/board width to accommodate. Do **not** place the sitemap below the screenshot grid, beneath the overview column, or inside the overview column.

**Coverage observations:** when the sitemap is created, also reflect the same `✗` pages in the `High-Level Overview — Content Gaps & Category Relevance` frame body so the missing-page coverage is reinforced in both deliverables.

**Layer name vs visible title** — these intentionally differ:
- Frame **layer name** uses `High-Level Overview — <Deliverable>` (used for cleanup/re-runs).
- Frame **visible title text** uses `Audit: <Deliverable>` (what reviewers see on the canvas).

Also, place all 8 summary frames **inside** the screenshot's containing section/board (not as siblings). If the section isn't wide enough, resize the section's width so frames + 80px right margin fit; preserve frames' absolute visual positions when reparenting.

### Frame content
- **Title row:** the deliverable name (without the "Audit — " prefix, e.g., "Brand Tone & First Impression")
- **Body:** the synthesized deliverable content, written in plain language. No code, no markup references. No sentiment markers (those are scoped to observation cards).
- **For the Roadmap frame:** structure body as three sub-sections with bold sub-headings — **High**, **Mid**, **Low** — each containing 2-4 ranked, actionable recommendations (1-2 sentences each, framed by impact on the page goal).

### Batching

Create all 8 summary frames in a **single** `use_figma` call. This avoids multiple plugin round-trips and keeps placement consistent.

---

## Step 7 — Confirm

After card-creation and frame-creation, report:

- Count of observation cards by sentiment (✅ / ⚠️ / ❌) and by lens category
- List of summary frames created, with their node IDs and the canonical names above
- Confirm the section/board was resized if needed
- Invite the user to review and adjust

---

## Card Creation Reference (Plugin API)

Inside one `use_figma` call (after loading the `figma-use` skill), iterate over the observations grouped by screenshot. For each observation:

1. Create a parent frame named per the convention in Step 6a — auto-layout vertical, 260px wide, corner radius 8, stroke `#D9D9D9` 1px, optional shadow
2. Inside it, create a header child frame — full-width, horizontal auto-layout, 10/12px padding, fill from sentiment table, text "<emoji> [<Lens>] <short label>" 12px bold in header text color
3. Inside it, create a body child frame — full-width, vertical auto-layout, 12px padding, white fill, text 12px regular in `#1A1A1A`
4. Position parent at `(screenshot.x + screenshot.width + 16, screenshot.y + module_y_offset)`; offset y by previous-card-on-same-module height + 8px if stacking

Pseudocode (illustrative — actual call uses the figma-use skill conventions):

```js
// Load fonts first: await figma.loadFontAsync({family:"Inter",style:"Bold"}) and ...{style:"Regular"}
const card = figma.createFrame();
card.name = "Audit Card — Opportunity — CTA Clarity — Homepage";
card.layoutMode = "VERTICAL";
card.itemSpacing = 0;
card.cornerRadius = 8;
card.fills = [{ type: "SOLID", color: hex("#FFFFFF") }];
card.strokes = [{ type: "SOLID", color: hex("#D9D9D9") }];
userNode.appendChild(card);            // append to the user's node BEFORE resize
card.resize(260, 1);                   // set width
card.counterAxisSizingMode = "FIXED";  // width stays 260...
card.primaryAxisSizingMode = "AUTO";   // ...height hugs — MUST come AFTER resize(), or the card collapses to 1px
card.x = screenshot.x + screenshot.width + 16;  // node-relative coords
card.y = screenshot.y + moduleYOffset;

const header = figma.createFrame();
header.layoutMode = "HORIZONTAL";
header.paddingTop = 10; header.paddingBottom = 10;
header.paddingLeft = 12; header.paddingRight = 12;
header.fills = [{ type: "SOLID", color: hex("#FBEFBC") }];
card.appendChild(header);
header.layoutSizingHorizontal = "FILL"; header.layoutSizingVertical = "HUG";
const headerText = figma.createText();
headerText.fontName = { family: "Inter", style: "Bold" };
headerText.characters = "⚠️ [CTA Clarity] Competing CTAs in hero";
headerText.fontSize = 12;
header.appendChild(headerText);
headerText.layoutSizingHorizontal = "FILL"; headerText.textAutoResize = "HEIGHT";

const body = figma.createFrame();
body.layoutMode = "VERTICAL";
body.paddingTop = 12; body.paddingBottom = 12;
body.paddingLeft = 12; body.paddingRight = 12;
body.fills = [{ type: "SOLID", color: hex("#FFFFFF") }];
card.appendChild(body);
body.layoutSizingHorizontal = "FILL"; body.layoutSizingVertical = "HUG";
const bodyText = figma.createText();
bodyText.fontName = { family: "Inter", style: "Regular" };
bodyText.characters = "Primary and secondary CTAs use the same weight and color, so users can't tell which action is recommended for their journey stage.";
bodyText.fontSize = 12;
body.appendChild(bodyText);
bodyText.layoutSizingHorizontal = "FILL"; bodyText.textAutoResize = "HEIGHT";
```

---

## Summary Frame Creation Reference (Plugin API)

Inside one `use_figma` call (after loading the `figma-use` skill), iterate over the 8 deliverables. For each:

1. Create a frame with the exact name from the list above
2. Position it relative to the screenshot column: `x = rightmost_screenshot.x + rightmost_screenshot.width + 80` (account for any cards attached to that screenshot — push further right if needed), then `y` incremented by previous frame's height + 24px gap
3. Set width 480, auto-layout vertical, 24px padding, 16px item spacing
4. Set fill white, stroke `#E5E5E5` 1px, corner radius 8
5. Add a title text node (20px bold) and a body text node (14px regular) — for the Roadmap frame, add three sub-headed sections instead of one body block

---

## Editing Existing Audits

If the user asks to revise or redo:

1. **Delete prior observation cards:** Via the `figma-use` skill (load it first), find all frames in the screenshot's parent whose names match the pattern `Audit Card — *` and delete them.
2. **Delete prior summary frames:** Via the `figma-use` skill, find all top-level frames in the screenshot's parent whose names match the pattern `High-Level Overview — *` and delete them.
3. **Delete prior sitemap (if rebuilding):** find frame named `Audit Sitemap — *` and delete it.
4. **Re-running from a new recording or capture:** if the user wants fresh screenshots, delete the prior screenshot rectangles (`Screen N — *`) inside the user's node before re-running Step 1b (screenshots live directly in the node — there is no separate audit container frame). If they're keeping the same screenshots, leave them and only redo cards/frames.
5. **Rebuild** using Steps 6a and 6b.

---

## Anti-Patterns

- Don't write multi-paragraph cards — one point per card; if it needs more, it's two cards
- Don't omit the canonical category tag from card headers
- Don't reference code, markup, or technical implementation
- Don't assume what competitors do or industry norms
- Don't describe what a module looks like — evaluate what it does for the user
- Don't add observations that don't connect to the page goal
- Don't put all cards in a single `use_figma` call when there are many — batch by screenshot
- Don't put sentiment markers (✅ / ⚠️ / ❌) inside summary frames — they're scoped to observation cards
- Don't call `use_figma` without first loading the `figma-use` skill
- Don't use Figma REST API for posting pin comments — that workflow was replaced by in-canvas observation cards
- Don't place cards so they overlap another screenshot — adjust x or move to a card lane below the screenshot column
- Don't require a live URL — a recording, or existing Figma screenshots, is an equally valid source; the audit only needs the Figma file plus one source
- Don't treat video as the primary input — accept URL, recording, or existing screenshots interchangeably
- Don't try to play or scrub a recording — extract keyframes via ffmpeg (Step 1b, Path A) and audit the frames
- Don't assume the 0.3 scene threshold captured every screen — quickly-navigated screens (reward-detail pages, modals, intermediate states) get skipped; when the user mentions a missing or hesitation screen, re-extract at a lower threshold (`scene,0.1`) and surface the relevant region rather than telling them it wasn't recorded
- Don't wrap screenshots in a new container/"audit" frame, and don't place them beside the user's node — place them DIRECTLY inside the node the user linked, or it reads as a confusing double-frame
- Don't inline image bytes into `use_figma` (`figma.createImage(<base64/bytes>)`) — the `code` field caps at ~50k chars; upload via `upload_assets` + `POST`, then set the IMAGE fill from the returned `imageHash`. Don't rely on `upload_assets`' `nodeId` auto-fill (it often leaves a gray placeholder); set the fill explicitly and verify with a screenshot
- Don't call `resize()` on a card frame AFTER setting `primaryAxisSizingMode = "AUTO"` — resize resets sizing to FIXED and collapses the card to a 1px sliver; resize first, THEN set `counterAxisSizingMode = "FIXED"` + `primaryAxisSizingMode = "AUTO"`, and verify heights are ~150–250px
- Don't pack auto-placed screenshots tightly — space them ~400px apart so each card fits to the right of its screen at the module's Y-position
- Don't re-capture screenshots when the submitted Figma file already has them — detect existing screenshots first (Step 1b, Path B step 1)
- Don't run the SEO/answer-engine items of Search + Discoverability against an app or non-web touchpoint — limit that lens to in-touchpoint findability
- Don't skip touchpoint detection — it gates the Experience Lenses and shapes the summary frames
