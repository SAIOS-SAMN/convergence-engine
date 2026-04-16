# Convergence Engine

Recursive convergence algebra using exact rational arithmetic
on multi-dimensional relational torsions. Research tool for
AI-assisted scientific discovery.

Developed by Daniel Edward McFarland. Independent researcher.

---

## What This Engine Does

Takes multi-dimensional relational measurements and recursively
converges them toward their irreducible form through exact
arithmetic. No floating point. No rounding. No approximation.
The math either converges or it doesn't.

## The Mathematical Journey

### From Calculus to Cohomological Topology

**Calculus** — the starting point. Traditional optimization:
compute gradients, descend toward a minimum. The problem:
floating point arithmetic accumulates rounding errors. After
enough iterations, the noise exceeds the signal. You can't
tell if you converged or if the rounding made it look like
you did.

**Luminal** — the shift. Instead of computing smooth
derivatives, work with discrete relational structure directly.
Don't approximate the curve — measure the relationships
between points exactly. This is where exact rational arithmetic
enters: no rounding means no accumulated error.

**Holonomics** — the realization. When you transport a
measurement around a loop in relational space, it may not
come back to where it started. That failure to return IS the
torsion. The torsion IS the information. You don't need to
minimize a loss function — you need to measure what the loop
tells you.

**Cohomological topology** — the solution. Project the
measurement onto its H^1 cohomology class representative.
This strips coordinate distortion (coboundaries) and leaves
only structural information (cocycles). Integer normalization
after projection keeps denominators at 1. The residual after
projection tells you what hasn't been resolved yet — and WHERE.

### How This Solves Recursion

```
Traditional approach (calculus):
    Iterate → compute gradient → step → hope you converge
    Problem: rounding errors accumulate
    Convergence is numerical (a loss approaches zero)
    You can't tell noise from signal after enough steps

This engine (cohomological):
    Iterate → apply operator → project to H^1 → measure residual
    The projection guarantees irreducible form at every step
    Bad operators are rejected by validation gates
    Convergence is structural (the cocycle class stabilizes)
    not numerical (a loss approaches zero)
    You can't diverge — the math won't let you
```

## Core Components

**The Delta** — an antisymmetric rational tensor encoding
relationships between entities. Not the entities themselves —
the RELATIONSHIPS. Δ_{ij} = -Δ_{ji}. All values exact
rational (Q). No floats.

**coboundary_reduce** — projects a Delta onto its H^1
cohomology class representative. Strips coordinate
distortion. Integer normalization: multiply by lcm of
denominators, divide by gcd of numerators. Denominators
become 1. Exact.

**coherence_functional** — L1 norm of the residual. Measures
how much unresolved structure remains. L1, not L2 — no
squaring. The structure of what remains is preserved, not
collapsed into a single magnitude.

**for_each_residual** — iterates every triple in the Delta
and computes the residual. The residual is structured
information about what has NOT been resolved. It is signal,
not noise.

**Validation gates** — every proposed operator must pass:
1. Productivity: did the coherence functional decrease?
2. Conservation: is the antisymmetric structure preserved?
3. Bounded magnitude: is the step size within safe limits?

Only proposals that pass all three are accepted.

## Relationship to AI-SCIENTISTS Framework

The [AI-SCIENTISTS](https://github.com/AlignmentConfirmed/AI-SCIENTISTS)
framework provides the conceptual architecture for AI-assisted
scientific discovery — domain separation, discovery pacing,
Cognitive Boundary Erosion (CBE) prevention, and the principle
of Mutually Assured Survival.

This engine provides the mathematical instrument that powers
the verification layer. The framework is the rules. The engine
is the tool.

| AI-SCIENTISTS (framework) | Convergence Engine (tool) |
|---|---|
| Separates VERIFIED from CREATIVE | Converges measurements to irreducible form |
| Paces discovery to absorption speed | Processes at computation speed internally |
| Prevents Cognitive Boundary Erosion | Provides structural verification via convergence |
| Domain separation by architecture | Domain separation by mathematics |

## Design Constraints

1. **Exact rational arithmetic.** All computation in Q
   (num_rational::BigRational). No floats in the derivation
   path. Denominator = 1 means quantized (integer truth).
   Denominator ≠ 1 means ongoing torsion (unresolved
   structure).

2. **No boolean gates in derivation.** The engine does not
   use if/else to select paths. All candidates are evaluated.
   The lowest coherence score wins. Selection by measurement,
   not by prescription.

3. **Residual is structured information.** The residual is
   not noise to be suppressed. It tells the system what it
   hasn't understood yet, and WHERE. The engine reads
   residuals. It does not gate on zero.

4. **L1 measurement.** No squaring. L1 preserves the
   structure of what remains unresolved. L2 collapses it
   into a single magnitude and loses the geometry.

5. **Convergence is cohomological.** Convergence means the
   cocycle class stabilizes — not that a number approaches
   zero. This is structural termination, not numerical
   approximation.

## Technical Stack

- **Language:** Rust (memory-safe, zero-cost abstractions)
- **Arithmetic:** num_rational::BigRational (exact Q)
- **Hashing:** BLAKE3 (receipt integrity)
- **Signing:** Ed25519 (receipt authentication)
- **Platform:** Linux
- **Dependencies:** Minimal. No runtime. No framework. Just math.

## Status

536 tests passing. Zero failures. The mathematical
foundation is implemented, tested, and published.

## Documentation

| Document | What it covers |
|----------|---------------|
| [HOW_TO_USE.md](HOW_TO_USE.md) | Two laptops, one mesh — how SAIOS + SAMN provides structural containment through independent measurement |
| [EVOLUTION.md](EVOLUTION.md) | Complete rename map from biological to engineering vocabulary — before/after, every struct, field, function, and comment |
| [DEVELOPMENT_METHODOLOGY.md](DEVELOPMENT_METHODOLOGY.md) | What happened, who is responsible, and why the code is clean |
| [DEVELOPMENT_JOURNEY.md](DEVELOPMENT_JOURNEY.md) | Seven phases from zero to multi-dimensional thinking |
| [CODEX.md](CODEX.md) | What each module does across four domains |
| [RESPONSIBLE_USE.md](RESPONSIBLE_USE.md) | Written by Claude (Anthropic) — responsible use statement and the AI's acknowledgment of its own role |
| [DISCLAIMER.md](DISCLAIMER.md) | All content AI-generated under human editorial direction |

## Related Repositories

| Repository | What it is |
|------------|-----------|
| [AI-SCIENTISTS](https://github.com/AlignmentConfirmed/AI-SCIENTISTS) | Research framework — CBE, failure modes, contamination, language, security, pacing, vision. 42 citations. |
| [GOVERNANCE](https://github.com/SAIOS-SAMN/GOVERNANCE) | Five-layer architecture — C2R, IRS, MDC, Chronometry, Creative Expansion |
| [DOCUMENTATION](https://github.com/AlignmentConfirmed/DOCUMENTATION) | Front door — thesis, principles, translations, methodology |
| [NEBULA-PRODUCTS](https://github.com/SAIOS-SAMN/NEBULA-PRODUCTS) | Product platform — SAIOS-OS, SAMN-MESH, WWW4 |

## License

Apache License 2.0. See [LICENSE](LICENSE).

Open source. Attribution required. Patent grant included.
The author is not liable for misuse. See
[RESPONSIBLE_USE.md](RESPONSIBLE_USE.md) for the author's
intent and the AI's acknowledgment.

---

*536 tests. 0 failures. 0 floats. 0 rounding.*
*Exact rational arithmetic on antisymmetric tensors.*
*The math works. Evaluate it.*
