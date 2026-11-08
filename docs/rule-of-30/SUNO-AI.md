# Rule of 30 — Mixing Suno / AI-Generated Audio

The Suno and AI-specific half of the system: the artefact table, the fix ladder, the r/suno
resource list, and how to get AI stems into the Rule of 30 session.

Everything here assumes the session has already been built by
`rule-of-30-session-builder.lua` and that you have imported the stems. See
**[MIXING_GUIDE.md](MIXING_GUIDE.md)** for the session itself,
**[TECHNIQUES.md](TECHNIQUES.md)** for the technique library and
**[PROMPT-REFERENCE.md](PROMPT-REFERENCE.md)** for the prompt work.

---

## 1. The five signature AI artefacts

These are the fingerprints of a neural-codec render. They account for the overwhelming
majority of "my Suno track sounds bad".

| # | Symptom | Why it happens | Fix | Can it be fully fixed? |
|---|---|---|---|---|
| **1** | **Quiet next to commercial tracks** | Exports land 1–3 LU below the streaming target; measured median across AI exports ≈ **−15.2 LUFS** | Master to a loudness target with a −1.0 dBTP ceiling | **Yes** — pure gain staging |
| **2** | **Muffled, boxy, muddy** | Energy buildup around **200–500 Hz** plus baked-in reverb | 2–4 dB wide-Q cuts on secondaries; HPF the sub-30 Hz rumble | **Mostly** — the source balance is baked in; only the mask can be reduced |
| **3** | **Shimmery reverb sheen** | A diffuse reverb rendered into the track, living mostly in the **stereo sides** | M/S EQ and M/S dynamics | **Never fully** — reduce it, don't expect it gone |
| **4** | **Harsh or lisping vocals** | Exaggerated sibilance in the **5–9 kHz** band, sharpened further by lossy coding | De-essing the full mix works surprisingly well | **Mostly** — there is rarely much else in that band at those moments |
| **5** | **Fizzy, swirly high end** | Lossy-codec artefacts from generation and export | Not fixable after the fact | **No** — export the best format and **never re-encode** |

**The uncomfortable truth:** #5 is in the samples. WAV gives you a cleaner *starting point*,
not a clean track. The codec residual is baked in before the file is exported.

---

## 2. The frequency problem table

"Fix in" names the section of [MIXING_GUIDE.md](MIXING_GUIDE.md) or
[TECHNIQUES.md](TECHNIQUES.md) that handles it.

### Low end

| Symptom | Where | Cause | Fix | Fix in |
|---|---|---|---|---|
| Rumble, infrasonic wobble | <30 Hz | Codec noise floor, sub-overs | HPF 25–30 Hz on the master; HPF everything but sub | MG §4 |
| Muddy, undefined low end | 60–120 Hz | Kick and bass both have energy here | Sidechain bass ← kick (2–4 dB); or carve the kick; or re-voice the bass | MG §5, TECH §D |
| Bloated, "boomy", phone-speaker disaster | 40–90 Hz | Sub and kick doubling | One sub owner only; narrow the sides below 120 Hz | MG §4 |
| Kick has no punch | 2–8 kHz transient + 50–90 Hz body | AI codec flattened the transient | Transient shaper (Kilohearts Disperser); small EQ lift at 3–5 kHz; re-sound the kick from MIDI | TECH hack 11, hack 24 |
| Bass disappears on small speakers | 60–100 Hz | Too little, or entirely mono-low | Ozone Bass Control (punch/warmth); Ozone Imager low-side control | MG §9 |

### Low mids — the mud zone

| Symptom | Where | Cause | Fix | Fix in |
|---|---|---|---|---|
| Everything is thick and unclear | **200–500 Hz** | Several elements stacking + baked-in reverb | 2–4 dB wide-Q cut on **secondaries**; carve the kick body and percussion bus | TECH §C |
| Boxy vocal | 300–600 Hz | Room/algorithmic reflection | Narrow sweep (Q 5–6, +4–6 dB), find the ugly frequency, flip to −3 dB | TECH §C |
| Honky / nasal | 500–800 Hz | Formant or resonance | Narrow sweep, 3–6 dB cut | TECH §C |
| Muffle / "underwater" | Wide | Too many instruments; worsens toward the end of long AI tracks | Cut the prompt to 3–4 main instruments and regenerate the section | §5 |
| Vocal thin after mud removal | 150–300 Hz | Over-subtraction | Restore +1–2 dB at 150–200 Hz, wide Q, **on the vocal only** | MG §4 |

