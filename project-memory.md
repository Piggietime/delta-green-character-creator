# Project Memory
Last updated: 2026-04-10

This file captures decisions, reasoning, and session context that
project-context.md doesn't hold. It is Claude's memory between sessions.

---

## Session — 2026-04-15

**Focus:** Character portrait feature — upload and AI generation.

**Decisions made:**
- Portrait upload feature implemented: FileReader converts uploaded image to base64; stored in `portraitData` state var; included in localStorage saves and JSON export; excluded from URL sharing (base64 too large); displayed in Personal Data section of character sheet with "PHOTO" placeholder when none set
- AI portrait generation attempted via Pollinations.ai — abandoned due to server blocking requests from localhost (HTTP 500 with message "authenticated users should use enter.pollinations.ai; this legacy endpoint is for anonymous requests only"); incognito window also failed, confirming it is not a cookie issue but a localhost/referrer block
- Switched to Stable Horde (stablehorde.net) as alternative — free, anonymous, community-run GPU network; uses async polling pattern (POST to submit, GET /check/{id} every 4s, GET /status/{id} when done); image returned as base64 in `generations[0].img`; MIME type detected from base64 magic bytes; still failed to display correctly (broken image link — MIME type detection did not resolve the issue)
- AI image generation scrapped entirely for now — all Pollinations and Stable Horde code removed; portrait section restored to upload-only

**Approaches discussed:**
- Pollinations.ai (free, no API key, URL-based): blocked from localhost by their server
- Stable Horde (free, anonymous API key `0000000000`, async polling): image data returned but could not render correctly in browser
- NotebookLM and Gemini mentioned by user — clarified neither is an image generator; Gemini API (Imagen) noted as an option requiring an API key
- Options requiring API keys (DALL-E, Stability AI, Hugging Face, Replicate) discussed but not pursued

**Left unresolved:**
- AI portrait generation — both services attempted failed; to revisit in a future session with a different approach or service

**Files changed this session:**
 delta_green_character_creator.html | 490 +++++++++++++++++++++++++++++++++----
 project-context.md                 |  31 ++-
 project-memory.md                  |  19 ++

---

## Key decisions (permanent record)

- 2026-04-10 — Project initialized
- 2026-04-10 — Architecture decision: Static frontend (localStorage + URL sharing, no backend) — simplest path, no accounts needed, shareable URLs handle cross-user sharing
- 2026-04-10 — Tech stack chosen: vanilla HTML/CSS/JS, localStorage, Vercel — keep it simple, no framework, build on existing single-file app

---

## Sessions

## Session — 2026-04-14

**Focus:** What's Next list — worked through all remaining to-do items in order.

**Decisions made:**
- Share via URL: character encoded as base64 in `?c=` URL param; import banner shown at top of page when shared URL is opened; `buildCharData()` extracted as shared helper used by both Save and Share
- Git repo initialized with local identity (`delta-green@local`); initial commit `15d20c5` includes all project files
- Edit mode gaps: `bondScores[]` parallel array tracks current bond score (capped 0–CHA); `hpOverride` follows same null=formula pattern as SAN/BP; `newCharacter()` resets all state and returns to step 01 with confirmation prompt
- Improvement rolls: `tickedSkills` object (session-only, not persisted); `toggleTick()` updates DOM in-place without full re-render to preserve scroll; `rollImprovements()` rolls d100 per ticked skill, adds 1d6 on success (roll > current %); results stored in `lastImprovementResults` and rendered at bottom of Skills panel on next render
- Overwrite warning: `saveCharacter()` checks if name key already exists in localStorage before writing; prompts confirm dialog if so
- JSON export: `exportToJSON()` uses Blob + object URL download; filename sanitized from agent name
- JSON import: file input hidden in load modal header; `importFromJSON()` validates `personal` and `stats` fields before applying; loads into edit mode
- Fillable current-value boxes: derived attributes now show starting/max value prominently + "Current: ___" underlined write-in box; edit-mode overrides pre-fill the box; WP always blank; new characters get all blank boxes
- Gear notes on sheet: gear items switched from flex-wrap to 2-column grid; right column is a `.sheet-gear-note-line` underline for qty/condition notes

**Files changed this session:**
- delta_green_character_creator.html — all changes (URL sharing, git init, edit mode gaps, improvement rolls, save/load QoL, sheet improvements)
- project-context.md — What's next updated throughout session

## Session — 2026-04-13 (second session)

**Focus:** Save/Load system, edit-mode Skills tab, adjustable Sanity/Breaking Point, load bug fix.

