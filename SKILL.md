---
name: figma-baymard-ux-audit
description: Audit a live website page or user flow against Baymard UX research principles (via NotebookLM), auto-place video keyframes into the user's Figma file, and post findings as Figma comments pinned to those screenshots. Use when the user says "audit", "site audit", "UX audit", "Baymard audit", "review this page", "review this flow", and provides a URL, a video, and a Figma link for review.
disable-model-invocation: true
argument-hint: "[site URL] [video path] [figma URL]"
---

# Baymard UX Audit

Audit a live website — using both the live URL and a screen recording of the user flow — against **Baymard UX research principles** stored in NotebookLM. The skill auto-extracts keyframes from the video and places them into the user's Figma file, then posts findings as **Figma comments** pinned to those auto-placed screenshots.

---

## Core Principle

Every observation must tie back to the **page's user goal** and cite a **specific Baymard UX principle**. Don't describe what a module is — evaluate whether it passes or fails against research-backed guidelines. Write like a senior product designer leaving notes for a colleague.

---

## Comment Delivery: Figma REST API

All audit notes MUST be posted as **Figma comments** via the REST API.

- Use `POST https://api.figma.com/v1/files/:file_key/comments`
- Use `curl` via Bash with the user's Figma personal access token
- The Plugin API (`use_figma`) does NOT support comments — do not use it for posting comments
- The Plugin API IS used to auto-place screenshots into the file (Step 2) and to build summary frames (Step 10)
- If you do not have a token, **ask for it before proceeding**

---

## Step 0 — Preflight: Verify Figma MCP Connection (Blocking)

Check Figma only at this stage. NotebookLM auth is checked later (Step 4, right before querying) to avoid token expiration.

### Figma MCP

Call Figma `whoami`.

- If it succeeds, the connection is live.
- Check project memory for a stored Figma personal access token. If missing, ask the user for it — this is required for REST API comment posting (separate from MCP auth).
- If Figma MCP fails, guide the user:
  - **If using terminal/CLI:** "Figma MCP is not connected. Check your MCP configuration and try again."
  - **If using Claude Code desktop or web app:** "Figma MCP is not connected. Open Settings > MCP Servers and ensure the Figma server is enabled and authenticated."

---

## Step 1 — Gather Inputs (Blocking)

Collect ALL of the following before proceeding. All three primary inputs are required:

1. **Site URL** — the live website being audited. Used for WebFetch to inspect live HTML content during principle evaluation.
2. **Video file path** — a local screen recording (MP4, MOV, WebM) of the user flow on that site. Used to extract step keyframes and to capture states the URL alone can't show (post-action states, modals, scroll positions, hover effects).
3. **Figma file URL** — the destination file where the skill will auto-place keyframes and post comments. Extract `fileKey` and `nodeId`. The skill places screenshots automatically — the user does NOT need to set up screenshots in advance.
4. **Figma personal access token** — check memory first; ask only if missing.

If any of (1), (2), (3) are missing, ask. Do NOT proceed without all inputs. Do NOT accept URL-only or video-only — both are mandatory.

---

## Step 2 — Extract Video Frames and Auto-Place in Figma (Blocking)

### 2A. Verify ffmpeg

Check if `ffmpeg` is installed: run `which ffmpeg`
- If not found, guide the user to install it:
  - **If using terminal/CLI:** "ffmpeg is required for video processing. Run `brew install ffmpeg` and let me know when it's done."
  - **If using Claude Code desktop or web app:** "ffmpeg is required for video processing. Open a terminal window (Spotlight > Terminal, or find it in Applications > Utilities), paste `brew install ffmpeg`, and wait for it to finish. If you don't have Homebrew either, first install it with `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`. Come back here and say 'done' when the install completes."
- Do NOT proceed until ffmpeg is confirmed available.

### 2B. Extract keyframes

Extract via scene-change detection into a clean working directory:
```
rm -rf /tmp/audit_frames && mkdir -p /tmp/audit_frames
ffmpeg -i "<video_path>" -vf "select='gt(scene,0.3)'" -fps_mode vfr /tmp/audit_frames/frame_%03d.png
```
Note: Use `-fps_mode vfr` (not `-vsync vfn`, which is deprecated in newer ffmpeg versions).

### 2C. Confirm step frames

1. Read the extracted frames as images and present them to the user
2. Ask the user to confirm which frames represent distinct steps (remove duplicates and transition frames)
3. If the user mentions viewing something not captured in the frames (e.g., FAQ section, modal), add it as a step and audit via WebFetch
4. Each confirmed frame becomes one audit step

### 2D. Auto-place confirmed frames into Figma

The skill places confirmed frames into the user's Figma file automatically — the user never has to drag screenshots in manually.

1. Load the `figma-use` skill (REQUIRED before any `use_figma` call). Always pass `skillNames: "figma-use"` when calling `use_figma`.
2. Determine placement anchor:
   - If the user's Figma URL included a `nodeId`, call `get_metadata` on it to read its absolute x/y/width/height. Place the audit container ~200px to the right of it, y-aligned to its top.
   - If no `nodeId`, place the container at page origin (0, 0).
