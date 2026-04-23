# Project: Delta Green Character Creator
Last updated: 2026-04-23 by Claude Code

## What this project is
A browser-based character creation tool for the Delta Green tabletop RPG. Users
fill out a 5-step wizard covering personal info, stats, profession, bonds, and
bonus skills, then get a formatted character sheet they can print or save as PDF.
Characters can be saved locally and shared via URL.

## Architecture decision
**Does this app need to save data between sessions or between users?**
- [x] Yes — via localStorage (same device) and shareable URLs (between users)

**Does this app need to hide secrets from the browser?**
- [x] No

**Current answer:** Static frontend — no backend needed. localStorage handles
saves; URL encoding handles sharing.

## Tech stack
| Layer | Tool/Language | Notes |
|---|---|---|
| Frontend | HTML/CSS/JS (single file) | No framework — keep it vanilla |
| Backend | None | Not needed |
| Database | localStorage | Browser-native, no server |
| Auth | None | No accounts |
| Deployment | Vercel | Free tier, static hosting |
| AI / APIs | None | None planned |

## Data model
Single Character object — personal info (name, employer, nationality, sex,
age/DOB, physical description), stats (STR/CON/DEX/INT/POW/CHA), selected
profession + profession choices, bonds array, bonus skills object. Stored as
JSON in localStorage and encoded in shareable URLs.

## User flow
User lands on character creator → fills out 5-step wizard (personal info →
profession → bonds → bonus skills → character sheet) → saves character to
browser (localStorage) → can load previously saved characters → can share via
generated URL → anyone with the link can open and import the character

## Current status
Working 5-step wizard with character sheet output and print button. Several
professions implemented in code. Not yet built: remaining profession options,
localStorage save/load, URL-based sharing. Known bug: PDF/print button leads
to a blank page.

## Where we left off
Last commit: 1eb528a — Fix print wrapper border: explicitly clear all sides before restoring border-top only
In progress: none
Branch: main

## What's next

### Edit mode gaps
- [x] Bond score tracking — adjust each bond's current score in edit mode
- [x] Current HP tracking — +/− HP in edit mode
- [x] New Character button — reset to blank sheet without page refresh

### Save/Load quality of life
- [x] Overwrite warning — confirm before silently overwriting an existing save
- [x] JSON export/import — download character as .json; import from file

### Sheet
- [x] Fillable current-value boxes on printed sheet — max/current pairs for HP, SAN, WP, BP
- [x] Gear notes on sheet — space for quantities/condition next to gear items

### Rules
- [x] Improvement rolls — roll-over-skill mechanic for post-session advancement in edit mode

### Deployment
- [x] Vercel deploy — https://delta-green-character-creator.vercel.app/

## File structure
```
/Delta Green Project
  delta_green_character_creator.html  → single-file app
  project-context.md                  → project status
  project-memory.md                   → session memory
  /docs                               → reference guides
  /archive                            → archived context docs
```

## Environment and credentials
- .env file: not needed
- Variables needed: none
- Where secrets are stored: N/A

## Key decisions made
- 2026-04-10 — Project initialized
- 2026-04-10 — Architecture: Static frontend (localStorage + URL sharing, no backend)
- 2026-04-10 — No user accounts — anonymous saves only
- 2026-04-10 — Tech stack: vanilla HTML/CSS/JS, Vercel deployment

## Known issues
- PDF/print button leads to a blank page — needs investigation and fix

## Context for each tool

### Chat
Thinking, planning, decisions, and fuzzy problems.
Flag architecture changes or scope changes before acting.

### Claude Code
Building and editing files. Tech stack: vanilla HTML/CSS/JS, single file.
Run /start-of-day at the start of every session.
Run /end-of-day at the end of every session.

### Cowork
Browser tasks, desktop automation, file management.

## Change log
- 2026-04-23 — Play mode skill checkboxes synced to sheet; Level Up button with confirm; Home Scene full redesign (DG rules); Home Scene button moved to Skills tab; Improve Skills/Stats corrected (fail-to-gain, 3d6%/+1 stat, bond cost); print layout: compact skills/stats/derived, fixed bonds height, page-break before Psych Data, border artifact fixes — Source: Claude Code
- 2026-04-22 — Vercel deploy live at https://delta-green-character-creator.vercel.app/; renamed to index.html — Source: Claude Code
- 2026-04-20 — Specialty skill selection for Art, Craft, Military Science, Pilot, Science, Foreign Language in Bonus Skills tab; Science and Foreign Language added to ALL_SKILLS; bonusSpecialty state saved/loaded with character — Source: Claude Code
- 2026-04-15 — Portrait upload feature (FileReader → base64, saved with character, excluded from URL sharing, displayed on sheet); AI portrait generation attempted via Pollinations.ai and Stable Horde — both failed from localhost environment, scrapped for future session — Source: Claude Code
- 2026-04-14 — URL sharing (?c= base64 param + import banner); git repo initialized; edit mode gaps (bond scores, HP tracking, New Agent button); improvement rolls (tick + d100/1d6 mechanic); save overwrite warning; JSON export/import; fillable current-value boxes on sheet; gear note lines on sheet — Source: Claude Code
- 2026-04-13 (session 2) — Moved Weapons & Equipment section after Psychological Data on sheet; added localStorage Save/Load with modal; edit-mode Skills tab (relabeled, +/-1 buttons, no budget cap); adjustable Sanity and Breaking Point in edit mode with Recalc BP button; fixed load bug (onclick double-quote collision) — Source: Claude Code
- 2026-04-13 — Rewrote professions to match Agent's Handbook, priority-stat auto-assignment for Quick Assign and Roll 4D6, added Equipment step (06) with weapons table and gear checklist, added weapons section to character sheet (section 16 with AP/Lethality/Kill Radius columns) — Source: Claude Code
- 2026-04-10 — Added all professions, swapped step order, stat highlights, fixed PDF/print — Source: Claude Code
- 2026-04-10 — Project initialized — Source: Claude Code
