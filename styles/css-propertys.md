**CSS Custom Properties** (also commonly called **CSS Variables**) are a native browser feature that lets you define reusable values directly in CSS. They were introduced around 2016–2017 and by 2025–2026 have become the **standard** for almost every serious styling system — especially in design systems, theming, and dynamic UIs.

They are declared using the `--` prefix and accessed with the `var()` function.

### Basic Syntax (2025–2026 style)

```css
/* Root-level (global) definitions — most common place */
:root {
  --color-primary:    hsl(210 100% 50%);
  --color-primary-600: hsl(210 100% 40%);
  --spacing-md:       1.5rem;
  --font-size-base:   1rem;
  --transition-fast:  150ms ease;
  --shadow-sm:        0 1px 3px hsl(0 0% 0% / 0.1);
  --radius-md:        0.5rem;
}

/* Usage — anywhere in CSS */
button {
  background: var(--color-primary);
  padding: var(--spacing-md) var(--spacing-lg);
  border-radius: var(--radius-md);
  transition: background 0.2s var(--transition-fast);
}

.card:hover {
  box-shadow: var(--shadow-sm);
  background: var(--color-primary-600);
}
```

### Key Characteristics (Why They're Powerful in 2025+)

| Feature                        | Details                                                                 | Compared to SCSS variables                  |
|--------------------------------|-------------------------------------------------------------------------|---------------------------------------------|
| **Scope & Cascade**            | Live in the CSSOM, follow cascade/inheritance rules                     | SCSS vars are compile-time, no cascade      |
| **Dynamic updates**            | Can change via JS, media queries, `:root`, classes, parent elements     | SCSS vars are static after compilation      |
| **Fallback values**            | `var(--color, #ff0000)` — safe when var is undefined                    | No native fallback in SCSS                  |
| **Runtime calculation**        | Can use `calc()`, trigonometric funcs (`sin()`, `cos()`), etc.          | SCSS can calc at compile time only          |
| **JavaScript interop**         | `element.style.setProperty('--color', 'lime')` or `getComputedStyle()`  | No direct JS access                         |
| **Browser support (2026)**     | 97–99% globally, full in all modern browsers since ~2018                | Preprocessor dependency                     |

### Modern Best Practices (2025–2026 Era)

1. **Use semantic / purposeful naming** (not primitive/raw values in most places)

   ```css
   /* Good: semantic */
   :root {
     --brand:        hsl(200 80% 50%);
     --text-primary: hsl(0 0% 10%);
     --surface:      hsl(0 0% 98%);
   }

   /* Avoid raw hex everywhere */
   ```

2. **Layered tokens (primitive → semantic → component)** — very popular pattern now

   ```css
   :root {
     /* Primitives */
     --hue-brand: 210;
     --sat-brand: 100%;
     --light-brand-500: 50%;

     /* Semantic */
     --color-brand-500: hsl(var(--hue-brand) var(--sat-brand) var(--light-brand-500));
     --color-brand-hover: hsl(var(--hue-brand) var(--sat-brand) 40%);

     /* Component-level overrides if needed */
     .dark {
       --color-brand-500: hsl(var(--hue-brand) 80% 60%);
     }
   }
   ```

3. **Dark mode / theming (easiest with custom properties)**

   ```css
   @media (prefers-color-scheme: dark) {
     :root {
       --surface:      hsl(0 0% 8%);
       --text-primary: hsl(0 0% 95%);
     }
   }

   /* Or class-based (more control) */
   .theme-dark {
     --surface: hsl(0 0% 8%);
     --text-primary: hsl(0 0% 95%);
   }
   ```

4. **Use `@property` for typed / animated custom properties** (strong in 2025+)

   Allows smooth transitions/animations on custom props and better type checking.

   ```css
   @property --progress {
     syntax: "<percentage>";
     inherits: true;
     initial-value: 0%;
   }

   .progress-bar {
     --progress: 0%;
     width: var(--progress);
     transition: --progress 0.8s ease;
   }
   ```

5. **Performance & organization tips**

   - Define most tokens on `:root` (global scope)
   - Override locally when needed (component or state)
   - Use `color-mix()` + custom props for dynamic shades/tints
   - Combine with modern color spaces: `oklch()`, `lch()`, `color(display-p3 ...)`
   - Avoid overuse in selectors (keep specificity low)

### CSS Custom Properties vs SCSS Variables (2025–2026 Reality)

Many teams (including trends in 2025–2026 articles) now use a **hybrid** or **mostly native** approach:

| Use case                          | Best tool in 2025–2026                  | Reason |
|-----------------------------------|-----------------------------------------|--------|
| Static design tokens (colors, spacing scales) | SCSS variables + output to `--var`     | Compile-time safety + DRY |
| Runtime theming (dark mode, user themes) | CSS Custom Properties                  | Must be dynamic |
| JavaScript-controlled values      | CSS Custom Properties                  | Direct `setProperty()` |
| Complex calculations at build     | SCSS                                   | `lighten()`, maps, loops |
| Global injection in preprocessors | SCSS `@use` / `@forward`               | Your current setup |
| Final delivered CSS               | Native custom properties               | No build step overhead |

**Current industry direction (2025–2026)**:
- Many projects are **reducing Sass usage** or removing it entirely
- Keep Sass for:
  - Mixins & functions
  - Complex color math/maps
  - Generating utility classes or static scales
- Deliver almost everything as **native CSS custom properties**
- Your current setup (`additionalData` injecting SCSS tokens/mixins) is actually a **great hybrid** — you get SCSS power at build time + runtime flexibility.

### Recommendation for Your Project (kafemestoyalta)

Since you're already using SCSS with global `@use` for colors & mixins:

1. Move **semantic values** to native custom properties (output from SCSS or directly in `:root`)
2. Keep SCSS for generating shades/tints, mixins, and build-time logic
3. Use `var()` everywhere in components → future-proofs for dark mode, JS theming, etc.

Example migration snippet:

```scss
// tokens/colors.scss
$color-primary-raw: hsl(210 100% 50%);
:root {
  --color-primary: #{$color-primary-raw};
  --color-primary-hover: #{darken($color-primary-raw, 10%)};
}
```

Then in Vue/SCSS components:

```scss
button {
  background: var(--color-primary);
  &:hover { background: var(--color-primary-hover); }
}
```

This gives you the best of both worlds in 2026.