### Mids and presence

| Symptom | Where | Cause | Fix | Fix in |
|---|---|---|---|---|
| Vocal buried | 1–4 kHz | Instruments mask the vocal's presence | **Carve 2–3 dB out of the instruments**; then +1.5–2.5 dB at 3 kHz on the vocal | TECH §C, MG §8 |
| Snare sounds like a "clack" | 2–5 kHz | Over-processed AI transient | Tame 2–4 kHz; add body at 200–300 Hz | MG §3 |
| Harsh upper mids, fatiguing | 3–6 kHz | Over-synthesis | **Dynamic** EQ so only the loud moments are tamed | TECH §C |
| Snare/hats metallic and piercing | 3–6 kHz | AI artefact | Tame 3–5 kHz; add saturation for warmth | MG §3 |

### High end

| Symptom | Where | Cause | Fix | Fix in |
|---|---|---|---|---|
| Sibilance, lisping, spitty S/T/CH | **5–9 kHz** | Exaggerated sibilance + lossy sharpening | De-esser at 6 kHz, 2–4 dB. Waves DeEsser or RX De-ess | TECH §C |
| Metallic hiss / digital fizz | 8–16 kHz | Neural-codec shimmer | **Spectral removal**, not a shelf cut. Clarity Vx Pro, Supertone Clear, IntRect, Neural Analog, RX Spectral De-noise at low reduction | §3 below |
| "Cheap 128 kbps" edge | 16–20 kHz | Codec cutoff | Nothing recovers it. Export WAV and never re-encode | §3 below |
| Airless / dull | 12–20 kHz | Over-correction | +1.5–3 dB shelf above 10 kHz on the vocal | MG §3 |
| Weird high-frequency "swirl" | 10–16 kHz | AI artefact in the musical spectrum | Spot-removal in the RX Spectral Editor; aggressive denoising makes it *watery* | §3 below |

### Reverb and space

| Symptom | Cause | Fix | Fix in |
|---|---|---|---|
| Vocal drowned in generated reverb | Suno bakes reverb into the vocal | RX De-reverb on the **vocal stem only**, blended ~70 % dry | MG §7 |
| Whole mix feels like a reverb | Diffuse sheen rendered into the sides | M/S EQ + M/S dynamics on the master; reduce the sides | MG §9 |
| Mix feels flat and mono | Everything centred | Haas pairs, panning, M/S width | TECH §E |
| Clipped-off ending | AI ran out of context | Generate extra seconds, trim, then a 2–3 s fade | MG §2 |

### Voices specifically

| Symptom | Cause | Fix |
|---|---|---|
| Robotic, flat delivery | Over-"typed" lyrics, too few syllables per line | Rewrite with contractions, dropped syllables, natural phrasing; keep syllable counts similar across verses |
| Slurred / rushed | Too many syllables per line | Shorten the line |
| Voice changes mid-song | Model drifts on long generations | Generate the section again; use a Voice or Persona for consistency |
| Wrong pronunciation | Model reads unfamiliar words literally | Spell it phonetically: `Ver-sus`. Change only that one word |
| Over-processed vocal | Voice influence set too high | Drop voice influence to **40–60 %** |
| Breath is a separate "block of noise" | The breath does not follow the vocal's intensity | Reduce or gate the breath layer; it should track the phrase |
| Delayed vocal sounds like a copy | AI doubles are literal copies | Vary the timing or pitch of the double; use ADT instead of a plain copy |

---

## 3. The fix ladder

Work in this order. Every step makes the next one easier, and skipping ahead makes things
worse.

```
1.  GENERATION-SIDE   Reduce the problem at the source (prompt, tags, segmentation)
2.  EXPORT            WAV, never re-encode lossy -> lossy
3.  SEPARATION        Stems - Suno 12-stem, plus a second separator for comparison
4.  IMPORT            SGM Suno ZIP Importer -> 12 tracks, or UVR into comping lanes
5.  GAIN STAGE        Common reference level across all stems
6.  DE-CLIP           Before any dynamics
7.  DE-CLICK          De-click, Mouth De-click, De-crackle
8.  DE-HISS           Spectral De-noise (learned) / Clarity Vx
9.  DE-REVERB         On the vocal stem only
10. DE-ARTEFACT       Spectral repair -> dedicated de-artefact -> gentle EQ
11. BUILD SESSION     rule-of-30-session-builder -> option 1
12. CHAINS            Options 2-5 (drums, bass, guitars, vocals)
13. ALIGN             phase-alignment-tool -> option 1
14. LOW-END OWNERSHIP One sub owner, one body owner
15. MASKING           Carve 2-3 dB out of the instruments in the vocal's band
16. SIDECHAIN         Bass <- kick, reverb <- vocal
17. SPACE             Sends, pre-delay, Haas offsets
18. MONO CHECK        Every offset pair, correlation above +0.4
19. MASTER            One master stage, one loudness target, then export
```

