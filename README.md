# Web GIS — semester project

**GIS874 Web GIS**
Institute of Geographical Information Systems, NUST Islamabad

| | |
|---|---|
| Name | Zaid Younas |
| Reg # | 537865 |
| Submitted to | Dr. Shahid Nawaz Khan |

This repository is the semester project for Web GIS. Each lab adds a commit, so
the history below the file list is part of the record.

## What is here

| Lab | Where | Status |
|---|---|---|
| **Lab 1 — My First Web GIS** | this folder | complete |
| Lab 2 — District Explorer | [webgis-lab-537865](https://github.com/zayd0123/webgis-lab-537865) | complete |
| Lab 3 — District Dashboard | [webgis-lab-537865/lab3](https://github.com/zayd0123/webgis-lab-537865/tree/main/lab3) | complete |

## Lab 1 — My First Web GIS

A single page showing a Leaflet map of Islamabad with a marker on NUST H-12.

| File | What it holds |
|---|---|
| `index.html` | the whole page: structure, CSS and the map script |
| `report.md` | the lab report, with the checkpoints inline and the five answers |
| `Report/` | the five checkpoint screenshots, and `lab01.md` |

**Live page:** <https://zayd0123.github.io/webgis-537865/>

## Running it locally

The page must be served over `http://`, not opened as a file — a browser blocks
runtime requests from `file://`. Either use the Live Server extension in VS Code,
or from this folder:

```
python3 -m http.server 5500
```

then open <http://127.0.0.1:5500>.

## Data source

| | |
|---|---|
| Basemap | OpenStreetMap tiles |
| Mapping library | Leaflet 1.9.4, from unpkg |
