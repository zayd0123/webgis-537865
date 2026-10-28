# Lab 4 — GeoDjango with PostGIS

Name: Zaid Younas
Reg #: 537865
GIS874 Web GIS, IGIS NUST
Submitted to Dr. Shahid Nawaz Khan

Repository: https://github.com/zayd0123/webgis-537865
Project folder: `lab4/`

This lab was done in Windows 11 on a Parallels virtual machine, so the install
steps differ from the handout in a few places. The differences are noted under
"What went wrong".

---

## Versions

| Software | Version |
|---|---|
| Windows | 11, ARM64 |
| Python | 3.14.10 (x64 build, `win-amd64`) |
| Django | 6.1.2 |
| psycopg | 3.3.6 |
| PostgreSQL | 18.6 |
| PostGIS | 3.6.2 |
| GDAL (OSGeo4W) | 3.13.3 |
| GEOS (OSGeo4W) | 3.15.0 |
| PROJ | 8.2.1 |

`postgis_full_version()` from the database:

```
POSTGIS="3.6.2 3.6.2" [EXTENSION] PGSQL="180" GEOS="3.14.1dev-CAPI-1.20.4"
PROJ="8.2.1" NETWORK_ENABLED=OFF
```

Two GEOS numbers appear because there are two copies: PostGIS carries its own
(3.14.1dev) and OSGeo4W supplies the one GeoDjango loads (3.15.0).

---

## Checkpoints

### 1 — Django running

![Django install worked](checkpoints/checkpoint1a.png)

![Django version](checkpoints/checkpoint1b.png.png)

### 2 — PostGIS in pgAdmin

![postgis_full_version](checkpoints/checkpoint%202.png)

### 3 — Django connected to PostGIS

![GDAL and GEOS](checkpoints/checkpoint3a.png)

![Settings check and migrate](checkpoints/checkpoint3b.png)

### 4 — The model turned into a table

![sqlmigrate output](checkpoints/checkpoint4a.png)

![districts_district columns](checkpoints/checkpoint4b.png)

### 5 — Loading the districts

![Loader and shell queries](checkpoints/checkpoint%205.png)

### 6 — The admin panel

![List filtered to a province](checkpoints/checkpoint%206a.png)

![Lahore with its map](checkpoints/checkpoint%206b.png)

![Population changed in the database](checkpoints/checkpoint%206c.png)

### 7 — Leaflet fed by Django

![Map with tooltip](checkpoints/checkpoint%207a.png)

![Network request](checkpoints/checkpoint%207b.png)

### 8 — Spatial question

![Click popup](checkpoints/checkpoint%208a.png)

![Area query](checkpoints/checkpoint%208b.png)

---

## What went wrong

This lab is mostly installation, and on an ARM64 virtual machine a lot of it
failed the first time. Each error, what it said, and what fixed it.

**1. Python 3.15 was first on PATH**

```
pip 26.2.2 from C:\Users\...\pythoncore-3.15-arm64\Lib\site-packages\pip (python 3.15)
```

Django 6.1 does not support 3.15. Fixed by creating the virtual environment from
3.14 explicitly, so `python` inside it is 3.14 whatever the machine default is.

**2. `psycopg[binary]` had no wheel**

```
ERROR: ResolutionImpossible
Additionally, some packages in these conflicts have no matching distributions
available for your environment: psycopg-binary
```

There is no ARM64 Windows build of `psycopg-binary`. Installed plain `psycopg`
instead, which is pure Python and uses the `libpq.dll` that PostgreSQL installs.

**3. `libpq library not found`**

```
ImportError: no pq wrapper available.
- couldn't import psycopg 'python' implementation: libpq library not found
```

`libpq.dll` was on disk but not on PATH. Added
`C:\Program Files\PostgreSQL\18\bin` to PATH.

**4. `%1 is not a valid Win32 application`**

```
- couldn't import psycopg 'python' implementation: [WinError 193] %1 is not a
  valid Win32 application
```

This was the real problem. The Python was an ARM64 build and `libpq.dll` is x64,
so it could not be loaded at all. Confirmed with:

