# Excitebike — a single-prompt HTML remake

An NES-style Excitebike remake that lives in a single HTML file: 1,341 lines
of Three.js, every asset generated procedurally from boxes and cylinders.

**One prompt wrote the whole thing.** `PROMPT.md` (reproduced at the bottom
of this page) went into `minimax-m3.1-flash`, and the model answered with the
complete game as one `index.html` — no scaffolding, no shims, no hand-written
code. It then went back over its own work and revised it: the rider
geometry, the camera, the physics, the landing tolerance and the difficulty
curve. It wrote an autopilot test harness that rides the track by itself so
the whole loop could be validated end to end, and it redrew every asset in
the NES style when I asked for it. The published `index.html` is that
reviewed build, copied byte-for-byte from the working file.

| | |
|---|---|
| Model | `minimax-m3.1-flash` — hosted by Ollama as `minimax-m3.1-flash:cloud` (weights are not published for this release) |
| Runner | Ollama for the one-shot run, LiteLLM for the reviewed build published here |
| Output | one self-contained `index.html` (60,477 bytes) |
| Dependencies | Three.js r128 from cdnjs, in a `<script>` tag in `<head>` |
| Assets | none — all geometry is procedural |

## Play it

Open the page and press **Enter** (or click) to start.

| Key | Action |
|---|---|
| `A` | throttle |
| `D` | turbo — fills the overheat meter; at 100 % the bike locks at zero speed for 3 s |
| `W` / `S` | shift one lane inward / outward (4 lanes) |
| `Space` | hop |
| `←` / `→` | pitch the bike while airborne — match the ground angle on landing |
| `R` | restart |

The HUD shows race time, distance, speed, remaining bikes, the overheat
meter and a pitch gauge showing your current angle against the acceptable
landing zone.

## How it works

- **2.5D side view.** The camera follows the bike down a left-to-right track.
- **Procedural track.** Four lanes, generated in 80-unit chunks that recycle
  as you ride: ramps, dirt mounds, striped hurdles and real gaps.
- **Physics.** Momentum with drag, a crest-launch condition on convex terrain,
  a pitch-tolerance check on landing, and a crash-and-respawn loop with three
  bikes per run.
- **The NES look.** The framebuffer is rendered at 240 px tall and scaled up
  with nearest-neighbour filtering, which is what gives the chunky pixels.
- **One file.** HTML, CSS, JavaScript, the favicons and the entire game.

## How it was made

The game came out of a single prompt:

```text
Role: Expert Web Game Developer.
Task: Create a single-file, self-contained HTML game inspired by the classic NES game Excitebike. Write the entire project inside a single index.html file using Three.js loaded via CDN.
Architecture Requirements:
* Single File: All HTML, CSS, and JavaScript must live directly inside index.html.
* CDN Dependencies: Load Three.js strictly via a script tag in the <head> (e.g., https://cloudflare.com). Do not use any local npm modules or external asset files.
* Self-Contained Assets: Build all 3D assets (the motorcycle, player, track hurdles, and background) procedurally using primitive Three.js geometries (BoxGeometry, CylinderGeometry) with solid color materials.
Core Game Mechanics & Modified Controls:
1. Perspective & Camera: Create a 2.5D side-scrolling view. The track runs left-to-right. The camera should smoothly follow the player's bike horizontally.
2. Multi-Lane Track & Lane Shifting: The track must have 4 distinct lanes (depth on the Z-axis). The player shifts lanes instantly using the W (Move Up/Inward) and S (Move Down/Outward) keys.
3. Speed, Turbo, & Jump Controls:
    * Holding the A key provides standard acceleration.
    * Holding the D key activates "Turbo" for higher speed but fills an on-screen "Overheat Meter". If the meter hits max, trigger a 3-second penalty cooldown where the bike is locked at zero speed.
    * Pressing the Spacebar allows the player to perform a manual jump/hop to clear smaller gaps or initiate a leap.
4. Hurdles & Mid-Air Pitch Control: Place procedural ramps and dirt mounds along the track lanes. When hitting a ramp or jumping, launch the bike into the air. While mid-air, use the secondary Arrow Left and Arrow Right keys to pitch/tilt the bike forward or backward.
5. Physics & Landing: The player must match the bike's pitch to the slope of the ground when landing. Landing flat maintains momentum. Landing unevenly or crashing into a hurdle head-on results in a "Crash" animation penalty (spinning out).
UI & Loop:
* Include an HTML overlay HUD showing the current Race Time, Distance traveled, and the Overheat Meter.
* Create a looping track that generates a series of random obstacles.
* Provide an automated layout with an arcade-themed dark background canvas centered in the browser window.
Output: Give me only the complete, working code block for index.html with zero conversational intro or post-code explanation. Ensure the code is production-ready and fully functional upon opening in a browser.
```

A note on reproduction: the Ollama CLI streams its answer behind a progress
spinner, which corrupts the generated code. Call the Ollama HTTP API with
`stream: false` and strip ANSI escapes from the response before extracting
the HTML document.
