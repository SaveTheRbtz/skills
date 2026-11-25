# Proof patterns

Use these patterns to guide implementation. Source excerpts below are illustrative and were read in their original repository contexts; they are not standalone files or claims of compatibility with every Lean version.

## Reuse and adapt

Lean core's `Option.get!_inj` adapts the corresponding theorem for `getD`:

```lean
theorem get!_inj [Inhabited α] {o₁ o₂ : Option α}
    (h₁ : o₁.isSome) (h₂ : o₂.isSome) :
    o₁.get! = o₂.get! ↔ o₁ = o₂ := by
  simpa [get!_eq_getD] using getD_inj h₁ h₂
```

Search for an equivalent interface before repeating case analysis. Use direct proof terms when they communicate the whole argument; a tactic block is optional.

## Show the key intermediate expression

Mathlib's `pow_eq_pow_mod` exposes Euclidean division and lets simplification finish:

```lean
lemma pow_eq_pow_mod (m : ℕ) (ha : a ^ n = 1) : a ^ m = a ^ (m % n) := by
  calc
    a ^ m = a ^ (m % n + n * (m / n)) := by rw [Nat.mod_add_div]
    _ = a ^ (m % n) := by simp [pow_add, pow_mul, ha]
```

Keep the decomposition visible. Avoid replacing it with an unexplained series of low-level rewrites merely to save a line.

## Expose why automation succeeds

FLT's `j_pos_aux` displays a positive sum of squares:

```lean
private lemma j_pos_aux (a b : ℤ) (hb : b ≠ 0) : 0 < (a + b) ^ 2 - a * b := by
  rify
  calc
    (0 : ℝ) < (a ^ 2 + (a + b) ^ 2 + b ^ 2) / 2 := by positivity
    _ = (a + b) ^ 2 - a * b := by ring
```

Use a comparable split for inequalities: obtain the substantive estimates, then combine them with arithmetic automation. PFR's `first_estimate` follows this pattern.

## Choose a useful representation

PhysLean's continuity proof rewrites to an expression understood by its function-property tactic:

```lean
@[fun_prop]
lemma eigenfunction_continuous (n : ℕ) : Continuous (Q.eigenfunction n) := by
  rw [eigenfunction_eq]
  fun_prop
```

Before adding tactics, check whether a representation lemma or `change` exposes the exact input expected by an existing theorem. Do not keep unfolding unrelated definitions.

## Use characteristic equations

Sphere Eversion proves how `DualPair.update` acts on its distinguished vector and kernel. A downstream proof body is then:

```lean
rw [map_add, map_smul, p.update_v, p.update_ker_pi _ _ hu]
```

Build the same kind of small interface around a new construction. Prefer mathematical equations over repeated access to implementation details.

## Keep the spine of an inequality visible

Carleson's Dirichlet-kernel estimate has these explicit stages:

1. Bound the norm of a sum by the sum of norms.
2. Bound each summand by one.
3. Count the terms.

Use a `calc` chain so a reader can see those stages without simulating each tactic. Leave casts and scalar normalization inside the corresponding local proof.

## Organize proofs that cannot honestly be short

Use the construction/correctness/estimate separation exemplified by Liquid's `lem98_int`:
- Name the constructed object with `let`.
- Establish structural identities with typed local claims.
- Prove the numerical estimate with a calculation.
- Assemble the conclusion through the established interface.

In a long analysis argument, explain auxiliary objects and limiting arguments as in Brownian Motion's zero-one law. Improve opaque names rather than copying every stylistic choice in a source.

## Avoid mechanical rules

- Do not ban `simp`, `aesop`, `grind`, or compact proof terms.
- Do not mandate `simp only`, fixed line counts, or a helper for every block.
- Do not replace a clear standard theorem application with arithmetic search.
- Do not claim readability gains by counting only a shortened theorem while ignoring new helpers.
- Do not copy Lean 3 syntax into Lean 4 or import an entire library solely to enable one tactic without checking local conventions.