```
python -c "import sysconfig; print(sysconfig.get_platform())"   ->  win-arm64
```

`platform.machine()` is misleading here. It reads `PROCESSOR_ARCHITECTURE`,
which Windows sets to the native architecture, so it reports ARM64 even for an
x64 process. `sysconfig.get_platform()` reports how Python was built.

Fixed by installing the x64 build of Python 3.14 from python.org and rebuilding
the virtual environment from it. The same problem would have hit GDAL later,
since OSGeo4W is x64 too.

**5. Stack Builder froze while downloading PostGIS**

It sat on "Review your selections" showing "Not Responding" for ten minutes,
twice. The first time a download did eventually start and finish. The second time
it did not. Downloaded the same bundle directly instead:

```
https://download.osgeo.org/postgis/windows/pg18/postgis-bundle-pg18x64-setup-3.6.2-1.exe
```

**6. `password authentication failed for user "postgres"`**

The password chosen during install was rejected. Reset it by temporarily setting
`scram-sha-256` to `trust` for the two localhost lines in `pg_hba.conf`,
restarting the service, running `ALTER USER postgres WITH PASSWORD`, then
changing the file back and restarting again.

**7. OSGeo4W setup closed instantly**

Running `osgeo4w-setup.exe` straight from the Mac share (`Z:`) closed it after a
second. Copying it to the Windows disk first and running it from there worked.

**8. OpenStreetMap tiles returned 403**

```
403 Access blocked. App is not following the tile usage policy of
OpenStreetMap's volunteer-run servers: osm.wiki/Blocked
```

The districts drew correctly, so this was only the basemap. The same tile URL
worked from the host machine, so the block was specific to this VM's connection,
most likely because it exits through a VPN. CARTO's basemaps were tried next and
now require an API key (`API KEY REQUIRED` watermark). The page was switched to
OpenStreetMap France tiles, which serve the same OSM data from different servers:

```javascript
L.tileLayer('https://{s}.tile.openstreetmap.fr/osmfr/{z}/{x}/{y}.png', { ... })
```

Worth noting that the handout's own tile dictionary has trailing spaces in three
province names (`'Khyber Pakhtunkhwa '`, `'Azad Kashmir '`,
`'Gilgit Baltistan '`), which do not match the values in the database. Three
provinces would have rendered grey. Stripped them.

**9. Brave blocked the developer tools**

```
DevTools not allowed on this page
Your organization has blocked the use of DevTools on this page
```

Set by a machine policy, `DeveloperToolsDisabled = 1` under
`HKLM\SOFTWARE\Policies\BraveSoftware\Brave`. Removed the value and restarted
the browser.

---

## Part 8 — areas compared three ways

One SQL statement, three ways of measuring the same polygons:

| District | Latitude | `area_km2` stored | `ST_Area(geography)` | `ST_Transform(3857)` | Mercator inflation |
|---|---|---|---|---|---|
| Gwadar | 25.4 | 11,574 | 11,575 | 14,246 | **+23.1%** |
| Lahore | 31.5 | 1,730 | 1,730 | 2,386 | **+37.9%** |
| Gilgit | 36.0 | 4,016 | 4,015 | 6,133 | **+52.7%** |

The stored figure and the geodesic area agree to the nearest square kilometre.
They are the same measurement arrived at two different ways, which is a useful
check that the loaded data is right.

Web Mercator is inflated, and it gets worse the further north the district is:
23% at Gwadar, 38% at Lahore, 53% at Gilgit. That is the 1/cos²(latitude) factor
from Lab 2 appearing again, this time computed by PostGIS rather than by QGIS.
Gilgit sits at 36° N, where 1/cos²(36°) = 1.53, so a 53% overstatement is
expected rather than surprising.

---

## Questions

### 1. In Labs 2 and 3, every visitor downloaded the whole GeoJSON file. What can a page backed by Django and PostGIS do instead? Give two examples from this lab.

It can decide what to send, and do work before sending anything.

