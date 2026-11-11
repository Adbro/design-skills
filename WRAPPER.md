# Make your own professor-brodo (a thin wrapper)

**For people:** hand this file to your Claude Code agent and say *"set up a professor-brodo wrapper for me using this."* It will ask you a few questions and write the skill. Or read on and do it by hand; it's one short file.

**For the agent:** the rest of this file is your instructions. Follow the steps in order.

---

## What a wrapper is, and when to make one

professor-brodo ships in a plugin. Plugin updates overwrite its files, so nobody should edit it. There are two places to put your own preferences instead:

| You want | Use |
|---|---|
| Different defaults for every crit you run (always quick, always web) | `~/.claude/professor-brodo/config.md`. No wrapper needed. |
| A **named variant** with its own trigger and rules: a crit for one course, one client, one team's design system, a house voice | A wrapper skill |

A wrapper is a small skill that calls `professor-brodo:professor-brodo` and hands it overrides. It holds only what's different. The checklist, reviewers and skeptic pass stay in the plugin and keep updating.

Override order, later wins: plugin defaults → `config.md` → **wrapper** → what the person says in the request.

## Step 1. Check the plugin is installed

Run `claude plugin list` and look for `professor-brodo@professor-brodo`. If it's missing, tell the person and offer to run:

```
claude plugin marketplace add Adbro/professor-brodo
claude plugin install professor-brodo@professor-brodo
```

Don't continue without it. A wrapper without the base skill does nothing.

## Step 2. Ask the person (one message, all at once)

Ask only what you can't infer. Suggest a default for each one.

1. **Name.** Short and kebab-case, like `studio-crit` or `ixd-201-crit`. It must not be `professor-brodo`.
2. **When should it run instead of the regular crit?** Turn this into trigger phrases ("crit this student project", "review for ACME"). Keep them narrower than the base skill's, or the two will compete.
3. **Which settings change?** Show them the keys and options:
   - `audience`: self | student | client | teammate
   - `voice`: professor (the default persona), plain, or the name of a voice skill they have installed
   - `volume`: comments per set, e.g. 12-20
   - `praise_share`: e.g. 1 in 6
   - `reviewers`: full | quick | a list of tags (flow, craft, a11y, platform, ai, ref, story)
   - `output`: list | page | figma
   - `platform`: ios | android | web | auto
4. **Context the crit should always know.** A course brief and rubric, a client's brand rules, a design-system doc, things to never comment on. Get it pasted, or get file paths.
5. **Calibration file.** Should this variant learn separately from their own crits? Default yes, at `~/.claude/professor-brodo/<name>-calibration.md`.
6. **Scope.** Just them (`~/.claude/skills/`), or a project or team (`.claude/skills/` in a repo, committed so others get it)?

## Step 3. Write the skill

Create `<scope>/skills/<name>/SKILL.md` from this template. Fill it in, and delete any override line the person didn't change.

````markdown
---
name: <name>
description: "<One sentence on what this variant is for.> Use for '<trigger 1>', '<trigger 2>'. Wraps professor-brodo with <audience/context> settings."
---

# <Name>

A variant of professor-brodo. Invoke the `professor-brodo:professor-brodo` skill with the Skill tool and run it as written, with these overrides. They sit at level 3 of its override order: they beat ~/.claude/professor-brodo/config.md and lose to anything the person says in the request.

## Overrides

```
audience:     student
voice:        plain
volume:       8-12
reviewers:    full
calibration:  ~/.claude/professor-brodo/<name>-calibration.md
```

## Always-on context

<Course brief, rubric, client brand rules, design-system notes. Keep it short and link files rather than pasting long docs, e.g. "Read ~/teaching/ixd201/rubric.md before writing.">

## Extra rules

- <Things to always do or never do, e.g. "Never comment on the logo; it's fixed by the client.">
````

Rules for writing it:
- **Don't copy professor-brodo's body into the wrapper.** If you copy it, the wrapper won't get the plugin's updates.
- Don't edit anything under `~/.claude/plugins/`.
- If a calibration file is set and doesn't exist yet, create it empty. The skill appends to it after each crit.
- Keep the whole file under ~60 lines. If it's growing, move the content into a referenced file.

## Step 4. Check it

1. Run `claude -p "List available skills whose name contains '<name>' or 'professor-brodo'. Names only."` from a neutral folder. Both `<name>` and `professor-brodo:professor-brodo` should appear.
2. Tell the person what you made: the path, the overrides, and the trigger phrases. Then tell them to try it in a **new** session, since skills load when a session starts.
3. If the person also has a standalone `professor-brodo` skill (in `~/.claude/skills/` or uploaded to their Claude account), point it out. It competes with the plugin version and they may want to remove it. Don't delete it yourself.

## Changing it later

Edit the wrapper's `SKILL.md` directly. Plugin updates (`/plugin marketplace update professor-brodo`) never touch it.
