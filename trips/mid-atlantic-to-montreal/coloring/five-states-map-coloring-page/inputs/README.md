# Inputs

`regions.json` holds the source data for the coloring page:

- `states` — MD, DE, NJ, NY outlines and county lines come from `us-atlas`
  (U.S. Census Bureau TIGER/Line boundaries, 1:10m resolution), pulled via
  the npm registry since `census.gov`/TIGERweb itself isn't reachable from
  this network. All four states are projected into one consistent
  equirectangular-with-latitude-correction projection so they line up
  geographically, the same way they actually sit on a map.
- `highways` — I-95 (Baltimore → Wilmington → Trenton → Newark → NYC) and
  I-87 (NYC → Albany → Plattsburgh), the two corridors this trip actually
  follows. These are real city waypoint coordinates connected by lines, not
  an actual road-centerline shapefile — no real TIGER/Line roads layer or
  OpenStreetMap/Overpass data was reachable from this environment, so this
  is an honest approximation rather than a literal road shape.
- `montreal_arrow_target` — Montréal/Québec sits outside all four states'
  shapes and has no reachable Canadian boundary dataset, so it's marked
  only with a directional arrow and label off the edge of the map rather
  than a fabricated shape.
