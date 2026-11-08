# Rule of 30 — Mixing Guide

**How to mix a session that `rule-of-30-session-builder.lua` has already built for you.**

This guide starts *after* the session exists. It does not ask you to create folders, name
tracks, wire sends or set up a bus — the builder does all of that. It tells you what to do
with the tracks it gives you, in the order that avoids having to redo work.

Companion documents:

- **[TECHNIQUES.md](TECHNIQUES.md)** — the technique library: comping, layering, frequency
  masking, sidechaining, depth and the millisecond offset, plus a grab-bag of mixing hacks.
- **[SUNO-AI.md](SUNO-AI.md)** — mixing an AI/Suno render: the artefact table, the fix ladder,
  the r/suno resource list, and how to get stems into the session.
- **[PROMPT-REFERENCE.md](PROMPT-REFERENCE.md)** — the prompt mechanics, a diagnosis of the
  supplied prompt, and two rewritten versions.

---

## 0. Before you start — read this bit

### The session you are mixing

The builder produces **67 tracks** plus REAPER's master (**68 in the project**):

| Section | Sub-folders | Content tracks |
|---|---|---|
| 🥁 DRUMS (TITAN BUS) | KICK, SNARE, CYMBALS/OH | 14 |
| 🔊 BASS ENGINE (Drop Bb) | — | 4 |
| 🎸 GUITARS (Wall of Sound) | RHYTHM (Quad), TEXTURE | 8 |
| ⚡ GLITCH & ATMOSPHERE | INDUSTRIAL FX, SYNTH LAYER | 6 |
| 🎤 VOCALS (Corrupted Robot) | CORE, GLITCH, WHISPERS, SCREAMS, AD-LIBS | 12 |
| 📡 SEND FX | — | 5 |
| **Content tracks** | | **49** |
| **Folder tracks** | 18 | |
| **+ REAPER master** | | **68** |

### Three things the builder does that you need to know

**1. Compact Mode is ON by default, and without an inventory it drops your kick tracks.**
Compact Mode only skips a track when the plugin inventory cannot satisfy that track's
category. With no `plugin_inventory.json` present, the three "smart" tracks —
`Kick In (Click)`, `Kick Out (Body)`, `Kick Sub (40-60Hz)` — are skipped, because there is
no inventory to satisfy their `Dynamics` / `EQ` categories.

You get **64 tracks instead of 67** (68 with the master), and the `KICK` folder is left
**empty**: with its three children gone, the folder-closing call lands on the folder's own
track, so the folder track itself becomes the folder-end marker and the folder contains
nothing. The track list is otherwise identical.

**Fix:** put a `plugin_inventory.json` next to the script — see
[plugin_inventory.example.json](plugin_inventory.example.json), which is a working example —
or set `CONFIG.compact_mode = false` at the top of `rule-of-30-session-builder.lua`.

> **Do not put `ReaEQ` in the `EQ` list or `ReaGate` in the `Dynamics` list.** The preset
> library also inserts those on the same three kick tracks, and the preset code **skips any
> plug-in that is already present without writing its parameters**. Verified result:
>
> | Inventory contains | `Kick Out (Body)` ends up with | `Kick Sub` ends up with | `Kick In` ends up with |
> |---|---|---|---|
> | `ReaEQ` / `ReaGate` | `ReaEQ` with **no band set** — the 40 Hz HP is silently lost | `ReaEQ` with **no band set** — the 30 Hz HP is lost | `ReaGate` at its **default threshold**, not −30 dB |
> | any other EQ (e.g. `Linear Phase EQ`) | that plug-in at defaults **plus** a properly configured `ReaEQ` with HP 40 Hz | that plug-in **plus** a properly configured `ReaEQ` with HP 30 Hz | the dynamics plug-in **plus** a properly configured `ReaGate` and `ReaEQ` |
>
> The second row is what the example file gives you.

**2. The Compact Mode toggle (menu option `0`) does not persist.**
`CONFIG.compact_mode` is a script-local. Option `0` flips it, prints a message, and the
script ends — the flag is gone before the next run. Verified: running `0` then `1` produces
an identical 64-track session to running `1` alone. Edit the constant instead.

**3. Twenty-four content tracks get no chain at all from the four chain buttons.**
The preset library defines chains for **46 of the 49** content tracks. The four chain buttons
(2 = drums, 3 = bass, 4 = guitars, 5 = vocals) select tracks by walking the track list and
remembering which *section folder* you are inside. `applyChainsForSection` does this:

```lua
for _, label in ipairs({ "DRUMS", "BASS", "GUITARS", "VOCALS", "GLITCH" }) do
  if name:find(label, 1, true) then
    current_section = label
    matched = true
  end
end
```

Three things go wrong:

