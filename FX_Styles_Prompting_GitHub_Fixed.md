# FX Styles Prompting in Suno Studio 1.2

## Executive summary

Official release notes and help-center documentation from  refer to the current Studio release as **“Suno Studio 1.2”** (published February 2026). In user communities, “Studio v12” is commonly used as shorthand for **Studio v1.2**; no separate official “Studio version 12” document set was found on Suno-owned domains during this research, so this report treats “v12” as **Studio 1.2**.

Suno’s official documentation does **not** publish a dedicated, formal “FX styles” prompt schema (i.e., no official parameter list like `reverb=0.7` inside the Style field). Instead, the **supported/encouraged “FX prompting” pattern is natural-language describing production and effects** (e.g., reverb, delay/echo, distortion, compression), alongside other musical descriptors. In parallel, Studio 1.2 introduces **Remove FX**, a post-generation tool that can generate “dry” versions of clips by stripping added **reverb and delay**, changing the practical workflow for dealing with “too wet” outputs.

Community practice (public Suno playlists, plus community-authored guides on ) shows repeatable ways to organize FX instructions—most commonly by grouping effect language behind labels such as **`FX:`**, **`Vocal FX:`**, or bracketed blocks like **`[FX: …]`**. These labels are best understood as *formatting conventions* (helpful to humans and often still intelligible to the model) rather than an official parser-backed feature.

The practical takeaway for Studio 1.2 is: **use plain-language FX descriptors to steer generation, use Exclude to suppress unwanted processing, and treat Remove FX + Studio mixing/EQ as your “second-stage” correction tools** when prompts alone can’t reliably hit a precise wet/dry or texture target.

## What FX-styles prompting means in Studio 1.2

In Suno workflows, “FX styles prompting” typically refers to **generation-time conditioning**: writing text in the **Style** (and sometimes **Lyrics/Prompt**) fields that describes production and sonic processing—e.g., *“plate reverb on vocals,” “tape delay throws,” “distorted guitars,” “vinyl crackle,” “gated reverb snare,” “bitcrushed vocal chops.”* Suno’s own glossary explicitly frames production/effects terms (reverb, delay/echo, distortion, compression, EQ, panning, etc.) as valid vocabulary to use when prompting.

Studio 1.2 adds an important distinction: some “FX control” becomes **post-generation** rather than prompt-only. Specifically, **Remove FX** can generate a “clean/dry” version of a clip by removing added reverb and delay effects, which is especially relevant when Suno output is “too wet.” This changes best practice:

- If you want **wet, stylized ambience**: prompt for it (and consider saving alternates).  
- If you want **dry/mixable stems** but Suno prints ambience: generate anyway, then run **Remove FX** (knowing it works best when reverb/delay were added during generation).  
- If you want **precise tonal shaping**: prompt broadly, then use Studio tools like **EQ** rather than relying on the model to “perfect” the mix.

## Verified syntax, fields, and supported formats

### Official inputs you can rely on in Studio 1.2

Studio’s Create Panel is explicitly positioned as letting you “Create a Song as you would in Create,” and Studio supports generating new stems (basslines, percussion, melodies, etc.) into a multitrack timeline. From official documentation, the most reliable “syntax” facts relevant to FX prompting are:

**Style instructions can be detailed and conversational** (not only short tags).  
**Musical production/effects terms are valid prompt vocabulary** (reverb, delay/echo, distortion, compression, etc.).  
**Exclude exists in Advanced Options** for specifying what you do *not* want in the track.  
**Song Editor replacement prompts** accept natural-language transformation requests (example given in official docs: “Make this section sound dreamier”), supporting the idea that descriptive “FX-direction” language is appropriate.

### Community “FX styles” formatting conventions that appear to work

Across public Suno playlists, “FX guidance” is frequently formatted in consistent, copyable conventions, including:

- **Inline tag lists**: comma/· separated descriptors (e.g., “vinyl FX,” “tape hiss,” “VHS delay”).  
- **Labeled blocks**: `FX: …`, `Vocal FX: …`, often after instrumentation.  
- **Bracketed key-value blocks**: `[Effects: …]`, `[Vocal FX: …]`, or multiple bracket blocks in sequence.

These structures are best treated as *human-readable organization* that also tends to remain interpretable to the model, rather than guaranteed machine-validated fields.

### “JSON prompt formats” and numeric parameters

