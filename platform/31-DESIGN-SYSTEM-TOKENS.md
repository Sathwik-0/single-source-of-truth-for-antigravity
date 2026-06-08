# Design System Tokens

This document defines the visual variables, fonts, colors, spacing, and animations that govern Chronicle's user interface.

---

## 1. Visual Archetype

Chronicle’s visual identity is inspired by **Perplexity, Linear, GitHub, and Cursor**. 
* We reject high-padding, generic enterprise designs (Jira/Confluence style).
* The design uses a deep dark mode baseline, glassmorphism, tight information density, and spring-based transitions.

---

## 2. Color Palette (Dark Theme Baseline)

All color tokens use HSL values for consistency and precise shade management:

* **Background Base:** `hsl(240, 10%, 4%)` (Deep slate black)
* **Background Surface:** `hsl(240, 10%, 7%)` (Subtle gray card overlay)
* **Border Default:** `hsl(240, 6%, 15%)` (Muted gray border divider)
* **Text Primary:** `hsl(0, 0%, 95%)` (Bright off-white)
* **Text Secondary:** `hsl(240, 5%, 65%)` (Muted gray)
* **Brand Highlight:** `hsl(180, 100%, 45%)` (Vibrant cyan/teal)
* **Brand Highlight Muted:** `hsl(180, 50%, 15%)` (Soft cyan glow backdrops)
* **Status Success:** `hsl(142, 70%, 45%)` (Emerald green)
* **Status Warning:** `hsl(38, 92%, 50%)` (Warm amber)
* **Status Error:** `hsl(0, 84%, 60%)` (Saturated red)

---

## 3. Typography

* **Headings:** `Inter`, Sans-Serif (font-weight: `600` or `700`).
* **Body Text:** `Inter`, Sans-Serif (font-weight: `400` or `500`).
* **Code & Logs:** `JetBrains Mono`, Monospace.

---

## 4. Layout & Information Density

Chronicle prioritizes high information density to prevent excessive scrolling during code reviews:
* **Line Heights:** Headings: `1.2`, Body Text: `1.45`, Monospace Code: `1.5`.
* **Container Margins:** Compact padding scales to maximize screen real-estate for side-by-side evidence layouts.

---

## 5. Border Radius & Spacing Scales

### Radius Tokens (in pixels)
* `radius-sm`: `8px`
* `radius-md`: `12px` (Standard card corners)
* `radius-lg`: `16px`

### Spacing Scale (in pixels)
* `space-2xs`: `4px`
* `space-xs`: `8px`
* `space-sm`: `12px`
* `space-md`: `16px`
* `space-lg`: `24px`
* `space-xl`: `32px`
* `space-2xl`: `48px`
* `space-3xl`: `64px`

---

## 6. Motion & Transitions

All UI state changes must feel instant. We avoid slow, decorative slide animations:
* **UI Focus/Hover States:** CSS transition `100ms cubic-bezier(0.16, 1, 0.3, 1)` (spring-like ease-out).
* **SSE Streaming Token Transitions:** Instant inline render to match fast terminal output.
* **Evidence Panel Slide:** Slide-in transition `150ms cubic-bezier(0.87, 0, 0.13, 1)`.
