# betterMiniMap

A client-side Mindustry mod that improves minimap readability and unit recognition.

## Features
- Draws minimap units with real unit `icons` (instead of geometric markers).
- Supports facing-direction rotation for unit icons.
- Clusters same-type, same-team units into a larger merged icon.
- Cluster threshold uses minimap pixel distance, so it adapts to minimap zoom.
- Includes filters and settings for units/buildings and visual tuning.

## Installation
- Download one of these artifacts from Releases:
  - `betterMiniMap-<version>.zip`
  - `betterMiniMap-<version>.jar`
  - `betterMiniMap-<version>-android.jar`
- Put it into your Mindustry `mods` folder and restart the game.

## Build
```bash
gradle deploy
```
