# learn

[![video](assets/thumbnail.png)](https://www.youtube.com/watch?v=kzcI5F4tGiU)

My AI learning system from this video: [How I Use AI to Learn Things](https://www.youtube.com/watch?v=kzcI5F4tGiU).

This is a personal system I built for myself, shared as-is. Built as a pi configuration: the teaching philosophy encoded in a skill, a few small extensions, and agent definitions.

## What's in it

- `skills/teach/` — the philosophy and the process, including a `knowledge-graph.md` record kept at the project root so concepts verified in one session don't get re-probed from zero in the next; fully mastered subgraphs compress into their generative roots for later reuse
- `skills/visualize/` — adds a correct, minimal diagram to a lesson when an idea is clearer as a picture
- `extensions/ask-user-question/` — the agent asks you questions through a UI popup
- `extensions/quiz/` — graded questions with instant feedback (✓/✗, correct answer, explanation)
- `extensions/md-log/` — link a markdown file to the session
- `extensions/visual-tools/` — tools for visualization subagents
- `agents/` — `researcher`, `svg-maker`, `mermaid-maker`: the subagents the system delegates to

## Install

This repo **is** a `.pi` directory. From your learning project's root:

```bash
git clone https://github.com/amosblomqvist/learn .pi
```

Then open pi in that directory. (Or copy the pieces you want into your existing project config.)

## Requirements

- [pi](https://github.com/earendil-works/pi)
- A subagent implementation, so the system can spawn the researcher and the visual makers. Recommended: [pi-interactive-subagents](https://github.com/amosblomqvist/pi-interactive-subagents) (tmux only). With it, everything works out of the box. Any other implementation works too, but expect to adapt the agent definitions, e.g. `agents/researcher.md` lists `safe_bash` in its tools, which is specific to that extension.
- `ask-user-question` — use the copy bundled here. If your setup already has an `ask-user-question` extension, use **this** one in its place. Popups from different extensions serialize through a shared UI lock, which only works when it's the same implementation.

## Notes

You can run the system without subagents. The main session does the teaching. You just lose the researcher (truth verification) and the generated visuals.

The teaching skill is written for one learner (me). Edit the skill to fit how you learn best.

## Troubleshooting

**The `md-log` file doesn't seem to update live in Obsidian.** `md-log` writes the full file synchronously (`fs.writeFileSync`) on every message and every quiz/question event — the content on disk is always current the moment a reply or quiz result lands, nothing is batched or delayed on this end. If Obsidian isn't visibly refreshing, it's Obsidian's editor not hot-reloading an externally-changed file while a pane is actively focused in edit/Live Preview mode — a known Obsidian behavior, not something this extension controls. Workarounds: view the note in **Reading view** (it reliably refreshes on external change), or click away to another file and back to force a reload.

## Implementation notes

This system implements the three proposals from [issue #2](https://github.com/amosblomqvist/learn/issues/2):

1. **Live DAG visualization** — dependency maps update with colored node status (pending/current/verified/misconception) as each concept is confirmed.
2. **Cross-session knowledge graph** — `knowledge-graph.md` persists verified concepts with spaced-repetition intervals, so subsequent lessons avoid re-probing known material.
3. **Collapse and expand** — fully mastered subgraphs compress into their generative roots at lesson end, expanding only when needed or when knowledge decays.

Also addresses:
- [issue #1](https://github.com/amosblomqvist/learn/issues/1): Mermaid label validation for Obsidian compatibility
- [issue #5](https://github.com/amosblomqvist/learn/issues/5): Documentation of md-log refresh behavior
- [issue #11](https://github.com/amosblomqvist/learn/issues/11): Gender-neutral pronouns throughout

## License

This project is licensed under the MIT License — see [LICENSE](LICENSE) for details.

You are free to use, modify, and distribute this system for personal, educational, or commercial purposes. See the [original learn repository](https://github.com/amosblomqvist/learn) for the source.
