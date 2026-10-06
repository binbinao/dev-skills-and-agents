# dev-skills-and-agents

A personal, unified skill-and-agent repository. Combines:

- **`skills/`** — 14 methodology skills from [`obra/superpowers`](https://github.com/obra/superpowers) v6.3.0 (brainstorming, systematic-debugging, TDD, subagent-driven-development, verification-before-completion, etc.)
- **`agents/`** — 9 curated persona agents from [`msitarzewski/agency-agents`](https://github.com/msitarzewski/agency-agents) (forked via `binbinao/agency-agents`), selected for the most frequent development scenarios: `backend-architect`, `code-reviewer`, `security-engineer`, `performance-benchmarker`, `technical-writer`, `accessibility-auditor`, `frontend-developer`, `mobile-app-builder`, `devops-automator`.

Goal — replace the dual-source dependency (superpowers as an omp plugin, agency-agents as a three-copy sprawl at `~/.claude/agents/`, `~/.omp/agent/agents/`, and project-level `.omp/agents/`) with **one self-owned repo** that loads both surfaces into the same session. Compared to the pre-merge state, this repo:

| Concern | Before | After |
| --- | --- | --- |
| Source of truth for methodology | `~/.omp/plugins/cache/plugins/superpowers-dev___superpowers___6.3.0/skills/` (a plugin cache) | this repo's `skills/` (you own the upgrade) |
| Source of truth for persona agents | 3 copies (`~/.claude/agents/*/`, `~/.omp/agent/agents/`, `<project>/.omp/agents/`) | this repo's `agents/` |
| Cross-surface coordination | manual glue (`~/.claude/skills/sdd-with-agency-agents/SKILL.md` style) | native — the `skills/subagent-driven-development` here is the upstream copy you can edit |
| Upstream drift | manual re-pull on every release | `git subtree pull` or selective file copy from upstream |

## Install

1. **Clone** this repo somewhere outside your workspace (it is an installation surface, not a per-project asset).
   ```bash
   git clone git@github.com:binbinao/dev-skills-and-agents.git ~/Documents/GitHub/dev-skills-and-agents
   ```
2. **Symlink into omp's discovery path** so skills, agents, and the session-start bootstrap are loaded:
   ```bash
   # skills → omp discovers ~/.claude/skills/<name>/SKILL.md
   ln -snf ~/Documents/GitHub/dev-skills-and-agents/skills/* ~/.claude/skills/
   # agents → omp discovers ~/.omp/agent/agents/*.md
   ln -snf ~/Documents/GitHub/dev-skills-and-agents/agents/*.md ~/.omp/agent/agents/
   # hooks / session-start bootstrap → must live under an omp-recognized plugin
   # (either ~/.omp/plugins/<plugin-name>/ or a path registered in package.json
   # `pi.extensions`). Easiest is a symlink:
   ln -snf ~/Documents/GitHub/dev-skills-and-agents ~/.omp/plugins/dev-skills-and-agents
   # that puts `.pi/extensions/superpowers.ts` and `hooks/` exactly where omp
   # expects them — see `package.json` at the repo root for the `pi.extensions`
   # entry that triggers loading.
   ```
   If you prefer not to symlink, run the equivalent `cp -R` once after each upgrade.
3. **Disable the original superpowers omp plugin** if you want a single source of truth — the `using-superpowers` skill here is the bootstrap; loading both will inject duplicate workflow mandates. Disable via:
   ```bash
   omp plugin disable superpowers
   ```

## Upgrade

- **From upstream `obra/superpowers`** — selective file copy, do not blanket-overwrite:
  ```bash
  rsync -a --delete obra/superpowers/skills/<changed-skill>/ \
      ~/Documents/GitHub/dev-skills-and-agents/skills/<changed-skill>/
  ```
- **From upstream `msitarzewski/agency-agents`** — same pattern over `integrations/omp/agents/`:
  ```bash
  rsync -a msitarzewski/agency-agents/integrations/omp/agents/<persona>.md \
      ~/Documents/GitHub/dev-skills-and-agents/agents/<persona>.md
  ```
- Commit each upgrade with a `chore(upstream): sync <name>` message and a one-line diff summary.

## Layout

```
.
├── skills/                # 14 SKILL packages copied from superpowers 6.3.0
│   ├── brainstorming/
│   ├── subagent-driven-development/
│   ├── systematic-debugging/
│   ├── test-driven-development/
│   ├── using-superpowers/   # bootstrap; loaded automatically at session start
│   ├── verification-before-completion/
│   └── ... (8 more)
├── agents/                 # 9 persona .md files copied from agency-agents
│   ├── backend-architect.md
│   ├── code-reviewer.md
│   └── ... (7 more)
├── references/             # cross-cutting notes (loaded on demand)
├── assets/templates/       # install / scaffold templates
├── LICENSE                 # MIT, see LICENSE
└── README.md
```

## Provenance

Each asset records its upstream commit at copy time. Re-running `sha256sum -c references/provenance.sha256` (if present) verifies byte-stable upstream sync.

## License

MIT. Skill bodies are © their original authors (obra/superpowers, msitarzewski/agency-agents). See upstream repositories for their respective licenses.