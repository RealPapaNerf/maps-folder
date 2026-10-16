# Flightline maps

Terrain maps for [Flightline RC Sim](https://github.com/RealPapaNerf/flightline-sim).
Served as plain static files, so the sim lists them straight from
`index.json` and downloads one when you pick it.

**Live at** <https://realpapanerf.github.io/maps-folder/> once Pages is on
(see below).

## What is in here

| Map | Where | Size | Notes |
|---|---|---|---|
| `demo-synthetic-ridge` | Nowhere | 2.05 km at 4 m | **Synthetic.** Not lidar, not a real place. |

The demo map exists so the library, the loader and the game's map picker can
be tested end to end. It is generated from a formula — a ridge, a bowl with
water in it, a face too steep to land on and a flat shelf with a runway — and
every field in it says so. **Do not mistake it for terrain.** Real maps
(Cedar City, Brian Head, Timpanogos, Zion) go in beside it as they are built.

## Using it

In the sim: **Settings → Map → List the public maps**. The library URL is a
field in that panel, so you can point the sim at a fork of this repo, or at
any static folder with an `index.json` in it.

Offline, or in the claude.ai artifact build (which cannot reach another
server): clone or download this repo and use **Open a map folder…** instead.

## Adding a map

1. Build it with [the map builder](https://github.com/RealPapaNerf/terrain-and-maps-builder):

   ```bash
   node bin/flmap.mjs build \
     --center 37.6775,-113.0628 --size 2048 --cell 4 \
     --out ../maps-folder/cedar-city-field \
     --id cedar-city-field --name "Cedar City field" --region "Cedar City, Utah" \
     --runway -12,0,90,120,10 --flatten-runway --classify
   ```

2. Rebuild the index, which reads every folder and checks each map against
   itself as it goes:

   ```bash
   node bin/flmap.mjs index ../maps-folder --name "Flightline public maps"
   ```

3. Commit the folder and `index.json` together.

A map folder is `map.json`, `height.u16`, and optionally `surface.u8` and a
colour image. The format is specified in
[`docs/map-format.md`](https://github.com/RealPapaNerf/flightline-sim/blob/main/docs/map-format.md).

## Turning GitHub Pages on

Settings → Pages → **Deploy from a branch** → branch `main` → folder `/ (root)`.

Nothing else is needed: these are static files and GitHub Pages serves
`.u16` and `.u8` as `application/octet-stream`, which is what the loader
reads with `fetch().arrayBuffer()`.

Pages adds the CORS header the sim needs to fetch from a different origin.
If you host this somewhere else, make sure `Access-Control-Allow-Origin` is
set, or the browser will refuse the download with a message that looks like
the file is missing.

## Size

A 2 km map at 4 m is about 800 KB: 514 KB of heightfield, 257 KB of surface
grid. With a 2048 px orthophoto, add about 600 KB. The index carries each
map's byte count so the picker can warn before a download on a phone tether.

Keep an eye on the repository total if a lot of maps land here — GitHub Pages
has a soft 1 GB limit and asks you to stay under 100 GB of bandwidth a month.
At 1.5 MB a map that is a lot of maps, but worth knowing.

## Licence

The maps are CC0 unless a map's own `license` field says otherwise.
Elevation from USGS 3DEP and imagery from USGS NAIP are both public domain;
each map carries its own `attribution` string and the sim shows it in the map
panel.
