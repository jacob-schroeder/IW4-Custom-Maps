# IW4-Custom-Maps

Custom maps, weapons, UI, and menus for PlayStation 3 Modern Warfare 2 (2009).

This layout and catalog prepare the repository for the Content menu integration.
The currently released 2.5.0 SELF does not read this catalog and still expects map
folders directly beneath `mods`. Publish this reorganization together with a SELF
that supports the categorized paths; the menu integration is still to be added.

## Repository and installation layout

```text
catalog.json
update_catalog.py                   # local-only publisher helper; gitignored
patch_mp.ff                         # working menu-editing input
mods/
  maps/
    mp_havana/
      mp_havana.ff
      mp_havana_load.ff
      mp_havana.pak
    mp_shipment_xmas/
      mp_shipment_xmas.ff
      mp_shipment_xmas_load.ff
      mp_shipment_xmas.pak
  weapons/                          # add actual weapon files when available
  ui/
    custom_ui_mp.ff
  menus/
    patch_mp.ff                     # menu release payload
```

Every catalog file path is relative to both the repository root and the PS3's
region-specific update directory. For example:

```text
mods/maps/mp_havana/mp_havana.ff
    -> /dev_hdd0/game/{REGION}/USRDIR/mods/maps/mp_havana/mp_havana.ff
```

Weapons will use `mods/weapons/{weapon}.ff`; UI and menu files use `mods/ui/` and
`mods/menus/`. Git does not track empty directories, so `weapons` will appear in
the published tree when its first payload is committed. Do not add placeholder
files beneath `mods`; every file there must be a real cataloged payload.

The root `patch_mp.ff` remains the working input for future menudef edits. Its
current bytes were copied to `mods/menus/patch_mp.ff` without modifying the input.
After finishing and verifying a later menu revision, explicitly update the
release copy before refreshing the catalog:

```sh
cp patch_mp.ff mods/menus/patch_mp.ff
```

The initial installation must supply a bootstrap menu that can open Content
before optional downloads are available. The future installer cannot depend on
downloading `mods/menus/patch_mp.ff` to expose its own install button. The SELF
integration must also handle loading menu fastfiles from the new directory.

## Catalog contract

[`catalog.json`](catalog.json) is the public package index. Its initial packages
are Havana, Shipment Christmas, Custom Map Previews, and Multiplayer Menus.

| Field | Meaning |
| --- | --- |
| `schemaVersion` | Catalog format version; currently integer `1`. Requires a client implementing this schema. |
| `revision` | Positive integer for the whole catalog. Increment when publishing package or catalog changes. |
| `packages[].id` | Stable, unique lowercase ID using letters, digits, dots, underscores, or hyphens. Keep it unchanged across package updates. |
| `name`, `description` | Player-facing package label and description. |
| `category` | `maps`, `weapons`, `ui`, or `menus`. |
| `version` | Package version in `MAJOR.MINOR.PATCH` form, independent of the SELF version. Initial packages start at `1.0.0`. |
| `dependencies` | Other package IDs that must be installed with this package. Current entries are empty; only add actual requirements. |
| `files[].path` | Exact case-sensitive payload path beneath `mods/{category}/`, relative to the repository and `USRDIR`. |
| `files[].size` | Exact file size in bytes, generated from the payload. |
| `files[].sha256` | Lowercase SHA-256 of the raw file bytes, generated from the payload. |

A map is one package containing all released files in its map folder, including
any load fastfile and texture package. The client should verify the complete
package before activating it. A file can belong to only one package; shared content should be a
separate package referenced through `dependencies`. Downloads must not accept
absolute paths, traversal, symlinks, or the reserved `.iw4-download-part` suffix.
Paths use printable ASCII, are at most 511 bytes, and cannot contain
backslashes or any of `: ? # * " < > | %`.

The client should resolve the published revision to one Git commit and fetch
both the catalog and selected files from that commit. URLs are derived from the
known repository and each file path rather than stored as mutable download URLs.

## Adding or updating content

1. Put finished payloads in their category directory. Keep a map's companion
   files together in one map folder.
2. Add a package to `catalog.json` with its ID, display metadata, category,
   version, dependencies, and file paths. `size` and `sha256` can be omitted for
   new files; the refresh command supplies them.
3. For an existing package, increase its version when its files change. Increase
   the catalog `revision` when publishing changes.
4. Refresh the generated file metadata using Python 3 from this checkout:

   ```sh
   python3 update_catalog.py
   ```

5. Review the catalog and payload changes before committing the release.

`update_catalog.py` is intentionally gitignored and stays in the publisher's
local checkout; it is not included in the public repository or a fresh clone.

The refresh command preserves package versions and catalog revision. It rejects
duplicate IDs or payload ownership, split map folders, symlinks, unsupported
categories, invalid paths, missing files, invalid dependency references, and
payloads absent from the catalog. It does not modify fastfiles, contact GitHub,
publish, or change the SELF.

The Content menu still needs the SELF command bridge, a catalog feeder, package
selection, installed-version tracking, and the new loader paths. The compatible
SELF will be supplied through [IW4-Binaries](https://github.com/jacob-schroeder/IW4-Binaries/).