| What happens | Consequence |
|---|---|
| `GLITCH` is in the label list, but there is **no menu option** for it | The six tracks under `⚡ GLITCH & ATMOSPHERE` — `White Noise Risers`, `Impact Booms`, `Digital Artifacts`, `Dark Pad (Minor)`, `Arpeggiator`, `Reverse Piano` — are never processed by any button |
| The nested folder `├─ GLITCH (Corrupted Data)` inside `🎤 VOCALS` contains the word **GLITCH**, and the loop has **no `break`** | `current_section` flips to `GLITCH` part-way through the vocal folder. `Demon Layer`, `Telephonic`, `Whisper L/R`, `Scream High L/R`, `Growl Center` and `Ad-lib 1–3` are all treated as GLITCH and never get their chains |
| `📡 SEND FX` contains no label at all | It inherits the previous section (GLITCH by then), so `Hall Reverb`, `Plate Reverb`, `1/4 Note Delay`, `1/8 Dotted Delay` and `Parallel Drum Crush` never get theirs either |

`Snare Sample (Trigger)`, `Hi-Hat Close` and `Ride` are the other gap: they have **no preset
entry in the library at all**.

Measured result after running options 2–5 on the full 67-track skeleton:

```
leaf tracks with FX:     25 of 49
leaf tracks with no FX:  24 of 49
```

**What to do:** apply those chains by hand using the values in §3. The underlying reason this
is a bug rather than a design choice: `Presets.forTrack` already matches chains by **track
name** (longest substring first), so the section walk is a second, redundant mechanism that
disagrees with the first one. Keying everything off `Presets.forTrack` would fix all 24
tracks in one line; a `break` in the label loop plus a `GLITCH`/`SEND FX` menu option fixes
it in three.

---

## 1. Build order

```
1  Session Builder → option 1  (Build Session)      ← structure, colours, pans, tempo
2  Session Builder → option 2  (Drum Chains)
3  Session Builder → option 3  (Bass Chains)
4  Session Builder → option 4  (Guitar Chains)
5  Session Builder → option 5  (Vocal Chains)
6  Session Builder → option 6  (Kick/Bass Sidechain) ← routing only; you add the plugin
7  Phase Alignment Tool → option 1 (Auto-Align)      ← the four rhythm DIs
8  …mix…
```

Steps 2–5 and 6 are independent and can be re-run at any time — the builder checks whether a
plug-in is already present before inserting another copy, so nothing stacks.

**Step 6 creates routing, not compression.** It creates a send from `Kick Out (Body)` to
`Bass DI (Lows <200Hz)` and to `Bass Synth Layer (Sub)`, routed to channels 3/4 so they can
be used as a sidechain input. It does not put a compressor on anything. You do that in §5.

---

## 2. Capture and alignment — before any mixing decision

### The quad guitars

The four rhythm tracks (`Rhythm L1 (100% L)`, `Rhythm R1 (100% R)`, `Rhythm L2 (80% L)`,
`Rhythm R2 (80% R)`) are four separate takes of the same part. Untreated, four slightly
different performances of one riff do not sum — they partially cancel, and the result is
thinner and quieter than one take.

**Run `phase-alignment-tool.lua` on them before anything else:**

1. Select the four rhythm tracks. **The first selected track with audio is the reference.**
2. Run the tool → option `1` (Auto-Align).
3. It measures normalised cross-correlation of the actual audio, then nudges each track by
   the number of samples it found.
4. It reports correlation, confidence and polarity **before** it moves anything, and it
   refuses to move a track whose correlation is below the threshold (default `0.30`) — two
   takes that do not correlate cannot be aligned by moving them.
5. A strongly negative correlation means the take is **inverted**. The tool says so rather
   than aligning it to the wrong lobe. Flip the polarity on the track.

**Limits, stated by the tool itself:** playrate other than 1.0 is refused; only the first
audio channel of a take is analysed; a track with several items is aligned on the item that
overlaps the reference and the others are left alone.

### Alignment is not width

These are two different jobs and the order matters:

| | Purpose | Tool | Result |
|---|---|---|---|
| **Alignment** | Make the four takes **coherent** so they sum | `phase-alignment-tool.lua` | The wall of sound gets *bigger and louder*, centred |
| **Offset** | Make the pairs sit **apart in depth** | Track playback offset / a delay | The wall gets *wide*, and the second pair sits *behind* |

Do alignment first. Offsetting an unaligned pair just delays the comb filtering.

### Other capture notes

- **DI before amp.** `Rhythm L1–R2` are DI tracks: the preset library high-passes them at
  90 Hz (L1/R1) and 110 Hz (L2/R2) so the mud is gone before an amp sim goes on them. If you
  are using Neural DSP or a Guitar Rig, that HPF is doing its job — leave it.
- **Bass is a three-band split.** `Bass DI` takes the raw DI (LP 200 Hz), `Bass Amp` takes
  the amp (HP 200 Hz), `Bass Fuzz` takes the fuzz (HP 2 kHz), `Bass Synth Layer` is sub only
  (LP 80 Hz). See §4.
- **The `Kick In` / `Kick Out` / `Kick Sub` split is a mic split, not a frequency split.**
  `Kick In (Click)` is the beater mic — it carries the click and the bleed, so it gets a
  gate. `Kick Out (Body)` is the front mic and gets the body. `Kick Sub (40-60Hz)` is the
  fundamental only. All three must agree or the kick will not glue.

