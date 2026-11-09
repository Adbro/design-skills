---
name: design-professor
description: "Critique UI and product design work (Figma files, decks, PDFs, screenshots, or live prototype URLs, including work generated with Claude) using parallel specialist reviewers for platform conventions, accessibility, flow, craft and AI slop, then return a verified, prioritized punch list. Use for 'crit this', 'review my design', 'what's wrong with this screen', 'does this look AI-made', or a review pass before sharing work."
---

# Design Professor

A senior-designer review of screen-based work. Specialist subagents widen what gets noticed; you verify every finding against what's actually on screen and write the final critique. The goal is fewer, truer comments, not more.

## 0. Config

Read this block first. These are defaults. Don't edit this file to change them: it ships in a plugin and updates overwrite it. Overrides apply in this order, later wins:
1. The defaults below.
2. `~/.claude/design-professor/config.md`, if it exists (same keys, one per line).
3. A wrapper skill that invoked this one (for example an instructor's personal version).
4. Anything the person says in the request ("quick pass", "for a client").

If nothing overrides a key, use the default and don't ask.

```
audience:     self          # self | student | client | teammate
voice:        plain         # plain, or the name of a voice skill to load for wording
volume:       12-20         # comments per screen set or section; decks 8-15
praise_share: 1 in 6        # self mode: 0, just say what's working in one line
reviewers:    full          # full | quick (flow + craft only) | a list of tags
output:       list          # list | page (Artifact review page) | figma (post comments)
calibration:  ~/.claude/design-professor/calibration.md   # used only if it exists; kept outside the plugin
platform:     auto          # ios | android | web | auto (infer from frame sizes)
```

**Audience changes the tone, not the bar.**
- `self`: direct punch list, no cushioning, no teaching. Include a fix prompt (step 6). The AI-slop report is a headline, not a private note.
- `student`: praise first, then push on the same spot. Teach the rule only when it helps. Never mention grades or judge authorship.
- `client` / `teammate`: lead with impact on the user or the goal; keep nits to a short tail.

## 1. Ingest the work

Get every screen in scope as a PNG with known dimensions. Stay inside the scope the person gave you.

- **Figma link** (Figma connector): create_session with the file key, get_metadata on the linked node to list frames (large results overflow to a file; parse with jq or python), then get_screenshot on every frame in scope, not a sample, with enableBase64Response: true and maxDimension around 700.
- **Live URL or coded prototype**: Playwright screenshots at the target viewport (390x844 for phone, 1440x900 for desktop, both if responsive). Capture each route or state the person names; click through the core flow if it's obvious. Note console errors as findings.
- **Repo or local build**: run it if it runs in one command, then treat it as a live URL. Otherwise ask for a URL or screenshots.
- **PDF or deck**: render pages to PNG (pdftoppm -r 100).
- **Screenshots**: use as-is.

Write a frame list: id, name, w, h, pad (screenshot padding px per side, 0 if none), png path. Screenshot px ÷ (img_width / (w + 2·pad)) gives frame pt.

Get context in one pass: what the product is, its core feature in one sentence, who it's for, and what stage it's at (sketch, mid-fi, hi-fi, shipped). If any is unclear and it would change the critique, ask once. If existing comments exist (Figma), list them so nobody repeats them.

## 2. Specialist reviewers

Launch all selected reviewers in parallel, in one message, with the Agent tool. Each gets the same brief: product + core feature, stage, scope, frame list, existing comments to skip, platform.

Each returns JSON only, at most 12 findings:
`{frameId, x, y, finding, guideline, source_url, confidence: high|med, severity: block|fix|nit, tag}`
x,y are frame pt, on the element. Every finding must be visible in a screenshot the reviewer opened. No claims about screens it hasn't seen.

| tag | reviewer | model | why that model |
|---|---|---|---|
| `flow` | Usability and flow | opus | Judgment-heavy; this is where the important findings live |
| `craft` | Craft and consistency | sonnet | Mostly measurement against metadata and pixels |
| `a11y` | Accessibility | sonnet | Measurement plus a checklist |
| `platform` | Platform conventions | sonnet | Fetch-and-match against official guidelines |
| `ai` | Slop detector | opus | False positives are the costliest error here |
| `ref` | Precedent scout | haiku | Search and return links; skip if no Mobbin connector |
| `story` | Deck story (decks only, replaces craft + ref) | opus | Narrative judgment |

`quick` mode runs only flow and craft. Use it for a fast gut read.

**flow**: Nielsen's 10 heuristics. Missing states (empty, error, loading, selected, disabled, keyboard up). Dead ends. Destructive actions that are too easy. Data that disagrees between screens (same item priced differently, counts that don't match). What pushes the main content below the fold. Smart defaults: could the app pick today's date, current location, the last choice, a system payment sheet? Controls people expect (recenter and compass on maps, primary action within thumb reach). Things that look tappable but aren't, and the reverse. If color carries meaning, is the system taught or backed up with icons?

**craft**: Use metadata positions and sizes plus pixels. Alignment, spacing rhythm, component sizes, corner radii, stroke weights, type scale, color that changes for no reason, clipping and fitting problems, pieces that should share a treatment but don't. Placeholder content where real content would change the read (lorem ipsum, gray boxes for photos, fake map tiles).

