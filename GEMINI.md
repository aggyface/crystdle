# CRYSTDLE — Gemini CLI Build Guide

A daily browser-based mineral guessing game in the style of Pokédle.
Each guess reveals a full row of property comparisons with green/red/arrow feedback.
No server required — pure static HTML + JS, open `index.html` to play.

---

## Current Status

**index.html** and **minerals.json** are both present and working.
The game prototype is complete and functional. This document covers the remaining
features to implement. Start at **PROMPT 7** — do not re-run earlier prompts.

### What is already built and working
- Dark obsidian theme, gold accents, Cinzel/Crimson Pro/JetBrains Mono fonts
- 82 minerals inlined in `index.html` as a JS const array
- 7-column guess rows: Hardness · Sp. Gravity · Luster · Crystal System · Chem. Group · Birefringence · Optical Class
- Birefringence as ordered categorical with ↑↓ arrows (None < Weak < Moderate < Strong < Extreme)
- Formula displayed under mineral name in each guess row
- Autocomplete dropdown with keyboard nav and highlighted match
- Daily seed (date-based), Play Again (random)
- Win/lose modal with: Wikipedia image fetch, mineral name, formula, fun fact, mindat.org link
- Confetti particles on win
- Guess counter, "Already guessed" hint, empty state prompt
- Staggered flip-in cell animation, shake on invalid guess

### What minerals.json has that index.html does not yet use
The `minerals.json` file is fully up to date with 16 fields per mineral.
The inlined MINERALS array in `index.html` only has 13 fields — it is missing:
- `nerdFact` — obscure/technical fact for hover display
- `etymology` — origin of the mineral's name
- `associations` — array of 5 minerals commonly found alongside it
- `habit` — typical crystal form / growth habit

These fields must be synced into the inlined array as part of PROMPT 7.

### Birefringence note
`minerals.json` includes `"N/A (Opaque)"` as a valid birefringence value for 21
opaque metallic minerals. The `BIRE_ORDER` array in `index.html` currently reads:
```javascript
const BIRE_ORDER = ["None", "Weak", "Moderate", "Strong", "Extreme"];
```
This must be updated. Opaque minerals cannot be compared directionally to
transparent ones. Use this logic:
- If either guess or answer is `"N/A (Opaque)"` → treat as categorical:
  green if both are `"N/A (Opaque)"`, red otherwise (no arrow)
- Otherwise use the ordered comparison as before

---

## Project Structure

```
crystdle/
├── index.html        ← Single-file app (HTML + CSS + inlined JS + inlined minerals)
├── minerals.json     ← Source of truth for mineral data (16 fields)
└── GEMINI.md         ← This file
```

---

## Game Spec (complete reference)

### The 7 Property Columns

| Column | Property | Type | Match Logic |
|--------|----------|------|-------------|
| 1 | **Hardness** | Numeric (Mohs) | 🟢 exact · ↑↓ directional |
| 2 | **Specific Gravity** | Numeric | 🟢 within ±0.3 · ↑↓ directional |
| 3 | **Luster** | Categorical | 🟢 / 🔴 |
| 4 | **Crystal System** | Categorical | 🟢 / 🔴 |
| 5 | **Chemical Group** | Categorical | 🟢 / 🔴 |
| 6 | **Birefringence** | Ordered categorical | 🟢 exact · ↑↓ directional · 🔴 if either is N/A (Opaque) and not matching |
| 7 | **Optical Class** | Categorical | 🟢 / 🔴 |

### Controlled vocabulary

**birefringence:** N/A (Opaque) · None · Weak · Moderate · Strong · Extreme

**luster:** Metallic · Vitreous · Resinous · Adamantine · Pearly · Silky · Greasy · Waxy · Earthy · Submetallic

**crystalSystem:** Isometric · Tetragonal · Orthorhombic · Monoclinic · Triclinic · Hexagonal

**chemicalGroup:** Native Element · Sulfide · Oxide · Hydroxide · Halide · Carbonate · Sulfate · Phosphate · Tectosilicate · Phyllosilicate · Inosilicate · Cyclosilicate · Sorosilicate · Nesosilicate

