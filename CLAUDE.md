# Maintaining this ReaPack repository

This is the ReaPack repository at https://github.com/BlindProducers/reapack. ReaPack reads `index.xml`; nobody edits it by hand. On every push to `main`, the `index` workflow (`.github/workflows/index.yml`) checks the packages with [reapack-index](https://github.com/cfillion/reapack-index), adds new versions to `index.xml` and commits it. On pull requests it only checks.

`README.md` is also the "About this repository" text in ReaPack, so it holds only what a REAPER user needs.

## Layout

- Each top-level folder is a ReaPack category, named by type: `Dynamics`, `Distortion`, `Reverb` and so on. Files at the root are never indexed.
- ReaPack installs effects to `Effects/Blind Producers/<category>/`.
- Only shipping files go here: the `.jsfx`, any `.jsfx-inc` it imports and its `.jsfx.rpl` preset library. Development material (analysis, build scripts, `.jsfx.in`, `gfx.eel`, screenshots) stays in the plugin's development folder. Where a `.jsfx` is built from `.jsfx.in` and `gfx.eel`, copy the built `.jsfx`.

## Package header

ReaPack reads these lines at the top of the `.jsfx`. REAPER ignores all of them except `desc:`, which names the plugin in both.

```
desc: Tape Stop
author: Blind Producers
version: 1.1
changelog:
  Fixed a click when Playrate returns to 100 %
provides:
  TapeStop.jsfx.rpl
about:
  Slows a track down like a stopping tape or turntable.
```

- `version:` is required. Use plain numbers (`1.0`, `1.2.1`); letters make a pre-release (`1.1beta1`).
- `provides:` lists every other file the plugin needs, relative to its folder.
- `about:` is Markdown, shown in ReaPack's "About this package".
- Indent continuation lines consistently, and leave no blank lines between tags.

## Publishing

1. `git pull` first: the workflow commits `index.xml` to `main` after every push.
2. Add or update the files and raise `version:`. A changed file with the same version is not published: the index keeps pointing at the commit of the earlier release.
3. Set `changelog:` to this version's changes.
4. Push to `main`, then check that the `index` run passed (`gh run list --limit 3`).

Changes to `author:`, `changelog:` or `provides:` of a version that is already released are ignored. Release a new version instead.

## Shared files

Each file can belong to only one package. When several plugins import the same `.jsfx-inc`, ship them as one package: one `.jsfx` lists the other `.jsfx` files and the `.jsfx-inc` in `provides:`, and each of the other `.jsfx` files has `noindex: true` in its header.
