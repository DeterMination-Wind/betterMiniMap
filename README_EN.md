# betterMiniMap

A client-side Mindustry mod that improves minimap readability and unit recognition.

## Features
- Draws minimap units with real unit `icons` (instead of geometric markers).
- Supports facing-direction rotation for unit icons.
- Clusters same-type, same-team units into a larger merged icon.
- Cluster threshold uses minimap pixel distance, so it adapts to minimap zoom.
- Includes filters and settings for units/buildings and visual tuning.
- Automatic GitHub update check: compares against the latest GitHub release every 6 hours (falls back to the repo's mod.json when the API fails) and shows an update dialog when a new version exists (open Releases, ignore this version, or disable the check/dialog in settings).

## Installation
- Download one of these artifacts from Releases (file names have no version suffix):
  - `betterMiniMap.jar` (merged, includes dex; works on both desktop and Android)
  - `betterMiniMap.zip`
- Put it into your Mindustry `mods` folder and restart the game.

## Build
```bash
gradle deploy
```
