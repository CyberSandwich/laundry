# CLAUDE.md

## Project Overview

Miele Laundry Guide: single-page web app recommending wash/dry programs for **Miele PWM 908 washer and PDR 908 dryer** (Little Giants) based on garment, color, and soil level.

## Development

- **No build system**: Static HTML, open `index.html` directly
- **Deployment**: GitHub Pages (main branch)
- **Single file**: Always edit `index.html` directly; no separate files

## Constraints

- Self-contained: No external dependencies
- Mobile-first: 440px max width, iOS web app capable
- Readable format: Multi-line formatting (no single-line minification)
- **NEVER use em dashes (—)**: Use colons, semicolons, or rephrase

---

## Testing

### Test Files (Git-ignored, local only)

Three comprehensive test files validate all recommendation logic:

| File | Tests | Purpose |
|------|-------|---------|
| `verify-logic.js` | 189 | Program selection, temperature, dryer, warnings |
| `comprehensive-content-test.js` | 95 | Tips, detergent, avoid, air drying text |
| `edge-case-tests.js` | 77 | Edge cases, priority order, regressions |

**Total: 361 tests**

### Running Tests

```bash
# Run all tests
node verify-logic.js && node comprehensive-content-test.js && node edge-case-tests.js

# Run individual test suites
node verify-logic.js                    # Program/temp/dryer/warning logic
node comprehensive-content-test.js       # Tips/det/avoid/air content
node edge-case-tests.js                  # Edge cases and regressions
```

### When to Update Tests

**If you change `index.html`**, you MUST:

1. **Update the `rec()` function in ALL test files** to match `index.html`
2. **Run all 361 tests** to verify no regressions
3. **Add new test cases** if adding new logic paths

### Test File Structure

Each test file contains a **copy of the `rec()` function** from `index.html`. When modifying recommendation logic:

1. Copy the updated `rec()` function from `index.html`
2. Adapt it for Node.js (remove DOM/render code, keep logic)
3. Paste into all three test files
4. Run tests to verify

### What Each Test Suite Validates

#### verify-logic.js (189 tests)
- **Single item matrix**: All 11 garments x 5 colors x 3 soil levels
- **Priority order**: Wool > Delicates > Shirts > Denim > Outdoor > Activewear > Towels > Bedding > Underwear
- **Conflict warnings**: All incompatible item combinations
- **Color warning text**: Temperature in warning matches program temperature
- **Hygiene temps**: 60C-90C for towels/bedding/underwear
- **Dryer pairings**: Correct dryer for each wash program
- **Express eligibility**: Light soil + few items only

#### comprehensive-content-test.js (95 tests)
- **Tips relevance**: Item-specific care instructions
  - Wool: mesh bag, lay flat, 30C max
  - Shirts: unbutton, 800rpm, remove immediately
  - Denim: inside out, 1000rpm, indigo bleeds
  - Jackets: DWR, close zips, extra rinse
  - Towels: shake, fungus, no softener
  - Bedding: dust mites, 55C+, inside out
- **Detergent text**: Correct type/dose for soil level
  - Light: 55ml (replaces 75ml)
  - Normal: 75ml
  - Heavy: 100ml
- **Avoid warnings**: Fabric-specific prohibitions
- **Air drying**: Proper method per fabric

#### edge-case-tests.js (77 tests)
- **Temperature-warning consistency**: Warning temp = program temp
- **Priority verification**: All priority combinations
- **Dark color handling**: Never uses Whites program
- **Underwear <=2 items**: Special hygiene path
- **Regression tests**: Previously fixed bugs

### Warning Text Rules (CRITICAL)

Warning temperature MUST match actual program temperature:

| Scenario | Warning Temp | Program Temp |
|----------|--------------|--------------|
| T-shirts + mixed | 20C | Colored Items (20) = 20C |
| Towels + mixed | 60C | Colored Items (60) = 60C |
| Underwear alone + mixed | 60C | Colored Items (60) = 60C |
| Delicates + mixed | 30C | Delicates (30) = 30C |
| Shirts + towels + mixed | 40C | Shirts = 40C |
| Bedding + mixed | 20C | Colored Items (20) = 20C |

---

## Code Reference

Short identifiers for size optimization:

### CSS Variables
| Short | Meaning | Short | Meaning |
|-------|---------|-------|---------|
| `--b` | burgundy #8B1538 | `--bd` | burgundy dark |
| `--g` | gold accent | `--sf` | surface bg |
| `--c` | card (white) | `--sk` | skeleton/muted |
| `--sb` | subtle bg | `--t` | text primary |
| `--tm` | text muted | `--tf` | text faint |
| `--br` | border | `--s` | success |
| `--sbg` | success bg | `--w` | warning |
| `--wbg` | warning bg | `--e` | error |
| `--ebg` | error bg | `--r` | radius |
| `--rl` | radius large | | |

