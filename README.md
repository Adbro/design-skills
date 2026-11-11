# professor-brodo

Claude skills for design work, by Adam Brodowski.

## Install (Claude Code)

```
/plugin marketplace add Adbro/professor-brodo
/plugin install professor-brodo@professor-brodo
/plugin install major-studio-crit@professor-brodo   # optional, Adam's class wrapper
```

Update later with `/plugin marketplace update professor-brodo`.

## professor-brodo

Tap in Professor Brodo to look at your work: "Professor Brodo, look at this", "get the professor on this". Or just ask for a crit on a Figma link, deck, PDF, screenshots, or a live prototype URL: "crit this", "review my design", "does this look AI-made".

Professor Brodo talks like a studio professor at the crit wall: a one-line verdict up top, pointed questions on the findings that matter, and an "Office hours" close with the one thing to fix first. Nits stay terse. Set `voice: plain` to turn the persona off.

See a full example: [a crit of inspofolder.com](examples/inspofolder-crit.md).

Defaults are set for reviewing your own work: a direct punch list, the AI-slop report up top, and a paste-ready fix prompt for the Claude session that built it.

**Your settings live outside the plugin**, so updates never overwrite them. Create `~/.claude/professor-brodo/config.md` with any keys you want to change:

```
audience:     self
voice:        plain
volume:       12-20
reviewers:    quick
platform:     web
```

Keys and options are listed at the top of the skill. If you keep a `~/.claude/professor-brodo/calibration.md`, the skill reads it before writing and appends what you keep, cut and add after each crit, so it learns your taste.

**Want a named variant** (a crit for one course, client or team, with its own trigger and rules)? See [WRAPPER.md](WRAPPER.md). Hand it to your agent and it will set one up with you.

Optional connectors: Figma (to read Figma files), Mobbin (for shipped-app references).

## major-studio-crit

Adam's own wrapper for Major Studio: drafts instructor comments on student Figma files and decks, gets his OK on a review page, then posts them pinned to frames. Runs on professor-brodo and needs the `adam-voice` skill, the figma-comments MCP server and his course files, so it's mostly here as a worked example of a wrapper.
