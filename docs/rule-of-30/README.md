# The Rule of 30 — Mixing System for Suno / AI-Generated Audio

**An actionable, step-by-step mixing system for REAPER, built on the Rule of 30 session
builder and preset scripts that already exist.**

This folder is the *mixing* documentation. It does not ask you to build a session, create
folders, name tracks, wire sends or set up a bus — **the session builder already does all
of that.** It tells you what to do with the tracks the builder gives you, in the order that
avoids having to redo work.

---

## Where the scripts live

| What | Where | What it does |
|---|---|---|
| **Session builder** | [`sgm-audio/sgm-studios-reapack`](https://github.com/sgm-audio/sgm-studios-reapack) → `Rule-of-30/rule-of-30-session-builder.lua` (v2.0.0) | Builds the session: 67 tracks, 18 folders, panning, levels, colours, tempo |
| **Preset library** | same repo → `Rule-of-30/rule-of-30-plugin-presets.lua` (v3.0.0) | The chain table the builder applies. Stock REAPER FX with real settings, keyed by the track names the builder creates |
| **Phase alignment** | same repo → `Rule-of-30/phase-alignment-tool.lua` (v2.0.0) | Sample-accurate alignment of the quad-tracked DIs, with polarity detection |
| **Suno importer + workflow** | [`sgm-audio/sgm-reascripts`](https://github.com/sgm-audio/sgm-reascripts) and [`sgm-audio/sgm-suno-workflow`](https://github.com/sgm-audio/sgm-suno-workflow) | ZIP importer, metadata organiser, tempo mapper, dynamic splitter, loudness master, stem aligner |
| **Essentials** | `sgm-studios-reapack` → `SGM-Essentials/` | Mix-bus routing, one-click mastering, theme tools |
| **Supporting scripts in this repo** | `Tracks Properties/DP_Set track playback offset.lua`, `FX/BuyOne_FX presets menu…`, `FX/kawa_TrackFXs_Routing_Matrix_DS_KW.lua`, `Tracks/marcosteinebach_AutoDuck.lua`, `Tracks/kaddaok_Import UVR separations…`, `Various/amagalma_Spectral Edits Preset System.lua` | Playback offset for the Haas effect, preset menus, routing matrix, envelope ducking, UVR import, spectral repair |

---

## The documents

| Document | Read it when | Contents |
|---|---|---|
| **[MIXING_GUIDE.md](MIXING_GUIDE.md)** | You are about to mix | The step-by-step mix: build order, the exact track map with the chain each track already carries, low-end ownership, sidechaining, depth and the millisecond offset, the send-FX buses, mastering with Ozone, and a pre-delivery checklist |
| **[TECHNIQUES.md](TECHNIQUES.md)** | You want the *why* behind a move | Comping, layering, frequency masking, sidechaining, the Haas/precedence offset — plus 30 mixing hacks and the reference tables |
| **[SUNO-AI.md](SUNO-AI.md)** | The source is AI-generated | The artefact and frequency patch table, the fix ladder, the r/suno FAQ resources, and how to get Suno stems into the session |
| **[PROMPT-REFERENCE.md](PROMPT-REFERENCE.md)** | Before you spend credits | Prompt mechanics, a diagnosis of the supplied 10-minute techno prompt, and two fully rewritten prompts |
| **[plugin_inventory.example.json](plugin_inventory.example.json)** | You want Compact Mode to keep your kick tracks | A working example of the optional plugin inventory, with the matching rules and the two gotchas |

---

## The five-minute version

```
1. rule-of-30-session-builder  -> 1  Build Session          (67 tracks, 68 with master)
2. rule-of-30-session-builder  -> 2  Drum Chains
3. rule-of-30-session-builder  -> 3  Bass Chains
4. rule-of-30-session-builder  -> 4  Guitar Chains
5. rule-of-30-session-builder  -> 5  Vocal Chains
6. rule-of-30-session-builder  -> 6  Kick/Bass Sidechain     (routing only)
7. phase-alignment-tool        -> 1  Auto-Align             (the four rhythm DIs)
8. …mix, using MIXING_GUIDE.md…
```

Then the short version of what the mix needs:

- **One sub owner, one body owner.** `Kick Sub` or `Bass Synth Layer` — not both. `Kick Out`
  or `Bass DI` for the 60–200 Hz body — not both.
- **Sidechain the bass from the kick.** Option 6 has already wired the routing; you still
  have to put a compressor with a sidechain input on `Bass DI` and `Bass Synth Layer`.
  **ReaComp cannot do this** — it has no sidechain input.
- **Duck the reverb from the lead vocal.** Nothing else in the session is worth as much.
- **Offset the second guitar pair by 15–30 ms.** `Rhythm L2/R2` are already panned 80 % and
  high-passed 20 Hz higher than `L1/R1`; the time offset completes the effect. Check it in
  mono.
- **One master stage.** The builder puts ReaComp → ReaEQ → ReaLimit on the master. Replace it
  with Ozone, or use `SGM_auto_master.lua` — never both.

---

## A note on the numbers

Every track count, chain value, pan and level in these documents was **measured against the
builder running under the repo's own mock-API harness** (`tests/reaper_mock.lua`), not
inferred from the source. Where the builder's own behaviour is wrong, the documents say so
and say what the fix is.

Three findings worth knowing before you start:

1. **The session is 67 tracks, not 48** (68 including REAPER's master). The older README
   figures were wrong.
2. **24 of the 49 content tracks get no chain** from the four chain buttons, because the
   section-matching walk has no `break` and the nested `GLITCH (Corrupted Data)` folder inside
   `VOCALS` re-assigns the section. See [MIXING_GUIDE.md §0](MIXING_GUIDE.md#0-before-you-start--read-this-bit).
3. **Compact Mode does not persist**, and without a `plugin_inventory.json` it silently drops
   your three kick tracks and leaves the `KICK` folder empty.

---

## Gear these documents assume

- **REAPER 7.x** — the whole system is ReaScript-driven.
- **Waves Ultimate** — `C6-SideChain` (external sidechain), `C6`, `F6`, `Q10`, `L1`/`S1`,
  `CLA-76`, `CLA-2A`, `CLA-3A`, `Linear Phase EQ`, `DeEsser`, `Clarity Vx Pro`, `OneKnob
  Pumper`, `Transient Shaper`, `TG Mastering Chain`.
- **Kilohearts Ultimate** — `Compactor` (the sidechain compressor of choice), `Haas` (the
  millisecond offset), `Disperser`, `Faturator`, `Slice EQ`, `Carve EQ`, `Snap Heap`.
- **iZotope Ozone 12** — `Equalizer`, `Dynamic EQ`, `Dynamics`, `Exciter`, `Imager`,
  `Maximizer (IRC 5)`, `Stabilizer`, `Bass Control`, `Match EQ`, `Master Rebalance`; with
  Advanced: `Stem EQ`, `Unlimiter`, `Clarity`, `Impact`.
- **iZotope RX 12** — `Spectral Repair`, `De-Click`, `De-Crackle`, `Mouth De-click`,
  `De-plosive`, `Spectral De-noise`, `Dialogue De-reverb`, `Breath Control`.
- **Plus whatever else you own** — the plugin inventory file lets the builder pick per
  category, and the documents always give a stock-REAPER fallback for every move.

---

## Licence and provenance

These are documentation files. The scripts they describe live in their own repositories and
keep their own licences (`MIT` for the SGM Studios bundles). The supporting scripts
referenced from this repository (ReaTeam collection) carry their own authors' headers.

© SGM Studios / ReaTeam contributors, 2026.