---

## 3. What each track already has

From `rule-of-30-plugin-presets.lua`. ReaEQ bands, ReaComp thresholds, ReaGate thresholds and
reverb/delay mix values are written in the plug-in's own units and converted at apply time
by `Utils.fx_normalize()`, which reads the plug-in's own formatting back to find the matching
0..1 position.

> Rows marked **†** are in the preset library but are **not reached** by the menu buttons
> (see §0 item 3). Apply them by hand from the values in this table, or patch the builder.

### The exact track list

The builder uses REAPER's folder convention where the **last child of a folder also carries
the folder-end marker** (`I_FOLDERDEPTH = -1`). In REAPER that means clicking that track
selects the whole folder, and it means the folder-closing track is the one carrying the
`└─` name. Knowing which track that is saves a lot of confusion.

```
🥁 DRUMS (TITAN BUS)                         folder
  ├─ KICK                                    folder
    ├─ Kick In (Click)
    ├─ Kick Out (Body)
    └─ Kick Sub (40-60Hz)          ← closes KICK
  ├─ SNARE                                    folder
    ├─ Snare Top
    ├─ Snare Bottom
    ├─ Snare Sample (Trigger)
    └─ Glitch Snare (Duplicate)    ← closes SNARE
  ├─ CYMBALS/OH                               folder
    ├─ OH Left
    ├─ OH Right
    ├─ Hi-Hat Close
    ├─ Ride
    └─ Room Mono (Crushed)        ← closes CYMBALS/OH
  ├─ Tom Rack
  └─ Tom Floor                   ← closes DRUMS
🔊 BASS ENGINE (Drop Bb)                      folder
  ├─ Bass DI (Lows <200Hz)
  ├─ Bass Amp (Mids 200-2k)
  ├─ Bass Fuzz (Highs >2k)
  └─ Bass Synth Layer (Sub)      ← closes BASS ENGINE
🎸 GUITARS (Wall of Sound)                    folder
  ├─ RHYTHM (Quad)                           folder
    ├─ Rhythm L1 (100% L)
    ├─ Rhythm R1 (100% R)
    ├─ Rhythm L2 (80% L)
    └─ Rhythm R2 (80% R)         ← closes RHYTHM
  ├─ TEXTURE                                 folder
    ├─ Octave Lead L
    ├─ Octave Lead R
    ├─ The Scrape (FX)
    └─ Clean Ambient (Wet)      ← closes TEXTURE
                                         ← closes GUITARS
⚡ GLITCH & ATMOSPHERE                        folder
  ├─ INDUSTRIAL FX                           folder
    ├─ White Noise Risers
    ├─ Impact Booms
    └─ Digital Artifacts         ← closes INDUSTRIAL FX
  └─ SYNTH LAYER                             folder
    ├─ Dark Pad (Minor)
    ├─ Arpeggiator
    └─ Reverse Piano             ← closes SYNTH LAYER
🎤 VOCALS (Corrupted Robot)                   folder
  ├─ CORE (Intelligibility)                   folder
    ├─ Lead Vocal
    └─ Double Vocal              ← closes CORE
  ├─ GLITCH (Corrupted Data)                  folder
    ├─ Demon Layer (-12st)
    └─ Telephonic (500-3k BP)    ← closes GLITCH
  ├─ WHISPERS (Stereo)                        folder
    ├─ Whisper L (ASMR)
    └─ Whisper R (ASMR)         ← closes WHISPERS
  ├─ SCREAMS                                  folder
    ├─ Scream High L
    ├─ Scream High R
    └─ Growl Center             ← closes SCREAMS
  └─ AD-LIBS (Throws)                         folder
    ├─ Ad-lib 1
    ├─ Ad-lib 2
    └─ Ad-lib 3                 ← closes AD-LIBS
📡 SEND FX                                    folder
  ├─ Hall Reverb
  ├─ Plate Reverb
  ├─ 1/4 Note Delay
  ├─ 1/8 Dotted Delay
  └─ Parallel Drum Crush        ← closes SEND FX
```

**Note the `GLITCH (Corrupted Data)` folder inside VOCALS.** It is a real sub-folder and it
is the reason nine vocal tracks never get their chains (§0 item 3). It is also the reason
the vocal section has five sub-folders instead of one flat list — worth knowing before you
drag things around.

### Drums

