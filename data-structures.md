# Field Companion — Data Structure Design

Three layers, each with a different lifetime and purpose:

| Layer | What it is | Who edits it | Lifetime |
|---|---|---|---|
| **Setting Template** | A genre content pack — name pools, terrain types, encounter/quest tables, oracle prompts, tension defaults | Authored once, maybe shared/downloaded | Reused across many games |
| **Map Template** | A recipe (or fixed layout) for generating/loading the hex grid at game start | Authored once per setting, or generated fresh | Reused across many games, or single-use |
| **Save Game** | One player's actual playthrough — explored hexes, NPCs met, quests tracked, tension state | Constantly, during play | One per campaign |

The key design decision underneath all of this: **saves should be self-contained.** A save embeds everything it needs to run offline forever, even if the original template is edited, deleted, or never touched again. Templates are the "character sheet blank," saves are the "filled-in sheet" — copying data across at creation time, not living references.

---

## 1. Setting Template

```json
{
  "templateId": "hollow-reach",
  "templateVersion": 1,
  "schemaVersion": 1,
  "meta": {
    "name": "The Hollow Reach",
    "genre": "low-fantasy",
    "author": "you",
    "description": "A damp, half-forgotten borderland."
  },

  "terrain": {
    "plains":     { "label": "Plains",     "color": "#cfc596", "weight": 24, "icon": "plains" },
    "forest":     { "label": "Forest",     "color": "#5b6b47", "weight": 22, "icon": "forest" },
    "hills":      { "label": "Hills",      "color": "#b89a64", "weight": 15, "icon": "hills" },
    "mountains":  { "label": "Mountains",  "color": "#7a6e63", "weight": 10, "icon": "mountains" },
    "water":      { "label": "Water",      "color": "#3f6363", "weight": 11, "icon": "water" },
    "swamp":      { "label": "Swamp",      "color": "#6a6b47", "weight": 8,  "icon": "swamp" },
    "ruins":      { "label": "Ruins",      "color": "#9c8b6b", "weight": 6,  "icon": "ruins" },
    "settlement": { "label": "Settlement", "color": "#a8763e", "weight": 4,  "icon": "settlement" }
  },

  "namePools": {
    "npcFirstNames": ["Brennic", "Sera", "Oddvan", "..."],
    "npcSurnames": ["Ashford", "Quillwright", "..."],
    "professions": ["hedge healer", "ferryman", "..."],
    "traits": ["counts everything under their breath", "..."],
    "settlementNames": ["Greywick", "Tarnhollow", "..."],
    "ruinNames": ["the Sunken Hall", "the Broken Choir", "..."]
  },

  "questTemplates": {
    "verbs": ["retrieve", "destroy", "deliver", "..."],
    "items": ["a sealed ledger", "the Last King's signet", "..."]
  },

  "oraclePrompts": [
    "A resource you depend on turns out to be insufficient.",
    "Someone here recognizes you — for better or worse."
  ],

  "encounterTables": {
    "generic":    [ { "tag": "Quiet",     "text": "The way is empty..." } ],
    "plains":     [ { "tag": "Traveler",  "text": "A trade caravan crosses your path..." } ],
    "forest":     [ { "tag": "Sign",      "text": "The canopy thins..." } ],
    "settlement": [ { "tag": "Traveler",  "text": "Locals eye you..." } ]
  },

  "tensionDefaults": {
    "startingValue": 12,
    "step": 12,
    "bleedTo": 8,
    "ceiling": 95
  }
}
```

**Notes:**
- `encounterTables` keys match `terrain` keys, plus a `generic` bucket that's always pooled in regardless of terrain — this is exactly what's hardcoded in the prototype right now, just externalized.
- Every list here is meant to be *appended to or swapped wholesale* per genre. A "modern day" template would replace `namePools`, `terrain` (maybe `urban`/`suburb`/`highway`/`wilderness`), and `encounterTables`, but the *shape* of the schema stays identical — that's what makes templates swappable without touching app logic.
- `templateId` + `templateVersion` exist so a save can record exactly which version of a template it was created from (see §3), even after the template itself changes later.

---

## 2. Map Template

Two modes worth supporting, since you'll likely want both eventually:

- **`generated`** — a recipe: radius, terrain weighting, a center hex override. This is what the prototype does now (randomized on "New Map").
- **`authored`** — a fixed, hand-placed layout: specific hexes with specific terrain/names locked in, for a setting where you want a *designed* map (a particular dungeon region, a specific city and its surroundings) rather than a random one.

```json
{
  "mapTemplateId": "hollow-reach-default",
  "mapTemplateVersion": 1,
  "schemaVersion": 1,
  "settingTemplateId": "hollow-reach",
  "mode": "generated",

  "shape": "hex-radius",
  "radius": 4,
  "hexSize": 30,

  "centerHex": {
    "terrain": "settlement",
    "name": "Hollow's Watch",
    "explored": true
  },

  "seed": null,
  "terrainWeightOverrides": null,

  "authoredHexes": []
}
```

For an `authored` map, `mode` flips and `authoredHexes` is populated instead:

```json
"mode": "authored",
"authoredHexes": [
  { "q": 0, "r": 0, "terrain": "settlement", "name": "Hollow's Watch", "explored": true, "locked": true },
  { "q": 1, "r": -1, "terrain": "ruins", "name": "the Sunken Hall", "explored": false, "locked": true }
]
```

