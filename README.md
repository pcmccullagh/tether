# Tether

An Apple Watch proximity "leash" for group bike rides. Tether buzzes the lead rider with escalating haptics when a paired rider drifts beyond set distance thresholds — so you never have to look back to check on the group.

## Concept

When riding single-file or spread out, the lead rider has no safe, eyes-forward way to know if riders behind have fallen back, stopped, or gotten into trouble. Looking over your shoulder is dangerous, shouting doesn't carry, and phones stay in pockets. Tether uses the Apple Watch to alert by feel alone as the gap between two paired riders grows.

## v1 scope

- Two paired adult riders, each with an iPhone + Apple Watch on cellular GPS.
- A single pairwise distance — distance only, no bearing.
- Fixed thresholds at 50 / 100 / 300 ft.
- Mutual alerts; explicit Start Ride / End Ride.
- The Watch is the entire ride experience; the phone never leaves the pocket.

## Alert tiers

| Tier | Distance | Haptic behavior |
| --- | --- | --- |
| Safe | < 50 ft | Silent — no alert |
| Tier 1 | >= 50 ft | Single light buzz |
| Tier 2 | >= 100 ft | Stronger / double buzz, repeating |
| Tier 3 | >= 300 ft | Continuous alert until the gap closes |

Alerts escalate and de-escalate with distance; closing the gap silences them. Hysteresis smooths GPS jitter near a threshold. Every tier is individually configurable and silenceable per rider.

## Architecture (pocket-relay model)

Everything the rider sees and feels is on the Watch, but the pocketed iPhone does the heavy lifting:

- Each rider's iPhone obtains GPS via Core Location and pushes coordinates to a shared relay over cellular.
- Each phone computes pairwise distance (Haversine, on-device) and sends the current tier to its Watch via WatchConnectivity.
- The watchOS app stays alive via an `HKWorkoutSession`, displays live distance, and fires the tiered haptics (`WKHapticType`).
- Native iOS + watchOS, Swift / SwiftUI. The watch app is the product; the iOS app is a thin companion for setup, pairing, and background relay.

## This repository

This repo hosts the **watchOS UI mockup** — an interactive prototype of the six watch screens (pre-ride, the four active-ride tiers, and end-of-ride), built with Vite + React + Tailwind and deployed via GitHub Pages. It is the design prototype for the interface, not the native app itself.

Tier color system: Safe → green, Tier 1 → yellow, Tier 2 → orange, Tier 3 → red. Dark background for OLED and sunlight contrast; large type and colorblind-safe cues (color paired with ring fill).

### Develop

```bash
npm install
npm run dev
```

### Build & deploy

```bash
npm run build      # outputs to dist/
npm run deploy     # publishes dist/ to the gh-pages branch
```

`vite.config.js` sets `base: '/tether/'` for the GitHub Pages project site, and `public/404.html` provides an SPA fallback. After the first deploy, set Pages source to the `gh-pages` branch in repo Settings.

## Roadmap (v2)

- Direction / bearing so you know where to glance.
- Adjustable thresholds per ride or road type.
- Groups of 3+ riders.
- Standalone cellular-watch mode (drop the phone entirely).
- Off-grid peer-to-peer fallback for trail rides with no signal.
- Optional audio cue in addition to haptics.