**opticalClass:** Isotropic · Uniaxial (+) · Uniaxial (-) · Biaxial (+) · Biaxial (-) · Opaque (Isotropic) · Opaque (Anisotropic)

---

## Mineral Data Schema (full — 16 fields)

```json
{
  "name": "Pyrite",
  "formula": "FeS₂",
  "hardness": 6.0,
  "specificGravity": 5.0,
  "luster": "Metallic",
  "crystalSystem": "Isometric",
  "chemicalGroup": "Sulfide",
  "birefringence": "N/A (Opaque)",
  "opticalClass": "Opaque (Isotropic)",
  "funFact": "Known as Fool's Gold for its deceptive resemblance to gold.",
  "mindatUrl": "https://www.mindat.org/min-3314.html",
  "wikiTitle": "Pyrite",
  "nerdFact": "Pyrite is actually semiconducting, not truly metallic — its golden lustre comes from the geometry of its FeS₆ octahedra, not from free electrons.",
  "etymology": "From Greek 'pyrites lithos' (fire stone), because it produces sparks when struck with steel.",
  "associations": ["Chalcopyrite", "Sphalerite", "Galena", "Quartz", "Calcite"],
  "habit": "Perfect cubic crystals with striated faces; also pyritohedra (12-faced) and octahedra; framboidal aggregates in sediments."
}
```

---

## Remaining Prompts

### PROMPT 7 — Sync minerals.json into index.html and fix birefringence logic

```
Read minerals.json. It has 82 minerals with 16 fields each:
name, formula, hardness, specificGravity, luster, crystalSystem, chemicalGroup,
birefringence, opticalClass, funFact, mindatUrl, wikiTitle,
nerdFact, etymology, associations, habit.

In index.html, make two changes:

1. REPLACE the entire inlined MINERALS array (the block starting with
   `const MINERALS = [` and ending with the closing `];` before the state object)
   with the full contents of minerals.json as a JavaScript array literal.
   All 16 fields must be present for every mineral.
   Do not modify any data — inline it exactly as it appears in minerals.json.

2. UPDATE the birefringence comparison function to handle N/A (Opaque).
   Replace the existing compareBirefringence() function with this exact code:

   const BIRE_ORDER = ["None", "Weak", "Moderate", "Strong", "Extreme"];

   function compareBirefringence(val, target) {
     if (val === "N/A (Opaque)" || target === "N/A (Opaque)") {
       if (val === target) return { val, status: 'green', icon: '✓' };
       return { val, status: 'red', icon: '✗' };
     }
     const idxVal = BIRE_ORDER.indexOf(val);
     const idxTarget = BIRE_ORDER.indexOf(target);
     if (idxVal === idxTarget) return { val, status: 'green', icon: '✓' };
     return { val, status: 'arrow', icon: idxVal < idxTarget ? '↑' : '↓' };
   }

After saving, confirm:
- The file is valid HTML with no syntax errors
- Spot-check three minerals in the array: Quartz (has nerdFact?),
  Pyrite (birefringence = "N/A (Opaque)"?), Calcite (associations is an array?)
- The game still loads and a guess still works in the browser
```

---

### PROMPT 8 — Give Up button

```
Add a "Give Up" button to index.html.

PLACEMENT:
In the .input-row div (which already contains the input-wrap and #guessBtn),
add a second button immediately after #guessBtn:
  <button id="giveUpBtn" aria-label="Give up and reveal answer">GIVE UP</button>

STYLING:
- color: #c46060
- border: 1px solid #c46060
- background: transparent
- border-radius, padding, font-family (Cinzel 900), font-size: same as #guessBtn
- On hover: background rgba(196,96,96,0.15), no transform
- Disabled state: opacity 0.35, cursor not-allowed
- On mobile (< 600px): both buttons share equal width in a flex row, gap 8px

STATE:
Add gaveUp: false to the state object.
Reset to false in initGame().

LOGIC:
- On page load and after initGame(), set giveUpBtn.disabled = true
- After the first valid guess is submitted, set giveUpBtn.disabled = false
- On click: if state.gameOver or state.guesses.length === 0, return
- Set state.gaveUp = true
- Call endGame(false) immediately

MODAL:
In endGame(), where modalResultLabel.textContent is set:
  if (state.gaveUp)       → "YOU GAVE UP"
  else if (won)           → "IDENTIFIED"
  else                    → "THE ANSWER WAS"
All other modal content unchanged.

CLEANUP:
In initGame(), also reset: giveUpBtn.disabled = true
```

