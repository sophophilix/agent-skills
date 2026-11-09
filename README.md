# agent-skills

Agent skills for technical writing, Python engineering, and solution
architecture. The repository is a Claude Code plugin marketplace. Each plugin
groups related skills, and you can install one plugin at a time.

| Plugin | Skills |
|---|---|
| `writing` | `technical-writing`, `humanizer` |
| `python` | `python-standards`, `python-style-guide`, `python-testing`, `python-architecture`, `python-review` |
| `architecture` | `senior-enterprise-solution-architect` |

The skills refer to each other. `technical-writing` loads `humanizer`, and the
Python skills load `python-standards` and `technical-writing`. Install
`writing` with any other plugin.

## Install in Claude Code

1. Add the marketplace:

   ```
   /plugin marketplace add sophophilix/agent-skills
   ```

2. Install the plugins you need:

   ```
   /plugin install writing@agent-skills
   /plugin install python@agent-skills
   ```

Claude Code gives plugin skills a prefix, for example
`writing:technical-writing`.

## Use without the plugin system

Each skill is a folder with a `SKILL.md`. Copy or link a folder into your
agent's skills directory, for example `~/.claude/skills/`:

```bash
ln -s "$PWD/plugins/writing/skills/technical-writing" ~/.claude/skills/technical-writing
```

## The STE linter

`technical-writing` includes `scripts/ste-lint.py`. It checks procedures and
warnings against the structural rules of ASD-STE100 Simplified Technical
English. It needs Python 3 and no packages:

```bash
python3 plugins/writing/skills/technical-writing/scripts/ste-lint.py docs/how-to/deploy.md
```

The linter is third-party code. See `THIRD_PARTY.md` for its source, licence,
and review status.

## Maintain the skills

1. Edit a skill under `plugins/<plugin>/skills/<skill>/`.
2. Raise the `version` in the skill's front matter for a change in behaviour.
3. Raise the plugin's `version` in `plugins/<plugin>/.claude-plugin/plugin.json`
   when you publish.
4. Push. The `check` workflow validates the front matter and the manifests,
   and it runs the linter's self-test.

## Licence

MIT. See `LICENSE`. `humanizer` and `ste-lint.py` keep their own MIT notices.
See `THIRD_PARTY.md`.