> **The single most damaging thing you can do** is master before restoring. A limiter lifts
> the shimmer, the hiss and the smeared reverb along with the music — the artefacts get
> louder and more obvious, not quieter.

### Which tool for which restoration step

| Step | Tool | Setting |
|---|---|---|
| De-clip | RX De-clip | Moderate. If the source was hard-limited, aggressive de-clipping produces audible warble |
| Mouth De-click | RX Mouth De-click | Target `s`, `t`, `p`, `b` — the consonants AI renders worst |
| De-click / De-crackle | RX De-Click, De-Crackle | Run after Mouth De-click, not instead of it |
| De-hiss | RX Spectral De-noise (learned) | Learn from the quietest passage. Low reduction only — 2–4 dB |
| De-reverb | RX Dialogue De-reverb, or Waves Clarity Vx Pro | **Vocal stem only.** Blend ~70 % dry |
| AI shimmer / fizz | RX Spectral Repair (manual), IntRect de-artifact, Neural Analog | IntRect: strength 50–70 %. Manual spectral repair for isolated events |
| Sibilance | Waves DeEsser or RX De-ess | 6 kHz, 2–4 dB. **Never on the whole mix if you can avoid it** — on the vocal track |
| Transient loss | Kilohearts Disperser | Set the transient amount to taste. Better than raising the level |
| Muffle | Ozone Equalizer or Waves Linear Phase EQ | 2–4 dB wide-Q cuts at 200–500 Hz on secondaries |