3. Build a `use_figma` script that:
   - Reads each confirmed frame's PNG as base64 (passed in as a string variable from Bash), converts to `Uint8Array`, and uploads via `figma.createImage(bytes)` to get an image hash
   - Creates a parent container frame named `Baymard Audit — [today's date]` (horizontal auto-layout, itemSpacing 100, padding 100, fill white, cornerRadius 12)
   - For each frame, creates a child rectangle sized to the frame's original pixel dimensions (preserve aspect ratio) with the image hash applied as a `SCALE` fill
   - Names each rectangle `Step N — Screenshot` (1-indexed) so they're identifiable later
   - Positions the container at the anchor computed in step 2 above
   - Returns the container frame ID and the image rectangle node IDs in step order
4. Capture the returned IDs. The image rectangle node IDs become the **screenshot image node IDs** used for pinning module-level comments. The container ID is used for summary frame placement.
5. For videos with many frames (8+), batch the uploads into multiple `use_figma` calls (3-4 frames per call) to keep script payloads manageable. Each batch appends rectangles into the same parent container.
6. Tell the user: "Placed N screenshots into your Figma file under frame 'Baymard Audit — [date]'. Take a quick look and confirm before I continue." Wait for confirmation before proceeding.

---

## Step 3 — Identify Page/Flow Goal (Blocking)

Analyze the live URL (via WebFetch) and the confirmed video frames together to **propose the goal yourself**.

### Single page (one confirmed frame)
"Based on the page content and recording, the primary goal appears to be **[proposed goal]**. Does this sound right?"

### Multi-step flow (multiple confirmed frames)
"This flow appears to be **[proposed flow goal]**. Each step seems to be:
- Step 1 — [goal]
- Step 2 — [goal]
- Step 3 — [goal]
Does this sound right?"

If the user says no, ask them to clarify. Do NOT proceed until the goal is confirmed. This goal anchors every finding.

---

## Step 4 — Authenticate NotebookLM & Discover Baymard Notebooks (Blocking)

Authenticate and discover notebooks NOW so you know which notebooks are available before classifying pages.

### 4A. Authenticate NotebookLM

Call `notebook_list` with `max_results: 1`.
- If it succeeds, proceed immediately.
- If auth error, call `refresh_auth` first, then retry.
- If still failing, guide the user to re-authenticate:
  - **If using terminal/CLI:** "NotebookLM auth expired. Run `notebooklm-mcp-auth` and say 'done' when finished."
  - **If using Claude Code desktop or web app:** "NotebookLM auth expired. Here's how to fix it: (1) Open a terminal — press Cmd+Space, type 'Terminal', hit Enter. (2) Paste this command and press Enter: `notebooklm-mcp-auth` (3) A browser window will open — sign in with your Google account. (4) Once you see 'Authentication successful', come back here and say 'done'."
- After re-auth, call `refresh_auth` to reload tokens, then proceed immediately.

### 4B. Discover Baymard Notebooks

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

## Step 5 — Classify Pages and Select Notebooks

For each page/step:

1. Analyze the page content (via WebFetch on the URL, plus the confirmed video frame)
2. Classify the page type: product page, product list, cart, checkout, account/self-service, homepage, category page, search results, navigation, etc.
3. Select the **primary notebook** from those discovered in Step 4, matching that page type:
   - Product pages, product lists, filtering/sorting → **Product Page** notebook
   - Cart, checkout flow → **Cart & Checkout** notebook
   - Account, login, order tracking, self-service → **Accounts** notebook
   - Homepage, navigation, search, category taxonomy → **Site-wide** notebook
4. Always add the **Site-wide** notebook as a secondary source (unless it is already the primary)
5. Present the classification to the user for confirmation or override

---

## Step 6 — Verify Figma Layout

Screenshots were already placed in Step 2D, so the layout is known. Verify and record:

1. Call `get_metadata` on the container frame ID returned in Step 2D
2. Confirm the screenshot image node IDs and their dimensions in step order
3. Optionally call `get_screenshot` on a screenshot node to visually re-confirm module positions before pinning

Record:
- Container/parent frame ID (for summary frame placement)
- Screenshot image node IDs in step order (for module-level comments pinned on screenshots)
- Image dimensions (width, height) for coordinate mapping

Since screenshots were placed by the skill in step order, no manual re-ordering or user confirmation of order is needed.

---

## Step 7 — Audit Against Baymard Principles

NotebookLM was authenticated and notebooks were discovered in Step 4. If auth has expired since then (e.g., user took a long break between steps), call `refresh_auth` first. If that fails, guide the user to re-authenticate:
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

Compare the Baymard principles returned by NotebookLM against the actual implementation visible on the live page (via WebFetch) and in the placed Figma screenshot.

#### 4. Assign verdicts

For each Baymard principle that applies to the module:

- **PASS** — the module adheres to the Baymard principle
- **FAIL** — the module violates the principle. State what is wrong and what Baymard recommends.
- **INCONCLUSIVE** — cannot determine compliance from available information (requires interaction testing, A/B data, or the element is not visible in the current view)

#### 5. Cite the principle

