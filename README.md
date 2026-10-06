# Halo CE Enemies, standalone version

Halo: Combat Evolved's enemies (Covenant, Flood, Sentinels and Marines), plus the Digsite / SPV3 / Halo 2 add-on, as UZDoom / GZDoom monsters that need **no HaloDoom Evolved**. They play on any IWAD, next to other gameplay and weapon mods.

What the standalone core (inside the Covenant pack) adds over the HDE version:
* **Built-in API:** the enemy API is inside the core, as `HCES_EnemyBase`.
* **Own projectiles and shields:** projectiles, grenades, explosions and shields are ported from HDE (`hces_lib.zsc`), with HDE's sprites, models and sounds renamed `HCES_` / `HZ**` / `HCES/...`, so nothing clashes with other mods.
* **Loot director:** `hces_loot.zsc` drops vanilla Doom weapons, ammo, health and armor by what the player needs.

The HDE version is in **HaloCE-Enemies-HDE**, and that repo's `generator/build_standalone.py` produces these packs.

## Layout

```
packs/<Pack>/        each folder is exactly the root of one pk3: edit files here
  HaloCE_Standalone_Covenant/    the main pack: the Core (enemy AI, HDE-derived projectiles / effects, loot director,
                                 handler, CVars, console commands), the Covenant (Grunts, Jackals, Elites, Hunters, and
                                 the Digsite / SPV3 / Halo 2 add-on: Drones, Brutes, Engineer, Blind Wolf, Thorn Beast,
                                 Slug Men, Drinol, carbine Elites) and the enemy dialogue
  HaloCE_Standalone_Marines/     the Marines, their arsenal and Sergeant Johnson, with the Marine dialogue
  HaloCE_Standalone_Flood/  _Sentinels/
build.py, Build_PK3s.bat   compile packs/ into dist/*.pk3, plus the merged pack
tools/                     merge_hce_packs.py (merging)
docs/PACK_README.md        the full player-facing manual
```

## Compiling

You need Python 3 and nothing else. On Windows, double-click `Build_PK3s.bat`.

```
python build.py              # every pack -> dist/<Pack>.pk3, plus the merged pack
python build.py Covenant     # just the packs whose folder name contains "Covenant"
python build.py --no-merged  # skip the merged pack
python build.py --force      # rebuild everything, changed or not
```

* **Incremental:** only what changed is rebuilt. A pack whose files are the same as at its last build is skipped (`dist/.buildcache.json` remembers), so a full build takes about 20 s and a rebuild after one edit about a second.
* **Compression:** sounds and images, which are compressed already, are stored as they are, and text and models are deflated. That took a full build from about 18 s to about 4 s, for about 1 MB more per pk3.
* **Byte-stable:** the same files always give the same pk3.

**GitHub Actions** (`.github/workflows/build.yml`) compiles everything (`--force`) on every push to `main`. The pk3s are under the run's *Artifacts*. Pushing a tag such as `v1.2` also attaches them to a GitHub Release:

```
git tag v1.2 && git push origin v1.2
```

## Load order

```
HaloCE_Standalone_Covenant.pk3 -> HaloCE_Standalone_Flood / _Sentinels / _Marines.pk3 (any) -> other mods -> nashgore.pk3 (optional, last)
```

* **Covenant first:** `HaloCE_Standalone_Covenant.pk3` carries the Core, so it's the one pk3 that's always needed; Flood, Sentinels and Marines load after it.
* **Everything in one file:** `HaloCE_Standalone_Merged.pk3`.
* **Don't mix:** don't load the merged pack together with the separate packs, and don't load old `HaloCE_Standalone_Core`, `HaloCE_Enemies_Voices` or `HDE_CE_Covenant_Standalone` pk3s from an earlier download: the Covenant pack contains them now. Delete them from your `dist/` folder.

## Editing notes

* **Core sources:** the core's own sources (in `packs/HaloCE_Standalone_Covenant/`) are `ZScript/HaloCE/hces_api.zsc` (enemy AI), `hces_lib.zsc` (projectiles, effects, shields), `hces_loot.zsc` (loot director) and `hce_handler.zsc` (Doom monster replacement).
* **ZScript:** `packs/HaloCE_Standalone_Covenant/zscript.txt` includes the Core, Covenant and Digsite files in load order.
* **Regenerating:** the HDE repo's `generator/build_standalone.py` writes separate Covenant and Digsite packs. Merge fresh ones into one Covenant folder: `python tools/merge_hce_packs.py HaloCE_Standalone_Covenant.pk3 HaloCE_Standalone_Digsite.pk3 -o cov.pk3`, then unpack it over `packs/HaloCE_Standalone_Covenant/`. A regeneration overwrites hand edits here, so carry them over.
* **Defaults:** Core's `cvarinfo.txt` holds the defaults: `hces_loot`, `hces_loot_weapons`, `hce_nerf_projectiles` (0.3 here), `hce_nerf_health` and `hce_nerf_shields`.

## Credits and rights

* **Halo assets:** Halo, its characters, models, textures and sounds are property of Microsoft / 343 Industries / Bungie, used under the Game Content Usage Rules (non-commercial).
* **Digsite and SPV3:** the Digsite add-on uses Digsite source assets (licensed for MCC projects only) and SPV3 content. **Keep this repository private** unless you have cleared that.
* **HaloDoom Evolved:** the projectile, effect and shield code and its sounds, sprites and models are ported from HaloDoom Evolved (Local_DEV); see `CREDITS_STANDALONE.txt` in the core.
* **Other credits:** Lewisk3/HaloDoomEnemies (voice files). Nash's Gore Mod is optional and not included.