| Track | Chain | Notes |
|---|---|---|
| `Kick In (Click)` | ReaGate −30 dB, attack 1 ms, release 80 ms → ReaEQ band +3 dB @ 3500 Hz | Gate the bleed out of the beater mic |
| `Kick Out (Body)` | ReaEQ HP 40 Hz → ReaComp −18 dB, 6:1, 5 ms / 120 ms | Fast and hard, for weight. **This is the sidechain source** |
| `Kick Sub (40-60Hz)` | ReaEQ HP 30 Hz | The fundamental, alone |
| `Snare Top` | ReaEQ band +3 dB @ 4000 Hz → ReaComp −20 dB, 4:1, 10 ms / 150 ms | The crack |
| `Snare Bottom` | ReaEQ band 0 dB @ 3000 Hz → ReaGate −24 dB | Snare-wire range, flat to start |
| `Snare Sample (Trigger)` | *no preset* † | Add: HP 300 Hz, light gate if it bleeds |
| `Glitch Snare (Duplicate)` | ReaComp −35 dB, 20:1 | Deliberately crushed. **Do not leave it full level all the way through** — it is a glitch layer, gate it to the moments it is meant to punctuate |
| `OH Left` / `OH Right` | ReaEQ HP 300 Hz | Cymbals only, ±0.7 pan |
| `Hi-Hat Close` | *no preset* † | Add: HP 500 Hz, LPF 8 kHz if it is harsh |
| `Ride` | *no preset* † | Add: HP 400 Hz, and it will fight the crash — pick one |
| `Room Mono (Crushed)` | ReaComp −40 dB, 20:1, 30 ms / 200 ms | Smashed for room character. **This is the Parallel Drum Crush send source** |
| `Tom Rack` / `Tom Floor` | ReaEQ HP 60 / 40 Hz | Start flat, tune before processing |

### Bass

| Track | Chain | Notes |
|---|---|---|
| `Bass DI (Lows <200Hz)` | ReaEQ LP 200 Hz → ReaComp −18 dB, 4:1, 5 ms / 120 ms | **This track owns the lows.** Sidechain input comes from the kick |
| `Bass Amp (Mids 200-2k)` | ReaEQ HP 200 Hz | High-passed out of the kick's way |
| `Bass Fuzz (Highs >2k)` | ReaEQ HP 2000 Hz | Fuzz only adds the top. Panned −0.15 |
| `Bass Synth Layer (Sub)` | ReaEQ LP 80 Hz | Sub only. **Second sidechain target** |

### Guitars

| Track | Chain | Pan |
|---|---|---|
| `Rhythm L1 (100% L)` | ReaEQ HP 90 Hz | 100 % L |
| `Rhythm R1 (100% R)` | ReaEQ HP 90 Hz | 100 % R |
| `Rhythm L2 (80% L)` | ReaEQ HP 110 Hz | 80 % L |
| `Rhythm R2 (80% R)` | ReaEQ HP 110 Hz | 80 % R |
| `Octave Lead L/R` | ReaEQ band +2 dB @ 2500 Hz | ±0.5 |
| `The Scrape (FX)` | ReaEQ HP 200 Hz | centre — sound design |
| `Clean Ambient (Wet)` | ReaVerbate wet 0.55, room 0.70 | centre — long ambient wash |

The L2/R2 pair is high-passed 20 Hz higher than L1/R1 *on purpose*: that is what makes it
sit behind. See §6 for the time-based half of that effect.

### Vocals

| Track | Chain | Level | Role |
|---|---|---|---|
| `Lead Vocal` | ReaEQ HP 100 Hz → ReaComp −20 dB, 3:1, 10 ms / 150 ms | — | Intelligibility. Everything else is subordinate to this |
| `Double Vocal` | ReaComp −22 dB, 2.5:1 | −6 dB | Weight and reinforcement |
| `Demon Layer (-12st)` | ReaEQ HP 150 Hz † | −8 dB | The "corrupted data" texture |
| `Telephonic (500-3k BP)` | ReaEQ BP 1500 Hz † | −10 dB | Band-passed. **Tune both edges by ear — the preset leaves them wide** |
| `Whisper L/R (ASMR)` | ReaEQ HP 150 Hz † | −12 dB | ±100 % pan |
| `Scream High L/R` | ReaComp −24 dB, 10:1 † | — | **No HPF — add one at 150–200 Hz** or it will muddy the lead and fight the bass |
| `Growl Center` | ReaEQ HP 120 Hz † | — | Centred. **Keep it below the lead or it fights the lead's fundamental** |
| `Ad-lib 1–3` | ReaVerbate wet 0.35, room 0.55 † | — | Throws |

### Glitch and ambience

| Track | Chain |
|---|---|
| `White Noise Risers` | ReaEQ BP 1000 Hz † |
| `Impact Booms` | ReaComp −25 dB, 8:1 † |
| `Digital Artifacts` | ReaEQ HP 200 Hz † |
| `Dark Pad (Minor)` | ReaVerbate wet 0.45, room 0.80 † |
| `Arpeggiator` | ReaDelay wet 0.30 † |
| `Reverse Piano` | ReaVerbate wet 0.50, room 0.75 † |

### Send FX buses

| Bus | Chain | Notes |
|---|---|---|
| `Hall Reverb` | ReaVerbate wet 1.0, room 0.90 † | 100 % wet — it is a bus, not a track |
| `Plate Reverb` | ReaVerbate wet 1.0, room 0.55 † | 100 % wet |
| `1/4 Note Delay` | ReaDelay wet 1.0 † | **The delay time is not set by the preset** — see §7 |
| `1/8 Dotted Delay` | ReaDelay wet 1.0 † | Same |
| `Parallel Drum Crush` | ReaComp −50 dB, 20:1 † | Heavily crushed parallel bus |