**Decisions made:**
- Weapons & Equipment sheet section moved to after 12 — Psychological Data (user confirmed correct order)
- Save/Load uses localStorage keyed by character name (`dg_characters` object); Load button lives in the dg-header (top-right, always visible); Save button lives on the Sheet panel next to Print
- `isEditMode` flag (default false) set to true when `loadCharacter()` is called; controls branching in renderBonusSkills() and updateDerived()
- Bonus Skills tab relabels to "Skills" in edit mode — nav button innerHTML updated directly via `step-btn-bonus` id
- Edit-mode Skills tab: no allocation budget, +/- 1 buttons, counter shows net change since last save, cap raises from 80 to 99; separate `skillEdits` object (distinct from creation `bonusAdded`)
- Sanity and Breaking Point show +/- 1 buttons in edit mode; stored as `sanOverride` / `bpOverride` (null = use POW formula); Breaking Point box includes "↺ Recalc BP" button
- `recalcBP()` formula: BP = current SAN − POW score (official Delta Green rule for post-damage recalculation)
- Load bug root cause: `onclick="loadCharacter(${JSON.stringify(name)})"` — JSON.stringify produces double-quoted strings which immediately closed the double-quoted onclick HTML attribute, silently breaking the handler. Fixed by switching to single-quoted onclick attributes.

**Problems solved:**
- Load button did nothing — caused by double-quote collision between onclick attribute and JSON.stringify output; fixed with single-quoted onclick attributes

**Files changed this session:**
- delta_green_character_creator.html — all changes

## Session — 2026-04-13

**Focus:** Rules accuracy (profession data from official source), stat auto-assignment into priority stats, Equipment step, weapons section on character sheet.

**Decisions made:**
- Profession data rewritten to match Agent's Handbook via pigeon-labs-stack/DELTA-GREEN-STATS/professions.js — previous entries had wrong bond counts, wrong skill values, wrong optional lists
- Renamed professions: Firefighter/Paramedic → Firefighter, Journalist → Media Specialist, Lawyer → Lawyer or Business Executive, Pilot → Pilot or Sailor, Soldier + Marine → Soldier or Marine, Nurse → Nurse or Paramedic
- Removed Marine and Sailor as standalone professions (merged into combined entries)
- Added Program Manager as new profession (from official source)
- Added ART_OPTIONS constant and `art` free-type handling for Media Specialist and Program Manager
- Quick Assign and Roll 4D6 now sort values descending and assign highest to priority stats first — non-priority stats fill in natural STAT_KEYS order (STR/CON/DEX/INT/POW/CHA)
- Roll 4D6 now calls `setArray()` instead of assigning directly, so it inherits the same priority-stat logic
- Added Equipment as step 06 (between Bonus Skills and Sheet); Sheet moved to step 07 / panel6
- Weapons data model has 8 fields: name, skill, range, damage, ap (armor piercing), lethality, killRadius, ammo
- WEAPONS_DB pre-populated with 16 weapons; GEAR_LIST has 27 common field items
- Character sheet section 16 matches official layout: note bar about body armor, columns Weapon / Skill % / Base Range / Damage / Armor Piercing / Lethality % / Kill Radius / Ammo, always ≥7 rows (a–g), empty rows with subtle fill
- Skill % on sheet is auto-calculated from the character's current profession + bonus skills via `getSkillTotal()`
- Print CSS updated: #panel5 → #panel6 for sheet printing

**Files changed this session:**
- delta_green_character_creator.html — all changes (professions rewrite, stat priority assignment, Equipment step, weapons sheet section)

## Session — 2026-04-10

**Focus:** Adding remaining professions, swapping step order (Profession before Statistics), stat suggestions per profession, fixing PDF/print blank page.

**Decisions made:**
- Profession moved to step 02, Statistics to step 03 — so profession choice informs stat allocation
- Suggested priority stats added per profession (e.g. STR/CON/DEX for Soldier) — highlighted visually on the stats panel with green border and PRIORITY tag
- Print fix: CSS custom properties don't reliably re-evaluate inside @media print, so all sheet colors replaced with explicit hex values in the print block
- `print-color-adjust: exact` added to force browsers to print background colors (green header, skill highlights, derived boxes)
- Print button now calls `renderSheet()` before `window.print()` to guarantee content is rendered
- `body::before` scanline overlay and `.dg-header` hidden in print
- PILOT_OPTIONS constant added (`Fixed-Wing Aircraft`, `Helicopter`, `Boat`, `Other`) — same subtype pattern as Military Science

**Problems solved:**
- PDF/print blank page — caused by CSS variables not resolving in print context; fixed with explicit hex overrides per element
- Step panel/nav mismatch after reorder — all `goStep()` references, button labels, and nav button text updated to match new order

**Files changed this session:**
- delta_green_character_creator.html — all changes (professions, step reorder, stat hints, print fix)
- project-context.md — created
- project-memory.md — created
- .claude/commands/new-project.md — created
- .claude/commands/start-of-day.md — created
- .claude/commands/end-of-day.md — created

<!-- end-of-day skill appends new sessions here -->
