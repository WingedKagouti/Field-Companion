# Field Companion

> **Solo Play Toolkit • Bookkeeping & Oracle, Not Arbitration**

Field Companion is a lightweight, browser-based toolkit for solo Tabletop Role-Playing Games. It handles procedural generation, bookkeeping, and oracle prompts — but never makes decisions for you. Interpretation and storytelling remain entirely yours.

Built as a single self-contained HTML file using vanilla web technologies. No internet connection, no installation, no server backend required.

---

## 🚀 Getting Started

1. Download `Field Companion.html` to your local machine.
2. Open it in any modern browser (Chrome, Edge, Firefox, Safari).
3. Click **New Game**, optionally load a custom Setting Template and/or Map Template, and start exploring. The built-in *Hollow Reach* setting launches immediately with no configuration needed.

---

## 💻 System Requirements

Field Companion runs entirely in the browser with no installation. However, some features rely on web technologies that older browsers do not support. This is most relevant if you are copying the file to an offline or air-gapped machine via USB stick or similar, where updating the browser may not be straightforward.

**Recommended:** Any browser released from early 2022 onward.

| Browser | Minimum version | Released |
|---|---|---|
| Chrome / Chromium-based (Edge, Brave, Opera, Vivaldi) | 99+ | Early 2022 |
| Firefox | 98+ | March 2022 |
| Safari | 15.4+ | March 2022 |
| iOS Safari | iOS 15.4+ | March 2022 |

The binding constraint is the `<dialog>` element, used for the New Game, Journal, and theme overlay screens. Safari and Firefox only added stable support in March 2022; Chrome had it earlier but Chromium-based browsers varied. On older versions the rest of the app will load and most features will work, but any overlay dialog will silently fail.

If you are on an older system and cannot update, Chrome 80+ (early 2020) or Firefox 90+ (mid 2021) will cover most functionality — the main losses are the New Game configuration screen, the Journal, and the theme loader. Everything else including map generation, NPC/quest tools, dice, and tension will work normally.

---

## ⬇ Download

