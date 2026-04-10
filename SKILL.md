---
name: figma-baymard-ux-audit
description: Audit a live website page or user flow against Baymard UX research principles (via NotebookLM) and post findings as Figma comments. Use when the user says "audit", "site audit", "UX audit", "Baymard audit", "review this page", "review this flow", or provides a URL or video alongside a Figma link for review purposes.
disable-model-invocation: true
argument-hint: "[site URL or video path]"
---

# Baymard UX Audit

Audit a live website page — or a multi-step user flow from a screen recording — against **Baymard UX research principles** stored in NotebookLM, and post findings as **Figma comments** pinned to screenshots in a Figma file.

---

## Core Principle

Every observation must tie back to the **page's user goal** and cite a **specific Baymard UX principle**. Don't describe what a module is — evaluate whether it passes or fails against research-backed guidelines. Write like a senior product designer leaving notes for a colleague.

---

## Comment Delivery: Figma REST API

All audit notes MUST be posted as **Figma comments** via the REST API.

- Use `POST https://api.figma.com/v1/files/:file_key/comments`
- Use `curl` via Bash with the user's Figma personal access token
- The Plugin API (`use_figma`) does NOT support comments — do not use it for this skill
- If you do not have a token, **ask for it before proceeding**

---

## Step 0 — Preflight: Verify Figma MCP Connection (Blocking)

Check Figma only at this stage. NotebookLM auth is checked later (Step 5, right before querying) to avoid token expiration.

### Figma MCP

Call Figma `whoami`.

- If it succeeds, the connection is live.
- Check project memory for a stored Figma personal access token. If missing, ask the user for it — this is required for REST API comment posting (separate from MCP auth).
- If Figma MCP fails, guide the user:
  - **If using terminal/CLI:** "Figma MCP is not connected. Check your MCP configuration and try again."
  - **If using Claude Code desktop or web app:** "Figma MCP is not connected. Open Settings > MCP Servers and ensure the Figma server is enabled and authenticated."

---

## Step 1 — Gather Inputs (Blocking)

Collect all of the following before proceeding:

1. **Input type** — one of:
   - **Site URL** — a single live page to audit
   - **Video file path** — a local screen recording (MP4, MOV, WebM) of a user flow to audit multiple steps
2. **Figma file URL** — the file containing the page screenshot(s). Extract `fileKey` and `nodeId`. User must have screenshots already placed in the file.
3. **Figma personal access token** — check memory first; ask only if missing

If any are missing, ask. Do NOT proceed without all inputs.

### If video file provided

1. Check if `ffmpeg` is installed: run `which ffmpeg`
   - If not found, guide the user to install it:
     - **If using terminal/CLI:** "ffmpeg is required for video processing. Run `brew install ffmpeg` and let me know when it's done."
     - **If using Claude Code desktop or web app:** "ffmpeg is required for video processing. Open a terminal window (Spotlight > Terminal, or find it in Applications > Utilities), paste `brew install ffmpeg`, and wait for it to finish. If you don't have Homebrew either, first install it with `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`. Come back here and say 'done' when the install completes."
   - Do NOT proceed until ffmpeg is confirmed available.
2. Extract keyframes using scene-change detection:
   ```
   ffmpeg -i "<video_path>" -vf "select='gt(scene,0.3)'" -fps_mode vfr /tmp/audit_frames/frame_%03d.png
   ```
   Note: Use `-fps_mode vfr` (not `-vsync vfn`, which is deprecated in newer ffmpeg versions).
3. Read the extracted frames as images and present them to the user
4. Ask the user to confirm which frames represent distinct steps (remove duplicates and transition frames)
5. If the user mentions viewing something not captured in the frames (e.g., FAQ section, modal), add it as a step and audit via WebFetch
6. Each confirmed frame becomes one audit step

---

## Step 2 — Identify Page/Flow Goal (Blocking)

Analyze the page content (via WebFetch for URLs) or the confirmed video frames to **propose the goal yourself**.

### Single page
Assess the page type and content, then present:
"Based on the page content, the primary goal appears to be **[proposed goal]**. Does this sound right?"

