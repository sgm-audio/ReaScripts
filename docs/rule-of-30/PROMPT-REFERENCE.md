# Rule of 30 — Prompt Reference

Prompt mechanics, a diagnosis of the example prompt you supplied, and two fully rewritten
versions — one that keeps the techno intent, one that matches the lyrics to their natural
genre.

Companion documents: [MIXING_GUIDE.md](MIXING_GUIDE.md) ·
[TECHNIQUES.md](TECHNIQUES.md) · [SUNO-AI.md](SUNO-AI.md)

---

## 1. Diagnosis of your example prompt

Your prompt and your lyrics describe two different records. That is the single biggest
problem with it.

| | What you asked for | What the lyrics are |
|---|---|---|
| **Genre** | 10-minute progressive house / dark neural techno | Indie-pop / alt-electronic art song about identity and data |
| **Vocals** | "slow vocal", instrumental-leaning, dub techno | A first-person narrative lyric with a chorus hook and a repeated phrase |
| **Energy** | "hypnotic, minimalist, gradually unfolding" | Concussive, literate, character-driven |
| **Structure** | `[Extended Intro] [Atmospheric Build] [Main Progression] [Neural Breakdown] [Climax] [Outro]` | `[Verse 1] [Chorus] [Verse 2] [Chorus]` |

Stacking those together produces a techno track with a wandering indie lyric — or a
genre-drifted hybrid. Pick one.

### Problems inside the style prompt itself

| Line | Problem | Fix |
|---|---|---|
| `10-minute Progressive House Marathon` | Suno will not reliably produce 10 minutes in one generation, and long generations degrade | Ask for the **structure**, then extend in 2-minute segments (see §5) |
| `Deadmau5-style mathematical progression` | Artist names are filtered or ignored, and when honoured they pull a specific timbre you may not want | Describe the *traits*: `stereo-detuned saw lead, sidechain-pumped pads, glossy sidechain pumping, four-on-the-floor` |
| `360-degree spatial audio` | Abstract — Suno has no concept of this | `wide stereo image, slow stereo rotation` |
| `rotating modular oscillators` | Abstract | `slow filter sweep, evolving arpeggio` |
| `Integrated 40Hz binaural beats` | **Will not work.** Binaural beats need a 40 Hz carrier tone and a stereo offset of a few Hz per ear — headphones only. Suno will render it as low-frequency noise or ignore it entirely | Remove. If you want that effect, add it in post: a 40 Hz sine at −45 dB on one side, offset by ~30 Hz on the other |
| `High-resolution audio, zero noise floor` | Asking for an **absence** is unreliable — and, ironically, "zero noise floor" describes the artefact you are trying to avoid | Replace with positive quality cues: `clean mix, professional mastering, hi-fi production, no distortion` |
| `Arrangement: [Extended Intro], ...` | A good idea in the wrong place — section names in the **style field** do not reliably drive the arrangement | Put the section tags **in the lyric field**, as `[Intro]`, `[Verse 1]`, etc. |
| The long tag list | `dub techno`, `métal`, `remix`, `dubstep`, `bass drop` are **four different genres**. This is the #1 cause of genre drift | One genre per generation. Keep the list to 8–12 tags |
| `plug` | This is not a typo — `plug` is a **real Suno style tag** meaning plucked synth. Keep it, but know it is a tag and not a word | Keep |
| `Side-Chained` | Suno's style vocabulary is hyphenated like this | Keep |
| `mitreißend`, `emotionel` | German. Suno handles them but they dilute the English-language weighting | Use `stirring`, `emotional` unless German is the target language |

### Problems inside the lyrics

| Issue | Fix |
|---|---|
| No `[Outro]` / `[End]` marker | **Add one.** Without it Suno frequently ends abruptly with a noise burst as it runs out of context |
| `"JSON"` | Will be read aloud as *"Jay-son"* or *"Jason"*. If you want "J-S-O-N", write it phonetically: `Jay-Son` or `J S O N` |
| Very short lines (2–4 words) | Modern and effective, but they make sections very short and can push the delivery toward spoken-word. Add a longer line to the first verse to anchor the melody |
| No instrumental tag | Add `[Instrumental]` or `[Instrumental Break]` before the second chorus for a drop in vocal density |
| Verse and chorus are structurally identical | Consider `[Verse 2]` with a `[Half-Time]` or `[Stripped]` tag for contrast |

---

