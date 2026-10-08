# personal-tutor

![thumbnail](assets/thumbnail.png)

An AI-powered learning system built as a pi configuration: the teaching philosophy encoded in a skill, a few small extensions, and agent definitions.

## What's in it

- `skills/teach/` - the philosophy and the process, including a `knowledge-graph.md` record kept at the project root so concepts verified in one session don't get re-probed from zero in the next; fully mastered subgraphs compress into their generative roots for later reuse
- `skills/visualize/` - adds a correct, minimal diagram to a lesson when an idea is clearer as a picture
- `extensions/ask-user-question/` - the agent asks you questions through a UI popup
- `extensions/quiz/` - graded questions with instant feedback (✓/✗, correct answer, explanation)
- `extensions/md-log/` - link a markdown file to the session
- `extensions/visual-tools/` - tools for visualization subagents
- `agents/` - `researcher`, `svg-maker`, `mermaid-maker`: the subagents the system delegates to

## Install

1. Install [pi](https://github.com/earendil-works/pi), [tmux](https://github.com/tmux/tmux) and [pnpm](https://pnpm.io):

   ```bash
   brew install tmux pnpm        # macOS
   sudo apt install tmux         # Debian/Ubuntu
   sudo dnf install tmux         # Fedora
   sudo pacman -S tmux           # Arch
   ```

   On Linux, install pnpm as described on [pnpm.io/installation](https://pnpm.io/installation). tmux has no native Windows version, and the subagent extension needs it.

2. Clone this repo as a `.pi` directory, from your learning project's root:

   ```bash
   git clone https://github.com/manuelebeh/personal-tutor .pi
   ```

3. Install the dependencies of the visual tools (Mermaid rendering):

   ```bash
   cd .pi/extensions/visual-tools && pnpm install && cd -
   ```

4. Install the subagent extension. The researcher and the visual makers run as subagents, in tmux panes:

   ```bash
   pi install git:github.com/amosblomqvist/pi-interactive-subagents
   ```

5. Start pi **inside tmux**. The subagent extension is tmux-only, so outside tmux the subagents cannot spawn:

   ```bash
   tmux new -A -s pi 'pi'
   ```

To avoid retyping it, add an alias to your `~/.zshrc` (or `~/.bashrc`). This syntax is for zsh and bash:

```bash
alias pit='tmux new -A -s pi "pi"'
```

Then `pit` starts pi inside tmux, and reattaches to the `pi` session if it already exists.

(Or copy the pieces you want into your existing project config.)

### Models

Each agent in `agents/` sets its own model, in the frontmatter:

| Agent | Model | Needs |
| ----- | ----- | ----- |
| `svg-maker`, `mermaid-maker` | `anthropic/claude-sonnet-5` | an Anthropic credential |
| `researcher` | `openrouter/z-ai/glm-5.3` | an OpenRouter credential |

Check a provider with `pi auth check --provider anthropic` (or `openrouter`). To use another model, edit the `model:` line of the agent.

## Requirements

- [pi](https://github.com/earendil-works/pi)
- [tmux](https://github.com/tmux/tmux) and a subagent implementation, so the system can spawn the researcher and the visual makers. Recommended: [pi-interactive-subagents](https://github.com/amosblomqvist/pi-interactive-subagents) (tmux only). With it, everything works out of the box. Any other implementation works too, but expect to adapt the agent definitions, e.g. `agents/researcher.md` lists `safe_bash` in its tools, which is specific to that extension.
- [pnpm](https://pnpm.io) (Node.js) for the Mermaid renderer in `extensions/visual-tools`.
- `ask-user-question` - use the copy bundled here. If your setup already has an `ask-user-question` extension, use **this** one in its place. Popups from different extensions serialize through a shared UI lock, which only works when it's the same implementation.

## Notes

You can run the system without subagents. The main session does the teaching. You just lose the researcher (truth verification) and the generated visuals.

The teaching skill is written for one learner (me). Edit the skill to fit how you learn best.

## Troubleshooting

**The `md-log` file doesn't seem to update live in Obsidian.** `md-log` writes the full file synchronously (`fs.writeFileSync`) on every message and every quiz/question event - the content on disk is always current the moment a reply or quiz result lands, nothing is batched or delayed on this end. If Obsidian isn't visibly refreshing, it's Obsidian's editor not hot-reloading an externally-changed file while a pane is actively focused in edit/Live Preview mode - a known Obsidian behavior, not something this extension controls. Workarounds: view the note in **Reading view** (it reliably refreshes on external change), or click away to another file and back to force a reload.

## Implementation notes

This system implements:

1. **Live DAG visualization** - dependency maps update with colored node status (pending/current/verified/misconception) as each concept is confirmed.
2. **Cross-session knowledge graph** - `knowledge-graph.md` persists verified concepts with spaced-repetition intervals, so subsequent lessons avoid re-probing known material.
3. **Collapse and expand** - fully mastered subgraphs compress into their generative roots at lesson end, expanding only when needed or when knowledge decays.
4. **Mermaid label validation** - ensures node labels are Obsidian-compatible by enforcing quotes on special characters.
5. **Gender-neutral language** - uses they/them/their pronouns throughout.

## License

This project is licensed under the MIT License - see [LICENSE](LICENSE) for details.

You are free to use, modify, and distribute this system for personal, educational, or commercial purposes.
