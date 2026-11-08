# Rule of 30 — Technique Library

Reference for the five core techniques — **comping**, **layering**, **frequency masking**,
**sidechaining**, **depth and the millisecond offset** — plus a grab-bag of mixing hacks.

Every example is written against the tracks
`rule-of-30-session-builder.lua` creates. If you are not sure what a track is for, the
[session map](MIXING_GUIDE.md#3-what-each-track-already-has) lists all 49 of them.

---

# A. Comping

## What comping is

Comping is assembling **one** performance out of many. In a DAW it is the discipline of
*choosing*, not stacking: the best phrase from take 1, the best phrase from take 2, and the
best phrase from take 3, made into one continuous line that never betrays the joins.

## Where it lives in this session

The Rule of 30 does not have a "lead + comps" vocal rig. It has something different and more
useful for metal: a **layered vocal system** where each track is a different *version* of the
same performance, and the job is to decide which of them is the performance and which are
the treatments.

| Track | What it is | Is it a comp candidate? |
|---|---|---|
| `Lead Vocal` | The intelligible performance | The one you protect |
| `Double Vocal` | A second take of the same line | **Yes** — comp it against the lead and use the best of each line |
| `Demon Layer (-12st)` | The same line pitched down an octave | Comp it, but to the *same* words as the lead |
| `Telephonic (500-3k BP)` | The same line, band-limited | **No** — it is a treatment, not a performance |
| `Whisper L/R (ASMR)` | A different delivery, two channels | **No** — a texture layer |
| `Scream High L/R` | Screamed line, two channels | **No** |
| `Growl Center` | A growled version | **No** |
| `Ad-lib 1–3` | Throws | **No** |

So the comping decision is narrow and specific: **`Lead Vocal` and `Double Vocal` are two
takes of the same thing. Pick the best line from each, and build one lead.**

## The workflow

1. **Record or generate two performances of the same lyric.** For AI vocals this is free —
   see [SUNO-AI.md](SUNO-AI.md#9-layering-two-separators-two-renderings).
2. **Put them on `Lead Vocal` and `Double Vocal`.** The builder has already set
   `Double Vocal` to −6 dB with a 2.5:1 compressor, so it is subordinate from the start.
3. **Audition them against each other, line by line.** If you are in this repository,
   `Takes Properties/BuyOne_Randomize active take in items (settings inside).lua` cycles
   takes for fast auditioning, and `Takes Properties/BuyOne_Reverse order of all takes in
   items.lua` reverses the lane order.
4. **Choose on four criteria, in this order:**

| Priority | Criterion | What you are listening for |
|---|---|---|
| 1 | **Diction** | Are the consonants clean? Metal and extreme vocal styles often bury them |
| 2 | **Conviction** | Does the line have intent? A flat, "phoned-in" delivery loses to a great word sung badly |
| 3 | **Pitch accuracy** | Is the note right, and is the drift natural? |
| 4 | **Onset timing** | Is the attack in the right place against the grid? |

5. **Cut at word boundaries, not mid-syllable.** Crossfade the join over 5–15 ms —
   `Items Properties/BuyOne_Apply fade-in, fade-out and crossfade to selected items.lua`
   applies your saved values, and `Items Properties/BuyOne_Fade presets.lua` stores named
   fade sets.
6. **Leave it imperfect.** Do not hard-quantise a vocal. Nudge emotional phrases 5–15 ms
   *behind* the beat and rhythmic phrases slightly *early*. Dead-on-grid vocals sound more
   robotic, not less.

## Pitfalls

- **Comping late.** If you comp after you have EQ'd and compressed the lead, you are
  re-processing material you have already processed, and different takes respond differently
  to the same chain. Comp **before** you build the mix.
- **A comp that is too clean.** Real performances have micro-timing. A comp assembled from
  perfectly aligned takes can sound more synthetic than a single take.
- **Comping the treatments.** Do not try to build a "best of" from `Telephonic` and
  `Demon Layer` — they are not performances, they are processes.
- **Splice audibility.** 5 ms is right for a vocal. For a sustained synth note you need
  30–50 ms, or the pitch step is audible.

---

# B. Layering

## The layers available to you in this session

The Rule of 30 is a dense session by design — 49 content tracks. Layering here is not about
adding more; it is about making the layers that already exist sit correctly relative to each
other.

## 1. Doubles

`Lead Vocal` + `Double Vocal` is the classic double. Rules:

- The double sits **3–10 dB below** the lead. The builder sets −6 dB.
- **EQ the layer differently from the lead** so the two occupy different space: high-pass
  `Double Vocal` at 120 Hz (it has no HPF in the preset) and cut 3 kHz by 2 dB, so it sits
  under the lead's presence instead of fighting it.
- A double that is a *different take* is better than a duplicated copy, because the
  micro-timing differences are what make it sound like two people.

## 2. Detune layers (artificial double tracking)

When you only have one rendition, manufacture the difference.

1. Duplicate the vocal.
2. **Detune the copy by ±5–15 cents.** Do not exceed ~2 semitones — format artefacts get
   magnified.
3. Pan the copy hard opposite the lead.
4. Optionally add **11–17 ms** of asymmetric delay with slow modulation (0.1–0.5 Hz,
   10–20 % depth).
5. Lift the side channel with an **M/S EQ at 8–12 kHz** for extra openness.

This is the **mono-safe** alternative to Haas, and for anything that has to survive a phone
speaker it is the correct choice.

## 3. Octave layers

`Demon Layer (-12st)` is exactly this, and it is already in the session.

- A −12 layer under a vocal gives weight without fighting the sub-bass.
- It wants a **high-pass at 150 Hz** — the preset already sets it.
- Keep it at **−8 dB** or lower. It is a texture, not a second lead.
- If you double it an octave *down* instead of up, high-pass it at 200 Hz and you have a
  sub-vocal layer with no pitch information — brutal, and it clears the entire midrange for
  the lead.

## 4. Band-limited layers

`Telephonic (500-3k BP)` is the technique, and the preset leaves the band edges wide so you
can tune them.

- Narrow it until the consonants survive. If the words stop being intelligible, the band is
  too narrow; if the low end leaks in, it is too wide.
- At 500 Hz–3 kHz it will mask the lead vocal's presence band — that is the point, but it
  means it must stay at −10 dB and be panned, not centred and not boosted.
- A telephonic layer wants to be **hard-panned** or narrow. Centred, it competes.

## 5. Stereo-pair layers

`Whisper L/R (ASMR)` and `Scream High L/R` are panned ±100 % and ±60 %.

- They are **pairs**, not two mono tracks. Keep them that way — a whisper that collapses to
  mono loses the entire reason it exists.
- Do not Haas them into the bargain. See §E.
- `Whisper L/R` sit at −12 dB with a 150 Hz HP. The screams have **no HPF** — add one at
  150–200 Hz or they will muddy the lead and fight the bass.

## 6. Instrument layering

- **Layer the wall, not the lead.** The four rhythm tracks are already a four-layer
  arrangement. Do not add a fifth.
- **Octave Lead L/R** is a doubled pair — if they are two separate performances, align them
  with the phase alignment tool.
- **The ambient wash** (`Clean Ambient (Wet)`, `Dark Pad (Minor)`) is the layer that makes
  the wall sound like it is in a room. It is more important than it looks, and it is the
  first thing to lose in a dense mix.

## 7. Arrangement layering — the AI-specific hack

**Generate the same lyric section twice with different style prompts and layer the results.**

For example, generate a chorus with (a) *"minimal, intimate, single acoustic guitar"* and
(b) *"full analog polysynth pad, wide stereo, lush reverb"*. Layer (b) under (a). You get two
genuinely different arrangements of the same words, and you can pick sections from each to
build a song that no single generation produced.

It is also the fastest fix for a genre-drifted generation: keep the vocal from one take and
replace the instrumental with a take that stayed on-genre. See the
[prompt reference](PROMPT-REFERENCE.md) for the two-prompt technique and the segmentation
rules that make it work.

---

# C. Frequency masking

## What it is

Two elements occupy the same frequency range at the same time. The louder one **masks** the
quieter one — not because the quieter one is absent, but because the ear cannot separate
them. The quieter element is still playing; you just cannot hear it.

This is why everything sounds great in solo and the mix is mush.

## How to detect it

1. **Solo, then together.** Solo the quiet element, then play it with the loud one. If it
   seems to *lose level* when the other is added, you have masking.
2. **Band-pass the master.** On the master, sweep a narrow band-pass through the suspect
   range. When a band-passed version sounds like a *fight* — two unrelated things competing —
   you have found the clash.
3. **Spectrum analyser.** In a spectrogram, a masked element looks like a small shape in the
   shadow of a bigger one.
4. **Mono.** Mono-folding collapses the stereo field and exposes masking that stereo width
   was hiding.

## The six solutions, in order of preference

**1. Fix it in the arrangement.** Always try this first. Re-voice a part, mute a doubling
layer, thin a chord, change the rhythm so two things never play together. The cleanest mixes
are mostly clean arrangements.

**2. Move it in the stereo field.** `Rhythm L1/R1` at ±100 % and `L2/R2` at ±80 % is the
builder's built-in answer to "two identical things masking each other".

**3. Subtractive EQ on the *less important* element.** Cut 2–3 dB where they collide. Never
cut the hero.

**4. High-pass.** The cheapest fix for low-end masking, and it never dulls the top. This
session already does most of it — see the HPF table in the mixing guide.

**5. Dynamic EQ.** Cut the clashing band *only when it clashes*. Ozone Dynamic EQ, Waves F6,
or a band in a Waves C6. Use this for the guitars-vs-snare-crack problem, which only exists
in the loud sections.

**6. Sidechain.** Duck the less important element in time with the more important one.
Compactor or Waves C6-SideChain.

## The frequency ownership map for this session

| Band | What lives here | Conflicts with | Fix |
|---|---|---|---|
| **20–40 Hz** | `Kick Sub`, `Bass Synth Layer` | Each other | **One owner.** HP the other at 35 Hz |
| **40–90 Hz** | `Kick Out (Body)`, `Bass DI` | Each other | Sidechain, or give the body to one of them |
| **90–200 Hz** | `Bass DI` upper, `Rhythm L1/R1` lower | The wall vs the bass | Raise the L2/R2 HP to 150 Hz |
| **200–500 Hz** | `Bass Amp`, guitar body, vocal chest | **The mud zone** | 2–4 dB wide-Q cut on secondaries. `Telephonic` is BP'd above this and wants it clear |
| **500 Hz–3 kHz** | `Lead Vocal` presence, `Telephonic`, guitars | **Vocal vs everything** | Carve the guitars. `Telephonic` lives here by design — keep it at −10 dB |
| **3–5 kHz** | `Snare Top` crack, guitar amp presence | **Snare vs guitars** | Dynamic EQ on the guitars, not on the snare |
| **5–9 kHz** | Sibilance, scream brightness | Piercing S | De-esser |
| **8–16 kHz** | Air, cymbals | **AI fizz** | Spectral removal, not a shelf cut |
| **16–20 kHz** | Air, dither region | Codec fizz | Leave alone |

## Special case: stereo (side-channel) masking

Sometimes the two competing elements are in *different* channels of the stereo field. Then a
**mid/side EQ** fixes it without touching the centre:

- Cut 2–4 dB at 200–500 Hz **in the side channel only**.
- The mono lead vocal stays untouched; the stereo image gets cleaner.

Do this on the mix bus or on a stereo return. It is the right tool for `Whisper L/R` and
`Scream High L/R` bleeding into the lead's band from the sides.

---

# D. Sidechaining

## The five forms

| Form | What it does | Use it for |
|---|---|---|
| **Full-range duck** | The whole signal ducks | The classic EDM pump. Audible, deliberate, genre-specific |
| **Band-limited duck** | Only one frequency band ducks | **The professional default.** Keeps the bass's harmonic character intact |
| **Envelope duck** | Envelope points duck on a timeline | Arrangement-level control, no plug-in needed |
| **Reverb/aux duck** | The send ducks instead of the source | Keeping the vocal intelligible in its own space |
| **Master duck** | The master ducks to the kick | **Almost never.** It makes the master pump and is a hallmark of a bad mix |

## Routing in REAPER

1. **Create a send** from the source track to the **target** track.
2. In the target track's plug-in, set the **sidechain input** to that route.
3. `FX/kawa_TrackFXs_Routing_Matrix_DS_KW.lua` (this repository) draws the whole matrix and
   lets you rewire it visually — the fastest way to understand a complex setup.

Keep the sidechain send **pre-fader** if you want the kick to trigger regardless of level,
and **post-fader** if you want the duck to follow the kick's actual dynamics.

**Channels 3/4 are the sidechain channel in REAPER.** That is what
`rule-of-30-session-builder` option 6 sets up: `I_SRCCHAN = 0` (stereo source 1/2) and
`I_DSTCHAN = 2` (channel index 2, which is channels 3/4).

## Plug-in specifics

**Kilohearts Compactor** — the right first choice.
- Sample-level ducking with no lag and no blurring.
- **Attack / Hold / Release** shape the duck; **Threshold and Range** set its depth.
- Three detection modes for accuracy vs smoothness.
- Stereo-wide control, so the duck can apply to the mono centre and leave the sides alone.
- With no sidechain it behaves as a very smart clipper.

**Waves C6** — the power tool.
- Six bands; **internal or external sidechain per band**.
- **Use the `C6-SideChain` component, not `C6`.** They are separate plug-ins; only the
  former has the external input.
- Recipe: bypass the low, low-mid and high bands; set the mid crossover to ~1 kHz and the
  high crossover to ~5 kHz; switch that band from Internal to External; set the external
  source to the vocal. Negative Range = compression, positive = expansion.

**Waves F6** — 16-band dynamic EQ with a sidechain input. Better when you want to duck one
band rather than the whole signal.

**Waves OneKnob Pumper** — instant ducking, no routing. Right for a fast pump, wrong for
anything subtle.

**ReaComp / ReaXcomp** — **no sidechain input.** The builder's console message suggests
"ReaComp with detector input 3+4", which does not work. Use a plug-in that has one.

**`Tracks/marcosteinebach_AutoDuck.lua`** — envelope-based, not a sidechain.
Focus the **music** track; the script requires the **speech** track to sit **directly
beneath** it. For every item on the speech track it creates volume envelope points on the
music track, ducking it by a set number of dB with configurable outer and inner fade times.
It ducks by **item boundary**, not by level.
- **Perfect for:** ducking the whole instrumental under a vocal that plays in fixed blocks.
- **Not suitable for:** following the dynamics of a vocal that breathes mid-line.
- **Also excellent for:** podcast and voiceover ducking.

## Settings by tempo and genre

| Situation | Amount | Attack | Release |
|---|---|---|---|
| `Bass DI` ← kick, low band only | 2–4 dB | 0.1–1 ms | 150–180 ms @100 BPM |
| `Bass Synth` ← kick, full band | 2–4 dB | 0.1–1 ms | 150–180 ms @100 BPM |
| `Rhythm L2/R2` ← `Lead Vocal` | 1–3 dB | 5–15 ms | 200–300 ms |
| `Hall`/`Plate Reverb` ← `Lead Vocal` | 2–4 dB | 10–30 ms | 250–400 ms |

**Release timing in musical terms.** The builder sets 100 BPM. A 16th note at 100 BPM is
150 ms and an 8th is 300 ms. A release around 150 ms returns the bass to full level just
before the next kick — that is what makes it feel tight rather than floppy. At 95 BPM use
~180 ms; at 110 BPM use ~135 ms.

## Pitfalls

- **Ducking the full band.** The bass loses its harmonic definition and the mix goes thin.
  Duck the low band only, unless the genre demands the pump.
- **Release too fast.** The compressor drags up room tone and tails between hits — the
  number-one cause of a mix that was clean and then became muddy.
- **Chasing the kick.** 2–4 dB. If you can clearly hear the pumping without the music, it is
  too much.
- **Sidechaining the master.** A ducked master is a broken master.
- **Ducking the whole guitar bus.** Duck `L2/R2`, not `L1/R1`. The first pair is the wall.

---

# E. Depth and the millisecond offset

## The physics

Sound reaches your two ears by two paths. The ear determines *direction* mostly from **time
differences** between the ears and **level** differences, plus spectral cues. Dr. Helmut
Haas demonstrated that two similar sounds arriving within about **40 ms** are fused by the
brain into one sound with a **spatial location** — you perceive a source that is wide and
slightly behind you, even though it is playing from a speaker in front of you.

That is the whole trick. It is not reverb, it is not a widener, and it costs one duplicated
track and one delay value.

**The window:**
- **0–10 ms** — mild, directional; can sound "out of phase" on sustained material
- **10–30 ms** — the sweet spot. Maximum perceived width and depth
- **30–40 ms** — still fused, noticeably wide
- **>40 ms** — the brain hears a separate reflection. It becomes an echo

## The four depth tools

| Tool | What it controls | Setting |
|---|---|---|
| **Pre-delay on the return** | Whether the source sounds in front of or behind the space | 0–10 ms = forward; 30–60 ms = set back |
| **Level** | Distance | Quieter = farther. The builder sets the hierarchy: Double −6, Demon −8, Telephonic −10, Whispers −12 |
| **High-frequency rolloff** | Air absorption with distance | LPF the "far" copy above 6–8 kHz. The L2/R2 HPF difference (90 vs 110 Hz) is the frequency half of this |
| **Millisecond offset** | Lateral placement and depth | 5–35 ms on one side |

## Applying it in REAPER

**Route 1 — the plug-in (fastest).** Insert **Kilohearts Haas** — a Snapin in Kilohearts
Ultimate with one **delay time** knob and **left/right select** switches. Ten seconds, no
routing, no offset flags.

**Route 2 — track playback offset (most control).**
1. Duplicate the track.
2. Pan the original hard **left**, the copy hard **right**.
3. `Tracks Properties/DP_Set track playback offset.lua` (this repository) sets the offset
   with a GUI — a slider, ±1 ms and ±10 ms steppers, and a numeric field.
   `Tracks Properties/edgemeal_Enable track playback offset for selected tracks
   (Samples).lua` gives sample-accurate behaviour.
4. Bring the delayed copy down 1–3 dB.

**Route 3 — send-based.** Send the source to a stereo delay set to free-running at 15–30 ms,
panned opposite the dry. Works on anything, including a bus.

**Caveat:** track playback offset moves the *entire* track — automation and markers included.
If you only want the audio shifted, nudge the **item** instead.

## Per-source offsets

| Source | Offset | Range |
|---|---|---|
| Percussion | 8–15 ms | 5–20 ms |
| Piano / keys | 10–25 ms | 8–30 ms |
| Rhythm guitar | 15–30 ms | 10–35 ms |
| Pad / ambient | 30–50 ms | 25–50 ms |
| Lead vocal | 0–12 ms, with detune | 0–15 ms |
| Bass / sub | 0 | never widen |
| Lead guitar | 0 | single-tracked leads read better |

## Mono safety — the part everyone forgets

Summing an offset pair to mono makes the two copies interfere and produces **comb
filtering**: peaks and nulls across the spectrum. The source sounds hollow, thin or phasey.
And most of your listeners are on a phone, a Bluetooth speaker, a car stereo or a smart
speaker — all effectively mono.

**Fixes, best first:**
1. **Detune the delayed copy ±5–10 cents.** Decorrelates the two signals enough to kill the
   comb while preserving the width in stereo. The best answer.
2. **Keep a dry centred copy.** Pan the offset pair slightly narrower and leave the dry source
   centred. Depth in stereo, solid centre in mono.
3. **Lower the delayed copy** 1–3 dB.
4. **Shorten the offset** toward 10 ms, which survives mono better than 25 ms.

Always check with the mono button and a correlation meter (keep above **+0.4** in loud
sections).

## The alternative: artificial double tracking

For anything that must survive mono — especially the lead vocal — use ADT:

1. Duplicate the vocal.
2. Detune the copy **±7 cents**.
3. Add **11–17 ms** of asymmetric delay.
4. Pan hard L/R.
5. Lift the side channel **8–12 kHz** with an M/S EQ.
6. Sit the copy **3–8 dB** below the lead.

Wide, mono-safe, and it costs no low end.

---

# F. Mixing hacks — the grab bag

Each of these is one specific, actionable move.

### 1. Cut then saturate

Remove 200–500 Hz and the track goes thin? Do not boost the mids back. **Add saturation
instead** — the upper harmonics restore perceived body without re-introducing the clash. Cut
3 dB, add 10 % tube drive, listen.

### 2. Mute before you EQ

If a layer does not need to be there, EQing it is wasted effort. **Muting an element improves
a mix more than any plug-in.** In a 49-track session this is the most valuable habit.

### 3. Mono the low end

Below 100–120 Hz, stereo bass phase-cancels unpredictably and translates badly. Narrow the
sides below 120 Hz with Waves S1, Ozone Imager, or Kilohearts' Stereo snapin.

### 4. High-pass the reverb returns

Returns must not carry bass. HPF at 200–400 Hz. This one move removes more mud than most EQ
chains.

### 5. Reference-match the *curve*, not the frequencies

When matching a reference track, look at the **overall tonal balance curve** across the whole
spectrum. Chasing individual frequencies to match a mastered track is how you wreck a mix.

### 6. Loudness-match before you compare

A louder track always sounds better. Match the LUFS of your mix and the reference *before*
you judge tone.

### 7. Boost the room, not the source

If the vocal sounds wrong, do not fix the vocal. Fix the **room it sits in**. Less reverb
return, drier vocal, more controlled space.

### 8. Reverse reverb and reversed cymbals

A reversed cymbal swell is the cheapest transition in music. It works in every genre and
takes seconds to make. The session already has `Reverse Piano` — the same trick applies to
any percussive sound.

### 9. High-pass plus saturate equals fat

The "fat EQ" trick: HPF a source at 120–250 Hz and apply saturation. The lost body returns
as harmonics above the cutoff instead of as bass, so it no longer conflicts with the kick.

### 10. Filter sweep into every section change

A filter opening over 4–8 bars across a transition costs one envelope point and adds more
energy than any volume ride.

### 11. Use a transient designer to undo AI compression

AI codecs flatten transients. **Kilohearts Disperser** restores the attack definition that
was lost — and it works better than raising the level.

### 12. Cut the sizzle, not the air

AI fizz lives at 10–16 kHz; real "air" lives at 8–12 kHz. A shelf cut at 12 kHz takes both.
Use **spectral removal** on the artefact instead.

### 13. Print and re-saturate

To give a whole bus the same saturation character, print the bus to a track and saturate the
new track. More control, and it breaks up the phase relationships slightly — which is a
feature.

### 14. Two-stage bass

`Bass DI` / `Bass Amp` / `Bass Fuzz` / `Bass Synth` is already a four-stage bass. If you
need a fifth, make it a *layer* rather than a split: a saturated, high-passed copy of the DI
sitting above the original.

### 15. High-pass the delay feedback path

In a delay plug-in, HPF the feedback loop. Otherwise each repeat carries more and more low
frequency and the mix turns to mud. ReaDelay has a `Lowpass`/`Highpass` on the feedback path.

### 16. Put the reverb throw on the snare

A snare hit that throws into a reverb is instantly a bigger moment. It costs nothing.

### 17. Automate the sidechain depth

Sidechain depth that increases through a build is far more exciting than a fixed amount.

### 18. Use a different mic distance for your double

Record a double 6–12 inches further from the mic. The different room-to-direct ratio adds
*depth* on top of width, so the double feels like it exists in a physical space rather than
being panned.

### 19. Layer real breaths

A breath at −22 dB between phrases is the most humanising five-second edit in music.

### 20. VCA rides, not fader rides

Automating a track's **fader** sounds like a mix decision. Automating a **VCA** underneath it
sounds like a performance.

### 21. Look-ahead compression for vocals

Look-ahead compressors (CLA-76) let the transient through and then catch the body, so the
punch survives while the sustain is even.

### 22. Undo double mastering before you master

If the source was already limited, **Ozone Unlimiter** (Advanced) recovers some dynamics
before you apply your own limiter.

### 23. Soft clip instead of brickwall limiting

Ozone's Maximizer in **Soft Clip** mode, or a tape-style saturation, produces loudness with
less harshness than a hard ceiling.

### 24. Replace the drums entirely

The single biggest "this doesn't sound like AI" upgrade available. Convert the drums to MIDI
and re-sound them with a real drum library — instant credibility. `Items Editing/ak5k_Drums
to MIDI.lua` (this repository) is the starting point.

### 25. Convert a melody to MIDI and re-sound it

If the melody is good but the sound is not, run it through an audio-to-MIDI tool (Basic
Pitch, or Melodyne's polyphonic detection) and re-sound it with a real instrument. Same
melody, completely different credibility.

### 26. Crossfade two generations of the same section

Generate the same section twice and crossfade between them over 4–8 bars. The result is an
arrangement neither generation produced, and the crossfade hides any artefact in either.

### 27. Mute the layer and listen

In a 49-track session, the fastest diagnostic is the mute button. If a layer is not
contributing to the moment you are in, mute it and see if anything is missing.

### 28. Look for the track that is a duplicate

If two layers are near-identical, you have just found free double-tracking material. Check
before you mute the second one.

### 29. Bandpassed noise for air

A layer of very quiet, heavily bandpassed noise (8–16 kHz) can add "expensiveness" without
a compressor. Keep it around −40 dB.

### 30. Do the whole mix at one level

Mixing at a consistent monitoring level makes every decision comparable. Changing levels
mid-session makes you recalibrate constantly and is the hidden cause of most inconsistent
mixes.

---

# G. Reference tables

## Delay times that scale with tempo

| Note | 95 BPM | 100 BPM | 110 BPM |
|---|---|---|---|
| 1/16 | 158 ms | 150 ms | 136 ms |
| 1/8 | 316 ms | 300 ms | 273 ms |
| 1/8 dotted | 474 ms | 450 ms | 409 ms |
| 1/4 | 632 ms | 600 ms | 545 ms |
| 1/4 dotted | 947 ms | 900 ms | 818 ms |

## Haas offsets by source

| Source | Offset | Range |
|---|---|---|
| Percussion | 8–15 ms | 5–20 ms |
| Piano / keys | 10–25 ms | 8–30 ms |
| Rhythm guitar | 15–30 ms | 10–35 ms |
| Pad / ambient | 30–50 ms | 25–50 ms |
| Lead vocal | 0–12 ms, with detune | 0–15 ms |
| Bass / sub | 0 | never widen |
| Lead guitar | 0 | single-tracked |

## Sidechain release times

| Tempo | 16th note | 8th note |
|---|---|---|
| 95 BPM | 158 ms | 316 ms |
| 100 BPM | 150 ms | 300 ms |
| 110 BPM | 136 ms | 273 ms |

## Q values

| Q | Width | Use for |
|---|---|---|
| 0.5–1 | Very wide | Shelf-like broad tilt |
| 1–3 | Wide | Gentle mud cuts |
| 4–8 | Medium | Surgical cuts, de-esser bands |
| 10+ | Very narrow | Resonances, split notches, sibilant bands |

## Loudness

| Destination | LUFS | True peak |
|---|---|---|
| Spotify / Apple Music / YouTube | −14 | −1.0 dBTP |
| Beatport / Bandcamp | −9 to −7 | −1.0 dBTP |
| Broadcast (radio) | −16 | −2 dBTP |
| Suno exports (measured median) | ≈ −15.2 | varies |

## Plugin role map

| Job | Reach for |
|---|---|
| External sidechain, band-limited | Waves **C6-SideChain** (not plain C6), Kilohearts Compactor, Waves F6 |
| Glue compression | Waves CLA-76, CLA-2A, CLA-3A, SSL G-Master, Ozone Dynamics |
| Dynamic EQ / unmasking | Waves F6, Ozone Dynamic EQ, Neutron Unmask, Trackspacer |
| Surgical EQ | Waves Linear Phase EQ, Waves Q10, Ozone Equalizer, Kilohearts Slice EQ / Carve EQ |
| De-essing | Waves DeEsser, RX De-ess |
| Reverb / de-reverb | Waves Clarity Vx Pro, RX Dialogue De-reverb, Kilohearts Snap Heap |
| Stereo width | Waves S1, Ozone Imager, Kilohearts Disperser, ADT |
| Transient shaping | Kilohearts Transient Shaper, Waves Transient Shaper, Ozone Exciter |
| AI de-artefact | RX Spectral Repair / De-Click / De-crackle, IntRect, Neural Analog, SpectraLayers |
| Mastering | Ozone 12 (IRC 5 Maximizer, Bass Control, Match EQ), Waves TG Mastering Chain |
| The millisecond offset | Kilohearts **Haas**, `DP_Set track playback offset.lua` |