**RX module order** (iZotope's own recommendation): Mouth De-click → De-plosive →
De-Click/De-Crackle → De-clip → Deconstruct → Spectral Repair → Breath Control → Dialogue
De-reverb → Spectral De-noise.

**Critical rule: run the de-artefact pass on the MASTER as the last step before mastering**,
not on the stems only. Stem-level cleaning misses the artefacts that are created when the
stems are summed back together.

---

## 4. What mastering can and cannot fix

### Cannot fix, ever

- **Codec artefacts** — already in the samples.
- **A buried vocal** — mastering processes the sum; it cannot pull a vocal out from under the
  drums. You need stems.
- **Wrong pronunciation, a changing voice, a song that cuts off** — regenerate or trim.
- **A boring arrangement or a weak hook** — costs credits, not plug-ins.
- **A badly balanced arrangement** — mastering is the final gloss on an already solid product,
  not a rescue.

### Fixes completely

- **Loudness.** The single biggest win. Suno sits below target; mastering closes the gap.
- **Overall tonal balance.**
- **Stereo width.**
- **Smooth, controlled high end** (within the limits of the source).

### Fixes partially

- **Low-mid mud** — mastering can tilt the overall balance and tame a broad buildup, but
  masking *between individual sources* lives in the mix.
- **Hiss and harsh highs** — a dedicated AI-artefact cleanup switch smooths these; set the
  strength only as high as needed, because too much removes highs that belong to the song.

---

## 5. Generation-side fixes

Stop the problem at the source and the mix gets easier.

### Prompt hygiene

| Do | Don't |
|---|---|
| Add explicit quality cues: *"clean mix, professional mastering, no distortion, hi-fi production"* | Stack energy words: *"massive, wall of sound, extreme, crushing"* → use *"powerful but clear, loud and punchy"* |
| Limit the prompt to **3–4 main instruments** | List a dozen instruments and hope |
| Keep tag stacks to **2–5 tags** per section | Stack 8 contradictory tags — Suno starts ignoring the later ones |
| Put the genre in **section tags**, not only in the style field | Rely on the style field alone; genre drift is the #1 complaint |
| Write lyrics the way a singer phrases them | Write lyrics that look "formal" — more robotic delivery |
| Use `[End]` / `[Outro]` to stop hallucinated noise at the tail | Assume the generation will end cleanly |
| Keep syllable counts similar across verses | Pack a chorus with twice the syllables of a verse — slurred vocals |

### Segmentation

- **Generate in 1.5–2 minute segments**, review, then extend. Treat every extension as a
  fresh quality check.
- If the second half degrades, **extend from the cleanest earlier point** instead of the end.
- Don't try intro-through-outro in one long generation — structural drift and audio
  degradation both increase with length.
- **Don't stack too many sections in one clip.** Build incrementally.

### Iteration economics

- Generate **3–4 variations** per idea and keep the cleanest. Artefacts are inconsistent —
  the same prompt twice gives one clean result and one broken one.
- **Simple prompts produce less noise** than prompts stuffed with descriptors.
- If the prompt is right but the output is broken, regenerate before you spend an evening
  repairing it. Credits are cheaper than time.

### Model controls

- **Weirdness / Chaos slider:** keep low for pop; 60–75 % unlocks genuinely unusual textures
  for metal and experimental work.
- **Voice influence:** 40–60 % is a practical ceiling above which artefacts increase.
- **Personas / Voices:** use them for consistency across a project, especially over long
  generations.
- **Remaster** can regain frequencies lost during separation — worth trying on a stem-based
  workflow.

---

## 6. The r/suno FAQ and resource list

### The wiki

**[r/SunoAI Wiki — Guide to AI Music Creation](https://www.reddit.com/r/SunoAI/wiki/index/)**

The canonical starting point. Contains the glossary, community guidelines, prompt tips, and
the post-Suno workflow section. Reddit blocks direct fetching from some hosts — if the wiki
link will not load, search for the sheet names below; they are the actual content.

### Post-Suno workflow (the wiki's own links)

- **Stem separators** — Suno's built-in separator is the first choice; the wiki's comparison
  of the alternatives is the second.
- **Brief Overview of a Workflow** — r/SunoWrestlers.
- **AI music source separators** — Google Sheets comparing every separator.
- **Tips & Tricks** — Google Sheets.
- **Listening test | Multisong dataset** — Google Sheets, with SDR scores per genre.
- **SDR per genre** — Google Sheets.
- **Frequency cutoff + Time elapsed per model (GPU vs CPU)** — Google Sheets. Tells you
  exactly where each model stops being trustworthy.
- **Model analysis** — Google Sheets.
- **"Instrumental, vocal & other stem separation & mix/master guide — UVR/MDX/Demucs/GSEP &
  others"** — the "insane master guide to all stem separators" by *deton24*. The single most
  useful document for stem work.

### Separators

| Tool | Notes |
|---|---|
| **Suno's built-in 12-stem** | Pro/Premier, 50 credits. Purpose-built for this model |
| **Ultimate Vocal Remover (UVR)** | Free, powerful, runs locally. Try `BS-Roformer`, `MelBand-Roformer`, `Demucs v4 HT` |
| **MVSEP.com** | Free, browser-based |
| **LALAL.AI** | Paid, high quality |
| **Gaudio Studio** (`studio.gaudiolab.io`) | Browser-based |
| **Moises.ai** | Commercial |
| **RipX DAW** | The best tool for "baking" AI audio — removes artefacts directly in the spectral view |

### Analysis and reference

- **Cyanite.ai** — analyse a track for genre/mood/structure.
- **Sonoteller.ai** — audio analysis.
- **Rate Your Music** — find a genuine reference track in your genre.
- **Basic Pitch** (Spotify) — free audio-to-MIDI, for re-sounding AI melodies.

### Cleanup tools named in the community

| Tool | What it does |
|---|---|
| **iZotope RX** | The gold standard for clicks, hiss, de-reverb, spectral repair. You own it |
| **Waves Clarity Vx Pro / DeReverb** | Real-time AI denoise and de-reverb, no learning pass. You own it |
| **Supertone Clear** (formerly Goyo) | Very clean noise and reverb separation |
| **Adobe Podcast Enhance** | Surprisingly good on AI vocals — free. "Remove vocal artefacts" |
| **SpectraLayers Pro** | Good for moderate artefacts in separated stems |
| **Neural Analog** | Spectral restoration, HF rebuild to 20 kHz, can target a single stem |
| **IntRect de-artifact** | Purpose-built for metallic shimmer; strength 50–70 % |
| **Auphonic / emastered.com / roex automix** | Automated mastering and loudness normalisation |
| **BandLab Mastering / LANDR** | Free and paid mastering services |
| **TrackGleam / MixMasterAI / Sunofix** | Suno-specific mastering and artefact tools |

### Community threads worth reading

- *"Suno AI — Stems and Mastering Guide"* (r/SunoAI) — the community's practical baseline:
  split the stems, EQ and compress each one, use **Kilohearts Compactor** to sidechain
  vocals/instruments so the vocal stands out, **Soothe2** (or a free equivalent) to tame
  harshness on the master, and **Pro MB** (Waves) for the master bus.
- *"Common Issues in SunoAI v4 — How to Fix Them"* — the classic symptom list, including the
  community consensus that **converting to mono and back** fixes many of Suno's
  extreme-panning problems.
- *"What tools do you use to remove Artifacts from Suno Songs?"* — the spectral-repair
  workflow in Audition, and the honest consensus that artefacts can often only be *masked*,
  not removed.
- *"How do you deal with unwanted static or noise in your tracks?"* — including the
  end-of-track-context exhaustion workaround (generate extra seconds, trim in the DAW).
- *"The only viable way to successfully 'master' Suno tracks"* — replacing sections in
  3-second increments.
- *"How the heck are people mastering their tracks?"* — the widest range of real workflows,
  including the low-end advice that recurs in every good answer: **HPF around 30–40 Hz, pull
  1–2 dB around 100 Hz, and use one master stage only.**
- *"Personal guide to enhance SunoAI stems splitting"* — MVSEP with Demucs4 HT, then RX 11
  De-Click / De-Crackle / De-Reverb, then **re-upload the cleaned stem** — Suno's re-render
  of a cleaned stem can be cleaner than the original.
- *"Things you need to know to master Suno hip-hop/R&B"* — "Remaster" can regain
  frequencies lost in separation.

---

## 7. A note on Suno's stems

Two facts that change how you work:

1. **Suno's stems are regenerated, not carved.** By Suno's own description it "listens to it
   and regenerates each stem from scratch". A stem is a *fresh rendering* of that element, not
   a slice of the mix you heard. That is why it can sound different, why it bleeds, and why
   you must audition it.
2. **Separation introduces artefacts.** Community consensus is that consonants (d, t, p, g, k)
   often come out harsher at word ends, and that separation can itself introduce artefacts
   that were not in the original.

**Therefore:** pull stems, but always keep the original master in the project as a reference,
and never destroy it. `Tracks/kaddaok_Import UVR separations into comping lane
structure.lua` (this repository) creates a muted `Original` track for exactly this reason.

---

## 8. Importing Suno output into the Rule of 30 session

### Option A — the ZIP importer (fastest)

`Various/SGM_Suno ZIP Importer.lua` (in the `sgm-reascripts` repo, branch
`add/sgm-suno-zip-importer`) takes a Suno export ZIP and gives you:

- an ImGui window, one row per stem
- WAV and MP3 handling, with the extension detected per file
- colour-coded folder tracks by category
- structural markers from lyric timestamps
- lyrics written into the Project Notes

Requirements: REAPER 6.70+, ReaImGui, SWS/S&M, and 7-Zip or `unzip` on the PATH.
Config: `IMPORT_LYRICS`, `CREATE_FOLDER_TRACK`.

### Option B — the Suno workflow suite

`sgm-suno-workflow` is a six-script pipeline:

| Script | What it does |
|---|---|
| `Suno_01_MetadataOrganizer.lua` | Strips 8–32-character hex IDs from filenames, applies a 12-colour cycle, sets region markers |
| `Suno_02_TempoMapper.lua` | RMS-onset BPM detection, sets the project tempo, adds a guard marker, allows a manual override |
| `Suno_03_DynamicSplitter.lua` | Splits tracks right-to-left into `Sec 01…` segments |
| `Suno_04_LoudnessMaster.lua` | Normalises to −18 dBFS RMS (`DEFAULT_TARGET`), peak ceiling −0.3 dBFS — **sample peak, not LUFS** |
| `Suno_05_StemAligner.lua` | Groups stems by base song name, `DRY_RUN = true` by default |
| `SGM_Suno ZIP Importer.lua` | See above |

Note that `Suno_04_LoudnessMaster` works in **dBFS RMS**, not LUFS. If you want a real
LUFS number, use `FX/amagalma_Calculate difference in LUFS for selected or all FX of focused
FX chain.lua` (this repository) or Ozone's loudness meter.

### Option C — into the Rule of 30 session directly

The Rule of 30 session expects **mic-split sources**: `Kick In (Click)`, `Kick Out (Body)`,
`Kick Sub (40-60Hz)`, `Snare Top`, `Snare Bottom`, `OH Left`, `OH Right`, and so on. Suno
stems are `vocals`, `drums`, `bass`, `guitar`, `other`, `instrumental`.

**Map them like this:**

| Suno stem | Rule of 30 destination | Notes |
|---|---|---|
| `vocals` | `Lead Vocal` | The primary. Everything else in the vocal folder supports it |
| `vocals` (2nd) | `Double Vocal` | A second vocal stem, if you have one. Set it to −6 dB and detune it |
| `drums` | `Snare Top` **and** `Kick Out (Body)` — on two tracks | **You cannot split one stereo drum stem into a click mic, a body mic and a sub mic with EQ.** You have two honest options: (a) copy the drum stem to `Kick Out` and `Snare Top` and use a high-pass on the snare copy to approximate the split, or (b) **replace the drums entirely** with a real kit or a MIDI re-sound (see TECH hack 24) |
| `drums` | `Room Mono (Crushed)` | Copy the drum stem a third time, crush it, and send it to `Parallel Drum Crush` |
| `bass` | `Bass DI (Lows <200Hz)` | The preset LPFs at 200 Hz for you |
| `bass` | `Bass Synth Layer (Sub)` | Only if the bass stem has a sub layer worth isolating |
| `guitar` | `Rhythm L1 (100% L)` and `Rhythm R1 (100% R)` | One stereo stem split across the pair. Use the phase alignment tool to check them |
| `other` / `instrumental` | `The Scrape (FX)`, `Dark Pad (Minor)`, `Clean Ambient (Wet)` | By ear — these are the catch-all texture slots |

**The honest limit:** the Rule of 30 session is designed for **real multi-mic captures**, and
a 12-stem AI separation is a *different* source. The structure still works — the folders,
levels, pans, buses, sends and mastering chain are all still correct — but the *mic-split*
assumptions (click vs body vs sub, top vs bottom snare) cannot be reproduced from a single
stereo stem by EQ. If the drums matter, re-sound them.

**A workable hybrid, which is what this system is really for:**

1. Use the Rule of 30 session as the **arrangement and routing skeleton** — every folder,
   bus, send, level and pan is already right.
2. Put the AI stems in on the obvious tracks.
3. **Replace the drums** with MIDI or a real kit if the drums are the weak point. This is
   hack 24 in the technique library and it is the single highest-impact upgrade available.
4. Replace or re-sound the vocal if the delivery is the weak point.
5. Use the Rule of 30's buses, sidechains, offsets and mastering chain on the result.

That way the AI contributes what it is good at — ideas, song structure, timbre, inspiration —
and the session contributes everything it is not good at.

---

## 9. Layering: two separators, two renderings

The cheapest way to make an AI track sound less like one AI track is to **layer two
independent renderings of the same section**, or two independent *separations* of the same
mix.

### Two renderings of the same section

1. Generate the section twice with the **same lyrics** but **different style prompts**.
2. Keep the vocal from one and the instrumental from the other if the vocal is the weak
   part.
3. Crossfade between them over 4–8 bars, or layer the instrumental of one under the vocal of
   the other.

This works because the two generations are genuinely different recordings of the same words,
which is exactly what a real double is. It also sidesteps Suno's worst habit — genre drift
— because you can keep the take that stayed on-genre.

### Two separations of the same mix

Suno's own 12-stem output and a second separator (UVR with `Demucs v4 HT`, MVSEP, LALAL) do
not produce the same stems from the same file. Where they agree, the material is solid.
Where they differ, the difference *is* the artefact.

1. Separate the same master twice.
2. Compare the two results on the same track — the differences are where the codec artefacts
   live.
3. Use the cleaner of the two for the mix, and the difference to find what to repair with
   RX Spectral Repair.

The community's practical baseline for the first route is to split the stems, EQ and
compress each one, use **Kilohearts Compactor** to sidechain the instruments from the vocal
so the vocal stands out, tame harshness with **Soothe2** (or a free equivalent) on the
master, and use **Waves Pro MB** for the master bus.

See [PROMPT-REFERENCE.md](PROMPT-REFERENCE.md) for the prompt work that makes two
renderings of the same section produce two *useful* results.
