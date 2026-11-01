# THUG2 THP8 Level Converter

> **Not affiliated with or endorsed by Activision, Neversoft or any other rights holder.** A non-commercial fan project
> for preservation, interoperability and education. No game files are included: the tools work only on copies of the
> games you own. Game names are trademarks of their owners. See LICENSE (MIT for the project's own work; no licence for material
> from the games); third-party parts (downloaded tools) are listed in THIRD_PARTY_NOTICES.md. Rights holders: open an
> issue and it will be dealt with promptly.

Converts Project 8's network levels and its standalone ones (training, skate shop, bedroom) from your own copy of Tony Hawk's Project 8 (Xbox 360) to the formats of Tony Hawk's Underground 2 (PC), for its level
select. Nothing of either game is in this repository: the files are made from your copies each time. How it works,
what is converted and what isn't: [thp8_levels/README.md](thp8_levels/README.md).

- `thp8/` - Tony Hawk's Project 8's formats (Xbox 360: archives `thp8.ts`, scenes `geom.ts`, textures `xenos.ts` / `dxt.ts`, materials `materials.ts`, Havok collision `havok.ts`) and the converter engine (`thp8_levels.ts`, run with a game profile), with its render and hole audits.
- `thp8_levels/` - the converter's command line (`thp8_levels.ts`: `convert`, `selfcheck`, `list` - Project 8's profile), its census and edge audit tools.
- `thp8_save/` - Project 8's save files: read, written, and a finished career made from the game's own data (`thp8_save.ts`).

## Dependencies

Each pinned to a tagged commit in package.json (`git+https://git@github.com/...#<commit>`; one copy of each: every repository that uses one pins the same commit):

- [thug2-level-converter-core](https://github.com/CanuckAussieKev/thug2-level-converter-core): THUG2's formats (scenes, collision, textures, archives, scripts), the level port's common steps.
- [thug2-thaw-level-converter](https://github.com/CanuckAussieKev/thug2-thaw-level-converter): Project 8 is built on THAW's formats - its QB node arrays and level scripts are THAW's form, so this converter reads them with that converter's `thaw/` readers and ports the scripts with its `thaw_scripts.ts`.

Used by: Proving Ground is built on Project 8's engine: [thug2-thpg-level-converter](https://github.com/CanuckAussieKev/thug2-thpg-level-converter) runs `thp8/thp8_levels.ts` with its own game profile and readers. The content pipeline's loading pictures read Project 8's archives with `thp8/thp8.ts`.

## Public API

What other repositories may import - package.json's `exports`, nothing else (`import ... from "thug2-thp8-level-converter/thp8/thp8.ts"`):

- `thp8/thp8.ts` - Project 8's archives and packages, read.
- `thp8/geom.ts` - scenes and meshes.
- `thp8/xenos.ts` - Xbox 360 textures made THUG2's.
- `thp8/dxt.ts` - DXT blocks.
- `thp8/materials.ts` - materials, shader templates and their passes.
- `thp8/havok.ts` - Havok collision.
- `thp8/render_audit.ts` - the converted levels' render checks (Proving Ground's too).
- `thp8/thp8_levels.ts` - the converter engine: `main`, the game profile (`useGame`, `THP8_GAME`), zones, lighting.

Commands (package.json's `commands`: the tools with a command line - importing a module never runs one): `thp8/hole_audit.ts`, `thp8/render_audit.ts`, `thp8/thp8_levels.ts`, `thp8_levels/alpha_census.ts`, `thp8_levels/edge_audit.ts`, `thp8_levels/env_census.ts`, `thp8_levels/thp8_levels.ts`, `thp8_save/thp8_save.ts`.

## Usage

Needs Node 24 and NeverScript's `ns` (the QB script compiler): `npx thug2-neverscript fetch` downloads it for your
system (Windows, Linux, macOS - from [our NeverScript fork](https://github.com/CanuckAussieKev/NeverScript)'s release,
SHA-256 checked) and the converter finds it there; or pass `--ns <ns>` / set `NEVERSCRIPT=<ns>`. Inside the
[THUG2 decompilation](https://github.com/CanuckAussieKev/thug2-decomp) its own build is used
(`tools\build_neverscript.ps1`). The converter needs only THUG2's data files, not its exe.

    git clone https://github.com/CanuckAussieKev/thug2-thp8-level-converter
    cd thug2-thp8-level-converter
    npm ci
    npx thug2-neverscript fetch
    node thp8_levels/thp8_levels.ts convert thp8_skateshop --thp8 "C:\Games\THP8" --thug2 C:\Games\THUG2
    node thp8_levels/thp8_levels.ts selfcheck --thug2 C:\Games\THUG2

`convert` writes `<thug2>\thug2-decomp-thp8\` (not read by the game) - or `--out <folder>`, which must be new, empty or an earlier output (any other folder is refused,
nothing in it touched); naming levels converts only those and keeps the others; without level names it converts the default
set (thp8_levels/README.md). The [THUG2 content pipeline](https://github.com/CanuckAussieKev/thug2-content-pipeline)'s
`menu_level/make_menu_level.ts` then installs every converted game's levels into the game's data overlay and level
select. In the decomp, where the pipeline installs this converter, the same commands are

    node tools/pipeline/run.ts thp8_levels/thp8_levels.ts convert --thug2 C:\Games\THUG2
    node tools/pipeline/menu_level/make_menu_level.ts --game C:\Games\THUG2

Checks: `npm run typecheck`, `npm run lint`, `npm test` (the tests that need a game install skip without it;
`package.test.ts` checks package.json's exports, commands and pins). GitHub Actions runs them on every push
(`.github/workflows/ci.yml`).

## Versions

Releases are tagged `v<major>.<minor>.<patch>`; this is `v1.2.1` (imports from core v5.0.0's numbers.ts / text.ts / json.ts / cli.ts (its py.ts split, names for what they do); behaviour unchanged); `v1.2.0` (paths written with / (path.join) instead of pyJoin and \-strings; core v4.0.0); `v1.1.1` (core v3.1.0: runs on Linux and macOS with the same output as on Windows; CI on all three); `v1.1.0` (ns found or fetched without the decomp, output folder checked; core v3.0.0); `v1.0.1` (core v2.0.0, THAW's converter v1.1.0). The repositories that use it pin the tagged commit.
