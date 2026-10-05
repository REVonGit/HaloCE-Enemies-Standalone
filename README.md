# Halo CE Enemies, standalone version

Halo: Combat Evolved's enemies (Covenant, Flood, Sentinels and Marines), plus the Digsite / SPV3 / Halo 2 add-on, as UZDoom / GZDoom monsters that need **no HaloDoom Evolved**. They play on any IWAD, next to other gameplay and weapon mods.

What the standalone core adds over the HDE version:
* **Built-in API:** the enemy API is inside the core, as `HCES_EnemyBase`.
* **Own projectiles and shields:** projectiles, grenades, explosions and shields are ported from HDE (`hces_lib.zsc`), with HDE's sprites, models and sounds renamed `HCES_` / `HZ**` / `HCES/...`, so nothing clashes with other mods.
* **Loot director:** `hces_loot.zsc` drops vanilla Doom weapons, ammo, health and armor by what the player needs.

The HDE version is in **HaloCE-Enemies-HDE**, and that repo's `generator/build_standalone.py` produces these packs.

## Layout

```
packs/<Pack>/        each folder is exactly the root of one pk3: edit files here
  HaloCE_Standalone_Bundle/      Covenant + Digsite / SPV3 / Halo 2 add-on + dialogue, with the enemy API and the
                                 parts of Core they use: the one pack most people load
  HaloCE_Standalone_Core/        enemy AI, HDE-derived projectiles / effects, loot director, handler, CVars,
                                 for the faction packs
  HaloCE_Standalone_Flood/  _Sentinels/  _Marines/
build.py, Build_PK3s.bat   compile packs/ into dist/*.pk3
tools/                     merge_hce_packs.py (used by build.py), make_bundle_folder.py (made the bundle folder)
docs/PACK_README.md        the full player-facing manual
```

## Compiling

You need Python 3 and nothing else.

```
python build.py            # every pack -> dist/<Pack>.pk3
python build.py Bundle     # just the matching packs
python build.py --merged   # + dist/HaloCE_Standalone_Merged.pk3 (the bundle + Core + Flood + Sentinels + Marines)
python build.py --all      # everything
```

On Windows, double-click `Build_PK3s.bat`.

**GitHub Actions** compiles everything on every push to `main` (the pk3s are under the run's *Artifacts*). A `v*` tag also attaches them to a Release.

## Load order

```
HaloCE_Standalone_Bundle.pk3 -> other mods -> nashgore.pk3 (optional, last)
```

* **Everything:** for Flood, Sentinels and Marines too, load `HaloCE_Standalone_Merged.pk3` instead.
* **Without the bundle:** `HaloCE_Standalone_Core.pk3 -> _Flood / _Sentinels / _Marines`.
* **Don't mix:** never load the bundle together with `HaloCE_Standalone_Core`, because it already contains its code.
* **Faction packs need Core:** the bundle leaves out Core's Flood / Sentinel / Marine-only sounds and models (rocket, flamethrower end, sentinel beam, sniper, magnum), so the faction packs need Core or the merged pack.

## Editing notes

* **Core sources:** the core's own sources are `ZScript/HaloCE/hces_api.zsc` (enemy AI), `hces_lib.zsc` (projectiles, effects, shields), `hces_loot.zsc` (loot director) and `hce_handler.zsc` (Doom monster replacement).
* **Two copies of Core:** these files, and the core sounds / sprites / models the bundle uses, exist both in `HaloCE_Standalone_Bundle/` and `HaloCE_Standalone_Core/`. **Edit both copies** (they must stay identical, or building the merged pack stops and names the file). The bundle's `sndinfo.hces_hde`, `modeldef.hces_core` and `gldefs.hces_core` are trimmed copies of Core's.
* **Folding new packs in:** put fresh Covenant / Digsite / voice packs in `packs/`, delete the bundle folder, and run `python tools/make_bundle_folder.py`.
* **Defaults:** `cvarinfo.txt` in the bundle and in the core holds the defaults: `hces_loot`, `hces_loot_weapons`, `hce_nerf_projectiles` (0.3 here), `hce_nerf_health` and `hce_nerf_shields`.
* **Regenerating:** a regeneration from the HDE repo's `generator/` overwrites hand edits here, so carry them over.

## Credits and rights

* **Halo assets:** Halo, its characters, models, textures and sounds are property of Microsoft / 343 Industries / Bungie, used under the Game Content Usage Rules (non-commercial).
* **Digsite and SPV3:** the Digsite add-on uses Digsite source assets (licensed for MCC projects only) and SPV3 content. **Keep this repository private** unless you have cleared that.
* **HaloDoom Evolved:** the projectile, effect and shield code and its sounds, sprites and models are ported from HaloDoom Evolved (Local_DEV); see `CREDITS_STANDALONE.txt` in the core.
* **Other credits:** Lewisk3/HaloDoomEnemies (voice files). Nash's Gore Mod is optional and not included.