No Suno-owned documentation in scope describes an official “JSON prompt spec” for Studio’s UI inputs. However, third-party API documentation (non-Suno domain) describes a JSON request format with fields like `style`, `prompt`, `negativeTags`, and slider-like weights (`styleWeight`, `weirdnessConstraint`, `audioWeight`) in the range **0.00–1.00**, step 0.01. This is useful if you are integrating through *external tooling*, but it should **not** be mistaken for an officially supported Studio “prompt format.”

### Parameter names and ranges comparison table

The table below separates: (a) **official Studio tool parameters** versus (b) **prompt-field conventions** and (c) **third-party JSON API parameters**.

| Surface | Parameter / field name | Type | Range / options (documented) | Required vs optional (documented) | Notes |
|---|---|---|---|---|---|
| Studio 1.2 | Time signature numerator | UI control | **1 to 99**  | Not framed as “required”; affects Studio grid/metronome  | Official note: time signature currently updates the Studio grid/metronome, **not yet sent to generative models** when creating new clips. |
| Studio 1.2 | Time signature denominator | UI control | Examples given: **4**, **8**  | Same as above | Interpreted as “beat duration.” |
| Studio EQ | Bands | UI control | **6 bands** (1–6)  | Optional (enable per track) | EQ is a Studio mixing tool, not a prompt directive. |
| Studio EQ | Gain | UI control | Typical range **-12 dB to +12 dB**  | Optional | Documented as “typical.” |
| Studio 1.2 | Remove FX | Action | Removes **reverb and delay** (generation-added)  | Optional | Designed to create drier clips for mixing; works best when reverb/delay were added during generation. |
| Create / Studio Create Panel | Styles (Style field) | Text | Officially: supports detailed conversational style instructions  | In Custom workflows, Styles are part of the standard steps  | Community-visible evidence suggests a **1000-character** style limit in common workflows. |
| Create / Advanced Options | Exclude | Text | No official numeric range | Optional | Intended to exclude “instruments, etc” (and likely can be used for FX descriptors as well, though not formally specified). |
| Remaster | Variation strength | UI choice | **Subtle / Normal / High**  | Optional | Remaster targets “mix and audio production details,” including audio effects, sonic character, and polish. |
| Third-party JSON API (non-official) | styleWeight / weirdnessConstraint / audioWeight | Number | **0.00–1.00**, step 0.01  | Required per that API | Not official Studio documentation; useful only if you use that external service. |

## Composition and chaining patterns

### A practical mental model for “FX styles” text

Community reference material suggests treating the Style field as **comma-separated descriptive phrases with a priority order**, often: **Genre → Mood → Lead instrument → Vocal style → Production → Atmosphere**. This aligns with what Suno’s own help-center says about the Style field becoming more capable of handling **longer, more descriptive instructions** (rather than only short tags).

From observed, successful public prompt writeups on Suno playlists, FX details are most often placed:

- after instrumentation, as `FX:` / `Vocal FX:` blocks (clear and scannable),  
- or interwoven into the “mix/mastering” portion (“clean radio-ready master,” “wide stereo imaging,” “warm saturation”).

### Chaining rules that are both copyable and low-risk

Because Studio does not expose an official numeric FX parameter grammar inside prompts, chaining is best done as **ordered language** rather than pseudo-math. Two patterns show up repeatedly:

1) **Target + effect + intensity**:  
“vocals: plate reverb, subtle slapback delay” (targeted), “snare: gated reverb” (localized), “mix: light tape saturation” (global).  

2) **Wet/dry guardrails using Exclude plus post tools**:  
If outputs skew too wet, explicitly request “dry / minimal reverb,” and use Exclude for “reverb wash / excessive delay,” then rely on **Remove FX** when needed.  

### Mermaid workflow diagram for Studio 1.2 FX prompting

```mermaid
flowchart TD
  A[Write Style with FX language: reverb, delay, distortion] --> B[Generate in Studio Create Panel with alternates]
  B --> C{Too wet?}
  C -- No --> D[Keep take and optionally remaster]
  C -- Yes --> E[Use Remove FX to dry the clip]
  E --> F[Mix with faders, pan, and EQ]
  D --> F
  F --> G[Export final audio or multitracks]
```

This workflow is grounded in official features: Studio Create Panel + take/alternate style generation, Remove FX, and Studio mixing/EQ tooling.

## Copy-paste prompt library

