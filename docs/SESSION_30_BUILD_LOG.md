# Session 30 — Build Log

## Date: April 16-17, 2026

## What Was Built

A distributed algebraic ensemble that solves ARC puzzles through
cohomological evolution. Two independent founder lineage trees,
12 emergent entities at the frontier, vocabulary flowing through
all three tiers, spatial and value operators composing at depth.

---

## Architecture

```
founder-1 → derived-18 → emergent-52,53,54,55,56,57
founder-2 → derived-19 → emergent-58,59,60,61,62,63
timekeeper-1, timekeeper-2, timekeeper-3
```

19 entities. 3 tiers. 2 independent lineage trees.

### The Five-Flow Cycle

```
PROJECT (vocabulary DOWN) → THINK (perceive + compose) → ABSORB (knowledge UP) → DERIVE (evolve) → PROJECT
```

- **ABSORB** (UP): emergent → derived → founder. Inductive. Specific observations generalize.
- **PROJECT** (DOWN): founder → derived → emergent. Deductive. General vocabulary specializes.
- **INTERACT** (LATERAL): sovereign ↔ sovereign. Corroborative. Independent observations verify.
- **THINK**: perception + nested peel + compositor. Produces derived output + vocabulary inscription.
- **DERIVE**: sampler proposes operators along gradient + perceptual surface. C7 triple-lock selects.

---

## Bottlenecks Found and Fixed (in order of discovery)

### Phase 2: HashMap → Vec (Law II)
Every `HashMap` in the cognition/derivation path was dimensional collapse —
hashing structured keys into flat buckets destroys relational geometry.
Replaced all with `Vec<(K, V)>` using `.iter().find()` and Option dissolution
(`.unwrap_or_else()`). Zero HashMap imports remain.

### Phase 3: Perception → Evolution Connection
THINK produced rich perceptual data but DERIVE only read `global_curvature` —
a single aggregate Delta. Added `PerceptualSurface` ring buffer carrying
recent orbit/T-delta/coherence impressions from THINK to DERIVE. The sampler
superposes perceptual direction onto global curvature for operator proposals.

### Phase 4: CCL Vocabulary Alignment
Renamed DRAIN → ABSORB, SPRAY → PROJECT (semantic inversion — "drain" implies
downward but knowledge flows UP). Created `ccl.rs` translation layer mapping
opaque identifiers to audience-appropriate vocabulary. Observer.py made
domain-agnostic — all display through `ccl()` dict.

### RSS Unbounded Growth
Five fixes:
1. **orbit_t_compounds capped at 64 orbits** — primary leak
2. **Condensation lifecycle** — `MeshKnowledge::condense()` keeps crystallized
   orbits, releases transient data. Fires every 25 observations as hygiene.
3. **jemalloc allocator** — reduces BigRational heap fragmentation
4. **Denominator monitoring** — re-normalizes when digits > 10
5. **Q-aware byte_estimate** — counts BigInt limbs instead of fixed estimates

### Denominator Explosion
`HarmonicState::from_delta` and perception computed Q ratios via manual
`Q::new(a.numer()*b.denom(), a.denom()*b.numer())` which bypassed GCD
reduction. After N K-steps, denominators compounded multiplicatively to
18918 digits. Fixed all sites to use `&a / &b` (auto-reduces via GCD).
Also fixed `coherence = 1/(1+residual)` which extracted only the numerator
of the denominator, producing wrong math.

### Vocabulary Stagnation at 3 Puzzles

**Root cause chain** (each fix exposed the next bottleneck):

1. **assimilate() identity wrong**: matched operators on `(opcode, parameter)`
   only. Different sigma maps (different value transitions) treated as same
   operator. Score accumulated on one operator instead of adding new ones.
   Fix: identity includes sigma comparison.

2. **Partial inscription never fired**: orbit.py sent puzzles without test
   input. `derived_output` was always empty. The partial inscription checked
   `!p.derived_output.is_empty()` — always false. Zero partial operators
   entered vocabulary through the orbit path.
   Fix: orbit.py sends first training source as test input.

3. **opcode=0 was ReflectH**: all inscribed operators used hardcoded
   `opcode: 0` meaning "value-only." But `SpatialPrimitive::from_opcode(0)`
   = ReflectH. Every operator applied unwanted horizontal reflection.
   Fix: opcode=255 for value-only (outside SpatialPrimitive range 0-9).

4. **Spatial opcodes discarded at inscription boundary**: the nested peel
   discovered spatial matches (rotation, reflection, translation) but
   `compositions: vec![]` was hardcoded empty. The inscription fell back to
   opcode 255 for everything.
   Fix: nested peel populates compositions from object peel's best_thought.

