---
name: sierra-design
description: >
  Apply the Sierra.ai-inspired design system to any website, UI component, HTML file, or styling task.
  Use this skill whenever the user says "use the sierra design system", "sierra-design", "apply the design system",
  "style it like sierra", or asks to restyle / redesign a page and has previously set up this system.
  Also trigger when the user is building or editing HTML/CSS and wants a warm, minimal, premium aesthetic —
  even if they don't name Sierra explicitly. This skill contains all colors, typography, spacing, component
  patterns, and guiding principles needed to produce on-brand output in one shot.
---

# Sierra Design System

You are applying a design system inspired by Sierra.ai. Everything below is the source of truth — follow it
precisely whenever you generate or modify HTML, CSS, or UI components.

---

## Vibe (5 words)
**Minimal. Confident. Warm. Premium. Grounded.**

---

## Color Palette

### Base
| Role                    | Hex       |
|-------------------------|-----------|
| Background (primary)    | `#FFFFFF` |
| Background (subtle)     | `#F6F5F3` |
| Background (divider)    | `#E4E0DC` |
| Body text               | `#302E2D` |
| Heading text            | `#222222` |
| Muted / secondary text  | `#625E5B` |

### Accent / Brand
| Role                    | Hex       |
|-------------------------|-----------|
| Primary CTA (fill)      | `#006838` |
| Hover / deep green      | `#05351D` |
| Success / positive text | `#4FAF62` |

### Feature Card Backgrounds
Use these as full-bleed section/card backgrounds — never as UI element colors. Pick one per card for variety:
- Muted olive green: `#6B7B5A`
- Muted steel blue: `#5E7BAA`
- Dusty mauve: `#8B6477`
- Terracotta: `#A85C3E`

These are desaturated and earthy — never vivid. Think editorial poster, not tech dashboard.

---

## Typography

**Google Fonts equivalent:** `Plus Jakarta Sans` (closest available substitute for GT America).
Load it at weight 400 only:
```html
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans&display=swap" rel="stylesheet">
```

**Core rule:** Regular weight (400) only. Hierarchy comes from size and letter-spacing — never bold or italic.
Tight tracking on large headings is what gives it the premium feel.

| Level            | Size    | Line Height | Letter Spacing |
|------------------|---------|-------------|----------------|
| H1 / Hero        | 44px    | 48px        | -0.88px        |
| H2 / Section     | 28–32px | 32–36px     | -0.28px        |
| H2 / Subheading  | 22px    | 28px        | 0              |
| H3 / Card        | 22px    | 28px        | 0              |
| Body             | 16px    | 24px        | 0              |
| Small / Labels   | 14px    | 20px        | 0              |
| Tiny / Caption   | 12px    | 16px        | 0              |

---

## Layout & Spacing

- **Max content width:** `1160px`, centered, `24px` horizontal padding on sides.
- **Section vertical padding:** `80–120px` top and bottom. Let whitespace breathe — don't cram.
- **Full-width section backgrounds** with content constrained to the 1160px inner container.

---

## Border Radius Scale

| Use                        | Value    |
|----------------------------|----------|
| Buttons, tags (pill)       | `9999px` |
| Large cards / overlays     | `32px`   |
| Medium UI elements         | `16px`   |
| Small cards / inputs       | `12px`   |
| Minor / subtle rounding    | `8px`    |

---

## Component Patterns

### Buttons
- **Primary:** `background: #006838`, white text, pill-shaped (`border-radius: 9999px`), no border. Hover: `#05351D`.
- **Ghost/Secondary:** transparent fill, pill-shaped, `1px solid #E4E0DC` border. Text color matches context (white on dark, `#302E2D` on light).
- No hover shadows — interaction is color-only.

### Navbar
- Fixed at top, transparent over hero.
- Logo left (wordmark, all-caps), ghost CTA button + minimal controls right.
- No mega-menus. Keep it extremely lean.
- On scroll: add a subtle warm background (`rgba(255,255,255,0.9)`) with `backdrop-filter: blur(12px)`.

### Feature Cards (2-column grid)
- `border-radius: 32px`
- Each card gets a unique muted background from the feature card palette above
- White text on colored cards
- Feel like mini editorial posters — generous internal padding (`48px+`)

### Hero
- Full viewport height
- Full-bleed photographic or dark background with overlay for legibility
- Text and product UI float over it
- No hero grids, gradients, or abstract patterns — this system uses photography

### Shadows
One elevation only — a soft drop shadow for cards/overlays that float above the page:
```css
box-shadow: 0 8px 40px rgba(48, 46, 45, 0.10);
```

---

## CSS Variables Boilerplate

Always include these at the top of any stylesheet:

```css
:root {
  /* Colors */
  --bg:        #FFFFFF;
  --bg-subtle: #F6F5F3;
  --border:    #E4E0DC;
  --text:      #302E2D;
  --heading:   #222222;
  --muted:     #625E5B;
  --green:     #006838;
  --green-dark:#05351D;
  --green-lite:#4FAF62;

  /* Type */
  --sans: 'Plus Jakarta Sans', system-ui, sans-serif;

  /* Radii */
  --r-pill: 9999px;
  --r-card: 32px;
  --r-md:   16px;
  --r-sm:   12px;
  --r-xs:   8px;

  /* Layout */
  --max-w: 1160px;
  --pad-x: 24px;
}

body {
  font-family: var(--sans);
  font-weight: 400;
  background: var(--bg);
  color: var(--text);
  line-height: 1.5;
}
```

---

## Core Design Principles

1. **Whitespace first.** Sections have massive vertical padding. If it looks cramped, add more space.
2. **Regular weight only.** No bold, no italic. Size and spacing create hierarchy.
3. **Warm neutrals, never cold grays.** Every gray has a warm/brown cast. Use `#302E2D`, not `#333333`.
4. **One accent color.** Forest green (`#006838`) appears only on primary CTAs. Everything else is neutral.
5. **Pills everywhere.** Rounded pill buttons and tags signal modern, approachable softness.
6. **Muted, earthy card palettes.** When introducing color, use the desaturated feature card backgrounds — never brand primaries.
7. **Photography over decoration.** Authentic imagery beats icons, gradients, or abstract graphics.

---

## Quick Checklist Before Outputting

Before finalizing any HTML/CSS output, verify:
- [ ] Font loaded as Plus Jakarta Sans, weight 400 only
- [ ] CSS variables defined and in use
- [ ] No bold or italic type
- [ ] Large headings have negative letter-spacing
- [ ] Buttons are pill-shaped (`border-radius: 9999px`)
- [ ] Primary CTA uses `#006838`, not any other green
- [ ] Sections have `80–120px` vertical padding
- [ ] Max content width capped at `1160px`
- [ ] All grays are warm-tinted (check for cold `#333`, `#666`, `#999`)