All examples below are designed as **Style-field text** you can paste into Studio’s Create Panel or Create (Custom Mode). They intentionally use **plain language FX descriptors** (supported vocabulary per Suno’s glossary) and **community-tested formatting conventions** like `FX:` / `Vocal FX:` blocks.

Each prompt includes:
- a one-line description,
- an “expected effect” (what it is trying to cause),
- a compatibility note/caveat (how to test or recover if it overshoots).

### Ambient

**Ambient prompt A1 — Cathedral shimmer drone (space + motion)**  
Expected effect: very long reverb tails, slow modulation movement, wide stereo ambience.

```text
STYLE: Ambient drone, 60 BPM, slow-evolving synth pads and distant choir-like textures
FX: huge cathedral reverb, subtle shimmer reverb on high pads, slow tape delay swells, gentle tape saturation, soft low-pass filter sweeps
MIX: wide stereo image, soft transients, no harsh highs, breathable dynamics
EXCLUDE: drums, snare, pop vocals, bright EDM drop
```

Notes: If the result is “too wet,” try again with “minimal reverb” *or* generate first then use Studio 1.2 **Remove FX** to pull it drier (best when the wetness is reverb/delay).

**Ambient prompt A2 — Lo-fi tape field ambience (texture-forward)**  
Expected effect: audible tape/vinyl texture, gentle wow/flutter feel, soft room reverb.

```text
Lo-fi ambient, 72 BPM, minimal piano motifs, warm pads, sparse sub bass
FX: vinyl crackle, tape hiss, gentle wow/flutter, short room reverb, faint quarter-note tape delay on melodic fragments
MIX: intimate, warm, rolled-off highs, mono-safe low end
EXCLUDE: bright synth leads, heavy percussion, aggressive distortion
```

Notes: “Vinyl FX / tape hiss” structures are common in public Suno prompts, but results vary by model and arrangement density.

**Ambient prompt A3 — Cinematic reverse swells (film trailer ambience)**  
Expected effect: reverse-reverb-like pulls into downbeats, big atmospheric transitions.

```text
Cinematic ambient, 80 BPM, slow orchestral pads, soft strings, distant brass beds
FX: reverse reverb swells into section changes, long hall reverb, subtle stereo delay throws, low rumble impacts, airy risers
MIX: cinematic wide, clear mids, gentle compression, restrained loudness
EXCLUDE: trap hats, pop chorus structure, upbeat dance groove
```

Notes: If reverses/risers dominate, keep the same prompt but remove “airy risers” and reduce to “subtle reverse swells.”

### Vocal

**Vocal prompt V1 — Intimate dry verse → lush chorus (R&B/neo-soul)**  
Expected effect: close-mic vocal feel with controlled processing; tasteful reverb/delay on harmonies.

```text
Neo-soul / R&B ballad, 72 BPM, warm Rhodes, deep sub bass, minimal drums
VOCALS: smooth, breathy lead, intimate delivery, clear diction, layered harmonies in chorus
Vocal FX: mostly dry lead in verses, light plate reverb on chorus harmonies, subtle slapback delay on key phrases, gentle de-essing
MIX: warm tape saturation, soft compression, no harsh sibilance
EXCLUDE: heavy autotune, huge reverb wash, EDM pumping
```

Notes: “Dry or lightly processed vocals—no autotune, minimal reverb” style constraints appear in successful public prompts; use Studio Remove FX if reverb/delay prints heavier than requested.

**Vocal prompt V2 — Dream-pop haze (ethereal + chorus widening)**  
Expected effect: breathy vocal timbre; wider chorus via doubling, reverb/delay.

```text
Dream pop, 98 BPM, shimmering guitars, soft synth pads, gentle bass
VOCALS: airy female vocal timbre, intimate verses, lifted chorus
FX: lush hall reverb on vocals, rhythmic eighth-note delay on chorus hook, soft chorus effect on guitars, subtle tape saturation
MIX: washed but controlled, wide stereo, soft transients
EXCLUDE: aggressive distortion, dry punk drums, hyper-compressed master
```

Notes: “Reverb-heavy vocals / dreamy delay” phrasing is consistent with how public prompts describe dream-pop/trance vocals.

**Vocal prompt V3 — Radio-distorted spoken hook (character effect)**  
Expected effect: deliberate “telephone/radio” vocal coloration; controlled grit.

