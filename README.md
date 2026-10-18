# OpenSail charts

Inland chart data for [OpenSail](https://opensail.g-zeven.nl), a marine navigation app, as [PMTiles](https://docs.protomaps.com/pmtiles/) vector tiles. This repository holds no code: a workflow builds the charts and publishes them as [releases](../../releases).

> **Not for navigation.** These files are a conversion of official data, not an official chart. Always carry the official charts and notices.

## Files

The newest build is always at:

| File | URL |
|---|---|
| Catalog (the apps read this) | `https://github.com/gjvanhalem/opensail-charts/releases/latest/download/index.json` |
| `nl-inland.pmtiles` | `https://github.com/gjvanhalem/opensail-charts/releases/latest/download/nl-inland.pmtiles` |
| `nl-inland.json` | Manifest: bounds, zoom levels, size, sha256, edition date, and the edition of every cell |

File names in `index.json` are relative to the catalog's URL. The archives are read with HTTP range requests, which GitHub's release downloads support. They have no CORS headers, so a web page on another site can't read them directly; the OpenSail desktop and phone apps fetch them natively.

## Regions

| Region | Name | Source download |
|---|---|---|
| `nl-inland` | Nederland (excl. Zeeland, Waddenzee) | Rijkswaterstaat inland ENC, updated about monthly |

Waddenzee and Zeeland (with full depth data) will follow once Rijkswaterstaat has confirmed the licence for them.

## Source and licence

Rijkswaterstaat inland ENC (S-57), downloaded from [vaarweginformatie.nl](https://www.vaarweginformatie.nl/frp/main/#/page/infra_enc). Rijkswaterstaat publishes it under **CC0-1.0** (public domain). The converted files in the releases are CC0-1.0 too.

Attribution shown in the app: "Rijkswaterstaat ENC, CC0-1.0, edition <date>".

The tiles contain depths, contours, soundings, hazards, bridges with clearances, fairways, locks and berths. Buoys, beacons and lights are left out (OpenSail shows them from OpenSeaMap).

## How it's built

[`.github/workflows/build.yml`](.github/workflows/build.yml) runs every Monday and on demand (Actions → Build charts → Run workflow). It:

1. reads the current download's file id from Rijkswaterstaat's [download list](https://www.vaarweginformatie.nl/frp/api/webcontent/downloads?pageId=infra/enc) (file ids change with every edition), and stops if the newest release was built from the same file;
2. runs the public image `ghcr.io/gjvanhalem/opensail-enc-pipeline` (GDAL and tippecanoe; source in the OpenSail repository, `tools/enc-pipeline`) with `nl-inland <file id> "Nederland (excl. Zeeland, Waddenzee)"`;
3. publishes `nl-inland.pmtiles`, `nl-inland.json` and `index.json` as a new release, marked latest;
4. checks that the catalog and a byte range of the archive download.