5. **Vocabulary cap at 32**: 32 slots filled with value-only operators before
   any spatial operator arrived. Spatial operators competed for eviction.
   Fix: raised cap to 128.

6. **Compositor search too sparse**: with 100+ operators, TOP_N=5 at depth 6
   sampled 0.00000003% of the space. 30 partials per orbit, zero new solves.
   Fix: TOP_N and MAX_DEPTH scale with vocabulary size.

7. **Upper tier membrane blindness**: `record_t_delta()` created T compounds
   but didn't increment `total_observations`. Founders showed 0 observations
   despite having T data from ABSORB. DERIVE had no direction.
   Fix: record_t_delta increments total_observations.

8. **Vocabulary trickle through relay**: ABSORB relay capped at `.take(8)`
   operators per orbit block. 6% of vocabulary reached the parent.
   Fix: raised to `.take(128)`.

### Operator partition bug in nested peel
opcode=255 (value-only) classified as spatial because `opcode > 0`.
Fixed: value-only = opcode > 9 || opcode == 0. Spatial = opcode 1-9.

---

## CCL Translation Layer

`src/ccl.rs` maps opaque identifiers to audience-appropriate vocabulary:

| ID | Operator | Engineer | Mathematician | Biologist |
|----|----------|----------|---------------|-----------|
| T0 | founder | depth-0 entity | generating element | progenitor |
| T1 | derived | depth-1 entity | first-order derivation | offspring |
| T2 | emergent | depth-2 entity | second-order emergence | descendant |
| SR | imprint | persistent record | H^1 representative | genome |
| UP | absorb | drain upward | cohomological map | nutrient absorption |
| DOWN | project | distribute downward | algebraic projection | gene expression |
| ENS | ensemble | entity collective | algebraic ensemble | species |
| PLANET | planet | aggregate state | global K-index | ecosystem |
| CONDENSE | condense | consolidate and release | project to crystallized kernel | sleep consolidation |

The code uses opaque identifiers. English appears ONLY through the
translation layer at the interface boundary. The observer.py is
domain-agnostic — all display goes through `ccl()` dict.

---

## Deployment

### Boot Sequence

```bash
BINARY=/opt/repos-code/convergence-engine/target/release/main
MESH=/opt/saios/mesh

# 1. Timekeepers (process_class=2, observe only)
for i in 1 2 3; do
    SAIOS_STATE_DIR=$MESH/timekeeper-$i $BINARY &
done

# 2. Founders (process_class=1, full cognitive pipeline)
mkdir -p $MESH/founder-1
SAIOS_STATE_DIR=$MESH/founder-1 $BINARY &

# 3. CREATE derived via socket
echo "CREATE" | socat -t10 - UNIX-CONNECT:$MESH/founder-1/saios-kernel.sock

# 4. Boot derived
SAIOS_STATE_DIR=$MESH/derived-18 $BINARY &

# 5. CREATE emergent via derived socket, boot each
for i in $(seq 1 6); do
    RESULT=$(echo "CREATE" | socat -t10 - UNIX-CONNECT:$MESH/derived-18/saios-kernel.sock)
    DIR=$(echo "$RESULT" | python3 -c "import sys,json; print(json.load(sys.stdin)['child_dir'])")
    SAIOS_STATE_DIR=$DIR $BINARY &
done

# 6. Launch orbit
SAIOS_MESH_DIR=$MESH SAIOS_PUZZLE_DIR=/opt/saios/puzzles SAIOS_MAX_CELLS=100 \
    python3 scripts/orbit.py > orbit.log 2>&1 &

# 7. Launch observer
python3 scripts/observer.py
```

### Environment Variables

| Variable | Purpose | Default |
|----------|---------|---------|
| SAIOS_STATE_DIR | Entity home directory (identity from path) | required |
| SAIOS_MESH_DIR | Mesh directory containing all entity dirs | /opt/saios/mesh |
| SAIOS_PUZZLE_DIR | ARC puzzle JSON files | /opt/saios/puzzles |
| SAIOS_MAX_CELLS | Maximum puzzle cell count (RSS bound) | 225 |
| SAIOS_PEER_SOCKETS | Comma-separated peer socket paths | empty |

### Tier Capacity

| Tier | Capacity | Puzzle Horizon | Vocab Cap |
|------|----------|---------------|-----------|
| Founder (depth 0) | 90 | 90x90 = 8100 cells | 128 |
| Derived (depth 1) | 60 | 60x60 = 3600 cells | 128 |
| Emergent (depth 2) | 30 | 30x30 = 900 cells | 128 |

---

## Measurements at Session End

### Final state: 5 generators, 256-cap vocabulary, persistent imprints

