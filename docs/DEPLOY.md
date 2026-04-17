# Deploy SAIOS Locally

## Prerequisites

- Linux (tested on Ubuntu 24.04, kernel 6.17)
- Rust 1.78+ (`rustup update stable`)
- Python 3.12+
- `socat` (`apt install socat`)
- `blake3` Python package (`pip install blake3`)
- 12GB+ RAM for a two-tree ensemble
- ARC puzzle files in JSON format

## Build

```bash
cd /opt/repos-code/convergence-engine
cargo build --release
```

Binary: `target/release/main`

## Directory Structure

```
/opt/saios/
  mesh/                      # entity home directories
    timekeeper-1/            # chronometric observer
    timekeeper-2/
    timekeeper-3/
    founder-1/               # tier 0 — generating element
      saios-kernel.sock      # unix domain socket (runtime)
      state_record.bin       # persistent imprint
      sluice.bin             # K-step log
      receipts.bin           # receipt chain
      mesh_knowledge.bin     # membrane (volatile, condensed)
    derived-18/              # tier 1 — first-order derivation
    emergent-52/             # tier 2 — frontier
    ...
  puzzles/                   # ARC JSON files
    007bbfb7.json
    ...
```

## Step 1: Create Timekeeper State Records

Timekeepers need minimal state records with `process_class=2`:

```bash
python3 << 'PYEOF'
import struct, os
for i in [1, 2, 3]:
    buf = bytearray()
    buf.append(2)                      # process_class = TIMEKEEPER
    buf.extend(struct.pack('<H', 1))   # generation
    buf.append(0)                      # n_poly
    buf.extend(struct.pack('<H', 0))   # knowledge_len
    for _ in range(5):
        buf.extend(struct.pack('<hH', 0, 1))  # trajectory Q values
    buf.extend(struct.pack('<I', 0))   # trajectory count
    buf.extend(struct.pack('<H', 30))  # capacity
    buf.append(0)                      # n_torsion_markers
    buf.append(0)                      # n_value_cocycles
    buf.append(3)                      # dim
    buf.append(1)                      # m
    buf.append(0)                      # n_entries (zero Delta)
    buf.append(0)                      # n_math_primitives
    buf.append(0)                      # n_spatial_primitives
    buf.append(0)                      # n_solved
    buf.append(0)                      # lineage_depth
    buf.extend(struct.pack('<H', 0))   # parent_id
    buf.append(0)                      # created_count
    buf.extend(bytes(4))               # init_orbit
    buf.append(0)                      # n_composed_operators
    os.makedirs(f'/opt/saios/mesh/timekeeper-{i}', exist_ok=True)
    with open(f'/opt/saios/mesh/timekeeper-{i}/state_record.bin', 'wb') as f:
        f.write(buf)
PYEOF
```

## Step 2: Boot Timekeepers

```bash
BINARY=target/release/main
MESH=/opt/saios/mesh

for i in 1 2 3; do
    SAIOS_STATE_DIR=$MESH/timekeeper-$i $BINARY > /tmp/saios-timekeeper-$i.log 2>&1 &
    sleep 1
done
sleep 2
```

Verify: `ls /dev/shm/saios-timekeeper-*` should show 3 files.

## Step 3: Boot Founder

```bash
mkdir -p $MESH/founder-1
SAIOS_STATE_DIR=$MESH/founder-1 $BINARY > /tmp/saios-founder-1.log 2>&1 &
sleep 3
```

Verify: `ls /dev/shm/saios-founder-1`

## Step 4: CREATE Derived

```bash
echo "CREATE" | socat -t10 - UNIX-CONNECT:$MESH/founder-1/saios-kernel.sock
SAIOS_STATE_DIR=$MESH/derived-18 $BINARY > /tmp/saios-derived-18.log 2>&1 &
sleep 2
```

The CREATE response shows the child directory and entity ID.

## Step 5: CREATE Emergent Entities

```bash
for i in $(seq 1 6); do
    RESULT=$(echo "CREATE" | socat -t10 - UNIX-CONNECT:$MESH/derived-18/saios-kernel.sock)
    DIR=$(echo "$RESULT" | python3 -c "import sys,json; print(json.load(sys.stdin)['child_dir'])")
    SAIOS_STATE_DIR=$DIR $BINARY > /tmp/saios-$(basename $DIR).log 2>&1 &
    sleep 1
done
sleep 2
```