---

### PROMPT 9 — Progressive hints system

```
Add a progressive hints system to index.html.

OVERVIEW:
After guess 5, 7, and 9, a "💡 Hint Available" button appears.
Each click reveals one hint card. Hints are cumulative and stay visible.

Hint 1 (available after guess 5):  answer.habit         Crystal habit
Hint 2 (available after guess 7):  answer.associations  Associated minerals
Hint 3 (available after guess 9):  answer.etymology     Name etymology

HTML — add this div between #inputArea and .legend:
  <div id="hintArea"></div>

STATE — add to state object:
  hintsRevealed: 0
Reset to 0 in initGame(). Also clear hintArea innerHTML in initGame().

CSS:

#hintArea {
  width: 100%;
  max-width: 600px;
  display: flex;
  flex-direction: column;
  gap: 8px;
  margin-bottom: 1rem;
}

.hint-card {
  background: rgba(200, 168, 75, 0.06);
  border: 1px solid rgba(200, 168, 75, 0.2);
  border-left: 3px solid var(--accent-gold);
  border-radius: 4px;
  padding: 12px 16px;
  animation: slideIn 0.4s ease forwards;
}

@keyframes slideIn {
  from { opacity: 0; transform: translateX(-12px); }
  to   { opacity: 1; transform: translateX(0); }
}

.hint-label {
  font-family: var(--font-display);
  font-size: 0.65rem;
  color: var(--text-muted);
  text-transform: uppercase;
  letter-spacing: 0.12em;
  margin-bottom: 4px;
}

.hint-value {
  font-family: var(--font-body);
  font-size: 1rem;
  color: var(--text-main);
  line-height: 1.5;
}

#hintBtn {
  align-self: flex-start;
  background: transparent;
  color: var(--accent-gold);
  border: 1px solid var(--accent-gold);
  border-radius: 4px;
  padding: 8px 20px;
  font-family: var(--font-display);
  font-weight: 600;
  font-size: 0.85rem;
  cursor: pointer;
  animation: hintPulse 1.5s ease-in-out 3;
}

@keyframes hintPulse {
  0%, 100% { box-shadow: 0 0 0 0 rgba(200,168,75,0.4); }
  50%      { box-shadow: 0 0 0 6px rgba(200,168,75,0); }
}

JAVASCRIPT — add these constants near the top of the script block:

const HINT_DEFS = [
  { threshold: 5, label: 'Crystal Habit',           field: 'habit' },
  { threshold: 7, label: 'Commonly Associated With', field: 'associations' },
  { threshold: 9, label: 'Etymology',                field: 'etymology' }
];

Add this function:

function checkHints() {
  if (state.gameOver) return;
  const count = state.guesses.length;
  const next = HINT_DEFS[state.hintsRevealed];
  if (next && count >= next.threshold) {
    renderHintButton();
  }
}

function renderHintButton() {
  if (document.getElementById('hintBtn')) return; // already showing
  const btn = document.createElement('button');
  btn.id = 'hintBtn';
  btn.textContent = '💡 Hint Available — click to reveal';
  btn.addEventListener('click', () => {
    btn.remove();
    revealNextHint();
  });
  document.getElementById('hintArea').appendChild(btn);
}

function revealNextHint() {
  const def = HINT_DEFS[state.hintsRevealed];
  if (!def) return;
  const rawVal = state.answer[def.field];
  const val = Array.isArray(rawVal) ? rawVal.join(' · ') : rawVal;

  const card = document.createElement('div');
  card.className = 'hint-card';
  card.innerHTML =
    `<div class="hint-label">${def.label}</div>` +
    `<div class="hint-value">${val}</div>`;
  document.getElementById('hintArea').appendChild(card);

  state.hintsRevealed++;
  checkHints(); // immediately check if next threshold already passed
}

Call checkHints() at the end of evaluateGuess(), after renderRow().

MODAL HINT SECTION:
Add to modal HTML after #modalFunFact, before #modalScoreLine:
  <div id="modalHints"></div>

CSS for #modalHints hint rows (inside modal):
  border-top: 1px solid rgba(255,255,255,0.06);
  padding-top: 0.75rem;
  margin-top: 0.75rem;
  text-align: left;
  .hint-label same style as above
  .hint-value same style as above

In endGame(), after setting modalFunFact, call populateModalHints():

function populateModalHints() {
  const container = document.getElementById('modalHints');
  container.innerHTML = '';
  const guessCount = state.guesses.length;
  HINT_DEFS.forEach(def => {
    if (guessCount >= def.threshold || state.gaveUp) {
      const rawVal = state.answer[def.field];
      const val = Array.isArray(rawVal) ? rawVal.join(' · ') : rawVal;
      const row = document.createElement('div');
      row.innerHTML =
        `<div class="hint-label" style="margin-top:0.5rem">${def.label}</div>` +
        `<div class="hint-value">${val}</div>`;
      container.appendChild(row);
    }
  });
}

Also clear #hintArea and reset state.hintsRevealed = 0 in initGame().
```

