# Dash With Motion — Web Prototype

A Geometry Dash-style auto-runner where you close your fist in front of your phone's camera to jump. Real hand tracking via MediaPipe, runs in mobile Safari, no app install.

## What you have

`index.html` is the entire game — one self-contained file, ~600 lines. Open it on your iPhone or iPad in Safari, tap **GESTURE ON**, allow camera, close your fist to jump.

Touch still works as a fallback — tap anywhere to jump.

## Why one HTML file

- No npm, no build, no Expo, no Apple Developer account.
- MediaPipe Gesture Recognizer loads from a CDN inside mobile Safari and recognizes `Closed_Fist`, `Open_Palm`, `Pointing_Up`, etc. natively.
- iOS Safari **requires HTTPS** for camera access, so we publish to GitHub Pages (free HTTPS) instead of running a local server.

## Deploy to GitHub Pages — first time, ~5 minutes

You only do this once. After that, every `git push` updates the live URL automatically.

### 1. Create a public GitHub repo

Go to https://github.com/new. Name it whatever you like (`dash-with-motion` is fine). **Public visibility is required** for free GitHub Pages.

### 2. Push this folder to the repo

Open a terminal in `C:\dev\Dashwithmotion` and run:

```bash
git init
git add index.html README.md MVP-Plan.md
git commit -m "Dash With Motion web prototype"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```

Replace `YOUR_USERNAME` and `YOUR_REPO` with what you used in step 1.

### 3. Turn on GitHub Pages

On your repo's GitHub page:

1. Click **Settings** (top tabs)
2. Click **Pages** (left sidebar)
3. Under **Build and deployment** → **Source**, select **Deploy from a branch**
4. Branch: `main`, folder: `/ (root)`. Click **Save**.

GitHub Pages takes ~30 seconds to publish. Your URL will be:

```
https://YOUR_USERNAME.github.io/YOUR_REPO/
```

### 4. Open it on your iPhone

Type the URL above into Safari on your phone (or AirDrop the link from your laptop — easier). The game loads. Tap **GESTURE ON**, allow camera, close your fist.

## Updating the game

Make any edit to `index.html` on your laptop, then:

```bash
git add index.html
git commit -m "tweak"
git push
```

GitHub Pages re-deploys in ~30 seconds. Refresh Safari on your phone, you've got the new build.

## How to play

- **Tap anywhere** to start / jump / restart after a crash.
- **GESTURE OFF / ON** pill at the bottom-left toggles camera mode. Grant permission the first time.
- In gesture mode, a small selfie-camera preview appears top-right with green dots tracking your hand landmarks. The label below shows the current detected gesture and confidence (e.g. `Closed_Fist 87%`).
- **Close your fist** to jump. Open your hand between fists — the game requires you to "release" the fist before counting another jump, so you can't just hold a fist.
- Score and best score show top-left. Best score persists in browser storage.

## What's intentionally simple

This is a prototype to validate the *feel* of fist-controlled rhythm gameplay, not a finished game. Not in scope yet:
- Real music / audio sync (uses a fixed 132 BPM placeholder for obstacle timing)
- Sound effects
- Multiple avatar forms (ship, ball, wave) — just the cube
- Gravity flip portals, jump pads, orbs
- More than three obstacle types
- Custom levels / level editor
- Background art beyond the parallax skyline

## What to evaluate when you play

1. **Tap mode first** — is the core jumping fun for 30+ seconds? If not, no input scheme will save it.
2. **Gesture latency** — when you close your fist, how long until the cube jumps? Anything under ~150 ms feels playable. Anything over ~300 ms feels broken. This tells us whether camera input is viable on your specific phone.
3. **Detection reliability** — does it consistently recognize your fist in your normal lighting? If you have to perform an exaggerated fist for it to register, we tune the confidence threshold (currently 0.6).
4. **Run length** — if you crash inside 10 seconds every time, the difficulty ramp is too steep.
5. **Does the camera actually help?** — would you rather tap? If yes, the gesture angle isn't the killer feature you thought, and the game pivots to "polished GD clone with a gesture toggle." If no, double down.

## Tuning knobs

All physics and difficulty constants are at the top of the `<script>` block in `index.html` under `const CFG = { ... }`. If jumps feel weak: raise `jumpVelocity` (more negative). Too easy: raise `bpm` and `scrollSpeed`. Fist trigger feels jumpy: raise `jumpDebounceMs`.

## Known issues

- First time you tap **GESTURE ON**, it takes ~2-4 seconds to download the MediaPipe model (~8 MB). Pill says `LOADING...`. After that it's cached.
- iOS Safari may pause the camera if you switch tabs / lock the phone. Toggle gesture off and back on to resume.
- The MediaPipe model is trained on hands, not arms in motion at extreme angles — hold your hand roughly upright in the camera frame for best results.

## Next steps once you've tested it

If gesture feels promising, the upgrade path is to wrap this same web build inside a native shell (Capacitor or a WKWebView) and ship it to TestFlight. The game logic doesn't change — only how it's distributed. Or, if you want to chase 60 FPS native performance and richer effects, port to Unity using the original MVP plan in `MVP-Plan.md`.
