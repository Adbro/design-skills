# Example crit: inspofolder.com

A real run of professor-brodo 0.3.0 with its defaults: `audience: self`, `voice: professor`, `reviewers: full`. The input was one desktop screenshot of [inspofolder.com](https://inspofolder.com), the author's private inspiration archive: a dark masonry wall of about 7,400 saved references with semantic search and a floating dock at the bottom.

Five reviewers ran: flow, craft, accessibility, platform and AI-slop. The Mobbin reference scout was skipped because no Mobbin connector was available. Instead of the skeptic agent, the findings were checked against the site's own code, which threw out six claims (listed at the end). What follows is the crit as delivered.

---

*A disciplined wall with one dirty secret: the references you save most are the ones it shows worst.*

**Must fix**

1. **Long captures are blurry, and it's the thumbnails, not the captures.** Why are the most information-dense references on the wall also the blurriest? `lib/thumbs.js` caps the grid thumbnail at 640px on its *long* edge, so a tall page capture gets a thumbnail about 155px wide. The wall shows it about 309px wide, which is about 4× the pixels it has. Craft measured those tiles at about 1/100th the sharpness of their neighbours. Your local copy has 394 images like this. Make the thumbnails for tall captures by width instead. *See me.*

**Fix**

2. **The same reference appears twice on one screen.** How many times do you need to see this legal site? "Practice Made Perfect" shows up twice, in columns 4 and 5, and "Build a Frontier Legal Organization" twice, in columns 2 and 3. They're near-duplicates, not byte-for-byte copies, so a perceptual hash would find them where an exact match won't.
3. **The fade on cropped captures turns white pages grey.** It's a dark overlay at 80% over the bottom 38% of the tile, so a white page ends in a muddy grey slab instead of fading into the background. Fade the image itself with `mask-image`, so every crop ends cleanly in the page colour.
4. **The gear opens Filters, but a gear means Settings.** Use the Lucide sliders icon at the same size and 2px stroke. The tooltip already says the right thing; the icon doesn't.
5. **What order is this wall in?** I can't tell, and neither can a friend on a guest code. Newest first? Shuffled? There's no cue, so new saves have no place to be found. One quiet word in the dock would do.
6. **Nothing tells people the search is semantic.** "Search 7,367 images" is good status, but people will type filenames. Two or three example queries on focus would teach it.
7. **There's no `/` to focus search.** Desktop habit says it should be there, and nothing in `app.js` handles it.
8. **The placeholder sits right on the contrast line.** It measured 4.3–4.7:1 against a glass background that changes with what's behind it, so it's on the 4.5:1 line. Lighten it to about `#8e8e94`.

**Nits**

- Dark captures fade to black and lose their bottom edge, so the grid's rhythm stalls there.
- 4px gutters let dark tiles fuse together. Try 6px, if that doesn't fight the look you want.

**AI-slop report:** clean. The slop reviewer called the UI "restrained and specific, not slop." Its one pattern note was that a glass pill is a familiar default. Ignore it; the pill was built from the author's own Figma file.

**Office hours**
Fix first: the tall-capture thumbnails. Every other note is about polish, and this one is about whether you can see your own archive.
Working: the grid. The gutters measure the same 4px everywhere, the dock is centred to the pixel, and putting the live count in the placeholder earns its keep.
For next crit: bring the wall after a real search, plus one with two filters on. That's the half of the product this shot doesn't show.

**Fix prompt** (paste into the session that built the site):

```
1. lib/thumbs.js: for images with height/width >= 2.2 (LONG_RATIO), make the grid thumb 640px WIDE, cropped to 1:2 from the top (sharp resize 640x1280, fit cover, position top). Add a thumb version to the /thumb URL so the year-long immutable cache can't serve the old file, then regenerate the thumbnails for tall images.
2. public/style.css .cell.tall: replace the ::before dark overlay with mask-image on the img (linear-gradient(#000 62%, transparent)); keep the hover lift.
3. Swap the Filters gear for Lucide sliders-horizontal at --ic size, 2px stroke.
4. "/" focuses the search field when no input is focused and the lightbox is closed.
5. Search placeholder colour: at least 4.5:1 on the darkest dock fill (about #8e8e94).
6. Report near-duplicate pairs with a perceptual hash. List only, delete nothing.
Don't change anything else.
```

---

**Cut in verification.** The reviewers raised these, and the site's code disproves them:

- The dock buttons have no labels or tooltips. *They do: `aria-label` and `title` on each.*
- The last row of tiles is stuck under the dock. *The wall has bottom padding equal to the dock plus its margin.*
- There's no fade behind the dock. *There is: a 143px radial fade on desktop.*
- The mark has no clear function. *It's labelled "Back to the start" and scrolls there.*
- Tiles aren't real links. *Every image has its own `/i/:id` URL.*
- The mark looks like a stock glyph. *It's the author's own mark, from their Figma file.*
