# ADR 0001: Add Italian as a fourth language

- **Status:** Accepted
- **Date:** 2026-11-05
- **Decider:** Jeramiah77
- **Implemented in:** commit `fe5c1eb`, merged to `main` in [PR #2](https://github.com/Jeramiah77/AI-Agentic-Coding/pull/2)

## Context

The Choose Your Own Adventure game supported three languages: English, Spanish and French. The player picks one at startup from a numbered menu in `Game.cs`.

All player-facing text lives in `language_data.json`. Each language has one section with six parts:

| Part | Purpose |
|---|---|
| `dictionary` | 69 game messages (prompts, story text, combat text, dragon taunts) |
| `raceMap` / `occupationMap` | What the player can type (for example `elfo`) mapped to the internal name (`Elf`) |
| `displayRaceMap` / `displayOccupationMap` / `displayWeaponMap` | Internal names mapped to what is shown on screen |

`Messages.cs` loads one section at a time. Game logic (`Player`, `Dragon`, `Combat`) only ever works with the internal English names, so it does not need to know which language is active.

We wanted to add Italian, so more players can enjoy the game in their own language. It would also show that the language system can grow without changes to the game logic.

## Decision

Add Italian as a fourth language by adding an `"Italian"` section to `language_data.json`, and add `4. Italiano` to the language menu in `Game.cs`.

The translation follows these rules:

1. **Every key is translated.** Italian has all 69 messages and all race, occupation and weapon entries, the same as the other languages.
2. **Command inputs are unchanged.** Players still type `n`/`s`/`e` for paths, `a`/`r` for combat, and `roll` for stats. These are hard-coded in the game logic. The letters happen to fit Italian well: **a**ttaccare, **r**itirarsi, **e**sci.
3. **No accented letters.** The other languages avoid accents so the text shows correctly in any Windows console, and Italian does the same. Sentences that would need an accent (for example *più* or *sarà*) were reworded. Agility is shown as *Destrezza* rather than *Agilità*.
4. **Italian race and occupation names are accepted as input:** Umano, Elfo, Nano, Mezzuomo; Guerriero, Mago, Ladro, Arciere. As in the other languages, matching ignores case.

## Options considered

### Option 1: Add a section to the existing `language_data.json` (chosen)
- ✅ Follows the pattern already used by English, Spanish and French.
- ✅ No changes to `Messages.cs` or to the game logic; only a three-line menu change in `Game.cs`.
- ✅ Easy to compare languages side by side and spot missing keys.
- ❌ The file grows by about 100 lines per language.

### Option 2: Move each language into its own file (for example `lang/it.json`)
- ✅ Smaller files, and translators can work on one language without touching the others.
- ❌ Needs changes to `Messages.cs`, the `.csproj` copy rules and the existing tests.
- ❌ More change than one new language justifies. Worth revisiting if more languages are added.

### Option 3: Do not add Italian
- ✅ Nothing extra to maintain.
- ❌ Italian-speaking players can't play in their own language.

## Consequences

### Positive
- Italian speakers can play the whole game in Italian, from the main menu to the ending.
- It shows that a new language can be added with data alone, apart from one menu line.
- 9 new tests in `CYOATests/ItalianLanguageTests.cs` check that every key exists and that input and display names map correctly. All 114 tests pass.

### Negative / trade-offs
- **More to maintain.** Every new message key now needs four translations. A missing one shows up on screen as `[key_name]`, so the tests should be kept up to date.
- **No native-speaker review yet.** The wording should be checked by an Italian speaker.
- **Some phrasing is less natural.** Weapons don't carry their grammatical gender (*la* spada, *il* pugnale), so attack lines use neutral wording such as *"sfoderi l'arma (pugnale)"*. Avoiding accents also limits word choice.
- **The language menu is hard-coded.** Each new language still needs a small code change in `Game.cs`, not only a data change.

## Follow-ups
- Ask an Italian speaker to review the translation.
- Optional: build the language menu from the languages found in `language_data.json`, so a new language needs no code change.
- Optional: add a test that checks every language has exactly the same keys as English.
- If more languages are added, reconsider Option 2 (one file per language).