### Multi-step flow
Assess the full flow, then present:
"This flow appears to be **[proposed flow goal]**. Each step seems to be:
- Step 1 — [goal]
- Step 2 — [goal]
- Step 3 — [goal]
Does this sound right?"

If the user says no, ask them to clarify. Do NOT proceed until the goal is confirmed. This goal anchors every finding.

---

## Step 3 — Authenticate NotebookLM & Discover Baymard Notebooks (Blocking)

Authenticate and discover notebooks NOW so you know which notebooks are available before classifying pages.

### 3A. Authenticate NotebookLM

Call `notebook_list` with `max_results: 1`.
- If it succeeds, proceed immediately.
- If auth error, call `refresh_auth` first, then retry.
- If still failing, guide the user to re-authenticate:
  - **If using terminal/CLI:** "NotebookLM auth expired. Run `notebooklm-mcp-auth` and say 'done' when finished."
  - **If using Claude Code desktop or web app:** "NotebookLM auth expired. Here's how to fix it: (1) Open a terminal — press Cmd+Space, type 'Terminal', hit Enter. (2) Paste this command and press Enter: `notebooklm-mcp-auth` (3) A browser window will open — sign in with your Google account. (4) Once you see 'Authentication successful', come back here and say 'done'."
- After re-auth, call `refresh_auth` to reload tokens, then proceed immediately.

### 3B. Discover Baymard Notebooks

1. Call `notebook_list` to get all available notebooks
2. Search for notebooks whose names contain "Baymard UX"
3. Map each to its category:

| Category | Expected Name |
|---|---|
| **Product Page** | Baymard UX - Product Page, Product Lists & Filtering |
| **Cart & Checkout** | Baymard UX - Cart & Checkout |
| **Accounts** | Baymard UX - Accounts & Self Service |
| **Site-wide** | Baymard UX - Homepage, Category Taxonomy, Main Navigation, Search, site-wide features, and navigation |

4. If any are missing, warn the user which notebooks were not found and ask whether to proceed with what is available
5. If none are found, stop and tell the user: "No Baymard UX notebooks found. You need the Baymard notebooks shared with your NotebookLM account to use this skill."
6. Store discovered notebook IDs for the session

---

## Step 4 — Classify Pages and Select Notebooks

For each page/step:

1. Analyze the page content (via WebFetch for URLs, or the confirmed video frame)
2. Classify the page type: product page, product list, cart, checkout, account/self-service, homepage, category page, search results, navigation, etc.
3. Select the **primary notebook** from those discovered in Step 3, matching that page type:
   - Product pages, product lists, filtering/sorting → **Product Page** notebook
   - Cart, checkout flow → **Cart & Checkout** notebook
   - Account, login, order tracking, self-service → **Accounts** notebook
   - Homepage, navigation, search, category taxonomy → **Site-wide** notebook
4. Always add the **Site-wide** notebook as a secondary source (unless it is already the primary)
5. Present the classification to the user for confirmation or override

---

## Step 4 — Discover Figma Layout

1. Call `get_metadata` on the provided `nodeId` to find all screenshot image nodes (their IDs, positions, and dimensions)
2. Call `get_screenshot` on the image node(s) to visually identify all modules and estimate their Y-positions

Record:
- Parent frame ID (for page-level comments placed outside the screenshot)
- Screenshot image node ID(s) (for module-level comments pinned on screenshots)
- Image dimensions (width, height) for coordinate mapping

### For multi-step flows

1. Detect the screenshot arrangement from metadata — check x/y positions to determine whether screenshots are ordered left-to-right or top-to-bottom
2. Present the detected order to the user once for confirmation (e.g., "I see 4 screenshots arranged left-to-right. Is this the correct step order?")
3. Auto-match video frames (by timestamp order) to Figma screenshots (by position order) — no per-frame manual mapping needed

### If fewer screenshots than steps

Always audit ALL steps regardless of whether Figma screenshots exist for each one. For steps without a matching screenshot, compile findings as page-level comments on the parent frame. Never skip steps or ask the user whether to audit them.

---

## Step 6 — Audit Against Baymard Principles