Every finding MUST reference the specific Baymard guideline it was tested against. Use the language returned by NotebookLM. Do NOT invent or paraphrase principles that were not in the query response.

---

## Step 8 — Map Findings to Coordinates

For each finding, determine the exact pin location on the placed Figma screenshot:

1. **Read the Figma screenshot** via `get_screenshot` to see the layout and module positions
2. **Cross-reference with the corresponding video frame** — the placed screenshots ARE the video frames, so coordinates map directly
3. **Estimate x/y coordinates** relative to the image node using its dimensions from `get_metadata`
4. Pin observations using `node_offset` with x/y relative to the image node
5. Vary x-positions (left/center/right of image) to avoid comment overlap

---

## Step 9 — Confirm Before Posting (Blocking)

Before posting any comments, present a summary:

- Findings grouped by step
- Count of PASS / FAIL / INCONCLUSIVE per step
- The specific Baymard principles tested
- Which screenshot node each set of comments will be posted on

Allow the user to request removal or modification of specific findings. Do NOT post until the user confirms. Once confirmed, post all comments without asking permission between batches — just post them all.

---

## Step 10 — Write and Post Comments

Post all module-level comments first, then create summary frames last.

### Module-Level Comments (ALWAYS pinned on screenshot image nodes, one finding per pin)

Every individual finding — PASS, FAIL, or INCONCLUSIVE — MUST be posted as its own comment pinned directly on the relevant screenshot image node using `node_offset`. This is the primary comment type. Do NOT group multiple findings into a single comment. Do NOT post module-level findings on the parent container frame — every step has a placed screenshot.

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

The 4 summary sections are created as styled Figma frames placed to the right of the audit container's last screenshot. Load the `figma-use` skill before calling `use_figma`. Always pass `skillNames: "figma-use"` when calling `use_figma`.

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

**Positioning:** Read the audit container frame's x + width from `get_metadata`, then place the first summary frame ~200px to its right, y-aligned to the container's top. Each subsequent frame is positioned at previousFrame.y + previousFrame.height + 32.

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

---

## Step 11 — Confirm Completion

After posting, summarize what was posted:

- Count of findings by verdict (PASS / FAIL / INCONCLUSIVE) per step
- Total Baymard principles tested
- Audit container frame name and location in Figma
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

### Summary sections (Compliance Summary, Page Summary, Top Priorities, Flow Summary)
These are NOT comments — they are created as styled Figma frames via `use_figma`. See the "Summary Frames" section above.

---

## Batch Posting

Post comments in parallel batches of 3-4 using `curl ... &` and `wait` to avoid rate limits while staying efficient. Run module batches first, summary frames last. Do NOT ask the user for permission between batches — once confirmed in Step 9, post everything.

---

## Editing Existing Audits

If the user asks to revise or redo:

1. Fetch all existing comments via `GET /v1/files/:file_key/comments`
2. Delete relevant comments via `DELETE /v1/files/:file_key/comments/:comment_id`
3. Repost revised comments
4. If the user wants to re-run from a new video, also delete or archive the previous "Baymard Audit — [date]" container frame before placing a new one

---

## Prerequisite Installation & Auth Guidance

Whenever a prerequisite is missing (ffmpeg, MCP auth, Homebrew, etc.), always provide instructions for both environments:

- **Terminal/CLI users:** Short command to run
- **Desktop/web app users:** Step-by-step instructions including how to open a terminal (Spotlight > Terminal or Applications > Utilities > Terminal), what to paste, any upstream dependencies, and to say "done" when finished

Make it as smooth and low-friction as possible. Never assume the user is on a terminal.

---

## Anti-Patterns

- Don't accept URL-only or video-only inputs — both are mandatory
- Don't ask the user to manually place screenshots in Figma — auto-place via `use_figma` in Step 2D
- Don't call `use_figma` without first loading the `figma-use` skill (pass `skillNames: "figma-use"`)
- Don't query all 4 notebooks for every module — select the relevant one(s) based on page type
- Don't fire notebook queries sequentially — batch 3-4 in parallel
- Don't post comments without user confirmation (Step 9)
- Don't ask permission between comment batches — post them all once confirmed
- Don't invent Baymard principles — only cite what NotebookLM returns
- Don't mark PASS/FAIL without a specific Baymard principle reference
- Don't post a principle without a finding or a finding without a principle
- Don't attempt to play video files — extract frames via ffmpeg
- Don't assume ffmpeg is installed — check first
- Don't write multi-paragraph module comments — one finding per pin
- Don't use category headers in module comments
- Don't reference code, markup, or technical implementation
- Don't assume what competitors do or industry norms
- Don't describe what a module looks like — evaluate what it does for the user against Baymard principles
- Don't add observations that don't connect to the page goal
- Don't check NotebookLM auth at preflight (Step 0) — check it at Step 4 right before notebook discovery, and refresh again at Step 7 if expired
- Don't post module-level findings on the parent container frame — every step has a placed screenshot, so always pin on the screenshot image node with the full PASS/FAIL + MODULE + INTERACTION + PRINCIPLE [#] + FINDING format
