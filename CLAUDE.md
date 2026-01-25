# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Miele Laundry Guide - a single-page web application that recommends optimal washing and drying programs for Miele W1/T1 machines based on garment selection, color, and soil level.

## Development

**No build system** - This is a static HTML file with embedded CSS and JavaScript. Open `index.html` directly in a browser to develop.

**Deployment**: GitHub Pages (main branch, serves index.html automatically)

**Testing**: Manual browser testing only. No test framework.

## Architecture

Single minified file (`index.html`, ~20KB) organized as:

1. **CSS Design System** (lines 1-10 in `<style>`): Uses shortened CSS variables and class names for size optimization.

2. **JavaScript** (in `<script>`): Minified with shortened identifiers.

## Minified Code Reference

The code is optimized for minimal file size. Use these mappings when reading/editing:

### CSS Variables
| Short | Meaning |
|-------|---------|
| `--b` | burgundy (brand color #8B1538) |
| `--bd` | burgundy dark |
| `--g` | gold accent |
| `--sf` | surface background |
| `--c` | card background (white) |
| `--sk` | skeleton/muted background |
| `--sb` | subtle background |
| `--t` | text primary |
| `--tm` | text muted |
| `--tf` | text faint |
| `--br` | border color |
| `--s` | success color |
| `--sbg` | success background |
| `--w` | warning color |
| `--wbg` | warning background |
| `--e` | error color |
| `--ebg` | error background |
| `--r` | border radius |
| `--rl` | border radius large |

### CSS Classes
| Short | Meaning |
|-------|---------|
| `.a` | app container |
| `.s` | section |
| `.st` | section title |
| `.rs` | row stack |
| `.r` | row (selectable item) |
| `.x` | selected state |
| `.ri` | row icon |
| `.rl` | row label |
| `.rc` | row checkbox |
| `.sw` | color swatch |
| `.si` | soil icon |
| `.bg` | button green/primary |
| `.br` | button reset |
| `.re` | results container |
| `.rh` | results header |
| `.rx` | results icon |
| `.rt` | results title |
| `.wa` | warning alert |
| `.wt` | warning title |
| `.p` | program card (wash) |
| `.ph` | program header |
| `.pn` | program name |
| `.pd` | program description |
| `.ps` | program stats |
| `.sp` | stat pill |
| `.sl` | stat label |
| `.sv` | stat value |
| `.dr` | dryer card |
| `.ad` | air dry section |
| `.ah` | air dry header |
| `.de` | detergent section |
| `.dt` | detergent title |
| `.av` | avoid section |
| `.at` | avoid title |
| `.ti` | tips section |
| `.tt` | tips title |
| `.fi` | fade-in animation |

### JavaScript Constants
| Short | Full Name | Purpose |
|-------|-----------|---------|
| `$` | SVG_PREFIX | Common SVG attributes |
| `T` | TSHIRT_PATH | Reused t-shirt icon path |
| `I` | ICONS | Icon SVG strings |
| `G` | GARMENTS | Garment type definitions |
| `K` | COLORS | Color category options |
| `O` | SOILS | Soil level options |
| `W` | WASH | Wash program definitions |
| `Y` | DRY | Dryer program definitions |
| `S` | STATE | App state object |

### Data Object Properties
| Short | Full Name |
|-------|-----------|
| `l` | label |
| `i` | icon |
| `f` | fabric |
| `t` | temp |
| `s` | spin |
| `d` | description |
| `v` | level (dryness) |
| `h` | heat |
| `p` | program name |
| `w` | swatch class suffix |

### State Object (`S`)
```javascript
S = { items: [], color: 'mixed', soil: 1, show: 0 }
```
- `items`: Selected garment IDs
- `color`: Selected color category
- `soil`: Soil level (0=light, 1=normal, 2=heavy)
- `show`: Show results flag (0/1)

### Core Functions
| Short | Full Name | Purpose |
|-------|-----------|---------|
| `rec()` | recommend | Decision engine - returns wash/dry programs |
| `render()` | render | Generates HTML from state |
| `toggle(id)` | toggle | Toggle garment selection |
| `setColor(id)` | setColor | Set color category |
| `setSoil(v)` | setSoil | Set soil level |
| `go()` | getSettings | Show results |
| `reset()` | reset | Clear all selections |

## Recommendation Engine Logic

The `rec()` function uses priority-based if-else matching:
1. Extract fabric types from selected garments
2. Check for special fabrics (wool, delicates) first
3. Apply color-specific temperature rules
4. Adjust for soil level
5. Generate contextual warnings (dye transfer, lint, denim bleeding)
6. Return program recommendations with care instructions

## Key Constraints

- Self-contained: No external dependencies, all CSS/JS inline
- Mobile-first: iOS web app capable, 440px max container width
- Research-backed: Program selections based on Miele documentation
- Size-optimized: Minified for smallest possible file size (~20KB)
