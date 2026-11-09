# design-skills

Claude skills for design work, by Adam Brodowski.

## Install (Claude Code)

```
/plugin marketplace add Adbro/design-skills
/plugin install design-professor@design-skills
```

Update later with `/plugin marketplace update design-skills`.

## design-professor

Ask for a crit on a Figma link, deck, PDF, screenshots, or a live prototype URL: "crit this", "review my design", "does this look AI-made".

It talks like a studio professor at the crit wall: a one-line verdict up top, pointed questions on the findings that matter, and an "Office hours" close with the one thing to fix first. Nits stay terse. Set `voice: plain` to turn the persona off.

See a full example: [a crit of inspofolder.com](examples/inspofolder-crit.md).

Defaults are set for reviewing your own work: a direct punch list, the AI-slop report up top, and a paste-ready fix prompt for the Claude session that built it.

**Your settings live outside the plugin**, so updates never overwrite them. Create `~/.claude/design-professor/config.md` with any keys you want to change:

```
audience:     self
voice:        plain
volume:       12-20
reviewers:    quick
platform:     web
```

Keys and options are listed at the top of the skill. If you keep a `~/.claude/design-professor/calibration.md`, the skill reads it before writing and appends what you keep, cut and add after each crit, so it learns your taste.

**Want a named variant** (a crit for one course, client or team, with its own trigger and rules)? See [WRAPPER.md](WRAPPER.md). Hand it to your agent and it will set one up with you.

Optional connectors: Figma (to read Figma files), Mobbin (for shipped-app references).