### Master

The builder applies `Presets.buses.master`: **ReaComp −14 dB, 2:1** (glue) → **ReaEQ HP
30 Hz** → **ReaLimit ceiling −1.0 dBTP**. The master track is named `🎛️ MASTER (Ozone
Chain)` — the intent is that you replace this with Ozone. See §9.

---

## 4. Low-end ownership

The bass is already split into three bands by the builder. That is the low-end plan — you do
not need to invent one, you need to **not undo it**.

```
sub        < 35 Hz    ← ONE owner only
kick sub   35–60 Hz   ← Kick Sub (40-60Hz)
body       60–200 Hz  ← Kick Out (Body)  or  Bass DI, NOT both
low mids   200–2k     ← Bass Amp
top        > 2k       ← Bass Fuzz
```

### The three decisions you have to make

**Decision 1 — does the kick or the bass own the body?**
`Kick Out (Body)` is HP 40 Hz with a 6:1 compressor. `Bass DI` is LP 200 Hz. They will
overlap between 40 and 200 Hz unless one of them gives way.

- Heavy, riff-driven metal: the **kick** owns the body. HP `Bass DI` at 120 Hz and let
  `Bass Amp` carry the bass's identity.
- Bass-forward: the **bass** owns the body. HP `Kick Out` at 60 Hz and lean on `Kick Sub` for
  the weight.

**Decision 2 — `Kick Sub` or `Bass Synth Layer`?**
Both want the sub, and `Bass Synth Layer` is LP 80 Hz. They cannot both play the sub of the
same note — it will be 3–6 dB louder than you expect and it will not translate.

- Use `Bass Synth Layer` when there is a synth bass doing the low notes.
- Use `Kick Sub` when the bass is a DI/amp bass and the kick carries the weight.
- If you use both, they must be playing **different notes**, and the synth sub should be at
  least 6 dB below the kick sub.

**Decision 3 — how much sub is there at all?**
Both `Kick Sub` and `Bass Synth Layer` can carry energy to 20 Hz. Pick one and high-pass the
other at 35 Hz. The master chain's HP 30 Hz will not save you — a limiter does not fix a
doubled sub.

### The high-pass pass

Everything that is not kick, bass or sub gets a high-pass filter. The presets already do
most of it. The ones that need adding:

| Track | Add HPF at | Because |
|---|---|---|
| `Snare Sample (Trigger)` | 300 Hz | A raw sample has weight it does not need |
| `Hi-Hat Close` | 500 Hz | Same |
| `Ride` | 400 Hz | Same |
| `Scream High L/R` | 150–200 Hz | Screams carry a lot of low-mid energy |
| `Ad-lib 1–3` | 200 Hz | Ad-libs are usually recorded close and are heavy |
| `Double Vocal` | 120 Hz | No HPF in the preset; it will double the lead's low end |
| `Growl Center` | already HP 120 Hz | Keep it there |
| All reverb and delay returns | 200–400 Hz | **A return must never carry bass** — see §7 |

---

## 5. Sidechaining

### What option 6 already did

Running option 6 creates, from `Kick Out (Body)`:

- a send to `Bass DI (Lows <200Hz)` — source channels 1/2, destination **channels 3/4**
- a send to `Bass Synth Layer (Sub)` — same

Destination channel 2 (`I_DSTCHAN = 2`) is what makes them usable as a **sidechain input**
rather than an audio send. The console prints:

```
✓ Sidechain: Kick Out -> Bass DI (channels 3/4)
✓ Sidechain: Kick Out -> Bass Synth (channels 3/4)
```

It re-uses an existing send if one is there, so it is safe to run twice.

### What you still have to do: put a compressor on the bass tracks

The routing exists; nothing is compressing anything yet. Add a plug-in to `Bass DI` and to
`Bass Synth Layer` that has a sidechain input, and point that input at channels 3/4 of the
target track.

**Do not use ReaComp for this.** The builder's own console message suggests "ReaComp with
detector input 3+4", but ReaComp has no sidechain input — it cannot do this. Use a plug-in
that has one:

| Plug-in | How | Notes |
|---|---|---|
| **Kilohearts Compactor** | Sidechain input → the track's channels 3/4 | Sample-level ducking, no lag. Attack/Hold/Release/Threshold/Range, three detection modes, stereo-width control. **Best fit** |
| **Waves C6** | Use the **`C6-SideChain`** component — it is a different plug-in from plain `C6` and only the SideChain variant has an external input | Per-band external sidechain. Bypass the low, low-mid and high bands and drive only the band you want |
| **Waves F6** | Sidechain input → channels 3/4 | 16-band dynamic EQ; good when you want to duck one band rather than the whole signal |
| **iZotope Neutron** (Unmask) | Sidechain → channels 3/4 | The builder's other suggestion; dynamic EQ, good for surgical unmasking |

### Settings