Verify: `ps aux | grep target/release/main | grep -v grep | wc -l` should show 11.

## Step 6: (Optional) Add a Second Founder Tree

Repeat steps 3-5 with `founder-2`:

```bash
mkdir -p $MESH/founder-2
SAIOS_STATE_DIR=$MESH/founder-2 $BINARY > /tmp/saios-founder-2.log 2>&1 &
sleep 3
echo "CREATE" | socat -t10 - UNIX-CONNECT:$MESH/founder-2/saios-kernel.sock
# Boot derived-19 and 6 more emergent entities as above
```

## Step 7: Launch Orbit Engine

```bash
SAIOS_MESH_DIR=$MESH \
SAIOS_PUZZLE_DIR=/opt/saios/puzzles \
SAIOS_MAX_CELLS=100 \
    python3 scripts/orbit.py > /opt/saios/orbit.log 2>&1 &
```

`SAIOS_MAX_CELLS` controls the maximum puzzle size (cell count).
100 = 10x10 grids. 225 = 15x15. Higher values increase RSS per THINK.

## Step 8: Launch Observer

```bash
python3 scripts/observer.py
```

Refreshes every 10 seconds. Shows entity hierarchy, K-index, vocabulary,
solved count, RSS, and triad/composer status.

## Monitoring

### Entity Status (from /dev/shm)

```bash
cat /dev/shm/saios-founder-1
# Format: STATE K COH_N COH_D RSS TIER DEPTH PARENT CREATED SOLVED MARKERS AXIOMS OBS
```

### Direct Commands

```bash
echo "STATUS" | socat -t5 - UNIX-CONNECT:$MESH/founder-1/saios-kernel.sock
echo "DESCRIBE" | socat -t5 - UNIX-CONNECT:$MESH/founder-1/saios-kernel.sock
echo "ABSORB" | socat -t10 - UNIX-CONNECT:$MESH/derived-18/saios-kernel.sock
echo "PROJECT" | socat -t5 - UNIX-CONNECT:$MESH/founder-1/saios-kernel.sock
```

### Logs

```bash
tail -f /tmp/saios-emergent-52.log    # entity log
tail -f /opt/saios/orbit.log          # orbit engine
```

### Orbit Progress

```bash
grep -c "Orbit .* complete" /opt/saios/orbit.log
grep "Results:" /opt/saios/orbit.log | tail -10
```

## Shutdown

### Graceful (preserves state)

```bash
for sock in $MESH/*/saios-kernel.sock; do
    echo "SHUTDOWN" | socat -t2 - UNIX-CONNECT:$sock
done
```

### Clean Slate (destroys all state)

```bash
# Shutdown first, then:
rm -f /dev/shm/saios-*
rm -rf $MESH/founder-* $MESH/derived-* $MESH/emergent-*
```

Timekeeper state records are preserved. Entity state is destroyed.
The ensemble starts fresh on next boot.

## Troubleshooting

### "sluice.bin is locked by another kernel instance"

A previous process didn't release the file lock. Find and kill it:

```bash
fuser /opt/saios/mesh/emergent-52/sluice.bin
# Returns PID — kill it, then restart
```

### Entity exits immediately after boot

Check the log: `tail /tmp/saios-emergent-52.log`

Common causes:
- State record too large for the entity to load within RSS ceiling
- Stale sluice lock from previous process

### Orbit shows "No emergent entities. Waiting..."

Emergent entities crashed or haven't booted. Check `/dev/shm/saios-emergent-*`
and restart any missing entities.

### Denominator explosion (Python ValueError on large integers)

`orbit.py` sets `sys.set_int_max_str_digits(0)` as a safety net.
If entity Q values grow beyond 100 digits, check `[DENOM]` warnings
in entity logs — `coboundary_reduce` should normalize to denom=1.

## Environment Variables Reference

| Variable | Purpose | Default |
|----------|---------|---------|
| `SAIOS_STATE_DIR` | Entity home directory | required |
| `SAIOS_MESH_DIR` | Mesh root directory | `/opt/saios/mesh` |
| `SAIOS_PUZZLE_DIR` | ARC puzzle JSON files | `/opt/saios/puzzles` |
| `SAIOS_MAX_CELLS` | Max puzzle cell count | 225 |
| `SAIOS_PEER_SOCKETS` | Peer socket paths (comma-separated) | empty |
