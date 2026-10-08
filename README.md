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
- **Drop-in** — `import { toSvg } from 'peek-vanilla'` works in any JS environment

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
