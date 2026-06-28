# Inputs

`shapes.json` holds the source data for each maze:

- `md`, `de`, `nj`, `ny` — real geometry, sourced from the `us-atlas` package
  (U.S. Census Bureau TIGER/Line state and county boundaries, 1:10m
  resolution). Each entry lists the real counties (`id`/`name`) and the real
  county-to-county adjacency edges (derived from shared TopoJSON arcs, i.e.
  actual touching/bridged borders, not guesswork) used to build that state's
  maze, plus the maze grid size.
- `qc` — Montréal/Québec has no county-equivalent boundary dataset reachable
  from this environment, so it keeps the previous stylized maple-leaf
  silhouette as a stand-in shape (clearly labeled as such, not survey data).

## How the real mazes are built

Each of MD/DE/NJ/NY is rebuilt directly from real geometry rather than a
hand-drawn outline:

1. The state and county polygons come from `us-atlas` (derived from Census
   TIGER/Line data), pulled via the npm registry since `census.gov`/TIGERweb
   itself isn't reachable from this network.
2. A fine grid is laid over the real state outline; each grid cell is
   assigned to whichever real county polygon contains it (point-in-polygon).
3. A spanning tree is computed over the *real* county-adjacency graph (which
   county actually borders which), so crossing from one county into another
   is only possible through one or two grid cells right at their real shared
   border — a "doorway" standing in for the crossing. New York's Staten
   Island/Richmond County, which doesn't share a land border with anything,
   gets one manually-added doorway to Kings County, representing the
   Verrazzano-Narrows Bridge.
4. Within that restricted cell graph, a randomized recursive-backtracker
   carves the actual maze (a spanning tree of the grid), so each county's
   interior is densely mazed and the county map itself controls the macro
   route between regions.
5. Small offshore islands that end up disconnected from the main carved
   maze (e.g. some Chesapeake Bay islands) are left as outline-only, since
   they're too small to carve a passage into at this grid resolution.

Real county borders are drawn bold on top of the carved maze walls so the
underlying map structure stays visible while solving.
