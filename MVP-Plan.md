# Dash With Motion — MVP Plan

A Geometry Dash–style rhythm runner with hand-gesture controls, fresh mechanics, a distinct theme, and procedural levels. Built for iPhone and iPad, vibe-coded with Claude Code.

## 1. The Concept in One Paragraph

You play a character that auto-runs left-to-right through a procedurally generated world synced to music. You can tap the screen to jump, or hold the phone up and use a hand gesture in front of the front camera — a closed fist to jump, an open palm to glide, a pointed finger to dash. The theme is not the standard neon-cube look; pick a strong direction (see §4) so it doesn't feel like a clone. Levels are generated on the fly from the music's beat map and difficulty curve, so every run is different.

## 2. What Geometry Dash Actually Is (so the MVP scope is grounded)

Core loop: auto-running avatar, single-input control (tap = jump/hold), avoid spikes/saws/gaps, stay synced to the music. Levels add gravity flips, jump pads, orbs, and form-changes (cube → ship → ball → wave). Difficulty comes from rhythm memorization and split-second reaction. The MVP only needs the cube form and tap-to-jump — that's enough to prove the loop is fun.

## 3. Tech Stack Recommendation

**Recommendation: Unity + AR Foundation, target iOS first.**

Why Unity over the alternatives:
- Claude Code handles C# well, and Unity has the largest training-data footprint of any game engine, so vibe-coding output quality will be highest.
- Unity has a built-in path to both touch input and front-camera hand tracking via AR Foundation / ARKit on iPhones with TrueDepth camera (iPhone X and later).
- Cross-platform later is one build target away — same project ships to Android when you're ready, no rewrite.
- Free for your revenue level.

