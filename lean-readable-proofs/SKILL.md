---
name: lean-readable-proofs
description: Write, simplify, refactor, and review Lean proofs for brevity, simplicity, and readability. Use for Lean theorem proving, mathlib proof cleanup, replacing verbose tactic scripts, designing supporting lemmas, or reviewing .lean proofs. Preserve theorem meaning and project trust conventions; prefer lemma reuse, explicit mathematical structure, and focused automation. Default to Lean 4 while respecting existing Lean 3 projects.
---

# Readable Lean Proofs

Optimize reader effort and maintainability. Make the mathematical idea visible; use an appropriate lemma interface and automate routine obligations. Assess the whole argument, including helper lemmas, rather than minimizing lines in the final theorem.

## Workflow

1. Inspect the project.
   - Read applicable AGENTS.md instructions, the pinned Lean version, Lake configuration (or Lean 3 project configuration), relevant imports, and neighboring proofs.
   - Establish whether the task is writing, refactoring, or review only. For review only, report concrete findings without modifying files.
   - Use the project's existing toolchain and dependencies. Do not upgrade them to make a proof work.
   - Search local declarations first with `rg`, including existing dependencies when present. Look for equivalent statements, stronger results, characterization lemmas, and namespace methods before constructing an argument.

2. Preserve the mathematical contract.
   - For refactoring, preserve the theorem statement, hypotheses, universes, instances, visibility, and public API unless the task requests a change.
   - Do not add assumptions, weaken the conclusion, introduce axioms, or use `sorry`/`admit` to claim completion.
   - Preserve trust conventions. Do not introduce `native_decide`, external unchecked results, or other changes to the trusted computation path merely to shorten a proof. Do not mistake legitimate `classical` reasoning for a proof hole.
   - Keep existing unfinished work distinct from new changes; do not claim a whole development is complete because a local proof body is complete.

3. Identify the mathematical move.
   - Formulate the argument in one or two sentences internally: which theorem, decomposition, construction, invariant, or estimate does the work?
   - Prefer a direct term, `exact`, or `simpa [...] using ...` when an existing result supplies the argument.
   - For a larger proof, choose meaningful intermediate statements before selecting tactics. Use typed `have`, `suffices`, or `calc` to make those stages explicit.
   - Prove small interface lemmas when repeated unfolding or repeated side conditions reveal a missing abstraction. Bundle recurring hypotheses only when they form a useful mathematical concept.

4. Write the proof in the goal's natural shape.
   - Use `ext`/`funext` for pointwise equality, constructors and `refine` for structured claims, and `obtain`/`rcases` for witnesses.
   - Use induction when the argument requires it. Generalize changing parameters when necessary to obtain a useful induction hypothesis; first check whether the library already proves the needed fact.
   - Use short `rw` chains for directed transformations; use `calc` for informative chains of equalities or inequalities.
   - Use `change` for a definitionally equal formulation and representation lemmas for substantive rewrites. Avoid indiscriminate unfolding.
   - Give distinct branches separate bullets or named cases. Use `<;>` when all branches genuinely share the same small finishing step.

5. Normalize, then automate routine work.
   - Use ordinary `simp` for established normal forms. Use `simp only` when controlling the rewrite set improves clarity or stability; do not expand every short `simp` into a long list.
   - Orient new simplification lemmas consistently; add `[simp]` only for a suitable canonical simplification.
   - Use `ring`, `linarith`, `omega`, `norm_num`, `positivity`, and `fun_prop` for their supported domains, when available in the project's version/imports.
   - Feed automation the meaningful facts first. Keep a sum-of-squares expression, key estimate, or invariant visible when it explains the result.
   - Allow `aesop` or `grind` when the remaining argument is evident. Avoid opaque configurations or broad search that conceals the central mathematical step.
   - Use search/suggestion tactics during discovery when available. Prefer a clear resulting lemma application over retaining exploratory search, when that improves the proof.

6. Clean up for the intended reader.
   - Name mathematical facts, especially those used across several steps. Keep conventional short names for immediate local use.
   - Add type annotations at conceptual checkpoints, ambiguous applications, and coercion boundaries; avoid redundant annotations.
   - Use `let` for objects and `have` for propositions about them.
   - Extract a helper for a reusable fact, meaningful subargument, or technical representation issue. Do not fragment the proof into arbitrary one-use helpers just to reduce the headline line count.
   - Remove dead steps, duplicate arguments, unused facts, temporary tracing, and accidental broad imports.
   - Comment on choices, constructions, invariants, and surprising reductions. Do not narrate obvious tactic syntax.
   - Avoid rigid line limits or blanket rules against automation, proof terms, or `<;>`.

7. Verify the final change.
   - Compile affected Lean 4 files in the project context, commonly `lake env lean path/to/File.lean`; build changed imported dependencies first when needed so the check uses current artifacts.
   - Run the repository's required build/lint checks and relevant downstream targets when the changed interface warrants it. Use the established commands for Lean 3.
   - Inspect diagnostics as well as exit status: Lean can accept `sorry` with a warning. Check the diff for new proof holes or unrequested statement changes.
   - Investigate axiom dependencies (for example `#print axioms theoremName`) when completion or trust is in question. Do not treat a text search for `sorry` as a transitive dependency audit.
   - Remove temporary diagnostic declarations before the final check. Report the exact checks performed and any limitations. If compilation is unavailable, label the result unverified rather than claiming it compiles.

## Proof-shape guide

| Situation | Preferred starting point |
| --- | --- |
| Existing theorem matches | Direct term or `exact` |
| Existing theorem differs by normalization | `simpa [...] using ...` |
| Equality of functions or structured objects | `ext` or `funext`, then pointwise reasoning |
| A few directed substitutions | `rw [...]` |
| Informative chain of expressions | `calc` |
| Conceptual subgoal or stronger intermediate claim | Typed `have` or `suffices` |
| Construction with separate obligations | `refine`, witnesses, and explicit branches |
| Mechanical arithmetic after the main idea | Appropriate domain tactic |
| Repeated implementation details | A useful interface lemma |

Treat these as choices guided by the argument, not a fixed tactic ladder.

## Review and delivery

Ask:
- Can the reader identify the main mathematical move quickly?
- Does each block serve one purpose, with technical details localized?
- Is there unnecessary re-proving, unfolding, coercion manipulation, or branching?
- Do helpers reduce the total conceptual burden?
- Are statements and trust conventions preserved, and are checks accurately reported?

For implementation tasks, deliver the changes and briefly explain the mathematical simplification and validation. For review tasks, prioritize concrete issues with file/line references and proposed improvements; do not present stylistic preferences as correctness defects.

Read [patterns.md](references/patterns.md) when choosing or explaining a proof pattern. Read [sources.md](references/sources.md) for the ten-repository evidence and pinned examples. Source excerpts require their original imports and context; verify any adaptation with the local toolchain.