### CSS Classes
| Short | Meaning | Short | Meaning |
|-------|---------|-------|---------|
| `.a` | app container | `.s` | section |
| `.st` | section title | `.rs` | row stack |
| `.r` | row (selectable) | `.x` | selected state |
| `.ri` | row icon | `.rl` | row label |
| `.rc` | row checkbox | `.sw` | color swatch |
| `.si` | soil icon | `.bg` | button green |
| `.br` | button reset | `.re` | results container |
| `.rh` | results header | `.rx` | results icon |
| `.rt` | results title | `.wa` | warning alert |
| `.wt` | warning title | `.p` | program card |
| `.ph` | program header | `.pn` | program name |
| `.pd` | program desc | `.ps` | program stats |
| `.sp` | stat pill | `.sl` | stat label |
| `.sv` | stat value | `.dr` | dryer card |
| `.ad` | air dry section | `.ah` | air dry header |
| `.de` | detergent section | `.dt` | detergent title |
| `.av` | avoid section | `.at` | avoid title |
| `.ti` | tips section | `.tt` | tips title |
| `.gl` | guide step line | `.gn` | guide number |
| `.gt` | guide text | `.ab` | app buttons row |
| `.al` | app link button | `.fi` | fade-in anim |

### JavaScript
| Short | Purpose | Short | Purpose |
|-------|---------|-------|---------|
| `$` | SVG prefix | `T` | T-shirt path |
| `I` | Icons | `G` | Garments |
| `K` | Colors | `O` | Soils |
| `W` | Wash programs | `Y` | Dry programs |
| `S` | State object | | |

**State**: `S = { items: [], color: 'mixed', soil: 1, show: 0 }`

**Properties**: `l`=label/load, `i`=icon, `f`=fabric, `t`=temp, `s`=spin, `d`=description, `u`=duration, `v`=level, `p`=program, `w`=swatch

**Functions**: `rec()`=recommend, `render()`=render, `toggle(id)`, `setColor(id)`, `setSoil(v)`, `go()`=show results, `reset()`

---

## Program Reference (Definitive)

### Washing Programs
| Program | Duration | Load | Spin | Temp |
|---------|----------|------|------|------|
| Cottons PRO | 0:59 | 7.0kg | 1400 | 60°C |
| Delicates (30) | 0:37 | - | 600 | 30°C |
| Minimum Iron (40) | 0:40 | - | 1000 | 40°C |
| Dark Garments | 0:45 | - | 1200 | 40°C |
| Denim | 0:42 | - | 1000 | 40°C |
| Shirts | 0:35 | - | 800 | 40°C |
| Towels | 0:41 | 5.5kg | 1400 | 40°C |
| Express | 0:20 | - | 1400 | 30°C |
| Whites (90) | 1:11 | - | 1600 | 90°C |
| Whites (75) | 1:11 | - | 1600 | 75°C |
| Colored Items (60) | 0:59 | - | 1600 | 60°C |
| Colored Items (20) | 1:09 | - | 1600 | 20°C |

### Dryer Programs
| Program | Duration | Load | Level | Extras |
|---------|----------|------|-------|--------|
| Cottons (Closet +) | 0:36 | 8.0kg | Normal + | - |
| Cottons (Closet) | 0:35 | 8.0kg | Normal | - |
| Delicates (Closet) | 0:46 | 4.0kg | Normal | - |
| Cottons (Iron) | 0:35 | 8.0kg | Hand Iron | - |
| Cottons (Spin) | 0:35 | 8.0kg | Machine Iron | - |
| Minimum Iron (Closet) | 0:24 | 4.0kg | Normal | Feather |
| Minimum Iron (Iron) | 0:24 | 4.0kg | Hand Iron | Feather |
| Smoothing (Iron) | 0:05 | 1.0kg | - | - |
| Woolens | 0:05 | 2.0kg | - | - |
| Timed Drying (40) | 0:40 | 8.0kg | - | - |
| Shirts | 0:20 | 2.0kg | Normal | - |
| Express | 0:29 | 4.0kg | Normal | - |

**UI Note**: Fields with "-" should not display.

---

## Recommendation Logic

### Program Priority Order (in rec() function)

This is the EXACT priority order in `rec()`. When multiple items are selected, the FIRST matching condition wins:

1. **Wool/Knitwear** (`hW||hK`): Delicates 30C, AIR DRY ONLY
2. **Delicates** (`hD`): Delicates 30C, 600rpm
3. **Dress Shirts** (`hSh`): Shirts 40C, 800rpm (Dark Garments if dark)
4. **Denim** (`hJ`): Denim 40C, 1000rpm
5. **Outdoor/Jackets** (`hO||hJa`): Minimum Iron 40C
6. **Activewear/Synthetics** (`hA||(hS&&!hT&&!hB)`): Minimum Iron 40C (Dark Garments if dark)
7. **Towels** (`hT`): Towels/Whites/Colored Items based on color/soil
8. **Bedding** (`hB`): Cottons Pro/Whites/Colored Items/Dark Garments based on color/soil
9. **Underwear alone** (`hU&&t.length<=2`): Whites 75C or Colored Items 60C
10. **Blends/Pants** (`hBl||hP`): Dark Garments/Cottons Pro/Minimum Iron
11. **T-shirts** (`hTs`): Various based on color/soil
12. **Color fallbacks** (`iD`, `iW`, else): Default programs

