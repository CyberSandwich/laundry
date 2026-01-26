# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Miele Laundry Guide - a single-page web application that recommends optimal washing and drying programs for Miele W1/T1 machines based on garment selection, color, and soil level.

## Development

**No build system** - This is a static HTML file with embedded CSS and JavaScript. Open `index.html` directly in a browser to develop.

**Deployment**: GitHub Pages (main branch, serves index.html automatically)

**Testing**: Manual browser testing only. No test framework.

**Single file**: Always maintain only `index.html` - no separate draft files or build steps. Edit index.html directly.

## Architecture

Single self-contained file (`index.html`, ~24KB) with readable multi-line formatting:

1. **CSS Design System** (`<style>`): Uses shortened CSS variables and class names for size optimization.

2. **JavaScript** (`<script>`): Uses shortened identifiers for size optimization while maintaining readable formatting.

## Code Reference

The code uses short identifiers for size optimization. Use these mappings when reading/editing:

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
| `l` | label / load (kg) |
| `i` | icon |
| `f` | fabric |
| `t` | temp |
| `s` | spin |
| `d` | description |
| `u` | duration |
| `v` | level (dryness) |
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
- Readable format: Keep multi-line formatting for maintainability (no single-line minification)

## Optimization Guidelines

Always look for ways to optimize **performance** and **reliability** while minimizing **size**:

- Use short but meaningful variable/class names (e.g., `--b` for brand color, `.st` for section title)
- Reuse common patterns (e.g., SVG prefix `$`, shared icon paths)
- Avoid redundant code and unnecessary abstractions
- Keep the DOM structure minimal
- **Do NOT** compress to a single line - maintain readable multi-line formatting

## Program Reference (Definitive Data)

**Units**: Duration (hours:minutes), Weight (kg), Spin Speed (rpm), Temperature (°C)

### Washing Programs

| Program | Duration | Weight | Spin | Temp |
|---------|----------|--------|------|------|
| Cottons PRO | 0:59 | 7.0 | 1400 | 60 |
| Delicates (30) | 0:37 | - | 600 | 30 |
| Minimum Iron (40) | 0:40 | - | 1000 | 40 |
| Dark Garments | 0:45 | - | 1200 | 40 |
| Denim | 0:42 | - | 1000 | 40 |
| Shirts | 0:35 | - | 800 | 40 |
| Towels | 0:41 | 5.5 | 1400 | 40 |
| Express | 0:20 | - | 1400 | 30 |
| Whites (90) | 1:11 | - | 1600 | 90 |
| Whites (75) | 1:11 | - | 1600 | 75 |
| Colored Items (60) | 0:59 | - | 1600 | 60 |
| Colored Items (20) | 1:09 | - | 1600 | 20 |

### Dryer Programs

| Program | Duration | Weight | Drying Level | Extras |
|---------|----------|--------|--------------|--------|
| Cottons (Closet +) | 0:36 | 8.0 | Normal + | - |
| Cottons (Closet) | 0:35 | 8.0 | Normal | - |
| Delicates (Closet) | 0:46 | 4.0 | Normal | - |
| Cottons (Iron) | 0:35 | 8.0 | Hand Iron | - |
| Cottons (Spin) | 0:35 | 8.0 | Machine Iron | - |
| Minimum Iron (Closet) | 0:24 | 4.0 | Normal | Feather |
| Minimum Iron (Iron) | 0:24 | 4.0 | Hand Iron | Feather |
| Smoothing (Iron) | 0:05 | 1.0 | - | - |
| Woolens | 0:05 | 2.0 | - | - |
| Timed Drying Warm Air (40) | 0:40 | 8.0 | - | - |
| Shirts | 0:20 | 2.0 | Normal | - |
| Express | 0:29 | 4.0 | Normal | - |

**Note**: Fields with "-" should not be displayed in the UI. Only show data that exists.