Hand-gesture options, ranked:
1. **Apple Vision framework (`VNDetectHumanHandPoseRequest`)** — runs on-device, 21 joints, low latency, no extra dependencies. Best for iOS-only MVP. Gets a model upgrade in iOS 26 (smaller, faster, more accurate per Apple's 2025 updates).
2. **MediaPipe Hands / Gesture Recognizer** — Google's solution, cross-platform, ships with prebuilt gesture classifications (fist, open palm, point, thumbs-up). Better choice the moment you add Android.
3. **ARKit hand tracking via AR Foundation** — wraps Vision framework, easier from Unity.

For an iOS-first MVP that you'll port to Android in v2, start with **MediaPipe via Unity plugin** — that way the gesture code survives the port.

Alternative if you want to stay closer to "pure" iOS: SwiftUI + SpriteKit + Vision framework. Smaller, faster to build, but locks you out of Android forever.

## 4. The Differentiator — Pick One Theme to Lead With

You picked new mechanics + new theme + procedural levels. The theme is what people will see in a screenshot and decide to download from, so commit hard. A few directions, pick one for the MVP:

- **"Origami World"** — paper-folding aesthetic, obstacles fold up out of the floor, gestures feel like you're folding the world.
- **"Liquid"** — character is a drop of water, flows around obstacles, pinch gesture shrinks you to fit through gaps.
- **"Shadow puppet"** — your hand on camera literally casts a shadow into the game and becomes part of the level. This is the most novel and the one that justifies the camera being on.

Shadow puppet is the strongest "why does this need a camera?" answer. Recommend it.

New mechanics tied to gestures:
- Tap / fist → jump (single beat)
- Hold / closed fist held → sustained float
- Open palm → glide forward through a hazard
- Two fingers up → split into two avatars temporarily

Procedural levels: feed the audio file through a beat-detection library (`aubio` or Unity's `AudioAnalyzer`), place obstacles on beats, scale density to a difficulty curve. MVP version: one hardcoded song, one procedural seed per run.

## 5. MVP Scope — What Ships, What Doesn't

**In:**
- One avatar, one form (cube), one theme committed
- Touch-to-jump (always available)
- Front-camera gesture mode: fist = jump, that's it
- One song with procedural obstacle placement on beats
- Death + restart loop
- Local high score
- Settings: toggle gesture mode on/off, calibrate hand position

**Out (defer to v2):**
- Multiple songs / song picker
- More avatar forms (ship, ball, wave)
- Gravity flip portals
- User-generated levels
- Online leaderboards
- Accounts, monetization, ads
- Android build

This is intentionally lean. The question MVP needs to answer: *is gesture-controlled rhythm gameplay fun, or is the camera latency a deal-breaker?*

## 6. Build Plan — 6 Weeks, Solo, Vibe-Coded

**Week 1 — Setup & boring foundations**
- Apple Developer account ($99/yr, takes 24–48h to approve — do this on day 1)
- Install Unity 6 LTS, Xcode, Unity iOS Build Support module
- Create empty Unity project, get an empty scene running on your iPhone via Xcode
- This single step (empty scene → your phone) is where most solo projects die. Don't move on until it works.

**Week 2 — Core loop, touch only**
- Auto-running cube on a flat plane
- Tap-to-jump with proper jump arc tuned to feel good
- Spike obstacles that kill you
- Death → restart
- Goal at end of week: 30 seconds of fun on touch alone. If it's not fun yet, gestures won't save it.

**Week 3 — Procedural generation + audio sync**
- Pick one song (royalty-free; Kevin MacLeod or Free Music Archive)
- Beat detection → array of beat timestamps
- Spawn obstacles aligned to beats, with a difficulty ramp
- Camera follow, basic particle/death feedback

**Week 4 — Theme pass**
- Commit to shadow-puppet (or your chosen theme)
- Replace placeholder art with the real direction
- One screenshot you'd actually post on social media

**Week 5 — Hand gestures**
- Integrate MediaPipe Unity plugin OR Vision framework via Unity native plugin
- Front camera feed in a corner of the screen (so player can see their hand)
- Detect closed fist → trigger same jump function as tap
- Tune detection threshold and latency; if latency > 100ms the game won't feel right
- Toggle in settings

**Week 6 — Polish, TestFlight, ship**
- Death animation, score display, juice (screen shake, hit-pause)
- Build for iOS, archive in Xcode, upload to App Store Connect
- TestFlight internal testing — invite yourself + 2–3 friends
- Iterate on feedback for one week before deciding what's next

## 7. Getting It on Your iPhone — The Path

Two paths, pick based on how often you want to test:

- **Daily iteration: Xcode direct install (free, but apps expire after 7 days).** Connect phone via USB, hit run in Xcode, it installs. This is what you'll use 90% of the time during development.
- **Sharing with testers: TestFlight ($99/yr Apple Developer required).** Upload build to App Store Connect, invite testers by email, they install via TestFlight app. Builds last 90 days. Use this once you have something worth showing.

iPad: same exact build, Unity scales the canvas. No extra work if you design the UI to be resolution-independent from day 1.

## 8. Risks & How to De-Risk Them Early

- **Gesture latency feels bad** → Test on real device in week 1 before building anything. Write a 50-line Unity script that puts a dot on screen wherever your fist is, look at the lag, decide if it's tolerable. If it isn't, pivot to touch-only.
- **Procedural levels aren't fun** → Hand-author one level first as a reference for what "good" feels like, then tune the generator until it produces something close.
- **Vibe-coding stalls on Unity-specific bugs** → Keep a running notes file of every error and fix; Unity has a lot of "you have to click this checkbox in the inspector" issues that pure code-gen will miss.
- **Apple rejects the build** → Apple is touchy about camera-permission justification text. Write a clear, honest `NSCameraUsageDescription` in week 1 and don't change it.

## 9. What I'd Want to Know After the MVP

- Does anyone choose gesture mode over touch after their first session?
- What's the average run length? (If under 15 seconds, the difficulty curve is broken)
- Do testers ask for more songs or more mechanics first?
- Is the theme memorable enough that a tester can describe the game to a friend a week later?

Those four answers tell you whether v2 is "double down on gestures," "this is a touch game with a gimmick," or "kill it and try something else."

---

## Quick Decision Checklist Before You Start

- [ ] Apple Developer account purchased
- [ ] Unity 6 LTS + Xcode installed and updating to latest
- [ ] Theme direction chosen (locking it in matters more than picking the "right" one)
- [ ] Royalty-free song selected
- [ ] iPhone with TrueDepth camera available (iPhone X or newer)
- [ ] Two hours of weekly time blocked for playtesting your own builds — this is the step solo devs skip