```text
Dark electro-pop, 115 BPM, punchy kick, muted bass synth, minimal chords
VOCALS: male spoken-sung delivery in verses, chant-like chorus hook
Vocal FX: lo-fi radio/telephone distortion on spoken parts, short slapback delay, small room reverb, occasional glitch-stutter on the last word of lines
MIX: clean low end, controlled mids, avoid harsh clipping
EXCLUDE: huge hall reverb, glossy stadium vocals
```

Notes: If “distortion” becomes unpleasant, change “radio/telephone distortion” to “band-limited lo-fi tone” and keep delay/reverb subtle.

### Percussion

**Percussion prompt P1 — Dub reggae space (spring reverb + tape delay)**  
Expected effect: spring reverb on skanks, tape delay throws, classic dub dropouts.

```text
Roots reggae dub, 72 BPM, one-drop groove, skanking guitar, bubbling organ
FX: spring reverb on guitar chops, analog tape delay throws on snare hits and vocal ad-libs, dub siren accents, occasional dropouts of bass and drums
MIX: warm vintage, tape hiss texture, deep clean sub
EXCLUDE: fast EDM fills, trap hats, pop vocal stacks
```

Notes: Public prompts for dub/reggae frequently specify spring reverb + tape delay + sirens/dropouts; if too cluttered, remove “dub siren accents.”

**Percussion prompt P2 — Techno percussion FX (gated reverb + sweeps)**  
Expected effect: tight 4/4 with controlled transient design, percussive FX stabs and risers.

```text
Warehouse techno, 132 BPM, punchy kick, rumbling bass, minimal synth stabs
FX: gated reverb snare/clap, short metallic delays on percussion hits, rising tension FX and filter sweeps every 8 bars
MIX: mono-safe low end, crisp transients, restrained highs
EXCLUDE: vocals, melodic pop chorus, bright trance leads
```

Notes: “Rising tension FX” and “gated reverb” appear frequently in techno-oriented public prompts.

### Experimental

**Experimental prompt E1 — Industrial glitch (bitcrush + stutter + alarms)**  
Expected effect: aggressive texture, audible glitch processing, “found sound” FX.

```text
Industrial EBM / glitch, 140 BPM, distorted kicks, metallic snares, dark synth drones
FX: bitcrushed vocal chops, distortion layers, glitch stutters, reverse gasps, alarm/klaxon hits, vinyl slow-down moments, noisy transitions
MIX: harsh but controlled, do not clip, keep sub punchy
EXCLUDE: clean pop vocals, bright cheerful chords
```

Notes: This style of “FX inventory list” is common in highly experimental public prompts; if the model over-prioritizes one gimmick (e.g., alarms), remove it and keep 2–3 signature FX.

**Experimental prompt E2 — Psychedelic funk pedalboard (wah/fuzz/phaser)**  
Expected effect: instrument-level modulation FX, tape stops, dynamic arrangement.

```text
Psychedelic funk instrumental, 115 BPM, heavy melodic bass, tight dry drums
GUITAR FX: wah + fuzz, occasional phaser sweeps, staccato comping
SYNTH FX: tape delay, tremolo modulation, filter sweeps
TRANSITIONS: tape stops, glitchy FX sweeps, sudden dropouts, odd-time breakdowns
MIX: lively, punchy, not over-reverbed
EXCLUDE: vocals, smooth jazz sax solos
```

Notes: Prompts that explicitly list pedal-style FX (wah/fuzz/phaser) and tape stops appear in public “psychedelic / glitch” prompt descriptions.

### Mastering

**Mastering prompt M1 — Clean club master (mono-safe low end)**  
Expected effect: punchy “finished” mix with controlled low end stereo/mono behavior.

```text
Modern club-ready house, 124 BPM, tight kick, clean bassline, minimal vocal chops
MIX / MASTER: clean loud master, mono-safe sub bass, wide stereo above 200 Hz, glue compression on drum bus, subtle tape saturation, crisp transients, no harsh high-end
FX: short room reverb only, minimal delay
EXCLUDE: reverb washout, over-compression pumping, noisy artifacts
```

Notes: Public prompts often call out “mono-compatible / club mix” goals; treat this as a steer, then use Studio faders + EQ to finalize balance.

**Mastering prompt M2 — Vintage mono master (Wall-of-Sound-ish glue)**  
Expected effect: narrow/mono vibe, vintage compression, echo chamber reverb.

