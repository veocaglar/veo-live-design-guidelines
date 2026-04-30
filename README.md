# Veo Live Design Guidelines

Working draft. Covers iOS and Android design conventions for typography, icons, and colors.

---

## Guiding Principles

1. **Native > custom.** Use UIKit/SwiftUI and Material 3 built-ins instead of bespoke implementations, unless the app genuinely needs something unique.
2. **System resources > custom resources.** Default to system-provided assets, colors, fonts, and behaviors. Only add custom when there's a brand or product reason.
3. **One style end-to-end.** Pick a typography style (or icon, or color token) and let it carry through. Inline overrides on top of a token are a smell — fix the token instead.

Brand assets stay custom: LIVE logo, Veo Crest, splash, empty-state illustrations, branded reactions, sport illustrations.

---

## Typography

### Fonts

- **Protokoll** is the default font for everything readable: body, labels, titles, buttons, navigation, dialogs.
- **OldSchool Grotesk Compressed Bold** is the brand font, used sparingly for "oomph" moments — club names, scores, key metrics/values, hero titles. **Never** for prose, body copy, or anything meant to be read at length.
- Inter is deprecated and being removed from both platforms.
- When a native component doesn't allow font override (e.g. iOS context menus, system share sheet), the system font is fine — don't fight the platform. Apply Protokoll wherever you reasonably can.

### Shared rules

- **Pick one style end-to-end.** A text element gets one type style and that style carries size, weight, line height, and letter spacing. Layering inline `fontSize` / `fontWeight` / `letterSpacing` / `lineHeight` on top of a style is a smell — fix the style or extend the scale.
- **Rarely create new styles.** Use the existing scale. Only add a token when a real need can't be expressed within it.
- **Color is the exception** — setting color inline (`.foregroundStyle(...)`, `color = OnSurfaceSecondary`) is fine. Color is a separate concern from the type scale.
- During Figma audits: pick the matching scale style; if the source has inline overrides, flag as a coverage gap and open a follow-up rather than reproducing the override in Figma.

### iOS

- Use **`VeoDesignSystem`** (`VeoFont.*`) — the canonical Veo design system package, shared with VeoGo and the Veo-Foundation Figma library. Naming follows Apple's typography scale (`headline`, `subheadline`, `body`, `callout`, `footnote`, `caption`, etc.).
- Protokoll ships **Regular / Medium / Bold** — there is no SemiBold. If migrating SemiBold call sites, apply `.weight(.bold)` at the call site.
- All `VeoFont.*` styles use `relativeTo:` so they support **Dynamic Type** (including Larger Accessibility Sizes AX1–AX5) automatically.
- For UIKit consumers, use `UIFont+VeoFont.swift` in `apps/veolive/LocalDependencies/Sources/UIComponents/Extensions/`. It wraps `UIFontMetrics(forTextStyle:)` for Dynamic Type. Currently used by `HomeViewController` nav bar and `ChromecastManager` (`GCKUIStyle`).
- Migration tracked in **VP-1737** (iOS PR [#859](https://github.com/recoord/veo-mobile-ios/pull/859)) — completes the rollout started in PR #396.

### Android

- Use **Material 3 type scale** — `MaterialTheme.typography.*` end-to-end. Protokoll is bound to all 15 M3 roles via `Typography().withFont(Protokoll)`.
- **Bold is not an Android convention.** Differentiate hierarchy with size and M3 role (e.g. `titleLarge` vs `titleSmall`), not by adding weight. M3's default weights are intentional — let them carry.
- **Brand variants** for hero/oomph text (OldSchool Grotesk Compressed Bold, with line-height trim for the font's bottom-heavy metrics):
  - `titleLargeBrand` — 22sp
  - `headlineSmallBrand` — 24sp
  - `headlineLargeBrand` — 32sp
- Use these for club names, scoreboards, big numerics, hero empty states. Never for body or running text.
- Functional inline overrides are OK in narrow cases: runtime-sized text (e.g. ClubLogo), debug-only tiny text, emoji sizing. Anything else should resolve to a typography role.
- Migration tracked in **VP-1738** (Android PR [#247](https://github.com/recoord/veo-mobile-android/pull/247)) — replaces `PaceFonts`, the bespoke `Typography` object, and removes 42 inline overrides.

---

## Icons

### iOS

- **Default to SF Symbols** (`Image(systemName: "...")` / `UIImage(systemName: "...")`).
- Custom asset-catalog images (PDFs/PNGs) only when SF Symbols genuinely lacks a glyph — brand marks, sport-specific iconography, anything Apple doesn't ship.
- No need to maintain selected/unselected variants for a single icon unless there's a specific design reason — UIKit reuses the image for both states.
- Established pattern from VP-1712 / VP-1726.

### Android

- **Material Symbols Rounded** is the system. Files named `sym_*`, stored in `core-ui/src/main/res/drawable/`.
- Selected-state convention:
  - **Outlined** (`fill=0`) = default
  - **Filled** (`fill=1`) = selected/active
  - Filled variants live alongside outlined: `sym_home_filled`, `sym_person_filled`, `sym_group_filled`, `sym_star_filled`
- Cross-module resource references need the full qualifier: `co.veo.core_ui.R.drawable.sym_*`
- Custom (non-replaceable) icons live in `core-ui`, `featurevideoplayer`, and `ui` drawable dirs — sport illustrations, brand marks, placeholders, american_football.
- Migration tracked in VP-1734 (PRs #245 + #246).

### Both

- When adding a new icon, ask first: does the system library cover it? If yes, use the system icon and skip the asset pipeline.

---

## Colors

> _TODO — fill in once we audit the current color tokens on both platforms._

Initial questions to resolve:
- iOS: are we using a centralized `Color` extension or `UIColor` asset catalog? Where do brand vs semantic tokens live?
- Android: M3 color roles (`primary`, `onSurface`, `surfaceVariant`, etc.) — is the brand mapped cleanly, or are there raw hex values leaking into Composables?
- Cross-platform: which colors are *brand* (must match exactly across iOS/Android/web) vs *semantic* (can differ slightly per platform)?
- Dark mode coverage gaps?

---

## Open Questions

- Apply Protokoll to native UIKit chrome via `UIAppearance` proxy (nav bar titles, tab bar items) — out of scope for VP-1737, worth a follow-up?
- AX5 layout audit on iOS — Dynamic Type now works end-to-end, but do any cells truncate at the largest sizes?
- Icon weight on Android — rounded is the default, but is there a stroke weight standard for non-Material custom icons (sport illustrations)?
- Spacing & corner radius tokens — separate doc, or fold into this one?