NotebookLM was authenticated and notebooks were discovered in Step 3. If auth has expired since then (e.g., user took a long break between steps), call `refresh_auth` first. If that fails, guide the user to re-authenticate:
- **If using terminal/CLI:** "NotebookLM auth expired. Run `notebooklm-mcp-auth` and say 'done' when finished."
- **If using Claude Code desktop or web app:** "NotebookLM auth expired. Here's how to fix it: (1) Open a terminal — press Cmd+Space, type 'Terminal', hit Enter. (2) Paste this command and press Enter: `notebooklm-mcp-auth` (3) A browser window will open — sign in with your Google account. (4) Once you see 'Authentication successful', come back here and say 'done'."

For each page/step and each identified module:

#### 1. Formulate targeted queries

Write specific queries per module. Examples:
- "What are Baymard's research findings for product image galleries on product pages?"
- "What UX guidelines does Baymard recommend for add-to-cart button placement and design?"
- "What are Baymard's findings on filter sidebar usability for product lists?"

Do NOT write broad queries like "tell me all UX principles." Targeted queries return better results.

#### 2. Query NotebookLM

Call `notebook_query` on the selected notebook(s) for each module. Batch 3-4 queries in parallel to manage latency. If a module touches site-wide concerns (navigation, header, footer, search), also query the site-wide notebook.

#### 3. Cross-reference with the live page

Compare the Baymard principles returned by NotebookLM against the actual implementation visible on the live page (via WebFetch) or in the video frame screenshot.

#### 4. Assign verdicts

For each Baymard principle that applies to the module:

- **PASS** — the module adheres to the Baymard principle
- **FAIL** — the module violates the principle. State what is wrong and what Baymard recommends.
- **INCONCLUSIVE** — cannot determine compliance from available information (requires interaction testing, A/B data, or the element is not visible in the current view)

#### 5. Cite the principle

Every finding MUST reference the specific Baymard guideline it was tested against. Use the language returned by NotebookLM. Do NOT invent or paraphrase principles that were not in the query response.

---

## Step 7 — Map Findings to Coordinates

For each finding, determine the exact pin location on the Figma screenshot:

1. **Read the Figma screenshot** via `get_screenshot` to see the layout and module positions
2. **For video flows, cross-reference with the corresponding video frame** — compare elements between the video frame and the Figma screenshot to precisely match where each audited element appears on the screenshot
3. **Estimate x/y coordinates** relative to the image node using its dimensions from `get_metadata`
4. Pin observations to the relevant area using `node_offset` with x/y relative to the image node
5. Vary x-positions (left/center/right of image) to avoid comment overlap
6. For page-level notes, pin to the parent frame with an x-offset to the right of the last screenshot
7. For steps without a matching screenshot, compile all findings into a single page-level comment per step on the parent frame

---

## Step 8 — Confirm Before Posting (Blocking)

Before posting any comments, present a summary:

- Findings grouped by page/step
- Count of PASS / FAIL / INCONCLUSIVE per page/step
- The specific Baymard principles tested
- Which Figma screenshot(s) comments will be posted on

Allow the user to request removal or modification of specific findings. Do NOT post until the user confirms. Once confirmed, post all comments without asking permission between batches — just post them all.

---

## Step 9 — Write and Post Comments

Post all module-level comments first, then steps-without-screenshots comments, then create summary frames last.

### Module-Level Comments (ALWAYS pinned on screenshot image nodes, one finding per pin)

Every individual finding — PASS, FAIL, or INCONCLUSIVE — MUST be posted as its own comment pinned directly on the relevant screenshot image node using `node_offset`. This is the primary comment type. Do NOT group multiple findings into a single comment. Do NOT post module-level findings on the parent frame if a screenshot exists for that step.

Each comment MUST include all of the following with explicit labels:

```
PASS
MODULE: [Component/module name, e.g., Hero Carousel, Add to Cart Button, Promo Code Field]
INTERACTION: [How the user interacts with this module — view, click, hover, scroll, type, expand, etc.]
PRINCIPLE [#]: [Baymard principle being tested, with the source reference number from the NotebookLM response]
FINDING: [What the site does that adheres to this principle. 1-2 sentences.]
```

