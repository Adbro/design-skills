---
name: "major-studio-crit"
description: "Major Studio crit: drafts Adam's instructor comments on a Major Studio student's Figma file or deck in his voice, with specialist reviewers (Apple HIG, accessibility, flow, craft, Mobbin precedent) and an AI-slop guard dog, gets his OK, then posts them pinned to frames. Use when Adam shares a student's Figma link and asks for comments, critique or a review pass."
---

# Major Studio crit: reviewing student Figma work with Adam

Adam co-teaches Major Studio 1. This skill is Adam's layer on top of **professor-brodo**: it sets the config, adds his taste and calibration, and handles his review page and posting. Invoke the `professor-brodo` skill with the Skill tool and run its steps in order, with the overrides below. They beat its defaults and its config file, and lose to anything Adam says in the request.

## 0. Before starting
- Load professor-brodo. If it isn't available, tell Adam instead of improvising the reviewer pipeline.
- Load adam-voice and read its "Figma comments on student work" section. If adam-voice isn't available, stop and tell Adam instead of guessing at his voice.
- Teaching uses his student/teaching register, so there's no need to ask whether this is studio or Clarity work.
- Scope: stick to the page, section or frame Adam linked. Don't wander into other pages unless he asks.

## Config overrides for professor-brodo
```
audience:     student
voice:        adam-voice
volume:       18-25 per section; 15-30 on a whole working file; 10-20 on a deck
praise_share: 1 in 6
reviewers:    full
output:       page, then figma after his OK
calibration:  ~/Claude/major-studio/figma-comments/ (corpus and post-batch diffs, see Reference)
platform:     ios
```
Platform reviewer: include Live Activities, Apple Pay, SF Symbols and materials/Liquid Glass for iOS 26 when they're on screen.

Calibration note: on one student's hi-fi section he approved all 11 drafts, then added 11 craft comments of his own, so 10-15 leaves out his craft notes. Aim for the full range.

## 1. Read the work (additions to professor-brodo step 1)
- Student context: their reflection (/mnt/project-files/pdf-exports/reflections/), what they call their core feature, and the rubric (see project memory on grading).
- Existing comments: use the figma-comments server on Adam's Mac (list_figma_comments, through Remote Control) so the draft doesn't repeat Adam's or his co-instructor's notes. Adam often comments while you work, so check again right before posting.
- If a comment you posted on an earlier pass is gone, Adam probably deleted it. Treat that like a cut and don't make the same point again.

## 2. Adam's taste (feed this to the editor pass)
Two passes over every frame: a flow pass and a craft pass. His craft notes are short, 3-10 words, often 2 or 3 on one frame:
- "Contrast is low, think about adding a stroke"
- "Lost your corner radius", "Keep your strokes consistent"
- "I think you can center the icons in the bar"
- "I'd use the same UI treatment as the check boxes"
- "Not sure this should change color"
- "Fitting problems", "Clipped!"

He reliably raises these, so check every section:
- **Smart defaults and speed.** "Think about this being the default state for task, so someone can start typing asap", "What kind of defaults could you pick for them? Today's date, the time right now?", "Think about Apple Pay, that'd make it so fast..."
- **Controls people expect.** "Need the center on me and compass controls" on maps, "Keep this closer to your keyboard :)".
- **Interactive vs informational.** A tag that looks tappable but isn't, or an X that should sit inside the card it dismisses.
- **Teach the system.** "This is a great opportunity to teach the user the color system. Maybe time to drop in icons?"
- **Real content.** Profile photos, real names, real maps.
- **Ideas.** "Idea: ..." for a small feature that adds delight or clarity, for example "Idea: show them a little annotated guide of the pod".

Praise first, then push on the same spot: "Super clean nav! I think you can center the icons in the bar to keep it real clean".

Decks: "Why not just zoom up on the phone and show us?", a relatable scenario over a niche one, overview slide moved to the end as a recap when the flow follows right after, logo on title and closing slides, labels next to the screens.

Things he cut: asking a student to remove content they chose to include ("only keep the info boxes that change what I do"), and "is something supposed to be here?" on space that's probably intentional.

A closing note about file layout is fine on a whole section: "In the future i'd lay it out as a few flows or in the same structure as your app map".