## 2. Prompt mechanics cheat sheet

### The three inputs

| Field | Controls |
|---|---|
| **Style** | Genre, timbre, production character, instrument palette, tempo feel |
| **Lyrics** | The words *and* the arrangement — section tags here drive structure |
| **Exclude Styles** | What must not appear |

### Tag rules

- **2–5 tags per section.** Beyond that Suno starts ignoring the later ones.
- **Never stack contradictions.** `[minimal]` and `[full orchestra]` in the same section
  produces mush. Pick one.
- **One tag per line**, each in its own brackets.
- **Reinforce the genre inside section tags**, not just in the style field. This is the
  documented fix for genre drift.

### Section tags

```
[Intro] [Verse] [Verse 2] [Pre-Chorus] [Chorus] [Bridge]
[Break] [Build] [Drop] [Outro] [End]
[Instrumental] [Instrumental Break] [Spoken Word] [Whispered]
[Belted] [Harmony] [Half-Time] [Double-Time] [Tempo: 128 BPM]
```

### Style-tagging conventions

- **Hyphenate compound terms:** `side-chained`, `high-fidelity`, `left-right stereo`.
- **Use commas, not slashes,** between descriptors.
- **Positive quality cues work; negative ones mostly do not.** `clean mix, professional
  mastering` beats `not distorted`.
- **Energy words change the amount of processing.** `massive`, `wall of sound`, `extreme`,
  `crushing` push the model toward overdriven output. Replace with `powerful but clear`,
  `loud and punchy`, `studio-quality`.
- **Name the production, not the song:** `sidechain-pumping analog pluck synths, monolithic
  4/4 kick, deep transparent sub-bass` beats `cool song`.

### Vocal direction

Unspecified vocals are generic. Specify delivery explicitly:
`warm breathy female alto with light vibrato, emotional delivery, belted chorus, whispered
verse`. State the gender up front (`female vocals` / `male vocals`), and ask for what you want
to hear (`layered harmonies`, `stacked chorus vocals`, `vocal harmonies`).

### Generation discipline

- **3–4 variations per idea.** Artefacts are inconsistent; one run in four is usually clean.
- **Simple prompts produce less noise** than prompts stuffed with descriptors.
- **Generate in 1.5–2 minute segments** and extend from the cleanest point.
- **Keep syllable counts similar across verses** — mismatched counts cause rushed or slurred
  delivery.

---

## 3. Rewritten prompt A — the progressive house / neural techno version

Keeps your intent. The lyrics are restructured for a house track: the short lines become a
spoken-ish vocal hook over the progression, with instrumental sections between them.

**Style**

```
Progressive house, dark neural techno, 128 BPM, hypnotic, minimalist, four-on-the-floor,
monolithic 4/4 kick, deep transparent sub bass, side-chained analog pluck synths, stereo-detuned
saw lead, wide evolving pads, granular noise textures, white noise sweeps, slow filter sweep,
high-fidelity transient response, wide stereo image, polished club master, clean mix, no
distortion.
```

**Exclude Styles**

```
acoustic, folk, live drums, vocal acapella, lo-fi, distorted guitars, arena rock
```

**Lyrics**

```
[Intro]
[Instrumental]
[Atmospheric Build]
[Instrumental Break]

[Verse 1]
Woke up
Debugging my dreams
Coffee code
Spilling at the seams
Swipe right
On a shipping delay
Heart read
"update pushed yesterday"

[Chorus]
Human dot JSON
Tell me what I'm made of
Ones
Zeros
Wrapped around a brain fog
Save file
Same file
Every single day on
Am I more than data
(Human dot JSON)

[Instrumental Break]
[Build]

[Verse 2]
Steps tracked
But I still feel lost
Blue glare
Painting every thought
Therapist
In a little green dot
Typed "fine"
Then I backspaced the plot

[Chorus]
Human dot JSON
Tell me what I'm made of
Ones
Zeros
Wrapped around a brain fog
Save file
Same file
Every single day on
Am I more than data
(Human dot JSON)

[Outro]
[Instrumental]
[End]
```

**Note:** for a 10-minute piece, generate this as a **2-minute segment** (Intro → Verse 1 →
Chorus), then use **Extend** to add the second half. Do not attempt the full 10 minutes in one
generation — the audio degrades and the structure drifts.

---

## 4. Rewritten prompt B — the version that matches your lyrics

