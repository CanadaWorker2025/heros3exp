# VCMI Feature Plan

## Baseline

Engine: `temp/vcmi-source-1.7.4`, branch `fix/magic-fader-battle-ai-1.7.4`.
Preserve existing BattleAI fixes. Game configuration lives in `Hero3Expasion1.83`.
The engine is a separate Git repository ignored by the parent repository.

## Step 1: Expert View Air Icons

Status: source and assets implemented; static checks passed; build and live-game checks pending.

- Keep artifact, hero and town filters; extend expert level only.
- Cover mercenaryCamp, marlettoTower, starAxis, gardenOfRevelation, arena, libraryOfEnlightenment, schoolOfWar, schoolOfMagic, scholar, hota.mapobjects:colosseumOfTheMagi and fairy:divinationHut.
- Scholar and divination hut are displayed by category regardless of their particular reward.
- Add a double upward-chevron rank icon without renumbering existing VwSymbol frames.
- Fall back to the artifact icon if the new image is missing; optional mods must remain optional.
- Preserve fog of war, map state, network packets and save format.
- Check JSON coverage, identifiers, asset dimensions/transparency and source diff.
- Compile with the dedicated MSVC x64 workflow, then verify surface/underground, fog, zoom, missing image, absent optional mods, and existing icons.

Implementation notes:

- Existing game configuration retains all 11 filters. Engine default configuration adds only the nine core objects so optional mods are not required.
- Client resolves the 11 object IDs once per world-view context, with silent optional-mod lookup.
- A separate 16x16 transparent PNG is stored at `Mods/vcmi/Content/Sprites/mapView/statBoost.png` in both the game and engine repositories.
- `CI/generate_stat_boost_icon.ps1` regenerates the PNG. Missing PNG falls back to the neutral artifact icon.
- Source changes are in MapRendererContext, MapRendererContextState and MapViewCache. No save/network fields changed.
- Local MSVC/CMake/Ninja tools were not found. No compilation or gameplay verification has been performed; do not replace installed binaries yet.
- Deployment after compilation: use the new client, include its PNG, and retain the integration's spell configuration (which includes the two mod filters).

## Step 2: Right-Click Details

Status: first implementation ready for compilation; runtime/visual validation pending.

Implemented in the client only (no save/network format changes):

- A bounded 520px scrolling popup reads town guild spells, university skills and CRewardableObject runtime configuration, including Pandora boxes, chests, scholars, witch huts and stat buildings.
- Town spell count is limited by faction guild level and current library capacity; built and unbuilt guild levels are labelled separately.
- Seer tasks show requirements, completion/deadline and reward options. Item removals and guards have separate sections.
- Configured options are displayed even when the current hero does not qualify; conditions, selection mode and reset period are labelled. These are configured rewards, not a promise that already exhausted rewards can be claimed again.
- Unknown special limiter fields and effects are explicitly marked as requiring the visit dialog; first version is not a full interpreter of every mod-specific effect.
- Adventure options includes a default-on detailedObjectPreview checkbox. Missing setting also defaults on for old installations.
- Visibility is checked through the player callback; multiple-human-player games fall back to the old popup.
- English and Chinese localization included. Existing local arrow-generator/image edits were present before this step and were left untouched.
- JSON syntax, unique keys, matching translation keys and diff whitespace checks passed. No local compiler was available; no compilation, in-game testing or cloud build was performed.
- After compilation deploy the client plus updated settings schema/widget JSON and translation files together; preserve custom game configuration when copying resources.

Acceptance checks before release:

1. Toggle off/on and confirm original town/hero/monster popups remain available when disabled or unsupported.
2. Inspect visible friendly/enemy towns, different guild levels and Tower library; compare each displayed spell with actual guild contents.
3. Inspect telescope Seer hut before accepting, after accepting and after completing; compare requirements/reward and ensure quest state does not change on right-click.
4. Inspect chest choices, guarded Pandora box, scholar, witch hut, university and previously visited stat buildings with/without a selected hero.
5. Repeatedly open/scroll/close large popups at 800x600 and higher resolutions; confirm no clipping or setting-row overlap.
6. Compare actual rewards and subsequent randomized outcomes against a control save with no preview clicks; confirm no reward/visitor/resource/RNG changes.
7. Test fogged tiles and multiple-human-player games; expanded preview must not activate.

- Add a separate setting; preview visible objects without Scouting by default.
- Town: actual generated guild spells, separate built/future guild levels, exclude reserve spells beyond capacity.
- Seer hut: requirements, rewards, expiry and completion status.
- Preview chest choices, Pandora contents/guards, scholar, witch hut, university and primary-stat buildings.
- Read runtime results only. Do not reroll rewards, consume RNG, visit objects, claim rewards or activate quests.
- Handle conditional/choice rewards, exhausted objects, periodic resets and missing hero context.
- Bound popup size and add scrolling; retain existing popup behavior for unsupported objects.
- Test unchanged RNG, resources, quests and visitor state after repeated right clicks.
- Review visibility and multiplayer access; deploy client/library/server consistently if interfaces change.

## Step 3: Daily Backpack Artifact Bonus

Status: compiled and deployed on 2026-11-06 (83cda461f / Actions 42177684899); in-game verification pending.

- The original nearby-map-object proposal is superseded by direct backpack grants.
- Register a mod-defined `DAILY_RANDOM_ARTIFACT` bonus; do not renumber built-in bonuses.
- Run once at global new day after object updates; use existing `NewArtifact` sync.
- Default to one per hero per new day from day 2, including garrisoned/embarked owned heroes.
- Ignore artifact bans per the user's updated rule; include normal combinations and valid scrolls.
- Exclude SPECIAL scenario items (ordinary scrolls excepted) and war machines; skip empty pools/full backpacks.
- Snapshot counts, enforce a safety bound and stable recipient order; do not grant on load/recruitment/movement.
- Viola's existing hypnosis specialty is preserved in the active specialty override layer.
- Old initialized heroes need a separately reviewed migration or a new map; loading alone does not add the bonus.
- Local source/configuration checks and CI C++ policy checks passed. Matching x64 binaries were deployed and hash-verified; existing mod data was preserved.
- Remaining: in-game save/load, backpack, scroll/combination and multiplayer verification; no existing-save migration has been applied.

## Scholar Experience Instruction

Status: source/resources implemented (2026-11-06); not compiled or deployed.

- Same-owner heroes only; strictly higher-level teacher, using the teacher's actual Scholar mastery.
- Top up student total XP to floor(teacher total XP * 20/40/80%), respecting level caps.
- Preserve native spell exchange; no spellbook/Wisdom requirement for XP and no extra XP multiplier.
- Defer until exchange/town reward queries complete; include town garrison/visitor swaps.
- Keep the original gain helper behavior for all existing callers; instruction grants hero XP only.
- No persistent pair records or hero reinitialization; repeated meetings naturally top up only the difference.
- Static checks passed. C++ math checks are registered with x64 CI; runtime query-stack verification remains pending.

## Delivery

- Separate reviewable commits; keep existing user changes.
- Do not push, trigger cloud builds or replace installed binaries until requested.
- Back up binaries and saves before deployment. Use matching client/server/library builds.
- Record static, compilation and in-game checks separately; static validation is not an in-game pass.
