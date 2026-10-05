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
  HaloCE_Standalone_Core/        enemy AI, HDE-derived projectiles / effects, loot director, handler, CVars
  HaloCE_Standalone_Covenant/  _Flood/  _Sentinels/  _Marines/
  HaloCE_Standalone_Digsite/     Digsite / SPV3 / Halo 2 add-on
  HaloCE_Enemies_Voices/         dialogue (optional; identical to the HDE version's)
build.py, Build_PK3s.bat   compile packs/ into dist/*.pk3
tools/                     merge_hce_packs.py (used by build.py), update_merged_pack.py
docs/PACK_README.md        the full player-facing manual
```

## Compiling

You need Python 3 and nothing else.

```
python build.py            # every pack -> dist/<Pack>.pk3
python build.py Digsite    # just the matching packs
python build.py --bundle   # + dist/HaloCE_Standalone_Bundle.pk3 (Core + Covenant + Digsite + voices)
python build.py --merged   # + dist/HaloCE_Standalone_Merged.pk3 (Core + every faction + Digsite)
python build.py --all      # everything
```

On Windows, double-click `Build_PK3s.bat`.

**GitHub Actions** compiles everything on every push to `main` (the pk3s are under the run's *Artifacts*). A `v*` tag also attaches them to a Release.

## Load order

```
HaloCE_Standalone_Core.pk3 -> any faction packs / HaloCE_Standalone_Digsite.pk3 -> HaloCE_Enemies_Voices.pk3 (optional)
-> other mods -> nashgore.pk3 (optional, last)
```

Or `HaloCE_Standalone_Bundle.pk3` on its own.

## Editing notes

* **Core sources:** the core's own sources are `ZScript/HaloCE/hces_api.zsc` (enemy AI), `hces_lib.zsc` (projectiles, effects, shields), `hces_loot.zsc` (loot director) and `hce_handler.zsc` (Doom monster replacement).
* **Defaults:** `cvarinfo.txt` in the core holds the defaults: `hces_loot`, `hces_loot_weapons`, `hce_nerf_projectiles` (0.3 here), `hce_nerf_health` and `hce_nerf_shields`.
* **Regenerating:** a regeneration from the HDE repo's `generator/` overwrites hand edits here, so carry them over.

## Credits and rights

* **Halo assets:** Halo, its characters, models, textures and sounds are property of Microsoft / 343 Industries / Bungie, used under the Game Content Usage Rules (non-commercial).
* **Digsite and SPV3:** the Digsite add-on uses Digsite source assets (licensed for MCC projects only) and SPV3 content. **Keep this repository private** unless you have cleared that.
* **HaloDoom Evolved:** the projectile, effect and shield code and its sounds, sprites and models are ported from HaloDoom Evolved (Local_DEV); see `CREDITS_STANDALONE.txt` in the core.
* **Other credits:** Lewisk3/HaloDoomEnemies (voice files). Nash's Gore Mod is optional and not included.
