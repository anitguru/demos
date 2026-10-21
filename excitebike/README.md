# Excitebike — one-shot HTML remake

A single-file 3D remake of the classic NES game *Excitebike*, generated in **one shot** by the open-weight model `minimax-m3.1-flash` (model id `minimax-m3.1-flash:cloud`) running locally through [Ollama](https://ollama.com). One prompt went in, a complete, playable game came out.

Play it in the browser: open `index.html` in any modern browser. Nothing to install, nothing to build.

## The game

A 2.5D side-scrolling dirt-bike racer in the spirit of the NES original:

- 4 lanes of procedurally generated endless track with ramps, dirt mounds, striped hurdles and real gaps.
- A momentum model with a crest-launch condition, a pitch-tolerance check on landing, and a crash-and-respawn loop (3 bikes).
- A temperature system with a cooldown penalty.
- Every asset is built from primitive Three.js geometries (`BoxGeometry`, `CylinderGeometry`) and solid-color materials, so there are no external image, model or audio files.
- The framebuffer is rendered at low resolution and scaled up, which is what gives it the chunky arcade look.
- An HTML overlay HUD shows race time, distance, speed, bikes remaining, the overheat meter and a pitch gauge.

### Controls

| Key | Action |
| --- | --- |
| `A` | Throttle (hold for standard acceleration) |
| `D` | Turbo (hold for higher speed — fills the overheat meter; at 100% the bike is locked at zero speed for a 3-second cooldown) |
| `W` | Shift lane inward (away from the camera) |
| `S` | Shift lane outward (toward the camera) |
| `Space` | Hop / manual jump |
| `←` / `→` | Pitch the bike forward or backward while mid-air — match the pitch to the slope of the ground on landing |
| `Enter` | Start the run (also on the title screen and after game over) |
| `R` | Restart |

Landing flat keeps your momentum. Landing at the wrong angle, or hitting a hurdle head-on, is a crash.

## How it was made

The whole game is **one HTML file**: `index.html`. Three.js is loaded from a CDN via a `<script>` tag in `<head>` (`https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js`); everything else — markup, CSS, and all game code — lives in that same file. There is no build step, no `package.json`, and no asset directory.

It was produced in a single generation pass: the prompt below was sent to `minimax-m3.1-flash` through Ollama, and the model returned the complete `index.html` in one response. The file published in this folder is that generated file, copied byte-for-byte with no changes.

### The exact prompt

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

## Files

- `index.html` — the entire game (markup, CSS, JavaScript, procedural assets).