The first example is the province filter. `/api/districts/?province=Sindh`
returns 29 features instead of 160, because the filtering happens in the database
query. The browser never receives the other 131 districts.

The second is `/api/where/`, which takes a latitude and longitude and returns one
district's name and population. No geometry crosses the network at all. PostGIS
does a `ST_Contains` on the server and answers with three values.

A third difference is that the data can be corrected without editing a file and
pushing a commit. The admin panel writes straight to the table.

### 2. Why does settings.py read the database password from an environment variable, instead of containing the password itself?

Because `settings.py` goes into the repository, and the repository is public. A
password written in it would be readable by anyone who opens the GitHub page, and
it would stay in the commit history even if it were removed later.

With the variable, the file only says where to look for the password. Each
machine supplies its own value, and the same file can be committed and shared
without exposing anything. It also means a different password can be used on a
server without editing code.

### 3. In Part 4, the SQL ends with an index created USING GIST. What does it do, and which query in this lab benefits from it?

It is a spatial index on the `geom` column. GeoDjango adds it to every geometry
field automatically, whether or not you ask for one.

Without it, a question like "which district contains this point" has to test the
point against the full outline of all 160 districts. With it, PostGIS keeps a
tree of bounding boxes, so it can discard in a couple of comparisons any district
whose box does not contain the point, and only check the outlines of the few that
might.

The query that benefits is the one in `district_at`, `geom__contains=point`,
which becomes `ST_Contains` in SQL. That is the query behind clicking on the map.
The province filter does not benefit. It filters on a text column and never
touches the geometry.

### 4. The loading script wraps some geometries in a MultiPolygon. What would happen without that step, and why?

The column is a `MultiPolygonField`, so PostGIS creates it as
`geometry(MULTIPOLYGON,4326)`. PostGIS checks the type on insert, so a plain
`POLYGON` value would be rejected with an error saying the geometry type does not
match the column type, and that district would not be loaded.

Wrapping a single polygon in a MultiPolygon of one part changes nothing on the
map. It is the same shape, described with one extra level of nesting, which is
what the column insists on.

In this case the step never actually fired. Lab 2's export had already normalised
all 160 features to MultiPolygon, so the loader reported `MultiPolygon` for the
layer and every feature arrived already wrapped. The handout expects `Unknown`
because its copy of the file mixes 152 polygons with 8 multipolygons. The check
is still correct, it just had nothing to do here.

### 5. Who should have an admin login for a district dataset like this, and who should not?

The people who maintain the data, and almost nobody else.

Anyone who can log in can edit or delete every row, including the geometry. There
is no undo beyond the history page. So accounts should go to the small group
responsible for the boundary layer, not the whole department and certainly not
the public.

Where other people need access, Django's permissions allow a narrower account:
one that can change a population figure but not delete a district, or one that
can only view. That covers most of the realistic cases. The admin is a tool for
the people who look after the data, and the public map should read from the API
instead.

### 6. Leaflet takes [lat, lng] and Point takes (lng, lat). Where in this lab is each order used, and how does the extension's URL avoid confusing them?

Leaflet uses latitude first in three places on the map page: `setView([30.3753, 69.3451], 5)`,
the `maxBounds` box `[[21.5, 58.5], [39.0, 80.5]]`, and the click handler, where
`e.latlng.lat` and `e.latlng.lng` are read separately.

GEOS uses longitude first. In the view the point is built as
`Point(lng, lat, srid=4326)`, which is the same order GeoJSON uses.

The URL avoids the confusion by naming both values. The handler sends
`?lat=33.6423&lng=72.9906`, and the view reads `request.GET["lat"]` and
`request.GET["lng"]` by name rather than by position. Nothing depends on the
order they appear in the string, and a swapped pair would have to be swapped
deliberately in the code.

Inside the database the order is GeoJSON's, because `ST_MakePoint(72.9906, 33.6423)`
in the pgAdmin query is longitude first as well. That is the one place where the
order is easy to get wrong silently, since a wrong point usually still lands
somewhere inside Pakistan.
