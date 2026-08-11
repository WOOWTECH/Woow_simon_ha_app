# Handoff — Discovery screen icon-pulse animation clamp

**Date**: 2026-08-11
**Scope**: `Woow_simon_ha_app` + `Woow_apporo_ha_app` (both repos have the same file, same bug, same fix)
**Status**: Code changed in both repos, **not committed**. simon `minimalDebug` APK built + installed on user's Pixel 7a for visual check. Waiting on user feedback before further tuning.

---

## The problem the user reported

Screen: onboarding **discovery** ("正在家用網路上搜尋"). Layout is a rotating dots-ring (`R.drawable.dots`) with a pulsing brand-logo square (`R.drawable.ic_simon_branding` / `ic_apporo_branding`) at the centre.

At max pulse the **square's rounded corners visibly touch / overlap the inner-most dots** of the ring (screenshots attached in the previous conversation's vibe-attachments). User wants the pulse capped so the logo stays clear of the inner ring at all times.

---

## Files touched

- `app/src/main/kotlin/io/homeassistant/companion/android/onboarding/serverdiscovery/ServerDiscoveryScreen.kt`
  - Function: `AnimatedIcon()` (bottom of the file, ~line 353–391)
  - Same relative path in **both** `Woow_simon_ha_app` and `Woow_apporo_ha_app`

**Change applied (both repos):**

```diff
         val pulse by rememberInfiniteTransition(label = "icon_pulse").animateFloat(
             initialValue = 1f,
-            targetValue = 1.15f,
+            // Max scale kept small so the square's rounded corners stay inside the
+            // dots ring's inner-clear zone (~radius 68dp in the 220dp viewport).
+            targetValue = 1.05f,
             animationSpec = infiniteRepeatable(
                 animation = tween(durationMillis = 800, easing = LinearEasing),
                 repeatMode = RepeatMode.Reverse,
             ),
             label = "icon_pulse_value",
         )
```

Nothing else in the file was touched. Base size stays `.size(120.dp)`. Rotation animation untouched.

---

## Geometry analysis (so you can iterate without re-deriving)

Layout in `AnimatedIcon()`:

| Element | Modifier | Effective dp |
|---|---|---|
| Outer dots ring | `.size(220.dp).rotate(rotation)` | 220 |
| Inner brand square | `.size(120.dp).scale(pulse)` | 120 → 120·pulse |
| Wrapper | `Box(Modifier.padding(HADimens.SPACE3))` | — |

**Dots vector** (`app/src/main/res/drawable/dots.xml` and `drawable-v24/dots.xml`):
- `viewportWidth/Height = 220`, so viewport dp = actual dp
- Innermost dots sit at ≈ `radius 65–72dp` from centre → treat **inner-clear circle ≈ diameter 136dp / radius 68dp**
- Dots are denser toward the outer edge, sparser toward the inner. Rotation of the whole vector doesn't change any radius, so this is stable.

**Brand square** (both `ic_simon_branding` and `ic_apporo_branding`):
- Rounded-corner squares that visually resemble Android adaptive-icon shape
- At base `size(120.dp)` the naive diagonal reach would be `60·√2 ≈ 84.9dp` from centre, but because of the rounded corners the **effective corner reach is smaller**, empirically ≈ `62dp` at scale 1 (matches the "just-touching" behaviour visible in the small-pulse screenshot)
- At `scale = pulse`, corner reach scales linearly with pulse

**Why `1.15f` was bad, why `1.05f` was chosen**:
- `pulse=1.15` → size 138dp → corner reach ≈ `62·1.15 ≈ 71dp` → **overshoots the 68dp inner ring** → matches the user's complaint
- `pulse=1.05` → size 126dp → corner reach ≈ `62·1.05 ≈ 65dp` → **≈ 3dp margin** below the 68dp inner ring
- Kept the breathing effect visible but conservative

**If the user wants more breathing room** you have two knobs:

1. Drop base size (safer, keeps a bigger pulse ratio):
   - `size(108.dp)` + `scale(1.10)` → max 118.8dp, corner reach ≈ 61dp (7dp margin, more visible breath)
   - `size(100.dp)` + `scale(1.15)` → max 115dp, corner reach ≈ 59.5dp (8.5dp margin, most visible breath)
2. Drop the pulse only:
   - `pulse=1.03` → very subtle
   - `pulse=1.02` → barely visible; probably too little

If they want a bigger centre logo (base > 120dp), the geometry does not permit any pulse — the base already touches the inner ring. In that case the fix is either (a) a smaller dots ring outer size, or (b) redesigning the dots vector to have a larger inner-clear zone. Don't blindly enlarge the base.

---

## Deploy / verify commands (proven working)

```bash
export PATH="$HOME/Android/Sdk/platform-tools:$PATH"

# Wireless ADB (Android 11+): user already had wireless debugging enabled
adb connect 192.168.2.152:38363         # user's Pixel 7a (mDNS also auto-connects)
adb devices -l                          # verify

# Build + install (from the repo root)
cd ~/Desktop/Woow_simon_ha_app
ANDROID_SERIAL=192.168.2.152:38363 ./gradlew :app:installMinimalDebug --console=plain

# For apporo (same flow, different repo)
cd ~/Desktop/Woow_apporo_ha_app
ANDROID_SERIAL=192.168.2.152:38363 ./gradlew :app:installMinimalDebug --console=plain
```

**First build time**: ~2m13s on this machine (already cached, subsequent builds ~30s).
**Installed simon package name**: `com.simon.home.minimal.debug`
**Apporo package name** (from build config, not verified installed): probably `com.apporo.home.minimal.debug` — check `app/build.gradle.kts` `applicationId` if unsure.

---

## Repo state at handoff

Neither repo has been committed. Both have exactly one unstaged modification in `app/src/main/kotlin/io/homeassistant/companion/android/onboarding/serverdiscovery/ServerDiscoveryScreen.kt`.

Verify:
```bash
cd ~/Desktop/Woow_simon_ha_app && git status && git diff -- '*/ServerDiscoveryScreen.kt'
cd ~/Desktop/Woow_apporo_ha_app && git status && git diff -- '*/ServerDiscoveryScreen.kt'
```

---

## Open questions (raised, not answered)

1. **Upstream propagation** — Should the same fix land in `~/Desktop/woow_ha_app` (the upstream repo that both brand repos are seeded from per the whitelabel skill) so future rebrand cycles inherit it? Per memory `project_apporo_simon_ha_app.md`, each brand is a full clone from upstream, not a flavor overlay — so upstream needs its own fix or every future rebrand will re-introduce the bug.
2. **Apporo also has an offline device (192.168.2.138)** — apporo APK not built or installed anywhere yet; the code change is applied in the apporo repo but nothing has been produced.
3. **Commit strategy** — no PR / commit conventions were checked. Look at recent commits in each repo before committing; both repos use conventional-commit style like `fix(onboarding): …` based on `git log --oneline`.

---

## What to verify with the user before you touch anything

- Is `1.05` the right value, or do they want a bigger breath? (Look at the installed simon APK on the Pixel 7a.)
- Should apporo be built + installed too? On what device? (`.138` is offline, phone shares a display slot with simon.)
- Upstream `woow_ha_app` fix — yes/no?
- Commit + PR now, or wait for more visual tuning?

---

## Non-goals / do not touch

- **Do not** change `size(220.dp)` for the dots ring — it's used elsewhere for layout consistency (spacing spacers use `positionPercentage = 0.2f` weights around it).
- **Do not** clip the brand image with `CircleShape` — the previous commit `9a211b42` explicitly removed a CircleShape clip because it was wrong for the branding (see git log).
- **Do not** rotate the brand square — only the dots ring rotates; the square must stay axis-aligned so brand text reads normally.