| Duck | Amount | Attack | Release @100 BPM |
|---|---|---|---|
| `Bass DI` ← kick, **low band only** | 2–4 dB | 0.1–1 ms | 120–180 ms |
| `Bass Synth` ← kick, full band | 2–4 dB | 0.1–1 ms | 120–180 ms |
| `Rhythm L2/R2` ← `Lead Vocal` | 1–3 dB | 5–15 ms | 150–250 ms |
| `Hall Reverb` / `Plate Reverb` ← `Lead Vocal` | 2–4 dB | 10–30 ms | 200–300 ms |

**Release timing.** The builder sets 100 BPM. At 100 BPM a 16th note is 150 ms and an 8th is
300 ms. A release around 150 ms puts the bass back at full level just before the next kick.
At 95 BPM use ~180 ms; at 110 BPM use ~135 ms.

**Duck the low band only where you can.** Ducking the whole bass track is the EDM sound, not
the metal sound — it removes the harmonic definition that makes a distorted bass readable.

### The ducks the builder does not set up

The vocal system has 12 tracks and no ducking at all. These are the three that earn their
keep:

1. **`Hall Reverb` and `Plate Reverb` ← `Lead Vocal`.** Without it the reverb fills exactly
   the 500 Hz–3 kHz band the lead vocal lives in and the vocal disappears into its own
   space. This is the single most valuable duck in the whole session.
2. **`Rhythm L2` and `Rhythm R2` ← `Lead Vocal`.** Only the second pair. The first pair is
   the wall; ducking it takes the guitars out of the song.
3. **`Telephonic` and `Demon Layer` ← `Lead Vocal`.** Both sit in the lead's presence band.
   They are already at −10 and −8 dB, so 1–2 dB is enough.

### The envelope-duck alternative

If you would rather not wire a sidechain, `Tracks/marcosteinebach_AutoDuck.lua` (in this
repository) creates volume envelope points on a track that automatically ducks a track
directly beneath it, whenever an item on the lower track plays. It ducks by **item
boundary**, not by level — so it follows the arrangement, not the performance. For a vocal
that plays in fixed blocks, that is exactly right, and it is more robust than a live
sidechain. It cannot follow a vocal that breathes in and out mid-line.

---

## 6. Depth and the millisecond offset

### The effect

If two similar sounds reach your ears within about **40 ms**, the brain fuses them into one
sound with a *spatial location*. Delay one copy of a mono source by 5–35 ms, pan the two
copies hard left and hard right, and the source becomes wide and slightly behind the
listener — no reverb, no stereo widener.

Past ~40 ms the brain hears a separate reflection and it becomes an echo.

### Where the builder has already done half of it

The four rhythm tracks are already panned `100 % L / 100 % R / 80 % L / 80 % R`, and the L2/R2
pair is high-passed 20 Hz higher than L1/R1. That combination — **frequency difference plus
pan difference** — is already what makes the second pair sit behind. The millisecond offset
adds the *time* dimension on top.

### How to add it in REAPER

**Route A — the plugin (fastest).** Kilohearts **Haas** is a Snapin in Kilohearts Ultimate
with a single delay-time knob and left/right select switches. Put it on the track, set the
time, done. It also exists as a standalone plug-in.

**Route B — track playback offset (most control).**
1. Duplicate the track (`Item: Duplicate items`, or copy the item to a new track).
2. Pan the original hard **left**, the copy hard **right**.
3. On the copy, set the playback offset to the value from the table below.
   `Tracks Properties/DP_Set track playback offset.lua` in this repository does this with a
   GUI (a slider, ±1 ms / ±10 ms steppers and a numeric field).
4. Bring the delayed copy down 1–3 dB.

**Important:** REAPER's track playback offset moves the *entire* track — automation, markers
and all. If you only want the audio shifted, nudge the **item** instead, or use
`Tracks Properties/edgemeal_Enable track playback offset for selected tracks (Samples).lua`
for sample-accurate behaviour.

### Offset guide for this session

| Track | Offset | Why |
|---|---|---|
| `Rhythm L2` / `Rhythm R2` | **15–30 ms** | The second pair should sit behind the first. This is the single best use of the effect in the session |
| `Whisper L/R (ASMR)` | 10–20 ms | Close-mic texture wants depth, not width |
| `Scream High L/R` | 8–15 ms | Short and urgent — shorter offsets |
| `Clean Ambient (Wet)` | 25–40 ms | It is already a wash; the offset makes it sit behind everything |
| `Octave Lead L/R` | 0 | A lead should be forward |
| `Lead Vocal` | **0** | See below |
| `Kick Out`, `Kick Sub`, `Bass DI`, `Bass Synth` | **0** | The bottom stays mono |
| `Snare Top`, `Snare Bottom` | 0 | The backbeat has to stay solid and centred |

### Mono safety — the part that bites on release

Summing an offset pair to mono makes the two copies interfere and you get **comb
filtering**: a series of peaks and nulls across the spectrum. The source goes hollow, thin
or phasey. And most listeners are on a phone, a Bluetooth speaker, a car stereo or a smart
speaker — all effectively mono.

Four fixes, best first:

