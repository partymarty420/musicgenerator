[readme_random_music_studio_v_4.md](https://github.com/user-attachments/files/22478937/readme_random_music_studio_v_4.md)
# Random Music Studio v4 — README

A single‑file Web Audio playground that lets you:

- Program a **drum sequencer** (Kick / Snare / Hat / Perc) with per‑lane sound types, volume, and FX sends.
- Generate and edit a **random melody** that follows a **key & scale** you choose.
- Perform on an **on‑screen keyboard** and overdub notes into the loop.
- Shape the groove with **swing**, **humanize** (micro‑timing & velocity), **delay / reverb / chorus**, and a **master compressor**.
- **Record** your jam to an audio file (WebM).

> Everything lives in one HTML file. Open it in a modern browser (Chrome/Edge/Firefox/Safari). On the first use, click **Start** to unlock audio.

---

## Quick Start
1. **Click Start.** (Mobile browsers require a user gesture to enable audio.)
2. Hit **✨ Randomize All** to get an instant groove.
3. Tweak **BPM**, **Swing**, and **Humanize** to taste.
4. Choose **Root / Scale / Octave**, then **🎲 Randomize Melody**.
5. Turn on **Overdub**, play the on‑screen keys to add notes to the loop.
6. Press **⏺️ Record** to capture a take; a download link appears when you stop.

---

## UI Guide

### Transport & Global
- **Start / Stop** — Controls the scheduler.
- **✨ Randomize All** — Generates a new drum kit pattern and melody using current musical settings.
- **BPM** — Tempo (40–200).
- **Swing** — Delays every other 16th‑note for a lilt.
- **Humanize ms ±** — Adds small random timing offsets (± milliseconds).
- **Humanize vel ±** — Adds small random loudness variation (velocity feel).
- **Master** — Overall output volume.
- **⏺️ Record** — Records the live mix to WebM; click the link to download when stopped.

### Key / Scale
- **Root** — C, C#, …, B.
- **Scale** — Major, Natural Minor, Pentatonic (major/minor), Dorian, Mixolydian.
- **Octave** — Base octave for melodic material.

### Lead Synth
- **Instrument** — Saw, Sine, Square, Triangle, SuperSaw, Pluck, FM, Noise.
- **Vol** — Lead layer loudness.
- **Voicing** — Harmonizes single notes into one of:
  - **Unison** `[0]`
  - **Octaves** `[0, +12]`
  - **Thirds** `[0, +4]`
  - **Fifths** `[0, +7]`
  - **Sixths** `[0, +9]`
  - **Triad** `[0, +4, +7]`
- **Delay / Reverb / Chorus** — Global FX sends for the melodic layer.

### Drum Sequencer (4 lanes)
Each lane has a **Type**, **Vol**, **Delay**, and **Reverb** control plus a 16‑step grid.

- **Kick** — Types: *Acoustic*, *808*, *Clicky*
- **Snare** — Types: *Noise*, *808*, *Clap*
- **Hat** — Types: *Closed*, *Open*, *Shaker*
- **Perc** — Types: *Clap*, *Tom*, *Cowbell*

Buttons below the lanes:
- **🎲 Randomize Drums** — Fills the grids with a groove (see algorithms below).
- **🧹 Clear** — Empties all drum lanes.

### Melody Lane + Keyboard
- **Melody grid** — 16 steps. Click cells to toggle a note at the current key’s root (you can overdub more notes via keyboard).
- **Overdub** — When enabled, playing the keyboard writes notes into the current step (quantized on the fly).
- **Keyboard** — Two octaves; change **Octave** to shift the range. Clicking keys plays immediately and (if Overdub is on) writes to the loop.

**Melody Generator controls**
- **Density** — Probability of notes per step.
- **Range** — How far random notes can roam around the chord center (in scale steps).
- **Gate** — Note length (as a fraction of a beat).
- **🎲 Randomize Melody** — Creates a melodic pattern.
- **🧹 Clear** — Empties melody and overdub notes.
- **🔁 Toggle Repeat** —
  - **On**: the melody loops.
  - **One‑shot**: the melody plays once per cycle then clears (great for evolving variations while you overdub live).

---

## How It Works (Under the Hood)

### Scheduler & Transport
- The sequencer runs at **16th‑note** resolution with a **25 ms lookahead** and schedules audio slightly in the future for stable timing.
- **Swing** offsets every other 16th by a fraction of a beat.
- **Humanize**:
  - **ms ±** adds a small random time shift to each hit.
  - **vel ±** adds a small random gain change to each hit.

### Audio Graph
```
Voices → (busDry) → [Sum] → Compressor → Master → Destination
         ↘ Delay ↘ Reverb ↘ Chorus →
```
- Voices (drums & lead) send to the **dry bus** and optionally to **Delay**, **Reverb**, and **Chorus**.
- **Delay** uses a short feedback line; **Reverb** uses a generated noise impulse; **Chorus** is a modulated delay.
- A gentle **compressor** glues the mix before the **Master** fader.

### Random Drum Generator
Per 16‑step bar:
- **Kick** — Always places the downbeats (steps 1,5,9,13), plus extra ghost hits with a small probability.
- **Snare** — Backbeat on step 9 (i.e., beat 3), with occasional off‑beat ghosts (never on strong downbeats).
- **Hats** — Moderate density for steady motion.
- **Perc** — Sparse accents mostly between strong beats.

> Each lane’s FX mix (Delay/Reverb) is per‑lane, so you can give the snare more reverb than the kick, etc.

### Random Melody Generator
1. **Build scale** from Root + Scale in the chosen **Octave**.
2. Extend across ±1 octave to avoid monotony.
3. For each step:
   - Flip a coin using **Density** (with a small boost on strong beats).
   - Pick a note within **Range** of the local center and clamp it to the scale.
   - If the whole bar ends up empty, force a note at step 1 so the ear has an anchor.
4. **Voicing** duplicates the chosen note at musical intervals (Octaves/Thirds/Fifths/Triad/etc.).
5. **Gate** determines note length in beats.

### Overdub & Repeating Melody
- The melody lane stores one **base note** per step **plus** a list of **extra notes** overdubbed from the keyboard.
- With **Overdub** on, playing a key at any time writes its MIDI pitch into the current step (quantized).
- **Toggle Repeat** set to **One‑shot** clears the melody after each 16‑step cycle—handy for evolving loops while you add new layers live.

### Recording
- Uses the browser’s **MediaRecorder** to capture the master bus to **audio/webm**.
- Click **⏺️ Record** to start; click again to stop and get a **Download** link.

---

## Tips & Customization
- **Make tighter grooves:** lower **Humanize ms** and **vel**; use **Closed Hat** and reduce hat reverb.
- **Bigger space:** increase **Reverb** on snare & perc; add **Chorus** on the lead.
- **Plucky leads:** choose **Pluck** instrument, shorter **Gate**, a little **Delay**.
- **Pads from voicing:** set **Voicing: Triad** or **Sixths**, add **Reverb** + **Chorus**, lower **Lead Vol** slightly.

### Editing the Code
- Add scales: update the `scales` object.
- Add lead instruments: extend the `playLeadFreq()` switch.
- New drum types: add a case in the respective `drumKick / drumSnare / drumHat / drumPerc` functions.
- Change random tendencies: tweak probabilities in `randomDrums()` and `randomMel()`.

---

## Troubleshooting
- **No sound?**
  - Click **Start** (mobile requires a user gesture to unlock audio).
  - Check device volume / mute switch (iOS silent switch will mute Web Audio).
  - Try another browser tab or close heavy pages if timing seems unstable.
- **Recording missing?** Some older browsers lack MediaRecorder for audio; use a current Chrome/Edge/Firefox.

---

## License & Credits
Use freely for learning, jamming, and prototyping. Built with 🧡 on the Web Audio API.

