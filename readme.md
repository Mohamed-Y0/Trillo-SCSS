# Trillo — Modern All-in-One Booking Landing Page

Trillo is a responsive, modern hotel and travel booking landing page built as part of Jonas Schmedtmann's **Advanced CSS and Sass** course. The project focuses heavily on mastering **CSS Flexbox**, modern **CSS Custom Properties (Variables)**, **Sass / SCSS architecture**, **SVG sprite workflows**, and advanced CSS animations.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Tech Stack & Tooling](#tech-stack--tooling)
- [Folder Structure](#folder-structure)
- [Key Learning Notes & Concepts](#key-learning-notes--concepts)
  - [1. CSS Custom Properties vs. Sass Variables](#1-css-custom-properties-vs-sass-variables)
  - [2. Flexbox Techniques & Patterns](#2-flexbox-techniques--patterns)
  - [3. Icon Masking with CSS (`mask-image`)](#3-icon-masking-with-css-mask-image)
  - [4. SVG Sprites & Styling](#4-svg-sprites--styling)
  - [5. Advanced CSS Animations](#5-advanced-css-animations)
  - [6. Responsive Strategy & Breakpoints](#6-responsive-strategy--breakpoints)
- [NPM Scripts & Build Workflow](#npm-scripts--build-workflow)
- [Code Improvements & Fixes Applied](#code-improvements--fixes-applied)

---

## Project Overview

- **Design Philosophy**: Desktop-first layout that smoothly adapts down to small mobile screens.
- **Core Layout Model**: Exclusively Flexbox (no float grids or CSS Grid).
- **Styling Architecture**: SCSS modular structure transitioning to modern Dart Sass (`@use`).

---

## Tech Stack & Tooling

- **HTML5**: Semantic tags, accessible forms, and SVG icon integration.
- **Sass (Dart Sass `^1.98.0`)**: Preprocessor with `@use` modules, variables, and nested rules.
- **PostCSS & Autoprefixer (`postcss-cli`, `autoprefixer`)**: Automatic vendor prefixing.
- **Live-Server**: Local development server with instant reload.
- **npm-run-all**: Orchestrating parallel and sequential build scripts.

---

## Folder Structure

```text
Trillo/
├── css/
│   ├── style.comp.css     # Expanded compiled Sass output
│   ├── style.prefix.css   # PostCSS autoprefixed CSS
│   └── style.css          # Production compressed CSS
├── img/
│   ├── sprite.svg         # Combined SVG symbols sprite
│   ├── favicon.png
│   └── ...hotel & user images
├── sass/
│   ├── main.scss          # Main entry file loading modules with @use
│   ├── _base.scss         # CSS resets, root font sizing, global body styles
│   ├── _variables.scss    # CSS custom properties & Sass breakpoint variables
│   ├── _layout.scss       # Page layout containers (header, sidebar, detail, etc.)
│   └── _components.scss   # Modular UI components (buttons, nav, cards, reviews)
├── index.html             # Main markup
├── package.json           # Dependencies and build scripts
└── readme.md              # Project documentation and notes
```

---

## Key Learning Notes & Concepts (Quick Reference)

### 1. CSS Custom Properties vs. Sass Variables

In this project, two kinds of variables are intentionally used:

| Variable Type             | Syntax                                | Where Used                        | Why                                                                                                             |
| :------------------------ | :------------------------------------ | :-------------------------------- | :-------------------------------------------------------------------------------------------------------------- |
| **CSS Custom Properties** | `:root { --color-primary: #eb2f64; }` | Colors, shadows, reusable borders | Live in the browser DOM, cascade down elements, inspectable in DevTools, dynamic.                               |
| **Sass Variables**        | `$bp-medium: 56.25em;`                | Media query breakpoints           | **CSS variables cannot be used in media queries** (e.g. `@media (max-width: var(--bp))` is invalid CSS syntax). |

### 2. Flexbox Techniques & Patterns

1. **`flex: grow shrink basis` shorthand**:
   - Sidebar: `flex: 0 0 18%` (cannot grow, cannot shrink below 18% of parent width).
   - Hotel View: `flex: 1` (occupies all remaining space in `.content`).
2. **`margin: auto` inside Flexbox**:
   - In `.overview__stars`, `margin-right: auto` pushes the stars to the left and moves the location/rating blocks all the way to the far right without requiring a separate wrapper!
   - In `.review__user-box`, `margin-right: auto` pushes user details left and the rating number right.
3. **Full-height flex alignment**:
   - In `.user-nav`, `align-self: stretch` makes the container take 100% of the header's 7rem height, enabling full-height hover backgrounds on nav items.
4. **Order manipulation on mobile**:
   - At `$bp-smallest` (500px), `.search` has `order: 1` and `flex: 0 0 100%`, which wraps the search bar to a second row below the logo and user nav.

### 3. Icon Masking with CSS (`mask-image`)

Instead of hardcoding colors inside SVG files or rendering raster images:

```scss
&__item::before {
  content: '';
  display: inline-block;
  height: 1rem;
  width: 1rem;
  margin-right: 0.7rem;

  // Mask defines the shape, background-color defines the color
  background-color: var(--color-primary);
  -webkit-mask-image: url(../img/chevron-thin-right.svg);
  -webkit-mask-size: cover;
  mask-image: url(../img/chevron-thin-right.svg);
  mask-size: cover;
}
```

> **Tip:** This pattern allows changing an icon's color simply by modifying `background-color`, without touching the SVG file! Both standard `mask-image` and `-webkit-mask-image` are included for browser cross-compatibility.

### 4. SVG Sprites & Styling

Using an SVG sprite (`img/sprite.svg`) with `<svg><use href="img/sprite.svg#icon-name"></use></svg>`:

- **Performance**: Loads all icons in a single HTTP request that can be cached.
- **Dynamic color inheritance**: With `fill: currentColor`, the SVG inherits the text color of its parent link or button automatically.

### 5. Advanced CSS Animations

- **Side navigation hover effect (Multi-stage transition)**:
  - Phase 1: `transform: scaleY(1)` (grows vertically from the center).
  - Phase 2: `width: 100%` with a transition delay `0.2s` and custom timing function `cubic-bezier(1, 0, 0, 1)`.
  - Phase 3: Background color shifts on `:active`.
- **Pulsating button focus animation**:
  - Uses `@keyframes pulsate` to scale to `1.05` and cast an ambient shadow.
- **Sliding CTA button**:
  - Two text layers (`.btn__visible` and `.btn__invisible`).
  - On hover, `.btn__visible` translates down (`translateY(100%)`) while `.btn__invisible` slides from `top: -100%` to `top: 0`.

### 6. Responsive Strategy & Breakpoints

- Breakpoints are defined in `em` units (`1em = 16px` default browser font size):
  - `$bp-largest: 75em;` (~1200px) — Container margins collapse to edge-to-edge.
  - `$bp-large: 68.75em;` (~1100px) — Base font-size drops to `50%` (8px), scaling down all `rem` values proportionally.
  - `$bp-medium: 56.25em;` (~900px) — Sidebar collapses from vertical column to horizontal top bar; detail section reduces padding.
  - `$bp-small: 37.5em;` (~600px) — Navigation items stack icon over label; description and reviews stack vertically (`flex-direction: column`).
  - `$bp-smallest: 31.25em;` (~500px) — Header items wrap search bar to its own row.

---

## NPM Scripts & Build Workflow

| Command                | Action                                                            |
| :--------------------- | :---------------------------------------------------------------- |
| `npm start`            | Runs `devserver` (live-server) and `watch:sass` concurrently.     |
| `npm run compile:sass` | Compiles `sass/main.scss` into expanded `css/style.comp.css`.     |
| `npm run prefix:css`   | Runs Autoprefixer via PostCSS to generate `css/style.prefix.css`. |
| `npm run compress:css` | Minifies and compresses the prefixed CSS into `css/style.css`.    |
| `npm run build:css`    | Executes the complete production pipeline sequentially.           |

---

## 👤 Author & Credits

- **Author**: Mohamed ([@Mohamed-Y0](https://github.com/Mohamed-Y0))
- **Design & Concept**: Inspired by the Natours project in Jonas Schmedtmann's _Advanced CSS and Sass_ course.

---

<p align="center">Made with 💚 and pure modern CSS/Sass</p>
