# Upgrade: Rational Arithmetic Hardware

## Priority: 4 (Long-term, highest impact)
## Complexity: VERY HIGH — chip design
## Status: CONCEPT (see also: frontier/sandbox/HARDWARE_BRIDGE.md)

---

## The Problem

All current computing hardware operates in IEEE 754 floating
point (f32, f64). Every arithmetic operation introduces
rounding error. Across billions of operations, these errors
compound and can exceed the signal being measured.

The core engine requires exact rational arithmetic (Q). In
software, this is achieved using arbitrary-precision integer
libraries (num_rational::BigRational in Rust). This works but
is 10-100x slower than hardware float because the CPU was
designed for float, not for rational.

## The Ideal Solution

A processor (or co-processor) that performs rational arithmetic
in HARDWARE — the same way GPUs perform matrix multiplication
in hardware. The rational arithmetic unit would:

- Store numbers as numerator/denominator integer pairs
- Perform exact add, subtract, multiply, divide
- GCD reduction after every operation (keep fractions simplified)
- No rounding at any step
- Zero error accumulation regardless of operation count

## Why This Matters

| Property | IEEE 754 Float | Exact Rational (Q) |
|----------|---------------|-------------------|
| Error per operation | ~10^-7 (f32) to ~10^-16 (f64) | ZERO |
| Error after 10^9 operations | Potentially exceeds signal | ZERO |
| Can detect true convergence | NO — noise floor masks it | YES — convergence is exact |
| Suitable for cohomological algebra | POOR — rounding destroys the cocycle equation | PERFECT — the equation is preserved exactly |

## Who Would Build This

- Chip design teams at NVIDIA, AMD, Intel, or custom silicon startups
- FPGA prototyping teams (for proof of concept)
- Academic hardware research groups

## The Path

| Phase | Work | Duration |
|-------|------|----------|
| 1 | FPGA prototype of rational ALU | 6-12 months |
| 2 | Performance benchmarks vs software Q | 3-6 months |
| 3 | Co-processor design | 12-24 months |
| 4 | Integration with existing CPU/GPU | 12-18 months |
| 5 | Production silicon | 24-36 months |

**Total: 5-8 years to production hardware.**

## The Interim Path

- Continue with software-based BigRational (works, just slow)
- Optimize the GCD reduction (the primary bottleneck)
- Profile the engine to identify which operations dominate
- Consider fixed-width rational (e.g., i128/i128) for cases
  where the denominator is known to stay bounded
- Explore GPU-based rational arithmetic (parallel GCD on
  tensor cores)

---

*The hardware should match the math.*
*Exact arithmetic should be fast, not just correct.*
*The CPU was designed for float. The science needs rational.*