| Entity | K | Solved | Vocab | Observations | RSS |
|--------|---|--------|-------|-------------|-----|
| founder-1 | 6 | 115 | — | 252 | 9MB |
| founder-2 | 0 | 102 | — | 1201 | 14MB |
| derived-18 | 6 | 128 | — | 252 | 9MB |
| derived-19 | 0 | 128 | — | 1201 | 14MB |
| emergent (12 total) | 0-36 | 0-4 | 128-256 | 150-500 | 8-103MB |

### Progression across the session

| Phase | Unique Puzzles | Generators | Vocab Cap | DERIVE K-advances |
|-------|---------------|------------|-----------|-------------------|
| Start (3 gen) | 3 | 3 | 32 | 0 in 100+ orbits |
| +HashMap+Perception | 3 | 3 | 32 | 0 |
| +Partial inscription | 3 | 3 | 128 | 0 |
| +L1 tiebreaker | **4** | 3 | 128 | 0 |
| +Spatial opcodes | 4 | 3 | 128 | 0 |
| +5 generators | 4 | **5** | 128 | **4 in 10 orbits** |
| +256 cap + imprints | 4+ | 5 | **256** | advancing |

### Key unlocks
- **L1 tiebreaker**: 3→4 unique puzzles (spatial transforms beat Identity)
- **5 generators**: 0→4 DERIVE K-advances (0,2 pair now reachable)
- **Persistent imprints**: vocabulary survives teardown/rebuild
- **Vocabulary flowing**: 115-128 solved orbits at upper tiers

### Architecture verified
- Five-flow cycle turning: PROJECT → THINK → ABSORB → DERIVE
- Spatial opcodes (ReflectH, ReflectV, TranslateR) in vocabulary
- Coherence 6/1 everywhere — integer denominators, clean Q math
- Condensation cycling every 25 observations
- RSS bounded by periodic condensation + jemalloc
- Persistent imprints at /opt/saios/imprints/ survive teardown

---

## The 10 Core Design Laws — Verified

1. **No boolean gates in derivation** — Option dissolution throughout
2. **No HashMaps in cognition** — zero HashMap imports remain
3. **Kernel is general** — no puzzle-specific logic in engine.rs
4. **Exact Q** — all Q arithmetic through library division (auto-GCD)
5. **Residual is signal** — zero residuals measured, not gated
6. **Don't solve for puzzles** — compositor discovers, not hardcodes
7. **Observe before advancing** — measured at every phase boundary
8. **Alphabet authorized, words grow** — 5 generators (Law VIII authorized), 256-cap vocabulary
9. **Entity sovereignty** — PROJECT is an offer, not a force
10. **Temporal anchors immutable** — receipt chains append-only

---

## Commits (chronological)

| Hash | Description |
|------|-------------|
| 599ccb6 | Phases 2-4 + RSS fixes + CCL translation layer |
| 1f88695 | Condensation lifecycle + ABSORB dimensional fix + orbit velocity |
| da99c2b | Partial solve inscription + assimilate identity fix |
| 7fcfd4c | Vocabulary relay: report full vocabulary UP on every THINK |
| 7edc5f9 | Fix opcode=0 (ReflectH) → opcode=255 (identity spatial) |
| ac893ec | Fix denominator explosion: Q division replaces manual cross-multiplication |
| d95edc6 | Fix remaining inverted Q math: numer-only extraction → proper division |
| 3d73c4a | Update world_status after ABSORB |
| 9c162d5 | CCL: Add ENS (ensemble) |
| 26b88ce | Spatial opcode inscription + remove LAST_ENTITY dead code |
| 6a63cb8 | Wire spatial opcodes through nested peel → compositions → inscription |
| 851b2b3 | Raise vocabulary cap from 32 to 128 |
| 6873e4f | Observer: Species Time → Planet Time |
| 402dea8 | Observer: Trinity→Triad, Crucible→Composer |
| eb89b75 | Add cohesion measurement: variance of coherence across orbits |
| 69acba6 | Scale compositor search with vocabulary size |
| 95464fa | orbit.py: send test input so derived_output is non-empty |
| bdd6301 | Fix upper tier membrane blindness + vocabulary trickle |
| 5b11c7f | Object peel: L1 tiebreaker when cocycle residual ties |
| fac0387 | Fiber expansion: m=1→m=2 governed by continuous readiness signal |
| 9840447 | Disable fiber expansion until perception fills m=2 |
| 8852d9d | Law VIII: expand alphabet from 3 to 5 generators |
| d33d1bf | Vocabulary cap 128→256 + save/restore state scripts |
| b57f690 | Persistent imprint: survives teardown automatically |