`locked: true` means "don't let procedural fill-in touch this hex" — useful if you want to hand-place a few key locations (a capital, a landmark ruin) but let everything else around them generate randomly. That gives you a hybrid mode for free without a third `mode` value: `generated` maps can still read `authoredHexes` as pinned overrides applied *after* the random pass.

**On `seed`:** worth including even now — a stored RNG seed means "regenerate this exact map" is possible later (for sharing a starting map with someone else, or for a "regenerate but keep my current campaign's history" feature). Can stay `null`/unused until you want it.

---

## 3. Save Game

This is the big one — the live, mutable state. It embeds a **snapshot** of the setting/map template content it was built from (not just an ID reference), so the save keeps working even offline, forever, regardless of what happens to the original template file.

```json
{
  "saveId": "save_9f2a1c",
  "schemaVersion": 1,
  "createdAt": "2026-07-01T14:20:00Z",
  "updatedAt": "2026-07-01T16:05:00Z",

  "campaignName": "The Hollow Reach — Solo Run",

  "provenance": {
    "settingTemplateId": "hollow-reach",
    "settingTemplateVersion": 1,
    "mapTemplateId": "hollow-reach-default",
    "mapTemplateVersion": 1
  },

  "contentSnapshot": {
    "terrain": { "...": "copied in full from the setting template at creation time" },
    "namePools": { "...": "..." },
    "questTemplates": { "...": "..." },
    "oraclePrompts": [ "..." ],
    "encounterTables": { "...": "..." }
  },

  "map": {
    "radius": 4,
    "hexSize": 30,
    "current": { "q": 0, "r": 0 },
    "hexes": [
      {
        "id": "hex_0_0",
        "q": 0, "r": 0,
        "terrain": "settlement",
        "explored": true,
        "name": "Hollow's Watch",
        "notes": ""
      }
    ]
  },

  "tension": {
    "value": 24,
    "step": 12,
    "bleedTo": 8,
    "ceiling": 95,
    "pending": [
      "Someone here recognizes you — for better or worse."
    ]
  },

  "npcs": [
    {
      "id": "npc_7bd2",
      "name": "Sera Ashford",
      "profession": "relic dealer",
      "trait": "collects small bones",
      "locationHexId": "hex_1_-1",
      "tags": ["merchant", "quest target"],
      "notes": "Owes the player a favor after the bridge incident.",
      "image": null
    }
  ],

  "quests": [
    {
      "id": "q_44de",
      "title": "A sealed ledger",
      "hook": "Sera Ashford wants you to retrieve a sealed ledger, last known to be near the Sunken Hall.",
      "status": "active",
      "npcId": "npc_7bd2",
      "locationHexId": "hex_2_-1"
    }
  ],

  "eventLog": [
    { "id": "evt_0091", "timestamp": "2026-07-01T16:05:00Z", "tag": "ENCOUNTER", "text": "Ruins (the Sunken Hall) — Old stonework still bears marks worth puzzling over." },
    { "id": "evt_0090", "timestamp": "2026-07-01T16:04:10Z", "tag": "COMPLICATION", "text": "A piece of information you trusted turns out to be wrong." }
  ]
}
```

### The one real bug-fix baked into this schema: ID-based linking

Right now the prototype links NPCs and quests to hexes by matching **name strings** — rename a hex and the link silently breaks. The save schema fixes this by giving every hex, NPC, and quest a stable `id`, and having NPCs/quests reference `locationHexId` / `npcId` instead of a display name. Names become purely cosmetic — free to rename anytime without breaking anything. I'd fold this fix into the prototype next regardless of what else changes.

### Why embed a `contentSnapshot` instead of just referencing the template?

- **Offline-safe**: no dependency on the template file still existing or being unchanged months later.
- **Editable mid-campaign**: if you tweak your NPC name pool in the template after starting a game, your existing save won't suddenly start generating inconsistent names — it keeps using what it started with, and only *new* games pick up the changes.
- Trade-off: bigger save files, and template bugfixes don't retroactively reach old saves. That seems like the right trade for a single-player offline tool — worth flagging in case you'd rather saves stay lightweight references instead.

### Images

`image` on an NPC is shown as a possible base64 data URL inline, matching how the prototype currently stores it (in-memory `FileReader` result). That's fine for a prototype but will bloat save files fast at real scale — if this becomes a real app with file-system access, storing images as separate files with the NPC record holding a relative path/filename would scale much better. Worth deciding once you know the actual storage layer (flat JSON file vs. real DB vs. IndexedDB, etc.).

---

## Open questions worth deciding before wiring this in

1. **Multiple saves per setting?** (e.g., "New Game" vs "Continue" vs multiple parallel campaigns in the same setting) — schema above already supports it trivially since each save is independent, just flagging it as a UI decision, not a data one.
2. **Template sharing/export** — do you want setting templates to be shareable files someone else could drop in, or always just built into the app? Affects whether `templateId` needs to be globally unique/namespaced now.
3. **Save file granularity** — one JSON file per save (simplest, matches this doc), or split into multiple files/tables (map, npcs, quests separately)? Single-file is simplest for an offline tool and is what I'd default to.

Let me know which of these you want to nail down, or if you'd like me to go ahead and refactor the prototype to use ID-based linking and snapshot-style save/load (with a working "Export Save" / "Load Save" button using the browser file APIs) as the next step.
