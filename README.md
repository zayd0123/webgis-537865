# Web GIS — semester project

**GIS874 Web GIS**
Institute of Geographical Information Systems, NUST Islamabad

| | |
|---|---|
| Name | Zaid Younas |
| Reg # | 537865 |
| Submitted to | Dr. Shahid Nawaz Khan |
| Live site | https://zayd0123.github.io/webgis-537865/ |

One repository, one folder per lab. Every lab added its own commit, so the
history is part of the record.

## Labs

| # | Lab | Folder | Live | Report |
|---|---|---|---|---|
| 01 | My First Web GIS | [`lab1/`](lab1/) | [open](https://zayd0123.github.io/webgis-537865/lab1/) | [`lab1/report.md`](lab1/report.md) |
| 02 | District Explorer | [`lab2/`](lab2/) | [open](https://zayd0123.github.io/webgis-537865/lab2/) | [`lab2/report.md`](lab2/report.md) |
| 03 | District Dashboard | [`lab3/`](lab3/) | [open](https://zayd0123.github.io/webgis-537865/lab3/) | [`lab3/report.md`](lab3/report.md) |
| 04 | GeoDjango — Getting Started | `lab4/` | — | in progress |

The root `index.html` is a landing page linking to each lab.

## Running any lab locally

Each lab is a self-contained folder that must be served over `http://`, because
a browser blocks runtime requests from `file://`. From the repository root:

```
python3 -m http.server 5500
```

then open:

- <http://127.0.0.1:5500/> — the landing page
- <http://127.0.0.1:5500/lab1/> — Lab 1
- <http://127.0.0.1:5500/lab2/> — Lab 2
- <http://127.0.0.1:5500/lab3/> — Lab 3

## What each lab contains

Every folder holds the same shape: the page itself, its styles and scripts, any
data it needs, a `report.md`, and a `Report/` folder with the checkpoint
screenshots.

| Lab | Files | Data |
|---|---|---|
| 1 | `index.html` | none — OpenStreetMap tiles only |
| 2 | `index.html`, `style.css`, `app.js` | `data/districts.geojson`, 160 districts |
| 3 | `index.html`, `style.css`, `app.js` | `data/districts.geojson`, `data/district_points.geojson` |

## Earlier repositories

Labs 2 and 3 were first built in a separate repository,
[webgis-lab-537865](https://github.com/zayd0123/webgis-lab-537865), before the
labs were consolidated here as the Lab 01 handout specifies. That repository is
left in place so the URL submitted for Lab 2 still resolves; this one is now
canonical.

## Data sources

| | |
|---|---|
| Boundaries | OCHA COD-AB Pakistan, admin level 2 — 160 districts |
| Population | Pakistan Bureau of Statistics, 2023 census |
| Basemaps | OpenStreetMap; Esri and Carto in Lab 3 |
| Mapping library | Leaflet 1.9.4 |
