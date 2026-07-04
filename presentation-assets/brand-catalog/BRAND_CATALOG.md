# Plinco Brand / Animation / Analytics Catalog
_Compiled 2026-07-03 from the actual source tree — every "exists" claim below is grep-verified against a real `require()`, component, or function, not assumed._

All matched image assets are copied alongside this file in this same folder (`presentation-assets/brand-catalog/`) so they can be handed off for improvement as one set.

---

## Brand language (visual assets)

| Item | Status | File in this folder | Source path | Notes |
|---|---|---|---|---|
| DCL P Icon | ✅ Exists | `p-3d.png` | `assets/p-3d.png` | Standalone 3D clay "P" mark, transparent background, 1024×1024. Used inline inside animated scenes (e.g. `SheepFlock.tsx`) as `icon-transparent.png` — **identical file, two filenames** (byte-for-byte duplicate). Worth consolidating to one canonical file. |
| Icon for application | ✅ Exists | `icon.png` | `assets/icon.png` (also `icon-new.png`, `adaptive-icon.png` — not copied, near-duplicates) | Full composite app icon: P mark + sheep + cloud, on blue gradient, 1024×1024. This is the literal App Store / adaptive icon artwork, distinct from the standalone P mark above. |
| Puppy | ✅ Exists | `puppy.png` | `assets/puppy.png` | 3D clay render, white glow. Used in `PuppyDropDiscovery.tsx`, `WolfEmergence.tsx`, `SheepFlock.tsx`. |
| Sheep | ✅ Exists | `sheep-new.png` | `assets/sheep-new.png` | 3D clay render — this is the one actually wired into the app (`SheepFlock.tsx`). **Note:** a second file, `assets/sheep.png`, is flat vector line-art and is NOT referenced anywhere in the codebase — dead asset, not copied here. |
| Wolf | ✅ Exists | `wolf.png` | `assets/wolf.png` | 3D clay render, red/orange neon glow, faces left. Used in `WolfEmergence.tsx`, `SheepFlock.tsx`. |
| Safe & Free (wordmark) | ✅ Exists (text component, no image) | — | `src/components/SafeFreeTag.tsx`, `src/components/LocalNetworkBadge.tsx` | Rendered as styled text ("Safe & Free" / "Safe & Free · never leaves the house" / "Safe & Free · Local network"), mortar-grey `#6B7280`, always paired with the brick icon below. No dedicated wordmark graphic exists yet — it's typography only. |
| Safe & Free : Brick wall, iconography | ✅ Exists | `brick.png` | `assets/brick.png` | 3D clay brick-cube render. Paired with the "Safe & Free" text in `SafeFreeTag.tsx` and referenced conceptually in the Banish animation's brick wall the phone flies over. |
| _(bonus, not requested but part of the same visual family)_ Cloud | ✅ Exists | `cloud-new.png` | `assets/cloud-new.png` | 3D clay cloud, appears alongside the sheep in the app icon and `SheepFlock.tsx`. Included since it's baked into both the icon and the sheep-counting scene. |

---

## Animations

| Item | Status | Component | Notes |
|---|---|---|---|
| Banish villager | ✅ Exists | `src/components/BanishAnimation.tsx` | Full continuous scene: catapult rolls in → loads phone → winds up → fires → phone catches fire, arcs over a fixed brick wall, tumbles off → "BANISHED" stamp. Every beat is a named timing constant (`ROLL_IN_MS`, `WIND_UP_MS`, `ARC_MS`, etc.) so tests can pin story beats. |
| Overthrow Chief | ⚠️ Partial | `src/pairing/VillageFullModal.tsx` (`overthrowChief()` in `src/pairing/family.ts`) | The **logic** is real and wired (confirm alert → `overthrowChief()` call → success haptic). There is **no dedicated visual animation** for it today — it's a confirm `Alert.alert` + a generic success-feedback haptic, not a custom motion sequence like Banish gets. Candidate for a real animation to match Banish's production value. |
| Puppy run off Wolf | ✅ Exists | `src/components/WolfEmergence.tsx` | "Send a puppy" full story on one surface: puppy sprints in from the right → wolf's glow dies, it bolts left → puppy chases it off-screen → puppy trots back (flipped) and holds the line, breathing, white glow → on connect, a happy double-bounce "Protected by your puppy" + gold puppy badge. ~2.8s chase timeline, named `CHASE` beat constants. |
| Welcome basket, puppies arrive | ✅ Exists | `src/components/PuppyDropDiscovery.tsx` | Slide + fade + wag-tail entrance (`slide`, `opacity`, `wag` shared values) when a kennel drop (signup bonus / monthly refill / bonus) is discovered. Uses `puppy.png`. |
| Sheep counting | ✅ Exists | `src/components/SheepFlock.tsx` | The idle/calm-mode "counting sheep" scene — layered sheep + cloud + icon sprites. |
| Sleepy icon rotation | ✅ Exists | `src/components/SheepFlock.tsx` (`rot` shared value, line ~139–159) | Gentle continuous ±10° wobble/rotation loop on the brand icon during calm mode — described in-code as "Playful wobble." |

---

## Analytics (mostly net new — confirmed)

There's one real analytics module today: `src/analytics/index.ts` — a typed `AnalyticsEvent` union dispatched through a single `track()` function (currently `console.log` + Crashlytics breadcrumb; wiring to Firebase Analytics is a scoped follow-up, not yet done). Below is what's already in that union vs. what's genuinely net new.

| Item | Status | Existing hook (if any) | Notes |
|---|---|---|---|
| Audio levels over a period of time | ❌ Net new | `src/components/AudioPulse.tsx` (UI only) | There's a live audio-level **visual pulse** component, but nothing logs loudness over time as an analytics series — no event, no aggregation. |
| Session analytics | ⚠️ Partial | `session_started`, `session_ended` (`{ role, duration_ms }`), `on_call_resume_restored` events already exist in `AnalyticsEvent` | The lifecycle events exist. Anything beyond start/stop/resume (e.g. quality, drops, reconnects, mode mix) is net new. |
| Distress detected | ⚠️ Partial | `cry_detected` event exists (`{ loudness_db, sensitivity_preset }`) | Cry detection itself is tracked. "Distress" as a broader/separate signal (vs. plain cry) is not distinguished in the event taxonomy today — worth deciding if this is the same event or a new one. |
| Battery Level of host device | ❌ Net new | `src/device/useHostBatteryPublisher.ts` (publishes to Firestore for live UI, not analytics) | Battery level is live-streamed to the parent's screen already; it is not captured as an analytics event/trend. |
| Heat detected | ❌ Net new | `src/device/thermal.ts` (`shouldWarnSession`, `shouldNotifyOverheat`, thermal levels/labels) | Full thermal-state logic and user-facing overheat warnings exist. None of it fires an analytics event — it's UI/notification only. |
| Network, free and safe mode preserved | ❌ Net new | `readEntitlement(family).remoteAccess`, `localNetworkMode` (derived in `baby.tsx`/`pair.tsx`) | Whether a session is running Safe & Free (local) vs. off-network/relay is known and used for gating + the badge UI, but never logged as an analytics event to track how often each mode is actually used. |

**Legend:** ✅ fully exists · ⚠️ partially exists (logic/UI present, one dimension missing) · ❌ net new (nothing wired yet)
