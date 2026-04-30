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

### iOS

- **Migrate off Inter → Protokoll + OldSchoolGrotesk** (the Veo design system package).
- The `Font.bodyCustom` / `Font.*Custom` family in `LocalDependencies/Sources/UIComponents/Extensions/Fonts.swift` calls Inter and is the migration target.
- Prefer system fonts (SF Pro) where reasonable for a more iOS-native feel — free Dynamic Type and accessibility scaling.
- No inline `fontSize` / `fontWeight` / `letterSpacing` overrides. Pick one `Font` style; if the scale doesn't cover a need, extend the scale.

### Android

- Use `MaterialTheme.typography.*` (M3) end-to-end.
- Brand variants exist for cases the M3 scale doesn't cover: `titleLargeBrand`, `headlineSmallBrand`, `headlineLargeBrand` (added in VP-1738).
- Avoid layering inline `fontSize`, `fontWeight`, `letterSpacing`, `lineHeight` next to a `style = MaterialTheme.typography.*` — that fragments the type scale and hides bugs (e.g. the latent FontFamily bug fixed in VP-1738 where subtitles silently rendered Bold).
- Color is a separate concern and is fine to set inline (`color = OnSurfaceSecondary`).

### Both

- During Figma audits: pick the matching M3 / iOS scale style; if the source has inline overrides, flag as a coverage gap and open a follow-up — don't reproduce the override in Figma.

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

- Should we treat system fonts (SF Pro / Roboto) as the default and keep Protokoll for headlines/brand moments only? Or full migration to Protokoll for body too?
- Icon weight on Android — currently rounded, but is there a stroke weight standard for non-Material custom icons (sport illustrations)?
- Spacing & corner radius tokens — separate doc, or fold into this one?