1. **Detune the delayed copy by ±5–10 cents.** This decorrelates the two signals enough to
   kill the comb while keeping the width in stereo. Best answer.
2. **Keep a dry, centred copy.** Pan the offset pair slightly narrower and leave the dry
   source centred. Depth in stereo, a solid centre in mono.
3. **Lower the delayed copy** 1–3 dB so the nulls are shallower.
4. **Shorten the offset** toward 10 ms, which survives mono better than 25 ms.

Always check the mono button, and keep the correlation meter **above +0.4** in the loud
sections.

**The lead vocal is the one place this goes wrong most often.** A Haas'd lead vocal sounds
wonderful in stereo and collapses on every phone in the world. If you want a wide vocal,
use **artificial double tracking** instead: duplicate the vocal, detune the copy ±7 cents,
add 11–17 ms of asymmetric delay, pan hard L/R, then lift the side channel 8–12 kHz with an
M/S EQ. Wide *and* mono-safe. The `Double Vocal` track already exists for exactly this.

### The other three depth controls

Haas is only one of four. The others are cheaper:

| Control | What it does | Setting |
|---|---|---|
| **Pre-delay on the reverb return** | Pushes the source *in front of* the space, or separates it from it | 0–10 ms = forward; 30–60 ms = set back |
| **Level** | Distance | Quieter = farther. The builder already sets the hierarchy: Double −6, Demon −8, Telephonic −10, Whispers −12 |
| **High-frequency rolloff** | Air absorption with distance | LPF the "far" copy above 6–8 kHz. The L2/R2 HPF difference (90 vs 110 Hz) is the frequency half of this |

---

## 7. The send FX buses

Five buses, and they are not interchangeable. Two things to know before you use them:

- **The builder creates the buses but no sends to them.** The only sends the builder ever
  creates are the two sidechain sends from option 6. Every send into `Hall Reverb`,
  `Plate Reverb`, the two delays and `Parallel Drum Crush` is yours to make.
- **The presets set the wet mix but never set the delay time.** The bus named
  `1/4 Note Delay` is a ReaDelay with wet 1.0 and whatever time was in the plug-in. You have
  to set it.

### Delay times

The builder sets 100 BPM (range 95–110). ReaDelay also exposes a tempo-sync control in its
own UI, which is the easier way to do this.

| Bus | Note | @95 BPM | @100 BPM | @110 BPM |
|---|---|---|---|---|
| `1/4 Note Delay` | quarter note | 632 ms | 600 ms | 545 ms |
| `1/8 Dotted Delay` | dotted eighth | 474 ms | 450 ms | 409 ms |

### Who sends to what

| Source | Bus | Level | Why |
|---|---|---|---|
| `Snare Bottom`, `Room Mono` | `Hall Reverb` | −12 to −18 dB | The room behind the snare |
| `Lead Vocal`, `Ad-lib 1–3` | `Plate Reverb` | −14 to −20 dB | The vocal's own space. **Duck this from the lead** (§5) |
| `Octave Lead L/R`, `The Scrape` | `1/4 Note Delay` | −16 to −22 dB | Throws on the guitar |
| `Arpeggiator`, `Dark Pad` | `1/8 Dotted Delay` | −18 to −24 dB | Movement without clutter |
| `Room Mono (Crushed)` | `Parallel Drum Crush` | unity | A parallel crush, not a send — see below |

### Reverb returns need a high-pass

A reverb return that carries bass re-introduces exactly the mud you just removed. High-pass
the returns: `Hall Reverb` at 200 Hz, `Plate Reverb` at 300 Hz, both delays at 400 Hz.

### Pre-delay is the depth control you are not using

Set the pre-delay on the return, not on the send. On `Plate Reverb`, a 20–40 ms pre-delay
pushes the vocal forward in the depth field and separates it from the instrumental. A
reverb with no pre-delay sits *on top of* the dry signal; with pre-delay it sits *behind*.

### The Parallel Drum Crush bus

`Parallel Drum Crush` is not a reverb — it is a parallel compression bus with a very heavy
setting (ReaComp at −50 dB threshold, 20:1). Send `Room Mono (Crushed)` into it and the
drum bus gets a crushed parallel layer. Two rules:

- **It is parallel, so keep the send pre-fader** if you want the crush to follow the drum
  level, or post-fader if you want it constant under a moving performance.
- **Do not also crush the source.** `Room Mono` already has a 20:1 ReaComp on it. Crush
  twice and the room disappears entirely.

---

## 8. Mixing the guitars

Four rhythm tracks, two octave leads, a scrape and an ambient wash. The wall is the
instrument; the lead is the exception.

### The wall

After alignment, the four rhythm tracks sum. Four problems follow:

1. **They sum to more level than one take.** Expect the wall to be 3–6 dB hotter than a
   single take. Pull all four down together — do not fix it on one.
2. **The L2/R2 pair is in the way once it sums.** The second pair is at 80 % pan and HP 110 Hz
   *before* summing. After summing with L1/R1 it is at 100 % and effectively HP 90 Hz. If the
   wall is muddy, raise the L2/R2 high-pass to 150 Hz and add a 2–3 dB cut at 300–400 Hz on
   L2/R2 only. That is the pair you want behind.
