# Maze Seq — dual generative sequencer for Ableton Move (Schwung)

Two Moog-Labyrinth-inspired generative sequencers for the [Schwung](https://github.com/charlesvestal/schwung)
framework on Ableton Move. This repository ships **two** modules:

| Module | ID | Type | What it is |
|---|---|---|---|
| **Maze Seq** | `maze_seq` | overtake tool | Full pad + step-button instrument with its own display |
| **Maze Seq Lite** | `maze_seq_lite` | slot MIDI FX | The same engine as a chain MIDI-FX slot (auto knob menu) |

Both run two 8-step generative sequencers that clock-sync to the Move transport,
quantise random voltages to a scale, and play MIDI out — a recreation of the
Labyrinth's dual generative sequencer section.

---

## Features

- **Dual 8-step generative sequencers** with per-step random CV, quantised to a scale.
- **Corrupt** — mutates stored voltages, and past 12 o'clock flips bits (evolving patterns).
- **CV Range** — bipolar pitch spread around the root, 0–100.
- **Trig Mix** — velocity cross-fade between Seq 1 and Seq 2 (−63 = Seq1 only @127, noon = both @100, +64 = Seq2 only @127).
- **Length / Bit-flip / Advance** per sequencer.
- **12 scales**, selectable key, note-rate (1/32…1 bar), gate length (1/4…2 steps).
- **Clock-synced** to the Move transport (24 PPQN, start/stop/continue).
- **Pattern + position persistence** (Maze Seq tool) — survives exit.

### Maze Seq (tool) hardware layout
- **16 step buttons** = the two sequencers. 1–8 = Seq1 (red bit / yellow play head), 9–16 = Seq2 (blue bit / yellow play head). Press to flip a bit.
- **Top 3 pad rows** = a fixed scale keyboard. Middle-row, first pad = root; press pads to transpose the whole sequence up/down by scale degree. **+ / −** shift an octave.
- **Bottom pad row** = per-sequencer advance ◄/► and length−.
- **Knobs, Track 1 page:** Seq1 Corrupt / Range / Length, Trig Mix, Seq2 Corrupt / Range / Length.
- **Knobs, Track 2 page:** Scale, Key, Note Rate, Note Length, Seq1 Ch, Seq2 Ch.
- **Back** = hide (keeps playing); **Shift+Back** = exit.

### Maze Seq Lite (slot MIDI FX)
Insert in a MIDI-FX slot, route to a synth, press Play. Seq 1 page: Corrupt,
CV Range, Length, Bit Flip, Advance, Trig Mix. Seq 2 page: same minus Trig Mix.
Global page: Scale, Note Rate, Note Length. Incoming notes set the root.

---

## Install

### Option A — manual (for testers, no store needed)
1. Download the latest `maze_seq-module.tar.gz` and/or `maze_seq_lite-module.tar.gz` from [Releases](../../releases).
2. Extract onto the Move:
   ```bash
   # tool
   scp maze_seq-module.tar.gz ableton@<MOVE_IP>:/tmp/
   ssh ableton@<MOVE_IP> 'cd /data/UserData/schwung/modules/tools && tar xzf /tmp/maze_seq-module.tar.gz'
   # lite (slot MIDI FX)
   scp maze_seq_lite-module.tar.gz ableton@<MOVE_IP>:/tmp/
   ssh ableton@<MOVE_IP> 'cd /data/UserData/schwung/modules/midi_fx && tar xzf /tmp/maze_seq_lite-module.tar.gz'
   ```
3. Power-cycle the Move. **Maze Seq** appears in Shadow → Tools; **Maze Seq Lite** in a MIDI-FX slot.

### Option B — Schwung Manager / Module Store
Once listed in the Schwung catalog (see *Publishing* below), install from the
Schwung Manager web UI at `http://move.local:7700`.

---

## Build from source

Requires Docker (the build cross-compiles the DSP for the Move's ARM64 chip).

```bash
bash scripts/build.sh
# produces:
#   dist/maze_seq-module.tar.gz
#   dist/maze_seq_lite-module.tar.gz
```

### Vendored headers
The DSP compiles against two Schwung API headers. Copy them from a Schwung
checkout into `src/include/` before building (kept out of git so they stay in
sync with the host ABI):

```bash
cp <schwung>/src/host/plugin_api_v1.h    src/include/
cp <schwung>/src/host/midi_fx_api_v1.h   src/include/
```

---

## Repository layout

```
src/
  maze_seq/                 # overtake tool
    module.json
    ui.js
    help.json
    dsp/maze_seq.c
  maze_seq_lite/            # slot MIDI FX
    module.json
    help.json
    dsp/maze_seq_lite.c
  include/                  # vendored Schwung headers (not committed)
    plugin_api_v1.h
    midi_fx_api_v1.h
scripts/
  build.sh
  Dockerfile
.github/workflows/release.yml
release.json
```

---

## Publishing (tagged releases)

```bash
# bump versions in the two src/*/module.json files, commit, then:
git tag v1.0.0
git push --tags
```

GitHub Actions cross-compiles both modules, attaches the two tarballs to the
release, and updates `release.json` on `main`. To be listed in the Schwung
Module Store, open a PR adding the entries in `catalog-entries.json` to
[`module-catalog.json`](https://github.com/charlesvestal/schwung/blob/main/module-catalog.json).

---

## Credits & license

Created by Sam Di Domizio. Inspired by the dual generative sequencer of the
Moog Labyrinth (concept only; original code). Built for
[Schwung](https://github.com/charlesvestal/schwung) by Charles Vestal.

Licensed under the MIT License — see [LICENSE](LICENSE).