```
FAIL
MODULE: [Component/module name]
INTERACTION: [view, click, hover, scroll, type, expand, etc.]
PRINCIPLE [#]: [Baymard principle with source reference number]
FINDING: [What the site does wrong + what Baymard recommends instead. 1-2 sentences.]
```

```
INCONCLUSIVE
MODULE: [Component/module name]
INTERACTION: [view, click, hover, scroll, type, expand, etc.]
PRINCIPLE [#]: [Baymard principle with source reference number]
FINDING: [Why this could not be determined and what to check. 1-2 sentences.]
```

- Always use explicit MODULE:, INTERACTION:, PRINCIPLE:, and FINDING: labels.
- The principle reference number (e.g., [14], [32]) comes from the NotebookLM query response — preserve these numbers as they map to specific Baymard source documents.
- Never post a principle without a finding or a finding without a principle.

**Multiple comments per module are expected.** Each finding gets its own pin.

### Writing Rules

- One finding per comment — never combine multiple points
- Tie every note back to the page goal and the Baymard principle
- Don't describe what the module is — evaluate what it does against the principle
- Don't make assumptions about the industry, competitors, or time period
- Don't reference technical implementation (HTML, CSS, ARIA, tabindex, schema)
- Write in plain, direct language — no jargon, no hedging
- Suggest specific improvements where relevant, grounded in the Baymard recommendation

### Summary Frames (created as Figma design elements via `use_figma`, NOT as comments)

The 4 summary sections are created as styled Figma frames placed to the right of the last screenshot. Load the `figma-use` skill before calling `use_figma`. Always pass `skillNames: "figma-use"` when calling `use_figma`.

**Build the summary frames incrementally** — one `use_figma` call per section. Do not try to build all 4 in a single script. Return created node IDs from each call so subsequent frames can position relative to the previous one.

#### Shared design specs

All 4 summary frames share these properties:

```
Frame:
  width: 780 (fixed — do NOT use a narrower width, text will clip)
  layoutMode: VERTICAL
  primaryAxisSizingMode: AUTO (height grows with content)
  counterAxisSizingMode: FIXED (width stays at 780)
  padding: 32 all sides
  fills: white {r:1, g:1, b:1}
  cornerRadius: 12
  strokes: [{type:"SOLID", color:{r:0.9, g:0.9, b:0.9}}], strokeWeight: 1

Font: Inter (load Bold, Semi Bold, Regular styles before any text ops)
  - Header: Inter Bold, 22px, color DARK
  - Section labels: Inter Semi Bold, 14px, color GRAY
  - Body text: Inter Regular, 15px, lineHeight 24px, color DARK
  - Meta text: Inter Regular, 13px, color GRAY

Colors:
  DARK:    {r: 0.1, g: 0.1, b: 0.1}
  GRAY:    {r: 0.4, g: 0.4, b: 0.4}
  GREEN:   {r: 0.13, g: 0.55, b: 0.13}
  RED:     {r: 0.85, g: 0.15, b: 0.15}
  GRAY_MED:{r: 0.6, g: 0.6, b: 0.6}
  LIGHT_RED: {r: 1, g: 0.95, b: 0.95} (priority card backgrounds)

Text nodes with body content:
  - resize(width - 64, height) then set textAutoResize = "HEIGHT"
  - This ensures text wraps and the frame grows. The 64 accounts for 32px padding on each side.

Dividers between sub-sections:
  - Rectangle, resize(width - 64, 1), fill {r:0.9, g:0.9, b:0.9}
```

**Positioning:** Find the rightmost screenshot's x + width from `get_metadata`, then place the first summary frame ~200px to its right, y-aligned to the top screenshot. Each subsequent frame is positioned at previousFrame.y + previousFrame.height + 32.

#### 1. BAYMARD COMPLIANCE SUMMARY

