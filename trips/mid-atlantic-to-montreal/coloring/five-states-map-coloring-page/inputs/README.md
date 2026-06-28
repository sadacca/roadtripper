# Inputs

`regions.json` holds the source data for the coloring page:

- `states` — MD, DE, NJ, NY outlines and county lines come from `us-atlas`
  (U.S. Census Bureau TIGER/Line boundaries, 1:10m resolution); the Québec
  outline and MRC lines come from `highcharts/map-collection-dist`'s
  `ca-qc-all.topo.json` (real MRC/municipalité régionale de comté
  boundaries). Both are pulled via the npm registry since `census.gov`/
  TIGERweb and `statcan.gc.ca` themselves aren't reachable from this
  network. All five regions are projected into one consistent
  equirectangular-with-latitude-correction projection so they line up
  geographically, the same way they actually sit on a map.
- `qc` — only the southern Québec MRCs the trip's route actually passes
  near (cropped to maxlat &le; 47.9, lon between -78.0 and -69.5) are
  included, since the province's real shape reaches past the 62nd
  parallel and would otherwise dwarf MD/DE/NJ/NY in the shared projection.
  The combined outline drawn in bold is a `shapely` `unary_union` of these
  66 real MRC polygons (one clean outer boundary) rather than 66
  overlapping individual MRC outlines.
- `highways` — I-95 (Baltimore → Wilmington → Trenton → Newark → NYC) and
  I-87 (NYC → Albany → Plattsburgh → Montréal), the two corridors this trip
  actually follows. These are real city waypoint coordinates connected by
  lines, not an actual road-centerline shapefile — no real TIGER/Line roads
  layer or OpenStreetMap/Overpass data was reachable from this environment,
  so this is an honest approximation rather than a literal road shape. The
  Montréal waypoint now lands inside the real Montréal MRC shape, traced to
  scale rather than marked with an off-map arrow.
