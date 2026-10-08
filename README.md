# peek-vanilla

Vanilla JS port of [Peek](https://github.com/doan-labs/peek) by Doan Labs — generate avatars from names, no React, no build step, no database.

A string in, a face out.

## Credit

All avatar generation logic is ported from [@doan-labs/peek](https://github.com/doan-labs/peek) (MIT License, © 2026 Doan Labs). This repo strips the React wrapper and TypeScript types, leaving pure JavaScript that runs anywhere.

Original: https://peek.doan-labs.com/

## Why this port?

The original Peek is a React component. That's fine for React apps, but many sites (like static Astro sites) don't use React. Pulling in React (~40KB runtime) just to render avatars defeats the purpose of a lightweight static site.

This port:
- **Zero dependencies** — pure JS, no React, no build step
- **Same output** — byte-identical SVGs to the original for the same inputs
- **Same animation** — the full animation rig (springs, blinks, saccades, breathing, per-expression choreography) ported 1:1; the original's own animation code never touched React in the first place
- **Drop-in** — `import { toSvg, mount } from 'peek-vanilla'` works in any JS environment

## Features

- 4 face styles, 7 colors, 11 expressions, 20,000+ combinations
- Deterministic: same name → same avatar, every time
- ~2.5KB SVG output per avatar
- Customizable: override face, color, eyes, brows, mouth, cheeks, expression, gaze
- No database, no network requests, no runtime dependencies

## Usage

```js
import { toSvg } from 'peek-vanilla';

// Basic: name in, SVG out
const svg = toSvg('Sakayori');
document.getElementById('avatar').innerHTML = svg;

// With options
const svg2 = toSvg('Rem', {
  size: 128,
  expression: 'happy',
  square: false,  // circular clip
});
```

### Live avatars (animation, no React)

`mount()` is the vanilla replacement for React's `<Peek animate />` — same tree, same animation rig, straight DOM updates:

```js
import { mount } from 'peek-vanilla';

const avatar = mount('Sakayori', '#avatar', {
  animate: true,
  expression: 'happy',
  gaze: 'pointer', // eyes follow the cursor
});

// later:
avatar.setExpression('surprised');
avatar.setGaze([0.5, -0.2]);
avatar.destroy(); // stop the loop, remove the SVG
```

`mount(name, target, options)` — `target` is an element or CSS selector. Options are the same as `toSvg`, plus `animate` (default `false`) and `className`. Returns `{ el, live, setExpression, setGaze, destroy }`. Respects `prefers-reduced-motion` (renders the static pose instead).

For full control, `Live` is also exported: `new Live(svgElement, tree, who, opts, expression, gaze)` — pair it with `draw()` + `restPose()` from the same package.

### Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `size` | number | 64 | Pixel size (drives stroke thickness) |
| `expression` | string | 'normal' | happy, sad, angry, surprised, etc. |
| `face` | string | (from name) | Override face style |
| `color` | string | (from name) | Override color |
| `eyes` | string | (from name) | Override eyes |
| `brows` | string | (from name) | Override brows |
| `mouth` | string | (from name) | Override mouth |
| `cheeks` | string | (from name) | Override cheeks |
| `gaze` | [x, y] | — | Eye direction, -1 to 1 |
| `square` | boolean | true | false = circular clip |
| `title` | string/false | name | Accessible name |

## License

MIT — same as the original. See LICENSE file.