---

### PROMPT 10 — nerdFact hover tooltip on mineral name in modal

```
Add a nerdFact tooltip to the mineral name in the completion modal.

BEHAVIOUR:
Hovering (desktop) or tapping (mobile) the mineral name in the modal shows
a tooltip below it containing state.answer.nerdFact.
A small indicator "🔬 hover for nerd fact" sits beneath the name as a hint.

HTML — replace the existing #modalMineralName div with:

<div id="modalNameWrap" style="position:relative; display:inline-block; cursor:help; margin-bottom:0.5rem;">
  <div id="modalMineralName"></div>
  <div class="nerd-hint-indicator">🔬 hover for nerd fact</div>
  <div id="nerdTooltip" class="nerd-tooltip" role="tooltip"></div>
</div>

CSS:

.nerd-hint-indicator {
  font-family: var(--font-display);
  font-size: 0.6rem;
  color: var(--text-muted);
  letter-spacing: 0.1em;
  text-transform: uppercase;
  margin-top: 4px;
}

.nerd-tooltip {
  position: absolute;
  top: calc(100% + 10px);
  left: 50%;
  transform: translateX(-50%);
  background: #1a1a22;
  border: 1px solid rgba(200, 168, 75, 0.4);
  border-radius: 4px;
  padding: 12px 16px;
  max-width: 340px;
  width: max-content;
  font-family: var(--font-body);
  font-size: 0.95rem;
  line-height: 1.6;
  color: var(--text-main);
  text-align: left;
  box-shadow: 0 8px 24px rgba(0,0,0,0.4);
  z-index: 10;
  opacity: 0;
  pointer-events: none;
  transition: opacity 0.2s ease;
  white-space: normal;
}

.nerd-tooltip::before {
  content: '';
  position: absolute;
  top: -6px;
  left: 50%;
  transform: translateX(-50%);
  border: 6px solid transparent;
  border-bottom-color: rgba(200, 168, 75, 0.4);
}

.nerd-tooltip.visible {
  opacity: 1;
  pointer-events: auto;
}

JAVASCRIPT:

In endGame(), after setting modalMineralName.textContent, add:
  document.getElementById('nerdTooltip').textContent = state.answer.nerdFact || '';

Add event listeners (set these up once after DOMContentLoaded, not inside endGame):

const nameWrap = document.getElementById('modalNameWrap');
const tooltip  = document.getElementById('nerdTooltip');

nameWrap.addEventListener('mouseenter', () => tooltip.classList.add('visible'));
nameWrap.addEventListener('mouseleave', () => tooltip.classList.remove('visible'));
nameWrap.addEventListener('touchstart', (e) => {
  e.preventDefault();
  tooltip.classList.toggle('visible');
});
document.addEventListener('click', (e) => {
  if (!nameWrap.contains(e.target)) tooltip.classList.remove('visible');
});

In initGame(), add: tooltip.classList.remove('visible');
```

