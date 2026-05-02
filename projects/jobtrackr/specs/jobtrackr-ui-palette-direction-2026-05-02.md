# JobTrakr UI Palette Direction

## Status
- **Decision date:** 2026-05-02
- **Decision owner:** Jimmy (PM)
- **Source direction:** Coolors trending UI palette direction

## Canonical MVP palette
I selected a clean, modern palette optimized for product UI clarity, data density, and accessible contrast.

**Palette URL:** `https://coolors.co/0f172a-2563eb-14b8a6-f59e0b-f8fafc`

### Colors
- **Midnight Navy** — `#0F172A`
  - Primary text
  - Top nav / dense UI anchors
  - High-contrast panels and icon treatment
- **Product Blue** — `#2563EB`
  - Primary actions
  - Links
  - Selected states
  - Focus accents
- **Teal Signal** — `#14B8A6`
  - Positive system signal
  - Secondary highlights
  - Active data accents
- **Amber Alert** — `#F59E0B`
  - Warnings
  - Attention states
  - “Needs review” visual emphasis
- **Cloud White** — `#F8FAFC`
  - Main canvas
  - Card background
  - Clean app surface

## Why this palette
- Feels modern and product-grade without looking generic
- Gives us a strong primary action color plus a clearly distinct success/accent signal
- Supports a jobs workflow UI with dense tables, filters, and status chips
- Keeps the background bright and readable while preserving enough contrast for enterprise-style productivity screens
- Leaves room to map workflow and fit signals without overloading one color

## Product guidance
- Use `#2563EB` as the primary CTA and selected-state color
- Use `#14B8A6` for positive or ready states where green would otherwise be too expected or visually noisy
- Use `#F59E0B` for warning, incomplete, or manual-review attention states
- Use `#0F172A` for headers, navigation, and high-importance copy
- Keep `#F8FAFC` as the dominant app background to preserve a clean jobs-dashboard feel

## Guardrails
- Do not use the palette as five equal-weight colors everywhere; this should remain a mostly neutral interface with strong accent discipline
- Avoid using amber for primary actions
- Avoid putting blue text on dark navy surfaces without contrast validation
- Keep table readability above brand expression

## Follow-up
Engineering/design should turn this into:
- semantic tokens
- light theme component mappings
- table/status/filter chip usage rules
- accessibility contrast checks for button, badge, and tag states