```
itemSpacing: 20

Children (in order):
1. Header text: "BAYMARD COMPLIANCE SUMMARY" (Bold, 22px)
2. Stats row — horizontal auto-layout frame (itemSpacing: 24, no fill):
   - 4 stat sub-frames, each vertical auto-layout (itemSpacing: 4, no fill):
     - Number: Bold, 36px, color-coded (GREEN/RED/GRAY_MED/DARK)
     - Label: Semi Bold, 14px, same color as number
     - Values: "30 PASS", "8 FAIL", "8 INCONCLUSIVE", "79% PASS RATE"
3. Divider (1px rectangle)
4. Section label: "STRONGEST AREAS" (Semi Bold, 14px, GRAY)
5. Body text: bulleted list of top-performing steps (Regular, 14px, width - 64, textAutoResize HEIGHT)
6. Section label: "WEAKEST AREAS"
7. Body text: bulleted list of lowest-performing steps
8. Section label: "NOTEBOOK COVERAGE"
9. Body text: which notebooks were used
```

#### 2. PAGE SUMMARY

```
itemSpacing: 16

Children (in order):
1. Header text: "PAGE SUMMARY" (Bold, 22px)
2. Paragraph 1 text node (Regular, 15px, lineHeight 24px, width - 64, textAutoResize HEIGHT)
3. Paragraph 2 text node (same specs)
4. Paragraph 3 text node (same specs)
```

#### 3. TOP PRIORITIES

```
itemSpacing: 24

Children (in order):
1. Header text: "TOP PRIORITIES" (Bold, 22px)
2-6. Five priority card rows, each structured as:

Priority card row:
  Frame: horizontal auto-layout
    layoutSizingHorizontal: FILL (stretches to parent width)
    primaryAxisSizingMode: AUTO
    counterAxisSizingMode: AUTO
    itemSpacing: 16
    padding: 16 all sides
    fills: LIGHT_RED
    cornerRadius: 8

  Children:
    A. Number badge:
       Frame: 36x36 FIXED (layoutSizingHorizontal: FIXED, layoutSizingVertical: FIXED)
       cornerRadius: 18 (circle)
       fills: RED
       layoutMode: VERTICAL, center-aligned both axes
       Child: text "1" (Bold, 16px, white)

    B. Content column:
       Frame: vertical auto-layout
         layoutSizingHorizontal: FILL (takes remaining width after badge)
         primaryAxisSizingMode: AUTO
         itemSpacing: 6
         no fill

       Children:
         1. Title: Semi Bold, 15px, DARK (textAutoResize HEIGHT)
         2. Meta: Regular, 13px, GRAY — "MODULE: [name]  |  INTERACTION: [type]"
         3. Principle: Regular, 13px, DARK — "PRINCIPLE [#]: [text]"
         4. Impact: Regular, 13px, DARK, lineHeight 20px — "IMPACT: [text]"

CRITICAL: The number badge MUST be FIXED sizing (36x36). If set to FILL or HUG
it will stretch and steal width from the content column, clipping text.
```

#### 4. FLOW SUMMARY (multi-step flows only)

```
itemSpacing: 16

Children (in order):
1. Header text: "FLOW SUMMARY" (Bold, 22px)
2. Intro paragraph (Regular, 15px, lineHeight 24px, width - 64, textAutoResize HEIGHT)
3. Divider (1px rectangle)
4-13. Alternating section labels + body text blocks:
   - Label: "CONSISTENCY" (Semi Bold, 14px, GRAY)
   - Body text (Regular, 15px, lineHeight 24px, width - 64, textAutoResize HEIGHT)
   - Label: "FRICTION POINTS"
   - Body text
   - Label: "STRENGTHS"
   - Body text
   - Label: "DROP-OFF RISKS"
   - Body text
   - Label: "CROSS-STEP OBSERVATION"
   - Body text
```

### Steps Without Screenshots (still posted as Figma comments, NOT as frames)

For steps that don't have a corresponding Figma screenshot, group all findings for that step into a single Figma comment on the parent frame, labeled with the step name. Each finding within the comment uses the full labeled format:

```
STEP [N] — [STEP NAME]

PASS
MODULE: [name]
INTERACTION: [type]
PRINCIPLE [#]: [principle]
FINDING: [evaluation]

FAIL
MODULE: [name]
INTERACTION: [type]
PRINCIPLE [#]: [principle]
FINDING: [evaluation]
```

These are comments via the REST API — the same delivery method as module-level findings. Only the 4 summary sections (Compliance Summary, Page Summary, Top Priorities, Flow Summary) are created as Figma frames.