```text
1960s retro pop, 120 BPM, girl-group style harmonies, tambourine + handclaps, lush strings
FX: echo chamber reverb, warm saturation, vintage compression
MIX / MASTER: mono mix vibe, smooth mids, rounded highs, no modern sparkle
EXCLUDE: modern EDM build/drop, wide super-stereo synth imaging
```

Notes: Public prompts explicitly specifying “mono mix” + echo chamber reverb and vintage compression appear in Suno playlists and are useful “mastering-direction” language.

## Studio 1.2 workflow changes that affect FX

Studio 1.2 adds four workflow capabilities that materially change how creators should approach “FX styles” prompts:

**Remove FX**: generates a dry version of a clip by stripping added reverb and delay, which is particularly useful for resetting a too-wet vocal/instrument stem before building your own mix chain.  
**Warp Markers with Quantize**: time-stretch and groove-tighten audio clips; this can reduce the urge to “re-prompt” timing fixes, but aggressive warping can introduce artifacts (so it’s better for subtle corrections).  
**Alternates**: updated take-lane behavior for auditioning variations more smoothly, which encourages generating multiple “FX interpretations” of the same part and comping/choosing the best.  
**Time signature support**: Studio sessions can be set to non-4/4 meters; however, the help-center explicitly notes that time signature changes currently adjust the grid/metronome for editing/recording and are **not yet sent to generative models when creating new clips**—a key “gotcha” if you expect prompt-generated material to inherently follow your chosen meter.

These are best treated as “breaking changes” in expectation rather than API-level breaks: if an older workflow relied on regenerating to fix wetness/timing or on the prior take-lane behavior, Studio 1.2 changes how you should iterate.

## Validation checklist and troubleshooting

A short, Studio 1.2–specific checklist for validating FX-styles prompts:

Check prompt placement: keep *global* sound/FX language in **Styles**, and use editing prompts (e.g., “make this section dreamier”) only when you are actively replacing/extending sections in editing workflows.  
Confirm you’re using Exclude correctly: put unwanted elements in **Advanced Options → Exclude** rather than relying on informal “minus-tag” conventions, since Exclude is the officially documented mechanism.  
A/B test wetness: generate two takes/alternates, then decide whether the “wet” version is musically desirable; if not, try **Remove FX** to produce a drier clip before re-prompting.  
Validate time-signature assumptions: if you set 6/8 or 7/8 in Studio, remember it changes the grid/metronome but is **not yet sent to the generative model** for new clips—so you may need to explicitly describe rhythmic feel (e.g., “6/8 waltz feel”) in the Style text.  
Use EQ as “precision finishing,” not as prompt text: when you need consistent tonal moves (reduce muddiness, add presence), apply Studio EQ (6-band; typical gain range -12 dB to +12 dB) instead of iterating prompts endlessly.  
Keep FX lists short when results get chaotic: numerous public prompts demonstrate long “FX inventories,” but field experience suggests collapsing to 2–3 “signature effects” improves controllability (e.g., “tape delay + spring reverb + vinyl crackle,” not 12 effects at once).

## Sources

Primary official sources used for “Studio 1.2” feature behavior and constraints:
- Studio 1.2 release overview and feature descriptions (Remove FX, Alternates, Warp Markers, time signatures).  
- Studio fundamentals and generation-in-timeline workflow (“Create a Song as you would in Create,” generating stems into the timeline).  
- Official vocabulary guidance for production/effects terms in prompts (reverb, delay/echo, distortion, compression, etc.).  
- Exclude feature description (official negative control).  
- Studio EQ parameterization (bands, typical gain range).  
- Remaster behavior for production-polish iteration (Variation strength; “audio effects” / “sonic character and polish”).  

Secondary community sources used specifically to validate common “FX styles” formatting patterns (e.g., `FX:` / `Vocal FX:` blocks and bracketed label conventions):
- Public Suno playlists illustrating `FX:` and `Vocal FX:` structures, plus mix/master phrasing (multiple examples).  
- Community-compiled prompting reference on  (style-field ordering and examples including reverb/delay language).  
- Community tag examples including explicit “Vocal Effect: Delay” patterns (useful for understanding bracketed FX-tag conventions, though not official Studio syntax).  

Non-official API-format reference (useful only if you are using external tooling; not a Studio UI spec):
- Third-party JSON API documentation showing fields and numeric “weights” ranges.