**Key Principle**: Protect from permanent damage first, then warn about recoverable issues (hygiene).

### Fabric Priority (Most Delicate Wins)
1. **Wool/Cashmere**: Delicates 30°C, 600rpm. Never enzymes. Air dry flat.
2. **Silk**: Delicates 30°C. No brighteners. Air dry only.
3. **Delicates/Lingerie**: Delicates 30°C. Mesh bag. Often air dry.
4. **Denim**: Denim 40°C, 1000rpm. Inside out. No brighteners.
5. **Dress Shirts**: Shirts 40°C, 800rpm. Wrinkles permanent above 1200rpm.
6. **Technical/Outdoor**: Minimum Iron 40°C. No softener.
7. **Synthetics**: Minimum Iron 40°C. No softener (reduces wicking 70%).
8. **Towels**: 40°C light, 60-75°C normal/heavy. Never softener.
9. **Bedding**: 55°C+ kills dust mites. 60°C+ for hygiene.
10. **Underwear**: 60°C minimum (40°C leaves fungi).

### Color Rules
| Color | Rule |
|-------|------|
| White | Brighteners OK. 60-90°C. Powder with bleaching agents. |
| Light | 40-60°C. Watch dye bleed from new items. |
| Bright | Cold (20°C) for new. Color-safe detergent. |
| Dark | No brighteners. Dark Garments program. Inside out. |
| Mixed | 20°C to minimize bleeding. Color-catcher sheets. |

### Soil Adjustments
| Level | Program | Detergent |
|-------|---------|-----------|
| Light | Express OK if <2kg, no stains | 55ml powder, 20ml liquid |
| Normal | Standard programs | 75ml powder, 27ml liquid |
| Heavy | Full cycles; pre-treat stains | 100ml powder, 40ml liquid |

Note: Doses are for CV4 water (164 mg/L, moderately soft).

### Wash → Dry Pairings
| Wash | Dryer | Notes |
|------|-------|-------|
| Cottons PRO | Cottons (Closet) | High spin = efficient dry |
| Whites | Cottons (Closet +) | 1600rpm max extraction |
| Colored Items | Cottons (Closet) | Avoid Extra Dry (fading) |
| Minimum Iron | Minimum Iron (Closet) | Lower heat for synthetics |
| Delicates | Delicates (Closet) | 4kg max |
| Dark Garments | Minimum Iron (Closet) | Prevents fading |
| Denim | Cottons (Iron) | Slight damp prevents stiffness |
| Shirts | Shirts | Remove damp for ironing |
| Towels | Cottons (Closet +) | Full dry restores loft |
| Wool | **Air dry only** | Woolens fluffs but felts if tumbled |

### Critical Conflicts (Must Separate)
- Towels + synthetics: Lint clogs wicking fibers permanently
- Towels + delicates: Weight stretches; lint embeds
- New darks + lights: Dye bleeding (separate 2-3 washes)
- Heavy items + shirts: Weight causes permanent creasing
- Wool + any hot items: Different temp needs
- Underwear + wool: 60°C vs 30°C conflict

### Never Tumble Dry
Wool, silk, linen, rayon/viscose, high elastane (>5%), bras/swimwear, sequins/beading, rubber/plastic, leather/suede, oil-soaked items (FIRE HAZARD)

### Never Use Softener On
Towels, sportswear, technical outerwear, microfiber, wool, swimwear, flame-retardant sleepwear

### Temperature Thresholds
| Temp | Effect |
|------|--------|
| 30°C | Wool/silk max; enzymes work |
| 40°C | Synthetic safe; minimal pathogen kill |
| 55°C | 100% dust mite death; enzyme deactivation starts |
| 60°C | Most bacteria/fungi killed; hygiene standard |
| 75°C+ | Thermal disinfection; cotton/linen only |

### Detergent Rules
- **Cold (<40°C)**: Liquid (dissolves better)
- **Hot whites (60°C+)**: Powder (bleaching agents activate)
- **Wool/silk**: pH-neutral, enzyme-free only
- **Darks**: Liquid, no brighteners (powder shows as white specks)

### Pre-Treatment
- **Protein stains** (blood, sweat): Cold water only; hot sets permanently
- **Oil/grease**: Dish soap direct
- **Tannin** (coffee, wine): Oxygen bleach; never bar soap
- **Dye transfer**: Keep wet, oxygen bleach soak; never dry

### Safety
- Oil-soaked items: FIRE HAZARD in dryers; air dry outdoors only
- Clean lint filter after EVERY load
- Check pockets; close zippers
- Fill drum ¾ max; remove within 8 hours