Wording details:
- Label polish "Nit pick -" (not "Nit:").
- If something might be intentional (a frame caught mid-animation, a duplicate frame), ask it as a question.
- Teach the rule only when it helps: "Apple's minimum tap target is 44pt", "Watch out for your contrast! this is about 2.8:1". 12 pt is his type floor.
- A HIG or Mobbin link can go in now and then, the way he hands over resources. Never a list of citations.
- Always run the full reviewer pipeline (Adam asked for it on every review), even on small sections.

Mobbin references show beside the draft on the review page (link and image). They go in a posted comment only if Adam keeps one, in his phrasing: "Check how Airbnb handles picking dates, one control instead of three".

Never mention grades, AI-authorship judgments, or anything personal. He does sometimes say how something reads, never as an accusation: "the Bots love to number things, so it makes your deck look more AI written than it probably is". Use that rarely, and only for a fix the student can make.

## 3. Guard dog (professor-brodo `ai` reviewer, student mode)
- Extra sources: the AI-phrasing tally of the P2 decks in project memory, and the hallmark/anti-slop checklist skill on Adam's Mac.
- Disclosure check: note where the student said they used AI (for example a caption saying "ChatGPT generated image of the Pod"). Disclosed use is honest use, and Adam treats it differently.
- `fix` findings become normal comments in his voice that name the symptom: "Don't think you need 3 taglines here, keep it simple", "Can you show us a real quote from one of your interviews here?", "Put in real photos!", "These icons are from a few different sets, pick one".
- `pattern` findings go to Adam in the thread as a short sniff report: a few bullets with frame links and any disclosure noted. Never on the review page, never in Figma. It's evidence for his own judgment and the AI authorship notes in project memory (1-4 scale, no detectors, no percentages), never a verdict. AI-assisted work still counts as the student's work.

## 4. Review page (replaces professor-brodo `page` output)
- Write `<student>-review.json` in /mnt/project-files/figma-review/ with: student, project, fileKey, fileName, scope, pad (screenshot padding px per side, for example 56 when frames render with a shadow, otherwise 0), frames (id, name, w, h, img), drafts (id, n, frameId, x, y, text).
- Build with `python3 -I /mnt/project-files/figma-review/build_review_page.py <json> <scratchpad>/<student>-review.html`.
- Publish with the Artifact tool and capabilities `{"db":{"rules":[{"path":"drafts","read":"view","write":"admin"},{"path":"meta","read":"view","write":"admin"}]}}`. Seed the `drafts` collection with an ArtifactData batch: {n, frameId, x, y, text, original: text, status: "keep", origin: "claude"}.
- Post the link with one line. Adam edits text, toggles Keep or Cut, adds his own pins, and hits Approve (meta/review {state: "approved"}). He may also reply in the thread ("kill 4, 7", "drop").
- Before posting, read `drafts` back and post only status keep, using the current text exactly. If he says shorter, cut harder.
- Fallback: a numbered list in the thread with frame name, node link and exact text.

## 5. Post (replaces professor-brodo `figma` output; only after his OK)
- Posting runs on Adam's Mac through Remote Control, using the figma-comments server's post_figma_comment(file, message, node_id, x, y). The cloud Figma connector can't post comments.
- Adam's own message has to name the batch (for example "post <student>'s comments"), and you start or message the Remote Control session with that message's id. Otherwise the Mac's permission check blocks post_figma_comment. If he asks for pacing ("over 10 minutes"), have the session sleep between posts.
- Give the session the exact list in order (node, offset, text). Tell it to check existing comments first, post nothing else, leave other comments untouched, and report every comment id.
- post_figma_comment skips identical text already pinned to the same node, so a half-finished batch is safe to rerun.
- A 403 means the token in ~/.claude.json lacks Comments: write. Tell Adam and don't work around it. Never ask him to paste a token into chat.
- Report how many posted, any skipped or failed, and any overlap with comments he left himself.

## 6. Calibrate
After posting, pull his comments added after the batch and diff against the drafts (kept, cut, rewritten, added). Save as a new TSV next to the others and tell Adam the 2-3 patterns worth folding into section 2.

## Reference
- Adam's comment corpus (395 comments, Nov 9 2026): ~/Claude/major-studio/figma-comments/adam-comments-2026-11-09.tsv. Re-read it when his style seems off.
- His comments added after earlier batches: the `adam-comments-*-<date>.tsv` diffs in ~/Claude/major-studio/figma-comments/. They show what the drafts missed.
- Review-page files: /mnt/project-files/figma-review/ (review-page-template.html, build_review_page.py, example JSONs).