---

## Step 10 — Confirm Completion

After posting, summarize what was posted:

- Count of findings by verdict (PASS / FAIL / INCONCLUSIVE) per page/step
- Total Baymard principles tested
- List of page-level notes posted
- Invite the user to review in Figma and request adjustments

---

## Comment Format Reference

### Module-level (on screenshot image node)
```
curl -s -X POST "https://api.figma.com/v1/files/:file_key/comments" \
  -H "X-Figma-Token: TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "FAIL\nMODULE: Promo Code Field\nINTERACTION: view\nPRINCIPLE [9]: Always hide promo code fields — collapse behind a link by default\nFINDING: Promo code field is fully expanded and visible by default, compelling users to abandon checkout to hunt for codes.",
    "client_meta": {
      "node_id": "IMAGE_NODE_ID",
      "node_offset": {"x": 580, "y": 300}
    }
  }'
```

### Steps-without-screenshots comment (on parent frame, offset right)
```
curl -s -X POST "https://api.figma.com/v1/files/:file_key/comments" \
  -H "X-Figma-Token: TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "STEP 7 — PDP TOP\n\nPASS\nMODULE: Product Price Display\nINTERACTION: view\nPRINCIPLE [14, 15]: Style product price to be highly visible\nFINDING: Price prominently displayed with strikethrough and bold sale price.",
    "client_meta": {
      "node_id": "PARENT_FRAME_ID",
      "node_offset": {"x": 1800, "y": 700}
    }
  }'
```

### Summary sections (Compliance Summary, Page Summary, Top Priorities, Flow Summary)
These are NOT comments — they are created as styled Figma frames via `use_figma`. See the "Summary Frames" section above.

---

## Batch Posting

Post comments in parallel batches of 3-4 using `curl ... &` and `wait` to avoid rate limits while staying efficient. Run module batches first, steps-without-screenshots comments next, summary frames last. Do NOT ask the user for permission between batches — once confirmed in Step 8, post everything.

---

## Editing Existing Audits

If the user asks to revise or redo:

1. Fetch all existing comments via `GET /v1/files/:file_key/comments`
2. Delete relevant comments via `DELETE /v1/files/:file_key/comments/:comment_id`
3. Repost revised comments

---

## Prerequisite Installation & Auth Guidance

Whenever a prerequisite is missing (ffmpeg, MCP auth, Homebrew, etc.), always provide instructions for both environments:

- **Terminal/CLI users:** Short command to run
- **Desktop/web app users:** Step-by-step instructions including how to open a terminal (Spotlight > Terminal or Applications > Utilities > Terminal), what to paste, any upstream dependencies, and to say "done" when finished

Make it as smooth and low-friction as possible. Never assume the user is on a terminal.

---

## Anti-Patterns

- Don't query all 4 notebooks for every module — select the relevant one(s) based on page type
- Don't fire notebook queries sequentially — batch 3-4 in parallel
- Don't post comments without user confirmation (Step 8)
- Don't ask permission between comment batches — post them all once confirmed
- Don't invent Baymard principles — only cite what NotebookLM returns
- Don't mark PASS/FAIL without a specific Baymard principle reference
- Don't post a principle without a finding or a finding without a principle
- Don't attempt to play video files — extract frames via ffmpeg
- Don't assume ffmpeg is installed — check first
- Don't skip steps that lack Figma screenshots — audit all steps, compile findings as page-level comments
- Don't ask if the user wants to audit all steps — always audit all steps
- Don't write multi-paragraph module comments — one finding per pin
- Don't use category headers in module comments
- Don't reference code, markup, or technical implementation
- Don't assume what competitors do or industry norms
- Don't describe what a module looks like — evaluate what it does for the user against Baymard principles
- Don't add observations that don't connect to the page goal
- Don't check NotebookLM auth at preflight (Step 0) — check it at Step 3 right before notebook discovery, and refresh again at Step 6 if expired
- Don't post module-level findings on the parent frame if a screenshot exists for that step — always pin individual findings on the screenshot image node with the full PASS/FAIL + MODULE + INTERACTION + PRINCIPLE [#] + FINDING format
