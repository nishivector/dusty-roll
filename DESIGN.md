# Dusty Roll — Design Document

---

## The One Thing

**Dusty cannot stop.** Not because he's brave, not because the level demands it — because tumbleweeds physically cannot. He's been rolling for three days and he is completely fine with that. The emotional core is cheerful helplessness meeting split-second survival instinct. The tension comes from the player caring more about Dusty's safety than Dusty does.

---

## Identity

**Game name:** Dusty Roll
**Tagline:** *Three days rolling and not even tired.*

**Protagonist:**
- Species: Tumbleweed (Salsola tragus — the common Russian thistle, for the record)
- Name: Dusty
- Personality: Unbothered. Cheerful. Has been rolling since Tuesday and considers this a pleasant afternoon. Dusty does not perceive danger. Dusty perceives *adventure*.

**Backstory:**
A freak windstorm east of Amarillo caught Dusty mid-tumble on Tuesday morning and sent him careening down US-287. The wind stopped. Dusty did not. He has now crossed three county lines, two state troopers, and one confused armadillo family. He is having an excellent time.

**World:**
The Llano Estacado — the vast, flat, bleached-by-god Texas panhandle, where the sky is bigger than anything and the highway is a long hot seam cut through the scrub. You can smell the creosote and old asphalt. The telephone poles repeat forever. Nothing changes for miles, and then a mailbox explodes past you at 60 mph and you realize nothing ever really stays the same either.

**Emotional experience:** Gleeful dread

**Reference games:**
- *Crossy Road* — DNA: the comedic timing of death, the cheerful protagonist who doesn't deserve what's about to happen, the one-button elegance
- *Thumper* — DNA: the feel of speed building until perception blurs, the satisfaction of nailing a pattern by muscle memory, the way the world starts to feel alive
- *Alto's Odyssey* — DNA: the zen interrupted by chaos, the scroll-forever backbone, the protagonist as a mood rather than just a character

---

## Visual Spec

**Start screen background:** `#C87A2E` (burned desert ochre — warm, golden, like sunbaked clay)

**Level backgrounds by level:**
1. Dusty Backroad: `#D4860A` (hot noon ochre)
2. Busy Highway: `#BF7A1A` (slightly cooler, more asphalt-dust beige underlay)
3. Tornado Alley: `#7A6040` (overcast khaki, sky dimmed by wall cloud)
4. Route 66 at Night: `#3A2E1E` (warm dark brown — NOT black — lit by neon wash. R:58, G:46, B:30 — all ≥ 30 ✓)
5. The Interstate: `#5C4A30` (diesel-haze dusk brown)