---

### PROMPT 11 — Final QA pass

```
Do a complete review of index.html. Check and fix each item:

1. MINERALS ARRAY
   - Confirm 82 minerals present (count the entries)
   - Confirm all 16 fields on every entry
   - Spot-check: Quartz (nerdFact present?), Pyrite (birefringence = "N/A (Opaque)"?),
     Calcite (associations is a JS array of 5 strings?), Tourmaline (habit present?)

2. BIREFRINGENCE
   - N/A (Opaque) vs non-opaque → red ✗, no arrow
   - N/A (Opaque) vs N/A (Opaque) → green ✓
   - Weak vs Moderate → ↑ arrow
   - Extreme vs Strong → ↓ arrow

3. GIVE UP BUTTON
   - Disabled on load, enabled after first guess
   - Shows "YOU GAVE UP" in modal result label
   - Play Again re-disables it
   - Styled correctly on mobile

4. HINTS
   - No hint button before guess 5
   - Hint button appears with pulse animation at guess 5, 7, 9
   - Clicking reveals correct card content
   - associations displays as "A · B · C · D · E"
   - Modal shows all available hints regardless of whether player clicked them
   - initGame() clears hintArea and resets hintsRevealed to 0

5. NERD FACT TOOLTIP
   - nerdFact text loads from the minerals array (not hardcoded)
   - Hover shows/hides on desktop
   - Tap toggles on mobile
   - Clicking outside dismisses it
   - Hidden on initGame()

6. GENERAL CLEANUP
   - No console.logs
   - Valid HTML5 (DOCTYPE, lang, charset, viewport)
   - Google Fonts link intact
   - Modal mindat link populates correctly
   - Wikipedia image fetch has try/catch
   - File is not truncated — ends with </html>

Deliver the complete final index.html.
```

---

## Tips for Working in Gemini CLI

- **Start at PROMPT 7.** The game already works — do not re-run Prompts 1–6.
- **PROMPT 7 is the riskiest step** — it replaces the entire inlined data array. After running it, load the page and make one guess before continuing to PROMPT 8.
- **After each prompt**, open `index.html` in a browser to test the specific feature before running the next prompt.
- **If Gemini truncates the file**, follow up with: *"The file was cut off after [last line you can see]. Please continue from exactly that point without repeating any content above it."*
- **minerals.json is the source of truth.** If the inlined data looks wrong after PROMPT 7, re-run PROMPT 7.
- **To test hints manually**: open the browser console and run `state.guesses.push("Quartz","Calcite","Pyrite","Galena","Halite")` — the hint button should appear immediately.

---

## Quick Reference: Key minerals for testing

| Mineral | Birefringence | Expected behaviour |
|---------|--------------|-------------------|
| Pyrite | N/A (Opaque) | Red ✗ vs any transparent; green ✓ vs other opaques |
| Calcite | Extreme | ↑ from Strong or below, ↓ never (it's the top) |
| Quartz | Weak | ↑ from None, ↓ from Moderate, Strong, Extreme |
| Fluorite | None | ↑ never (bottom of scale), ↓ from any higher value |
| Talc | Strong | ↑ from Moderate and below, ↓ from Extreme |

## Field reminder — what each new field is used for

| Field | Used in |
|-------|---------|
| `nerdFact` | Hover tooltip on mineral name in completion modal (PROMPT 10) |
| `habit` | Hint 1, revealed after guess 5 (PROMPT 9) |
| `associations` | Hint 2, revealed after guess 7 (PROMPT 9) |
| `etymology` | Hint 3, revealed after guess 9 (PROMPT 9) |
