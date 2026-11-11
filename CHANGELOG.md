# Changelog

## major-studio-crit 0.2.0
- Now wraps professor-brodo instead of the old standalone design-crit skill. Still needs the `adam-voice` skill. Student and co-instructor names removed.

## major-studio-crit 0.1.0
- Added. Formerly the `professor-bro` claude.ai skill ("Cyber Brodo Professor"); same content, renamed.

## professor-brodo 0.4.0
- Renamed from design-professor: the skill is now Professor Brodo, a persona you tap in to crit your work. Plugin, skill and settings folder are `professor-brodo` (`~/.claude/professor-brodo/`). Reinstall with `/plugin install professor-brodo@professor-brodo`, uninstall `design-professor`, and move any `~/.claude/design-professor/` files over. Triggers now include "Professor Brodo, look at this". `voice: professor` is still the key. The repo and marketplace are now `Adbro/professor-brodo` (was `design-skills`): run `/plugin marketplace remove design-skills` and `/plugin marketplace add Adbro/professor-brodo`.

## design-professor 0.3.0
- New default `voice: professor`: one-line opener, pointed questions on block findings, an "Office hours" closer, and at most one "See me." Nits, the fix prompt and posted Figma comments stay plain. `voice: plain` turns it off.

## design-professor 0.2.0
- Renamed from design-crit. Plugin, skill and settings folder are now `design-professor` (`~/.claude/design-professor/`). Reinstall with `/plugin install design-professor@design-skills` and move any `~/.claude/design-crit/` files over.

## design-crit 0.1.0
- First release: flow, craft, a11y, platform, AI-slop, Mobbin and deck-story reviewers; skeptic pass; self/student/client modes; fix prompt; calibration file.
