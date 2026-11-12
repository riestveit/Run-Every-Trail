# Bicentennial Trail Planner

Pick trails in Far North Bicentennial Park (Anchorage) and get the shortest route that covers all of them,
with distance, climb, a playable route animation and a GPX download. A **Data check** tab finds gaps,
overlaps, double-backs and dead ends in the trail data.

Everything runs in the browser. There is no server and no build step.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole app |
| `trails.geojson` | The trail data the app loads (GeoJSON, WGS 1984, with Z elevations) |

## Updating the trails

1. Edit `All_Trails_V4` in ArcGIS Pro.
2. **Interpolate Shape** (3D Analyst): surface = Living Atlas *Terrain*, check **Interpolate Vertices Only**.
3. **Features To JSON** on the result: check **Output to GeoJSON**, **Project to WGS_1984**, **Include Z values**.
4. Rename the export to `trails.geojson` and upload it to this repository, replacing the old one
   (*Add file → Upload files*, then *Commit changes*). The site updates within a minute or two.

The app needs the fields `Name`, `Label` (shown first if filled) and `Trail_Type`.
If step 2 was run without *Interpolate Vertices Only*, the app removes the extra vertices itself.

## How routes are found

Trails are split at every junction into pieces. The planner solves the Rural Postman Problem: cover every
selected piece at least once, using other trails as connectors if allowed, with the least total distance.
It joins separate groups of picks with a minimum spanning tree of shortest paths, pairs up odd-degree
junctions with an exact minimum-weight perfect matching (Edmonds' blossom algorithm), and walks the result
as an Euler tour. With all trails selected this matches the classic Chinese Postman answer exactly.

Basemap: Esri World Topographic Map (Esri, USGS, OpenStreetMap contributors).
