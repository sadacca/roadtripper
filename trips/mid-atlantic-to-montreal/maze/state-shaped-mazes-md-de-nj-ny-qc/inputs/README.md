# Inputs

`shapes.json` holds the source data for each maze:

- `md`, `de`, `nj`, `ny` — real geometry, sourced from the `us-atlas` package
  (U.S. Census Bureau TIGER/Line state and county boundaries, 1:10m
  resolution). Each entry lists the real counties (`id`/`name`) and the real
  county-to-county adjacency edges (derived from shared TopoJSON arcs, i.e.
  actual touching/bridged borders, not guesswork) used to build that state's
  maze, plus the maze grid size.
- `qc` — Québec, sourced from `highcharts/map-collection-dist`'s
  `ca-qc-all.topo.json` (real MRC/municipalité régionale de comté and TÉ
  boundaries), decoded with `topojson` and pulled via the npm registry since
  `statcan.gc.ca` itself isn't reachable from this network. The entry lists
  the real regions, the real region-to-region adjacency edges (derived from
  shared TopoJSON arcs), the excluded far-north regions, the one manual
  bridge, and the maze grid size/cell count.

## How the real mazes are built

Each of MD/DE/NJ/NY/QC is rebuilt directly from real geometry rather than a
hand-drawn outline:

1. The state/county polygons come from `us-atlas` (derived from Census
   TIGER/Line data); the Québec MRC polygons come from the highcharts
   topojson. Both are pulled via the npm registry since the official
   `census.gov`/TIGERweb and `statcan.gc.ca` sources aren't reachable from
   this network.
2. A fine grid is laid over the real outline; each grid cell is assigned to
   whichever real county/MRC polygon contains it (point-in-polygon).
3. A spanning tree is computed over the *real* adjacency graph (which
   county/MRC actually borders which), so crossing from one region into
   another is only possible through one or two grid cells right at their
   real shared border — a "doorway" standing in for the crossing. New
   York's Staten Island/Richmond County, which doesn't share a land border
   with anything, gets one manually-added doorway to Kings County,
   representing the Verrazzano-Narrows Bridge. Québec's Île-d'Orléans,
   which has no shared-arc land border in the source topology (it's an
   island reached only by bridge), gets one manually-added doorway to La
   Côte-de-Beaupré, representing the Île-d'Orléans bridge.
4. Within that restricted cell graph, a randomized recursive-backtracker
   carves the actual maze (a spanning tree of the grid), so each
   county/MRC's interior is densely mazed and the region map itself
   controls the macro route between regions.
5. Small offshore islands or regions that end up disconnected from the main
   carved maze (e.g. some Chesapeake Bay islands in MD, the Gaspé
   peninsula's tip and Îles-de-la-Madeleine in QC) are left as outline-only,
   since they're too small/remote to carve a usable passage into at this
   grid resolution.

Real county/MRC borders are drawn bold on top of the carved maze walls so
the underlying map structure stays visible while solving.

### Québec-specific notes

Québec the province reaches past the 62nd parallel — including its real
far-north MRC-equivalents (Nord-du-Québec, Sept-Rivières--Caniapiscau,
Minganie--Le Golfe-du-Saint-Laurent, Le Saguenay-et-son-Fjord) in the grid
would starve the densely-packed small MRCs around Montréal and Québec City
of any usable resolution, so those four vast, sparsely-populated regions
are excluded from the maze entirely (the trip's route never goes near
them). The remaining 94 regions cover the populated St. Lawrence corridor
the trip actually visits.

A real region-adjacency edge from the source topology can still have zero
buildable grid-cell doors if the shared border is too short or thin to
register at this grid resolution (common for river-separated regions like
Montréal/Laval/Longueuil) — picking such an edge for the spanning tree
would permanently sever the maze into disconnected blobs no matter how much
the grid resolution is increased. The generator filters the adjacency graph
down to edges with at least one real, buildable door *before* picking the
spanning tree, then tries many random seeds and keeps whichever gives the
best real-region coverage, since even a guaranteed-buildable edge's exact
door-cell pick can otherwise strand a small region in a pixel-isolated
pocket. This reaches 91 of 94 included regions (the Gaspé peninsula's tip
and the intentionally-disconnected Îles-de-la-Madeleine are left as
outline-only).