3. **The guitars will mask the snare crack.** The snare sits at 3–5 kHz; distorted guitars
   have their own presence there. The fix is to take the guitars down, not the snare up: a
   dynamic EQ on the guitar bus, cutting 3–5 kHz by 2–3 dB only when the guitars are loud.
4. **Every guitar needs a high-pass that changes with the arrangement.** The 90 Hz HP is for
   the riff. When the riff drops out and only the octave lead is playing, raise the lead's
   HP to 200 Hz — otherwise it fills the space the rhythm guitar just left.

### The octave leads

`Octave Lead L/R` are panned ±0.5 with a +2 dB band at 2500 Hz. They are the melodic
foreground, so:

- Keep them centred-ish (±0.5 is fine, but do not widen them further).
- Do **not** Haas them.
- They are a doubled pair — if they are two separate performances, align them with the phase
  alignment tool, same as the rhythm guitars.

### The scrape

`The Scrape (FX)` is sound design. It is the one track where the preset is a starting point
rather than a recommendation. It wants to be loud, wrong, and in a place nobody expects —
which usually means it is too prominent until it is not.

---

## 9. Mastering

### The stock chain

The builder applies to the master: **ReaComp −14 dB, 2:1** → **ReaEQ HP 30 Hz** →
**ReaLimit −1.0 dBTP**.

`SGM_auto_master.lua` (SGM-Essentials) applies the same idea with different numbers:
HPF 30 Hz, ReaComp −18 dB / 2:1 / 10 ms / 100 ms, ReaLimit −1.0 dBTP.

**Do not run both.** They are two master stages on the same bus.

### The Ozone chain

The master track is literally named `🎛️ MASTER (Ozone Chain)`, so the intent is that you
replace the stock chain. Do it like this:

1. **Select the master track**, open its FX chain, remove the stock ReaComp / ReaEQ /
   ReaLimit.
2. Insert **Ozone 12** (or Ozone 11). Chain order:

| # | Module | What for | Starting setting |
|---|---|---|---|
| 1 | **Equalizer** | Corrective | HP 25–30 Hz, cut 200–500 Hz by 1–2 dB if it is still there |
| 2 | **Imager** | Narrow the sides below 120 Hz | Side-only low cut. Pull the image in if it is wider than the reference |
| 3 | **Dynamics** | Glue | 1.5–2:1, threshold ≈ −18 dB |
| 4 | **Bass Control** | Cross-system low-end translation | *Punch* for a metal record, *warmth* if the low end is too clean |
| 5 | **Exciter** *(optional)* | Re-add perceived brightness over codec fizz | Low. It is a mask, not a cure |
| 6 | **Maximizer (IRC 5)** | Limiting | Ceiling **−1.0 dBTP**. Intensity: start at 2, raise until the loudness target is met |
| 7 | **Dither** | Only on the 16-bit export | — |

3. Set the loudness target. A heavy metal record wants **−9 to −7 LUFS**; if you are
   targeting streaming and want it level with other tracks, **−14 LUFS**.

### Rules

- **One master stage.** If you used Suno's own mastering, either unmaster it first with
  Ozone's **Unlimiter** (Advanced) or master from the stems and ignore Suno's master.
- **Never re-encode lossy to lossy.** Export 24-bit WAV from the session, and make the MP3
  from the WAV.
- **The master is not a mix bus.** Do not fix a buried vocal or a muddy low end here —
  mastering cannot separate sources.

---

## 10. Pre-delivery checklist

Run down this list before you export.

**Structure**
- [ ] All four rhythm DIs aligned, correlation reported, no inverted takes
- [ ] The kick's three tracks agree with each other
- [ ] Low-end ownership decided: one sub owner, one body owner

**Levels**
- [ ] The wall of guitars is 3–6 dB down from where it started after summing
- [ ] `Double Vocal` at −6, `Demon Layer` at −8, `Telephonic` at −10, `Whispers` at −12
- [ ] `Growl Center` below the lead vocal
- [ ] `Glitch Snare` is gated to the moments it punctuates, not playing all the way through

**Frequency**
- [ ] Every non-bass track high-passed (`Scream High`, `Double Vocal`, `Ad-libs`,
      `Snare Sample`, `Hi-Hat`, `Ride` — these have no HPF in the presets)
- [ ] Reverb and delay returns high-passed
- [ ] The guitars do not mask the snare crack — 3–5 kHz taken out of the guitars, not added
      to the snare

**Space**
- [ ] Both delays set to their actual note value, not the plug-in default
- [ ] Reverb pre-delay set
- [ ] `Hall Reverb` / `Plate Reverb` ducked from the lead vocal
- [ ] Every offset pair checked in mono; correlation above +0.4

**Master**
- [ ] One master stage only
- [ ] Loudness target met; true peak under −1.0 dBTP
- [ ] Checked on headphones, monitors, a phone speaker, and at low volume
- [ ] Exported as 24-bit WAV, with the MP3 made from the WAV