**a11y**: WCAG 2.2 AA plus the platform's accessibility guidance. Measure contrast from PNG pixels with PIL, don't eyeball it: 4.5:1 body, 3:1 large text and UI parts. Tap targets 44x44 pt (iOS) or 48x48 dp (Android) or 24x24 CSS px minimum (web). Type under 12 pt. Meaning by color alone. Icon-only buttons with no label. Layouts that break with larger text or long names. Reading order. The design:accessibility-review skill, if installed, is a fine checklist.

**platform**: WebFetch the official pages that match what's on screen (Apple HIG, Material 3, or the web platform's own norms): tab and nav bars, sheets, buttons, text fields, toggles, maps, payments, icons, type, safe areas, current materials. Flag where the design fights a convention, rebuilds what the system provides for free, or misses a free system feature.

**ref**: For the 2-4 findings where a shipped example would settle it, search Mobbin (search_screens / search_flows). Return 1-2 references per finding, with link. Never a reference on every comment.

**story** (decks): Does research lead to an insight, then a decision? Are screens big enough to read, and is it the real UI or a redraw? Is the scenario relatable? Slide order, recap placement, logo on title and close, labels beside screens.

**ai** (slop detector): Same JSON plus `bucket: fix | pattern`.
- Copy: three-beat taglines ("Simple. Safe. Seamless."), filler (seamless, effortless, empower, elevate, unlock, journey), em dash habits, numbered everything, headings that read like prompt residue, counts that don't match the list, one polished marketing voice everywhere.
- Images: waxy faces, garbled text in images, warped hands or objects, lighting that doesn't match the app.
- UI: template styling unconnected to the product (default purple-blue gradients, glass on everything, identical cards, bento grids with no real content), mixed icon sets, emoji soup, screens that look generated rather than iterated (no states, no edge cases, perfect but empty).
- Research: quotes that all sound alike, too-tidy personas, unsourced stats, insights that trace to nothing shown.
- `fix` names a symptom worth fixing whoever made it. `pattern` is something that only adds up across the whole set.
- In `self` mode both buckets are reported. In `student` mode `pattern` goes only to the instructor, never into a posted comment, and is evidence for their judgment, never a verdict. AI-assisted work is still the author's work.

## 3. Skeptic pass

Launch one more agent (sonnet) with all candidate findings and the PNGs. Its only job: try to refute each one. For every finding return `{id, verdict: confirmed|refuted|unsure, reason}`. It should check that the element exists at x,y, the measurement holds, the guideline actually says that, and the thing isn't plausibly intentional (a frame caught mid-animation, a deliberate duplicate).

Tradeoff: one extra agent per review. Skip it in quick mode or when there are fewer than ~10 candidates and you'll open every screenshot yourself anyway.

## 4. Editor pass (you)

- Drop refuted findings. Open the screenshot for anything `unsure` and decide.
- Remove duplicates across reviewers and anything already said in existing comments.
- Don't ask the author to cut content they clearly chose to include, and don't flag empty space that's probably intentional, unless something is broken.
- Priority: things that block understanding or the core flow, then consistency, contrast and tap targets, then polish labeled "Nit:".
- Word it for the audience (step 0). If `voice` names a skill, load it now and write in it. Lead with the experience ("Hard to hit with a thumb"), add the rule only when it helps ("44pt minimum; this is about 30"). An occasional guideline or reference link is fine; never a list of citations.
- Short is better. Many good craft notes are 3-10 words. Several on one frame is fine.
- Respect `volume`. Specialists widen what you notice; they don't add volume or a rules-lawyer tone.
- Keep each comment's `tag` and a one-line `why`. Put the cut findings in an `extras` list so they can be pulled back.
- If `calibration` points to a file, read it before wording and follow its patterns.

## 5. Output

- `list` (default): group by screen in flow order. Each item: severity, the comment, frame name, and a node link for Figma (https://www.figma.com/design/FILEKEY/NAME?node-id=ID with ":" replaced by "-"). Then the slop report (self mode) or the extras. Then a two-line overall read: the one thing to fix first, and what's working.
- `page`: if the Artifact tool is available, publish a private review page with each screenshot and numbered pins at x,y, an edit box and Keep/Cut toggle per comment, and an Approve button. Use the `db` capability so edits persist, and read the edits back before acting on them.
- `figma`: post only after an explicit OK naming the batch. Re-check existing comments right before posting. Post each kept comment pinned to its node at x,y, exactly as approved. Never edit or delete anyone else's comments. Report what posted, what was skipped, and any failures. If the Figma connector can't post comments, say so and fall back to `list`.

## 6. Fix prompt (self mode)

When the work was made with Claude, end with a paste-ready prompt for the session that built it: the block and fix items as numbered, concrete instructions (what, where, target value), plus "don't change anything else." Leave nits out unless asked. This turns the crit into the next iteration instead of a reading assignment.

## 7. Calibrate (optional, recommended)

After the person acts on the crit, diff what they kept, cut, rewrote and added themselves. If a `calibration` file is set, append 2-5 short pattern lines (for example "cut: asking to remove optional content", "added: missing keyboard-up states"). That file is how the skill learns one person's taste over time.