**Primary color:** `#D4860A` (desert gold)
**Secondary color:** `#8B4513` (saddle brown — used for obstacles, road elements)
**Accent color:** `#F5C842` (bright straw yellow — Dusty's highlight and UI elements)
**Road color:** `#555550` (faded blacktop gray)
**Road line color:** `#E8D870` (faded highway yellow)
**Danger flash color:** `#FF4422` (squish red)

**Bloom:** Yes
- Strength: 0.4
- Threshold: 0.75
- Applied to: Dusty's highlight color only, the neon signage in Level 4, and the danger flash

**Camera:** Fixed side-scrolling orthographic. Camera does NOT move vertically — it stays locked to ground level. The world scrolls left. Dusty occupies 25% from left edge of screen, horizontally fixed. Vertically, Dusty sits on the road line, and jumps upward within a fixed vertical range of 120px (at 1080p reference resolution).

**Player silhouette:** Spiky bramble sphere perpetually rolling rightward
- Dusty is a rough circle, 48×48px at base scale, drawn as a dense tangle of curved strokes radiating from center — like a hand-drawn ball of sticks. He spins clockwise as he rolls. His internal structure rotates at exactly 1 full rotation per 0.5 seconds at base speed (synced to rolling feel). He has two tiny dark spots that read as eyes — positioned at 45°, rotating with the body, so they spin. When tucked, the outer spiky fringe compresses inward: Dusty becomes a tighter, denser ball, 36×36px, visibly squashed. When hopping, the fringe fans out slightly — he "opens up" mid-air by 10% in radius.

---

## Sound Spec

### Music Identity

**Genre/vibe:** Comedic Western — think the love child of Ennio Morricone's lighter cues and Chet Atkins playing on a roadtrip with no destination.

**Character:** The music sounds like someone who has been on the road so long they've achieved inner peace — upbeat, rolling, slightly absurd, with a wink in every bar.

**The Hook:** A four-note descending slide guitar phrase that drops in pitch like a ball rolling downhill: E4 → D4 → C4 → B3, each note slid into from a half-step above, landing on beat 1 of every 2 bars. The phrase lifts back up (B3 → E4 quick ascending flick) at the top of bar 3. Players will unconsciously hum "nyeooow nyeooow nyeooow nyeooow — *zip!*" within 60 seconds. This phrase is Dusty's leitmotif: it plays whenever things are going well, speeds up in danger, fragments on death.

**BPM:** 132
**Bar length:** 4/4
**Loop length:** 8 bars (primary loop), 4-bar bridge variations for state changes

---

### Arrangement

**Layer 1 — Slide Guitar Lead (Dusty's Voice)**
- Synth: `Tone.Synth` with `oscillator: { type: "sawtooth" }`, `portamento: 0.08` (the portamento IS the slide), envelope `attack: 0.01, decay: 0.1, sustain: 0.7, release: 0.3`
- Add: `Tone.Vibrato` (depth: 0.3, frequency: 5Hz) on sustained notes only
- Role: Carries The Hook. The melody. This is Dusty's personality in audio form.
- Enters: Bar 1, immediately on gameplay start
- Exits: Fades to 20% volume during Danger state. Cuts entirely on death.

**Layer 2 — Acoustic Rhythm Guitar (The Road Pulse)**
- Synth: `Tone.PluckSynth` with `attackNoise: 2.0, dampening: 3000, resonance: 0.95`
- Pattern: Strummed pattern on beats 1 and 3, with a ghost 16th-note brush on the "and" of 2. Gives the rolling rhythm.
- Role: The heartbeat of the loop. Keeps time without sounding mechanical.
- Enters: Bar 1, plays throughout
- Exits: On death, stops instantly (no fade, like the record scratching)

**Layer 3 — Bass (The Rolling Foundation)**
- Synth: `Tone.Synth` with `oscillator: { type: "sine" }`, envelope `attack: 0.05, decay: 0.2, sustain: 0.8, release: 0.4`
- Pattern: Walking bass line — root note on beat 1, fifth on beat 3, approach note on beat 4. Creates physical sense of forward motion. In E: E2, B2, D2 | E2, B2, D2...
- Role: The gravity. The reason the music feels like rolling downhill.
- Enters: Bar 1
- Exits: Sustains (at half volume) even during Danger — the momentum never fully stops

**Layer 4 — Kick/Snare Percussion**
- Synth: Kick = `Tone.MembraneSynth` (`pitchDecay: 0.08, octaves: 6`); Snare = `Tone.NoiseSynth` (`noise: { type: "white" }`, `envelope: { attack: 0.001, decay: 0.15, sustain: 0, release: 0.1 }`)
- Pattern: Kick on 1 and 3; snare on 2 and 4; hi-hat 8th notes via a second `NoiseSynth` with `decay: 0.05`
- Role: The drive. The "clackety-clack" of road gravel under Dusty.
- Enters: Bar 1 — but at 40% volume for first 2 bars (sneaks in), then full
- Exits: On Level 4 (night), snare drops out; kick and hi-hat only (nocturnal feel)

**Layer 5 — Comedy Whistle (The Absurdist Commentary)**
- Synth: `Tone.Synth` with `oscillator: { type: "sine" }`, envelope `attack: 0.02, decay: 0.05, sustain: 0.9, release: 0.2`
- Pattern: Plays only The Hook phrase, one octave up. Enters on bar 3 of every 8-bar loop, plays bars 3–4, drops out.
- Role: The comedic voice. Makes the music feel like it's narrating Dusty's adventure with bemusement.
- Enters: After player survives first 15 seconds (reward for staying alive)
- Exits: During Danger, Danger replaces it with a muted minor inflection

**Layer 6 — Maracas/Shaker (The Desert Texture)**
- Synth: `Tone.NoiseSynth` with `noise: { type: "pink" }`, envelope `attack: 0, decay: 0.02, sustain: 0, release: 0.02`
- Pattern: 16th-note shaker pattern, continuous
- Role: Wind, dust, desert. Fills the space between beats. Without it the track feels like it's indoors.
- Enters: Bar 2
- Exits: Fades out on Level 4 night transition (replaced by silence/ambience)

---

### Dynamic Music — State Changes

| State | Music Change |
|---|---|
| Normal gameplay | All 6 layers active, BPM 132, major key. The Hook plays every 8 bars. Cheerful and rolling. |
| Danger (obstacle approaching fast, < 0.4s to impact) | Slide guitar stutters on its current note (tremolo: `Tone.Tremolo` at 12Hz snaps on). Maracas layer doubles speed to 32nd notes. Whistle stops. Bass drops to root notes only. No key change — tension through texture, not pitch. |
| Near win (last 5% of level) | Layer 1 slide guitar begins playing The Hook phrase continuously (no gaps). Kick drum adds a flam on beat 1 (layered hit 10ms apart for drama). BPM nudges up 4bpm. Feel: "almost there, PUSH." |
| Player dies (squish) | All music cuts instantly to silence (0ms fade). Then, 0.4 seconds later, a single descending slide guitar gliss: E4 → B2, portamento 0.6s — the sonic equivalent of a cartoon death. Then silence. Restart fades music back in over 1.2 seconds. |
| Level complete | Music resolves to a held chord (all layers): PluckSynth strums a full E major chord once, hard. Slide guitar plays a triumphant upward flick (B3 → E5, fast 0.1s portamento). Whistle plays The Hook phrase at 1.5× speed. Everything fades over 2 seconds as the level transition screen appears. |

### Start Screen Music

The Hook plays on loop, but arranged as a solo piece: just the slide guitar (Layer 1) and the bass (Layer 3), no percussion. BPM slowed to 108. Lazy, dusty, like Dusty rolling in slow motion across the title screen. Every 16 bars, the comedy whistle (Layer 5) plays one phrase. The PluckSynth does a single strum every 4 bars, extremely quiet. It feels like waiting for something to start — it should make the player want to tap "Play" just to hear the drums come in.

---

### Sound Effects (10 total)

**1. HOP**
- Trigger: Player taps (pointerdown, no hold)
- Tone.js: `Tone.Synth`, `oscillator: { type: "triangle" }`, envelope `{ attack: 0.005, decay: 0.08, sustain: 0, release: 0.1 }`. Trigger note C5, immediate pitch sweep to E5 over 0.06s via `Tone.FrequencyEnvelope`. Quick, light, airy.
- Why it fits: Dusty is light — he's made of dry sticks. The hop should sound like a dry twig springing, not a heavy thud. The upward pitch sweep reinforces the physical upward motion.

**2. TUCK**
- Trigger: Player holds (pointerdown held > 80ms)
- Tone.js: `Tone.Synth`, `oscillator: { type: "sawtooth" }`, note G2, envelope `{ attack: 0.03, decay: 0.15, sustain: 0.4, release: 0.2 }`. Add `Tone.Filter` (lowpass, cutoff 200Hz, Q: 3) — sounds dense and compressed. Plays for the duration of the hold, max 1.2s.
- Why it fits: Compression is the mechanic. The sound is compressed — muffled, low, dense. You feel Dusty pulling himself tight.

**3. TUCK RELEASE**
- Trigger: Pointerup after a TUCK
- Tone.js: `Tone.Synth`, oscillator `triangle`, quick ascending flick C3 → E4 over 0.1s portamento, envelope `{ attack: 0.005, decay: 0.12, sustain: 0, release: 0.05 }`. Dusty "pops" back to full shape.
- Why it fits: The spring-back. Reinforces that Dusty snaps back to normal, ready to roll. Satisfying.

**4. SQUISH (player death)**
- Trigger: Dusty hits wrong obstacle
- Tone.js: `Tone.NoiseSynth`, `noise: { type: "white" }`, envelope `{ attack: 0, decay: 0.3, sustain: 0, release: 0.1 }`. Simultaneously: `Tone.Synth` descending B3 → F2, portamento 0.25s, envelope `{ attack: 0, decay: 0.25, sustain: 0, release: 0.1 }`. Layered: dry crunch + pitch drop.
- Why it fits: "Squish" is the word. Crunch = the physical sticks snapping. Pitch drop = cartoon deflation. Dusty died. Briefly.

**5. CACTUS CLEARED (low obstacle hop success)**
- Trigger: Dusty successfully clears a cactus/rock/armadillo by hopping over it
- Tone.js: `Tone.Synth`, oscillator `triangle`, rapid two-note figure: G4 then B4, 0.06s apart, envelope each `{ attack: 0.005, decay: 0.06, sustain: 0, release: 0.05 }`. Short, chirpy, double-tap.
- Why it fits: Two notes = confirmation. "Yes, you did it." Rewards correct reading with a tiny musical punctuation.

**6. CAR POWERED THROUGH (tall obstacle tuck success)**
- Trigger: Dusty successfully tucks through a car/sign/mailbox
- Tone.js: `Tone.Synth`, oscillator `sawtooth` with `Tone.Filter` (bandpass, 400Hz), note E3, envelope `{ attack: 0.01, decay: 0.2, sustain: 0, release: 0.1 }`. A low, satisfying "thwump." Like something chunky passing overhead.
- Why it fits: Heavier than the hop confirm. Tall obstacles are physically bigger — their success sound should feel bigger, more physical. Dusty pushed THROUGH something.

**7. ROLLING AMBIENCE (continuous)**
- Trigger: Plays continuously during gameplay, volume tied to speed
- Tone.js: `Tone.NoiseSynth` with `noise: { type: "pink" }`, continuous tone at near-zero envelope (sustain: 1.0), passed through `Tone.Filter` (highpass, 800Hz). Volume: 0 at rest, scales linearly to 0.15 at max speed.
- Why it fits: The gentle crackle of Dusty rolling over asphalt. It's the world's quietest sound effect and the most important. Players won't notice it consciously. They'll notice when it's gone.

**8. TORNADO WHOOSH (Level 3 special)**
- Trigger: When the Level 3 tornado sweep event begins
- Tone.js: `Tone.Synth`, oscillator `sawtooth` + `Tone.Vibrato` (frequency: 8Hz, depth: 50), note sweeps from G2 to C5 over 1.5s via `Tone.FrequencyEnvelope`, volume 0.3. Also `Tone.Reverb` (decay: 3.0, wet: 0.8) applied.
- Why it fits: A tornado approaching. It needs to feel vast and slightly unreal. The vibrato makes it feel atmospheric and unstable, not a clean synth tone.

**9. LEVEL COMPLETE JINGLE**
- Trigger: Level goal reached
- Tone.js: Sequence of 5 notes on `Tone.Synth` (oscillator `triangle`): E4, G4, A4, B4, E5 — each 0.12s duration, 0.05s gap. Then a `Tone.PluckSynth` full E major chord strum (3 notes simultaneous: E3, G#3, B3). Total: ~0.9 seconds.
- Why it fits: Quick, Western, resolved. Feels like a cowboy tip-of-the-hat. Not triumphant — just… satisfied. Because Dusty is satisfied with everything.

**10. ROADRUNNER BEEP (start screen Easter egg)**
- Trigger: When the roadrunner darts across the start screen idle animation
- Tone.js: Two-tone `Tone.Synth` sequence: F4 then G4, each 0.04s, oscillator `triangle`. A tiny "beep beep." Playful.
- Why it fits: The roadrunner is a visual gag. The sound makes it a running gag (literally). Players who notice it will smile.

---

## Mechanic Spec

**Core loop:** Read the incoming obstacle's height in under 0.5 seconds, choose HOP or TUCK, survive.

**Primary input:**
- `pointerdown` (< 80ms duration → registered as tap → HOP): Dusty launches upward. Hop commits immediately — no canceling. Arc height: 120px (1080p ref). Arc duration: 0.55s total (0.22s up, 0.33s down — slightly back-heavy, feels natural). During the hop, Dusty cannot tuck.
- `pointerdown` (≥ 80ms held → TUCK mode): Dusty compresses to 75% of normal height (48px → 36px). Profile reduction makes him immune to tall obstacles. He cannot jump while tucked. Tuck can be held indefinitely (for Level 4 tunnels). Release → Tuck Release animation (0.15s bounce back).
- `pointermove`: Not used for game control. Used only for hover state on UI elements.

**Key timing/physics values:**
- Base scroll speed: 280px/s (1080p ref) at Level 1
- Hop launch velocity: 550px/s upward (gravity at 1400px/s²)
- Hop arc: tops out at 120px above road, total airtime 0.55s
- Tuck height: 36px (normal 48px)
- Obstacle approach warning distance: None. No UI indicator. The screen width IS the warning. Obstacles enter from the right edge.
- Obstacle visible approach time at Level 1: ~1.1 seconds from screen entry to Dusty's x-position
- Input deadzone for tap vs hold: 80ms (below = tap/hop, at or above = tuck)
- Hop invincibility frame: 0 (Dusty can be hit while mid-hop if the timing is wrong — being airborne doesn't protect from tall obstacles, it EXPOSES you to them)
- Death: Dusty held at squish-frame for 0.4s, then respawn animation (0.6s fade-in + spin-up from stopped). Respawn happens at the beginning of the last checkpoint (every 20% of level).

**Obstacle height categories:**
- LOW (hop to clear): height 40–60px. Visual: cacti, rocks, roadkill armadillos, tumbleweed siblings (irony noted)
- TALL (tuck to clear): height 80–140px. Visual: parked cars, road signs, mailboxes, hay bales on Level 3
- WIDE (tuck and hold): width 80–200px. Visual: long cattle trucks (Level 2+), tunnels (Level 4)
- MIXED PAIR: a tall obstacle immediately followed by a low obstacle, requiring tuck then instant hop — introduced at Level 3. Gap between: 0.4 seconds at Level 3 speed.

**Obstacle spawn rules:**
- Minimum gap between any two obstacles: 1.2 seconds at current scroll speed (ensures player always has time to respond)
- Maximum consecutive same-type obstacles: 4 (prevents pattern-guessing replacing skill)
- At Level 5: minimum gap reduced to 0.7 seconds (still survivable; human reaction minimum is ~0.25s with the visual read cue)

**Win/Lose:**
- Win: Reach the end of the level distance goal (see Level Design). Measured in meters "rolled." Progress bar at top of screen.
- Lose: Wrong obstacle call → squish → respawn at last checkpoint. No lives system. Infinite attempts. Score only (no fail state that ends the game).

**Score system:**
- Base score: 1 point per meter rolled
- Hop bonus: +5 points per successful hop (obstacle cleared without being hit)
- Tuck bonus: +8 points per successful tuck-through (tuck-throughs are harder to read; higher reward)
- Combo multiplier: Each consecutive successful obstacle cleared without a squish adds ×0.1 to score multiplier. Cap at ×3.0. Squish resets to ×1.0.
- Speed bonus: At Level 5, all point values ×1.5.
- Score displayed in top-right, compact font. Multiplier shown next to it in accent yellow when above ×1.0.

---

## Level Design

### Level 1 — Dusty Backroad
**What's new:** Introduction to the mechanic. Players learn HOP and TUCK separately before they're mixed.
**Atmosphere:** Noon, flat scrubland, the kind of road nobody drives on. A fence runs the distant background. Telephone poles every 10 seconds.
**Scroll speed:** 280px/s
**Obstacle density:** 1 obstacle per 2.8 seconds average
**Obstacle mix:** 70% LOW (cacti, rocks), 30% TALL (parked pickup trucks, mailboxes). No MIXED PAIRS.
**Goal:** Roll 400 meters
**Duration:** ~85 seconds at full survival
**New mechanic introduced:** Nothing yet — this is pure learning. Second 0–15: only LOW obstacles. Second 15–40: only TALL obstacles introduced. Second 40+: mix begins.

---

### Level 2 — Busy Highway
**What's new:** WIDE obstacles (cattle trucks) requiring sustained tuck. Speed increases. Road gains lane markings and visible dashed center line.
**Atmosphere:** A real two-lane highway. Other cars pass in the opposite lane (background only, no threat). Rest stop sign visible in background at 200m.
**Scroll speed:** 350px/s
**Obstacle density:** 1 obstacle per 2.0 seconds average
**Obstacle mix:** 40% LOW (cacti migrating onto road, armadillos), 40% TALL (cars, road signs, highway patrol cone formations), 20% WIDE (cattle trucks, flatbeds: 180px wide, require 1.2s sustained tuck)
**Goal:** Roll 600 meters
**Duration:** ~100 seconds
**New mechanic introduced:** WIDE tuck. The cattle truck requires holding tuck for a full 1.2 seconds — players must resist the urge to release early. Visual cue: truck has trailer, player can see the end of the trailer before the gap. When truck passes, a brief horn sound (part of the ambience layer, not a dedicated SFX) plays as commentary.

---

### Level 3 — Tornado Alley
**What's new:** MIXED PAIRS (tuck immediately into hop, or hop immediately into tuck). The Tornado Event. Hay bales added to obstacle set.
**Atmosphere:** Sky yellows-green. Wall of clouds visible in the distance background. Wind-blown dust moves across road. Fence posts lean sideways. A weather vane atop a barn is visible at horizon, spinning frantically.
**Scroll speed:** 420px/s
**Obstacle density:** 1 obstacle per 1.5 seconds average, with MIXED PAIR combos appearing every 8–12 seconds
**Obstacle mix:** 30% LOW, 35% TALL, 15% WIDE, 20% MIXED PAIR (e.g.: tall hay bale + immediate low rock; parked car + immediate cactus)
**Goal:** Roll 800 meters
**Tornado Event:** At exactly 600m (75% through the level), a tornado visual sweeps the background left to right over 3 seconds. During this, scroll speed doubles to 840px/s for those 3 seconds. All three obstacle types rush in rapid succession (timing calculated so 3 obstacles appear in 3 seconds; survivable but demanding). After the 3 seconds, speed returns to 420px/s. If player survives, the music resolves triumphantly (see dynamic music: Level complete variant). This is THE MOMENT.
**Duration:** ~115 seconds (longer due to tornado event recovery time factored in)

---

### Level 4 — Route 66 at Night
**What's new:** Neon-lit obstacles. Long tunnel sections (WIDE, 240px — require 1.8s sustained tuck). Dusty's eyes glow faintly (Bloom on the two eye-dot sprites). Visual darkness changes obstacle silhouette reading — players must rely on neon color-coding: LOW obstacles outlined in green neon, TALL in red neon. This is the only level where color carries gameplay information.
**Atmosphere:** Warm dark brown sky `#3A2E1E`. Route 66 shield signs glow neon green. A diner sign pulses pink on the horizon. Headlights of oncoming traffic (background only) sweep past every 15 seconds. Dusty's eyes are the brightest things on screen for a moment when he emerges from a tunnel.
**Scroll speed:** 490px/s
**Obstacle density:** 1 obstacle per 1.2 seconds average
**Obstacle mix:** 25% LOW (green-outlined), 40% TALL (red-outlined), 20% WIDE (tunnel sections), 15% MIXED PAIR
**Goal:** Roll 1000 meters
**Neon color rule:** This must be communicated to the player within the first 5 seconds via a brief text flash: "GREEN = JUMP / RED = DUCK" that appears and fades in 2.5 seconds. This is the only tutorial text in the game.
**Duration:** ~125 seconds

---

### Level 5 — The Interstate
**What's new:** Everything at once, at full speed. Multiple simultaneous lanes in background (visual only) create overwhelming visual noise. Obstacles now include semi-trucks with double trailers (WIDE, 280px). No new mechanic — this is execution under maximum pressure.
**Atmosphere:** Dusk. Highway is wide, featureless, infinite. Sound of wind constant. Other tumbleweeds occasionally blow past in the background (Dusty's cousins — they fly past in 0.8 seconds, parallel to Dusty but in the background layer, visual gag). The progress bar throbs at Level 5 as if impatient.
**Scroll speed:** 560px/s (rising to 640px/s at 80% through the level)
**Obstacle density:** 1 obstacle per 0.8 seconds average at 80% speed. Minimum gap 0.7s.
**Obstacle mix:** 20% LOW, 35% TALL, 25% WIDE, 20% MIXED PAIR
**Goal:** Roll 1200 meters
**Final 100 meters:** Scroll speed locks at 640px/s. Obstacle density: 1 obstacle per 0.7 seconds. The music adds the full Layer 1–5 stack plus a tambourine hit (`Tone.NoiseSynth` with `decay: 0.08`) on every beat. You feel it.
**Win condition:** When Dusty reaches 1200m, a small sign appears: "WELCOME TO NEW MEXICO." Dusty rolls off the right edge of the screen, still rolling. Still cheerful. Still cannot stop. Fade to white.

---

## The Moment

At exactly 600 meters into Tornado Alley, when the sky turns sick yellow and three obstacles rush simultaneously at double speed and the music screams and the tornado tears through the background and somehow — *somehow* — Dusty makes it through, still cheerful, still rolling, and the music resolves like a sigh of relief while Dusty hasn't even flinched.

---

## Emotional Arc

**First 30 seconds:** "Oh, I get it. Tap to jump, hold to duck. This is simple." *[SQUISH]* "Oh. Oh that's how it works. I need to read the HEIGHT not just the presence. Okay. Okay I see." The first death teaches the whole game. It should happen. It's designed to happen.

**After 2 minutes:** You're in Level 3. The mixed pairs are arriving. Your fingers are moving before your brain catches up. You've stopped narrating to yourself — you're just *doing it*. The music and your inputs are synchronized. You feel like you ARE Dusty — rolling forward with cheerful unstoppability, the obstacles just a rhythm you're reading.

**Near the win (Level 5 end):** Your hands are tense. The speed is almost absurd. But you're doing it. When the "WELCOME TO NEW MEXICO" sign appears and Dusty rolls off-screen, the release is physical. The music doesn't crescendo into triumph — it just… ends gently. Dusty found the state line. He'll keep rolling. You just can't watch anymore.

---

## This Game's Identity

**This is the game where you cheer for a tumbleweed who doesn't need your help.**

---

## Start Screen

### Idle Animation (Three.js canvas — full game-world scene)

**Objects present and their behavior:**

1. **Dusty** — centered horizontally, on the road, rolling slowly left to right at 60px/s. He bounces gently: a subtle vertical sine wave, amplitude 8px, period 1.2s. His body spins clockwise continuously. Every 6 seconds, he reaches the right edge, teleports to the left edge, and continues. He never stops.

2. **Cactus (left side)** — a single tall saguaro-style cactus, static, positioned at 15% from left edge. No movement. Deep sage green `#3A6B2A`. Serves as a visual bookmark.

3. **Rusty pickup truck (right side)** — a broken-down pickup at 78% from left, tilted 3° to the right (flat tire implied). Static. Rust-brown `#7A3A1A` with faded turquoise `#2A7A7A` windshield. Also a visual bookmark. A heat shimmer effect (slight vertical blur, 1px oscillation at 2Hz) applied just above the truck hood.

4. **Roadrunner** — appears from the left edge every 8 seconds, darts across the screen at 600px/s (much faster than Dusty), passes behind the cactus, disappears behind the truck and off screen. Body: small, horizontal teardrop shape. Legs: two fast vertical strokes cycling at 20Hz while running. Color: dark gray `#4A4A3A`. On each pass, plays the two-tone Roadrunner Beep SFX.

5. **Tumbleweed sibling** — appears from the right edge every 15 seconds, rolls slowly RIGHT TO LEFT at 40px/s (opposite to Dusty). Same visual style as Dusty but slightly smaller (36px diameter). Dusty ignores it completely.

6. **Background: telephone poles** — 5 telephone poles, equidistant, scroll from right to left at 20px/s (parallax — slower than Dusty). When one exits left, a new one enters right. Height: 70% of screen. Color: weathered brown `#6B4A2A`.

7. **Sky gradient** — static. Top: `#E8A042` (golden afternoon) fading to `#D4860A` at horizon line (70% down). Road occupies bottom 30%: `#555550` with two dashed center lines in `#E8D870`.

8. **Heat shimmer** — a full-width subtle distortion at the horizon line (70% down): sin-wave vertical displacement, amplitude 1.5px, frequency 0.3Hz, wavelength 120px horizontal. Simulated via a canvas scanline displacement effect or Three.js displacement map.

9. **Color cycling** — none on main objects. The sky subtly shifts between `#E8A042` and `#D49030` over a 20-second period (barely perceptible — like real sunset light). The road line dashes pulse very slightly in brightness (0.9–1.0 opacity, 3s period).

---

### SVG Overlay Spec

**Option A — Title (required):**

Text: "DUSTY ROLL"
Font: `'Alfa Slab One', serif` (heavy Western slab serif — load from Google Fonts)
Size: 11vw
Color fill: `#F5C842` (bright straw yellow)
Letter-spacing: 0.08em
Position: centered, 30% from top

SVG filter (glow):
```
id="dusty-glow"
feGaussianBlur: stdDeviation="8", result="blur"
feComposite: in="SourceGraphic", in2="blur", operator="over"
flood-color: #D4860A (desert orange glow)
```
Animation: The title has a gentle horizontal wobble — `translateX` oscillates ±3px over a 1.8s period (`CSS animation: wobble 1.8s ease-in-out infinite`). This simulates Dusty's rolling motion. Subtle but alive.

**Option B — Silhouette (Dusty, beneath the title):**

A tumbleweed silhouette, 80px diameter, centered below the title text at 50% vertical.
Constructed from:
1. Circle, r=40, fill `none`, stroke `#F5C842`, strokeWidth=2, opacity=0.6 (outer boundary)
2. 8 lines from center to perimeter at 45° intervals, length 38px, stroke `#F5C842`, strokeWidth=1.5, opacity=0.5
3. 4 lines at 22.5° offset, length 30px, stroke `#D4860A`, strokeWidth=1, opacity=0.4 (inner layer)
4. Circle at center, r=5, fill `#F5C842`, opacity=0.8
5. Two tiny circles at 45° position, r=2 each, fill `#3A2A14` — the eyes
Animation: The entire silhouette group rotates clockwise via `CSS animation: spin 2s linear infinite`. Glow filter (same `dusty-glow` filter applied to the group, strength 0.5).

**Option C — Decorative Border:**

A thin border made of repeating small SVG cactus glyphs (each 12px tall, a simple `+` with a vertical line and two horizontal arm nubs). Placed along the bottom edge of the SVG, evenly spaced every 28px, full width. Color: `#3A6B2A` (sage green), opacity 0.5. Static. No animation.
Below the cactus row, a single thin line in `#8B4513`, full width, 1px. Like a horizon line at the bottom of the UI element.

---

## Hard Notes for the Programmer

1. **The tap/hold deadzone (80ms) is critical.** Below 80ms: hop. At or above: tuck. If this feels wrong in playtesting, adjust to 100ms max. Do not remove the distinction. The whole game is this distinction.

2. **Dusty's eyes rotate with his body.** They're not cosmetically separate. They spin with the ball. This makes him feel like a real creature, not a character drawn on a spinning disc.

3. **Death must feel instant and slightly comedic.** The squish animation: Dusty goes from circle to flat oval in 2 frames (0.03s) — very fast. He stays as a flat oval with "×" eyes for 0.4s. Then the respawn. The squish is never lingered upon because Dusty himself would not linger upon it.

4. **The roadrunner on the start screen runs at a scale that is clearly smaller than Dusty but not tiny.** Dusty is 48px. The roadrunner is 20px tall, 32px long. It must be recognizable as a bird running, not a smear.

5. **Obstacle color-coding in Level 4 (neon) must be visible at 560px/s approach speed.** Test at full speed. If players can't read the neon color distinction at that speed, increase the obstacle's advance time by spawning it 10% further off-screen (giving 10% more approach time). Do not reduce the speed.

6. **The "WELCOME TO NEW MEXICO" ending sign:** white rectangle, simple highway-style sans-serif font, appears at the right edge at 1200m, scrolls left at normal speed. Dusty rolls past it and off the right edge. This is not animated in any special way — its understatement is the point.

7. **The score multiplier display:** shown as "×1.4" in `#F5C842` next to the score. When it ticks up (each obstacle cleared), it briefly scales to 120% size and back over 0.15s. When a squish resets it to ×1.0, it flashes red `#FF4422` for 0.3s then disappears (×1.0 is not displayed — only multipliers above ×1.0 are shown).

---

*End of Design Document — Dusty Roll*
*Round 19 | dusty-roll*