Your lyric sheet is an art-pop song about a person discovering they might be a data structure.
That is a much better song than the techno one. This version keeps your structure and adds
the production cues that produce it.

**Style**

```
Indie electronic, art pop, alternative R&B, melancholic, intimate, confessional male vocals,
layered vocal harmonies, analog synth pads, felt piano, muted electric guitar, tight electronic
drums, warm sub bass, tape saturation, wide stereo pads, slow build, cinematic, high-fidelity
transient response, professional mastering, clean mix, no distortion.
```

**Exclude Styles**

```
dubstep, metal, hardstyle, EDM drop, aggressive bass, chipmunk
```

**Lyrics**

```
[Intro]
[Soft Synth Pad]
[Instrumental]

[Verse 1]
Woke up
Debugging my dreams
Coffee code
Spilling at the seams
Swipe right
On a shipping delay
Heart read
"update pushed yesterday"

Chrome tabs
Like a second spine
Inbox
Measuring my time
Mom calls
I let it ring twice
Type back
"love you" in device light

[Chorus]
Human dot JSON
Tell me what I'm made of
Ones
Zeros
Wrapped around a brain fog
Save file
Same file
Every single day on
Am I more than data
(Human dot JSON)

Human dot JSON
Scrolling through my own log
Comments in the margin
Asking what I stand on
Heartbeats
Heat maps
Drawn across the day gone
Trying to read my soul from
Human dot JSON

[Verse 2]
Steps tracked
But I still feel lost
Blue glare
Painting every thought
Therapist
In a little green dot
Typed "fine"
Then I backspaced the plot

Group chat
Laughing on mute
Half-truths
In a status I boost
Mirror shows
A tired old friend
Patch notes
Say I'm better again

[Chorus]
Human dot JSON
Tell me what I'm made of
Ones
Zeros
Wrapped around a brain fog
Save file
Same file
Every single day on
Am I more than data
(Human dot JSON)

Human dot JSON
Scrolling through my own log
Comments in the margin
Asking what I stand on
Heartbeats
Heat maps
Drawn across the day gone
Trying to read my soul from
Human dot JSON

[Outro]
[Instrumental]
[End]
```

**If "JSON" is mispronounced**, change that one word only — in the working version, not in the
published lyrics. Try `Jay-Son`, then `Jay Son`, then `J-S-O-N`.

---

## 5. Which prompt to use with the Rule of 30 session

| Prompt | Session fit | What to lean on |
|---|---|---|
| **A — progressive house / neural techno** | The four-on-the-floor kick, the side-chained sub and the wide pads map directly onto `Kick Out (Body)`, `Bass Synth Layer (Sub)` and the `Dark Pad` / `Clean Ambient` slots. The instrumental breaks become `Glitch Snare`, `Digital Artifacts` and `White Noise Risers` | The **sidechain** and the **sub-vs-kick ownership** are the whole mix — MIXING_GUIDE §4 and §5 |
| **B — indie electronic / art pop** | The felt piano becomes `Reverse Piano` or `Clean Ambient`, the muted guitar becomes `Rhythm L2/R2` (the behind pair), the layered harmonies become `Double Vocal` + `Ad-lib 1–3`, and the tight drums become `Snare Top` / `OH L/R` | The **vocal layering** and the **space** are the whole mix — TECHNIQUES §B and §E, MIXING_GUIDE §6 and §7 |

**For a 10-minute piece:** generate in 2-minute segments with prompt A, extend from the
cleanest point, and lay the segments out on the Rule of 30 timeline as separate items —
one item per segment on the same track, so you can re-align the whole thing with the phase
alignment tool afterwards.

---

## 6. Pre-generate checklist

Before you spend credits:

- [ ] Style field: **8–12 tags, one genre**
- [ ] Exclude Styles field: filled in with the genres you do **not** want
- [ ] Lyrics: every section tagged, `[End]` at the bottom
- [ ] Syllable counts similar between verses
- [ ] "JSON" written phonetically if you want it said correctly
- [ ] Vocal delivery specified explicitly (gender, character, dynamics)
- [ ] Tempo stated in the style field **and** in a `[Tempo: 128 BPM]` section tag
- [ ] No artist names — describe traits instead
- [ ] No "absence" instructions — no `no reverb`, no `zero noise floor`
- [ ] Segment plan written down: which 2-minute chunk comes next
- [ ] You have 3–4 variations queued, not 1