| Version | Link |
|---|---|
| Latest stable release | [Download from GitHub Releases](https://github.com/YOUR_USERNAME/YOUR_REPO/releases/latest/download/Field%20Companion.html) |
| Current development build | [View on GitHub](https://github.com/YOUR_USERNAME/YOUR_REPO/blob/main/Field%20Companion.html) (use the download button) |

---

## 🗺️ Hex Map

- Flat-top axial hex grid with fog of war. Move hex by hex to reveal terrain, or fast-travel directly to any previously explored location.
- **Multiple maps per campaign** — load additional Map Templates mid-campaign (a dungeon, a city district, a castle interior). A tab switcher appears automatically when more than one map is active.
- Click any hex to inspect it without moving. The detail panel shows linked NPCs, linked quests, and free-text notes. Rename any hex, change its terrain type, and save notes — all without leaving the map view.
- **Markers** — small dots appear at the bottom of explored hexes to indicate content at a glance: amber for a linked NPC, teal for a linked quest, grey for a saved note.
- Debug tools: Reveal All and Reset Fog for quick testing.

---

## ⚡ Event / Encounter

Generates a contextual encounter prompt drawn from a three-tier pool:

1. **Setting-wide** generic encounters (from the Setting Template)
2. **Map-wide** generic encounters (from the active Map Template)
3. **Terrain-specific** encounters for the current hex type

If complications have built up in the Tension meter, they surface here alongside the encounter — keeping them tied to the moment rather than interrupting play mid-scene.

---

## ⏱️ Tension

An SVG dial tracking narrative pressure across the whole campaign.

- Advances automatically when you move or travel, and can be checked manually at any time.
- **Per-map modifier** (`tensionModifier` in the Map Template) scales how fast tension climbs on a given map — dungeons run hotter, safe settlements run cooler.
- When tension triggers, a complication is queued (drawn from the Setting Template's `complications` list) and held until the next generated event.
- **Frequency slider** lets you tune the step size (Rare / Average / Frequent) to taste during play.
- The modifier for the active map is displayed on the dial when it differs from 1.0×.

---

## 🎲 Dice

Standard dice roller with configurable count, die type (d4–d100), and a +/- modifier. Individual dice are shown as chips — green for maximum rolls, red for minimum — alongside the final sum.

---

## 👥 Characters & World

A tabbed panel covering all campaign entities.

### NPCs
- Generator draws from the Setting Template's name pools, professions, and traits.
- Each NPC can be linked to a map hex, affiliated with one or more Organisations, and assigned a Nation.
- Supports free-text notes, comma-separated tags, and an optional portrait (URL or local file upload with automatic compression).
- Full inline editing after saving to the roster.

### Quests
- **Generated hooks** combine a verb, an item, an NPC, and a location from the current explored world.
- **Manual creation** for quests you have in mind — set title, description, status, linked NPC, and location directly.
- Full inline editing. Status tracks Active / Complete / Failed.

### Orgs (Organisations)
- Simple records: name and description.
- Add and edit freely during play, or seed them from the Setting Template.
- Appear as clickable affiliation pills on NPC forms.

### Nations
- Same structure as Organisations — name and description.
- Appear as a single-select on NPC forms (a character belongs to one nation).

**Referential integrity:** Deleting an NPC, Organisation, or Nation automatically cleans up any references to it held by quests, NPC affiliation arrays, and open draft forms — no orphaned IDs reach the save file.

---

## 📓 Journal

A free-text notepad for session summaries, narrative notes, or anything else. Opened via the **Journal** button in the header. Saved and restored with the campaign file.

---

## 💾 Save / Load

Progress is saved as a plain `.json` file downloaded to your machine. Load it back at any time to resume exactly where you left off.

The save file is fully self-contained: it embeds a snapshot of the Setting Template and all Map Templates it was created from, so it remains playable even if the original template files are edited or deleted. Save files carry `schemaVersion: 2`.

**Note:** Version 1 save files (from earlier builds) are not compatible with the current format.

---

## 🎨 Themes

Load a CSS theme file to restyle the UI colour palette without affecting layout or functionality.

A theme file may contain only a `:root { }` block with overrides for the allowed set of CSS custom properties, and an optional `body { background: … }` rule. Any other content is rejected with a specific error. Themes are stored in the save file and restored on load.

**Allowed properties:**
`--ink`, `--ink-soft`, `--parchment`, `--parchment-deep`, `--parchment-panel`, `--brass`, `--brass-deep`, `--rust`, `--moss`, `--teal`, `--gold`, `--line`

**Example theme file (dark mode):**
```css
:root {
  --ink: #e8dfc0;
  --ink-soft: #a89878;
  --parchment: #0f111a;
  --parchment-deep: #05070c;
  --parchment-panel: #171a26;
  --rust: #ff0055;
  --line: #334466;
}
body {
  background: #0f111a !important;
}
```

Use **Reset Theme** to return to the default appearance without reloading the page.

---

## 🛠️ Customising with Templates

Field Companion separates reusable content from live campaign data through two template types, both of which are plain `.json` files and can be created or edited in any text editor.

### Setting Template

Defines the world's content: NPC name pools, professions, traits, quest verbs and items, oracle complications, setting-wide generic encounters, tension defaults, and optional seed data for Organisations and Nations.

Key fields:
```json
{
  "templateId": "my-setting",
  "meta": { "name": "My Setting", "genre": "..." },
  "namePools": { "npcFirstNames": [...], "npcSurnames": [...], "professions": [...], "traits": [...], "settlementNames": [...], "ruinNames": [...] },
  "questTemplates": { "verbs": [...], "items": [...] },
  "complications": [...],
  "genericEncounters": [ { "tag": "...", "text": "..." } ],
  "organisations": [ { "id": "...", "name": "...", "description": "..." } ],
  "nations": [ { "id": "...", "name": "...", "description": "..." } ],
  "tensionDefaults": { "startingValue": 12, "step": 12, "bleedTo": 8, "ceiling": 95 }
}
```

### Map Template

Defines a specific map's terrain types, encounter tables, visual appearance, and tension behaviour. One per map (wilderness region, dungeon, city district, etc.).

Key fields:
```json
{
  "mapTemplateId": "my-dungeon",
  "meta": { "name": "The Undercroft", "description": "..." },
  "mode": "generated",
  "radius": 4,
  "hexSize": 30,
  "tensionModifier": 1.8,
  "centerHex": { "terrain": "chamber", "name": "Entry Hall", "explored": true },
  "terrain": {
    "corridor": { "label": "Corridor", "color": "#3a3530", "weight": 30, "icon": "passage" },
    "chamber":  { "label": "Chamber",  "color": "#5a4e42", "weight": 20, "icon": "chamber", "iconColor": "#4a3e32" },
    "vault":    { "label": "Vault",    "color": "#6a5e52", "weight": 5,  "icon": "bighouse", "namePool": "vaultNames" }
  },
  "encounterTables": {
    "generic":  [ { "tag": "Drip",   "text": "Water echoes somewhere in the dark." } ],
    "corridor": [ { "tag": "Patrol", "text": "Footsteps approach from down the passage." } ]
  }
}
```

**Authored maps** (`"mode": "authored"`) define specific hex layouts. A generated map with `authoredHexes` entries applies those as pinned overrides after the random fill, giving a hybrid approach.

**Icon system:** Terrain entries reference built-in SVG icon shapes by name. `iconColor` overrides the default colour of the icon shape.

Built-in shape names:

| Group | Shapes |
|---|---|
| Wilderness | `plains`, `forest`, `hills`, `mountains`, `water`, `swamp`, `ruins`, `settlement` |
| City / buildings | `house`, `bighouse`, `shop`, `temple` |
| Cave / dungeon | `chamber`, `passage`, `chasm`, `stairs`, `pool`, `pillar` |

Set `"iconUrl"` to any image URL or data URI to use a fully custom tile image instead.

**`namePool`** — optional string. When a hex of this terrain type is first revealed, its auto-generated name is drawn from the named pool in the Setting Template's `namePools` object. For example, `"namePool": "settlementNames"` draws from `content.namePools.settlementNames`. Any pool key defined in the Setting Template can be used — this means custom terrain types like `"village"`, `"dungeon-entrance"`, or `"tavern"` can have their own name lists without any hardcoded assumptions about terrain key names. Omit the field to fall back to the terrain label.

---

## 📄 License & Technical Stack

**Stack:** Vanilla HTML5, CSS3, JavaScript (ES6+), inline SVGs. No npm packages, CSS frameworks, or runtime dependencies.

**License:**

- **Modification and Derivative Works:** You are free to copy, modify, distribute, and build upon this software, its templates, or its underlying structures for any purpose, including commercial products (custom TTRPG settings, modules, or derivative toolkits).
- **Original Software Restriction:** You may not sell, redistribute for profit, or commercialise the original, unmodified `Field Companion.html`, `Example Theme.css`, `MapTemplate.json`, `Authored Map Example.json`, or `SettingTemplate.json` as standalone products.
- **Attribution:** Any derivative works must include a credit line acknowledging this original project.

*THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.*
