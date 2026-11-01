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

Status: pending.

- Add a separate setting; preview visible objects without Scouting by default.
- Town: actual generated guild spells, separate built/future guild levels, exclude reserve spells beyond capacity.
- Seer hut: requirements, rewards, expiry and completion status.
- Preview chest choices, Pandora contents/guards, scholar, witch hut, university and primary-stat buildings.
- Read runtime results only. Do not reroll rewards, consume RNG, visit objects, claim rewards or activate quests.
- Handle conditional/choice rewards, exhausted objects, periodic resets and missing hero context.
- Bound popup size and add scrolling; retain existing popup behavior for unsupported objects.
- Test unchanged RNG, resources, quests and visitor state after repeated right clicks.
- Review visibility and multiplayer access; deploy client/library/server consistently if interfaces change.

## Step 3: Daily Nearby Artifact Bonus

Status: pending.

- Implement a documented new bonus with configurable artifact classes, radius and daily count.
- Run on the authoritative server at new day, using game RNG and normal map-object creation/sync.
- Default to one per hero per new day; no initialization-day award; initially skip embarked/garrisoned heroes.
- Search a bounded set of reachable empty land tiles; skip if none exist.
- Respect map bans; exclude Grail, war machines, combination results and special scenario artifacts by default.
- Define stacking and prevent duplicate generation across save/load, ownership changes and movement.
- Review serialization versioning before adding fields/enums; old-engine save compatibility is not assumed.
- Test consecutive days, save/load, blocked terrain, multiplayer/RNG determinism and artifact pickup.

## Delivery

- Separate reviewable commits; keep existing user changes.
- Do not push, trigger cloud builds or replace installed binaries until requested.
- Back up binaries and saves before deployment. Use matching client/server/library builds.
- Record static, compilation and in-game checks separately; static validation is not an in-game pass.
