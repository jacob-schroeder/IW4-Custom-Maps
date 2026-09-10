# IW4-Custom-Maps
A repository for custom map release for PS3, Modern Warefare 2 (2009) (IW4)

## Platform
These maps are for the Playstation 3 Platform Only.

### Pre-requisite
This requires the installation of a patched default_mp.self which you can obtain here: https://github.com/jacob-schroeder/IW4-Binaries/

### How to use
Please add `patch_mp.ff` and the `mods` folder in this repository to your game update directory:
```
/dev_hdd0/game/{region}/usdir
```

Place any custom maps in that `mods` directory and they will automatically be loaded under the "Custom Maps" menu in multiplayer.

- The `mods` folder is optional. 
- With the matching `default_mp.self` and `patch_mp.ff` installed together, Custom Maps is hidden when no custom-map main fastfiles are found under `mods`. 
- Only installed custom maps appear in the list. 
- Restart the game after adding, removing, or renaming map folders.

### Custom map previews
Copy `maps/ui/custom_ui_mp.ff` to `/dev_hdd0/game/{region}/usdir/mods/ui/custom_ui_mp.ff`. This self-contained fastfile supplies the custom-map menu previews; it does not require a UI PAK.

Use it together with the updated `patch_mp.ff` and a patched `default_mp.self` that loads `custom_ui_mp`. The UI fastfile loads at startup and when returning to the frontend. Map folders can remain in their own subdirectories under `mods`, with their own texture packages.
