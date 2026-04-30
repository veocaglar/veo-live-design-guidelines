# Veo Live Design Guidelines

Design conventions for Veo Live across iOS and Android.

Describes the **target state**. Migrations in progress are listed for situational awareness, not as work to do unless asked.

---

## Guiding principles

1. **Native > custom.** Use UIKit/SwiftUI and Material 3 built-ins. Reach for bespoke only when a brand or product requirement demands it.
2. **System resources > custom resources.** Default to system-provided assets, components, and behaviors.
3. **One style end-to-end.** A text/icon/color element gets one token. Inline overrides on top of a token are a smell — fix the token instead.

Brand assets stay custom: LIVE logo, Veo Crest, splash, empty-state illustrations, branded reactions, sport illustrations.

---

## Typography

### Fonts

- **Protokoll** — default font for everything readable: body, labels, titles, buttons, navigation, dialogs.
- **Oldschool Grotesk Compressed Bold** — brand font. Use only for "oomph" moments: club names, scores, metrics/values, hero titles.
  - **DO NOT** use Oldschool for prose, body copy, descriptions, or anything meant to be read at length.
- Native components that don't allow font override (iOS context menus, share sheet, etc.) — accept the system font, don't fight the platform.

### Shared rules

- Pick **one** type style per text element. It carries size, line height, and letter spacing together.
- **DO NOT** layer inline `fontSize` / `letterSpacing` / `lineHeight` on top of a typography style. Treat as a smell. Fix the style or extend the scale.
- **Weight is platform-specific** — see iOS and Android rules below.
- **Rarely add new styles.** Use the existing scale. Only extend when it genuinely can't express the need.
- **Color is the exception** — setting color inline is fine (`.foregroundStyle(...)`, `color = OnSurfaceSecondary`). Color is separate from the type scale.
- During Figma audits: pick the matching scale style. If the source has inline overrides, flag as a coverage gap and open a follow-up — do not reproduce overrides in Figma.

### iOS

- Use **`VeoDesignSystem`** package. API: **`VeoFont.*`**.
- Naming follows Apple's typography scale: `headline`, `subheadline`, `body`, `callout`, `footnote`, `caption`, etc.
- Protokoll ships **Regular / Medium / Bold only** — no SemiBold.
- **Weight inline is OK** when the style+weight combo is the intent (e.g. `.font(VeoFont.headline).fontWeight(.bold)`). This is idiomatic SwiftUI and avoids inventing a new scale entry per weight pairing. Don't reach for it just to make something "bolder-looking" if a heavier style fits — pick the heavier style first.
- All `VeoFont.*` styles use `relativeTo:` and support **Dynamic Type** including AX1–AX5.
- For UIKit consumers, use `UIFont+VeoFont.swift` in `apps/veolive/LocalDependencies/Sources/UIComponents/Extensions/`. It wraps `UIFontMetrics(forTextStyle:)`.

### Android

- Use **Material 3 type scale**: `MaterialTheme.typography.*`. Protokoll is bound to all 15 M3 roles via `Typography().withFont(Protokoll)`.
- **Bold is not an Android convention.** Differentiate hierarchy with size and M3 role (`titleLarge` vs `titleSmall`), not added weight. Let M3 default weights carry.
- **DO NOT** override `fontWeight` inline. If a heavier emphasis is needed, use a different M3 role or a brand variant.
- Brand variants for Oldschool Grotesk Compressed Bold (with line-height trim for the font's bottom-heavy metrics):
  - `titleLargeBrand` — 22sp
  - `headlineSmallBrand` — 24sp
  - `headlineLargeBrand` — 32sp
  - Use for club names, scoreboards, big numerics, hero empty states. Never body or running text.
- Functional inline overrides OK only for: runtime-sized text (e.g. ClubLogo), debug-only tiny text, emoji sizing.

---

## Icons

### iOS

- **Default to SF Symbols**: `Image(systemName: "...")` / `UIImage(systemName: "...")`.
- Custom asset-catalog images (PDF/PNG) only when SF Symbols genuinely lacks a glyph — brand marks, sport-specific iconography.
- **DO NOT** maintain selected/unselected variants for a single icon unless design requires it. UIKit reuses the image for both states.

### Android

- **Material Symbols Rounded** is the system. Files named `sym_*`, stored in `core-ui/src/main/res/drawable/`.
- Selected-state convention:
  - **Outlined** (`fill=0`) = default
  - **Filled** (`fill=1`) = selected/active
  - Filled variants live alongside outlined: `sym_home_filled`, `sym_person_filled`, `sym_group_filled`, `sym_star_filled`
- Cross-module resource references need the full qualifier: `co.veo.core_ui.R.drawable.sym_*`
- Custom (non-replaceable) icons live in `core-ui`, `featurevideoplayer`, and `ui` drawable dirs — sport illustrations, brand marks, placeholders, `american_football`.

### Shared rule

- New icon needed? Check the system library first. If covered, use it and skip the asset pipeline.

---

## Colors

> _TODO — fill in after color token audit on both platforms._

Open questions:
- iOS: centralized `Color` extension vs `UIColor` asset catalog? Where do brand vs semantic tokens live?
- Android: M3 color roles (`primary`, `onSurface`, `surfaceVariant`, etc.) — brand mapped cleanly, or raw hex leaking into Composables?
- Cross-platform: which colors are *brand* (must match exactly across iOS/Android/web) vs *semantic* (can vary per platform)?
- Dark mode coverage gaps?

---

## Open follow-ups

- iOS: apply Protokoll to native UIKit chrome via `UIAppearance` proxy (nav bar titles, tab bar items).
- iOS: AX5 layout audit — Dynamic Type works end-to-end, but some cells may truncate at the largest accessibility sizes.
- Android: stroke weight standard for non-Material custom icons (sport illustrations).
- Spacing & corner radius tokens — separate doc, or fold in here?
