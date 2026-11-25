# Codex skills

Reusable skills for Codex. Each directory contains a `SKILL.md` entry point and any supporting references.

| Skill | Purpose |
| --- | --- |
| [lean-readable-proofs](lean-readable-proofs/SKILL.md) | Write, simplify, refactor, and review Lean proofs for clarity and brevity while preserving theorem statements and project trust conventions. |

The Lean skill emphasizes lemma reuse, visible mathematical structure, useful intermediate claims, and focused automation. It defaults to Lean 4, respects existing Lean 3 projects, and uses the project's pinned toolchain for verification. Its references include proof patterns and commit-pinned examples from ten Lean repositories.

## Install

Clone this repository using a GitHub account with access, then copy the skill into your Codex user skills directory. The commands below stop if the destination already exists; they do not overwrite an installed skill.

```sh
git clone https://github.com/SaveTheRbtz/skills.git codex-skills
cd codex-skills

skill_parent="${CODEX_HOME:-$HOME/.codex}/skills"
skill_dest="$skill_parent/lean-readable-proofs"
mkdir -p "$skill_parent"
if mkdir "$skill_dest"; then
  cp -R lean-readable-proofs/. "$skill_dest/"
else
  printf '%s\n' 'Installation stopped: destination exists or could not be created.' >&2
fi
```

Start a new Codex session after installation. If the skill does not appear, restart Codex. To update an existing installation, review and back up that directory before replacing it deliberately.

## Use

Invoke the skill in a Codex prompt:

```text
Use $lean-readable-proofs to simplify the proofs in MyProject/Basic.lean.
Preserve the theorem statements and verify the changes with the project toolchain.
```

For a review without edits:

```text
Use $lean-readable-proofs to review MyProject/Basic.lean for readability.
Report concrete improvements without changing files.
```

## Layout

```text
lean-readable-proofs/
├── SKILL.md                 # Workflow and proof review guidance
├── agents/openai.yaml       # Codex display metadata and default prompt
└── references/
    ├── patterns.md          # Illustrative proof patterns
    └── sources.md           # Pinned source examples and provenance limits
```

Source excerpts depend on their original imports and context. Adaptations must be checked against the target project's Lean version; the reference survey is not a guarantee that every repository or excerpt builds unchanged.
