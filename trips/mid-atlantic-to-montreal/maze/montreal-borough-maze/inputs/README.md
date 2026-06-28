# Inputs

`shapes.json` holds the source data for the Montréal maze:

- The 19 real boroughs (arrondissements) of the City of Montréal, sourced
  from `codeforamerica/click_that_hood`'s `montreal.geojson` (pulled via
  `raw.githubusercontent.com`, since `donnees.montreal.ca` itself isn't
  reachable from this network).
- The real borough-to-borough adjacency graph. This dataset doesn't publish
  a separate shared-arc topojson, so adjacency is derived directly from the
  raw polygon geometry instead: two boroughs are adjacent if their real
  polygons touch (geometric distance effectively zero), which is the same
  ground truth a topojson shared-arc graph would encode. All 19 boroughs
  are connected by at least one real adjacency edge, so no manual bridge
  edge was needed (unlike, e.g., Staten Island or Île-d'Orléans in the
  five-region maze).
- 12 real landmarks, each verified by point-in-polygon against the actual
  borough geometry before being placed on the map (not asserted from
  memory): Notre-Dame Basilica, Old Port of Montréal, and Mont-Royal
  lookout (Ville-Marie); Saint Joseph's Oratory (Côte-des-Neiges–Notre-
  Dame-de-Grâce); Olympic Stadium and Montréal Botanical Garden
  (Mercier–Hochelaga-Maisonneuve); Atwater Market (Sud-Ouest); Lachine
  Canal (Lachine); Jean-Talon Market (Rosemont–La Petite-Patrie); Parc La
  Fontaine (Plateau-Mont-Royal); Verdun Beach (Verdun–Île-des-Sœurs);
  Parc-nature du Bois-de-Liesse (Saint-Laurent).

## How the maze is built

Same method as the five-region maze (`maze/state-shaped-mazes-md-de-nj-ny-qc/`):

1. A fine grid is laid over the real city outline; each cell is assigned to
   whichever real borough polygon contains it (point-in-polygon).
2. The real adjacency graph is filtered down to edges that have at least
   one real, buildable grid-cell doorway at this resolution, since a real
   adjacency edge can still have zero buildable doors if the shared border
   is too short or thin to register on the grid (this matters here because
   several boroughs meet only at narrow points). A spanning tree is then
   built over that filtered graph; many random seeds are tried and scored
   by how many boroughs end up reachable, keeping the best (all 19 of 19
   boroughs are covered).
3. Within that restricted cell graph, a randomized recursive-backtracker
   carves the actual maze, so each borough's interior is densely mazed and
   the real adjacency graph controls the macro route between boroughs.
4. Real borough borders are drawn as thin dashed lines behind the maze
   (so they read as reference, not extra walls), and the maze layer is
   clipped to the real union of all 19 borough polygons so square grid
   cells don't visibly poke past the bold real city outline drawn on top.

Two blank gaps appear near the middle of the island: Mont-Royal Park and
the Parc Jean-Drapeau islands (Île Sainte-Hélène / Île Notre-Dame). Both
are city parkland administered outside the regular borough system, so
they have no polygon in the source borough dataset and are left as empty
space rather than carved with a maze.

## Medium and Hard difficulty tiers

The page also ships two harder variants built from the same real borough
geometry/adjacency data above, through the same generic difficulty
generator used for the five-region maze (`maze_core2.py`/
`gen_maze_difficulty.py`), kept separate from the baseline generator so the
baseline (Easy) maze is unchanged:

- **Smaller grid cells**: Medium ~0.75x the baseline cell size / ~1.7x the
  target cell count; Hard ~0.58x cell size / ~2.8x target cells.
- **Start/finish anchored to real landmarks** instead of the baseline's
  farthest-grid-point heuristic: Notre-Dame Basilica → Olympic Stadium
  (Medium); Verdun Beach → Parc-nature du Bois-de-Liesse (Hard), both
  verified by point-in-polygon against the real borough geometry. All 12
  baseline landmarks are still marked on both tiers.
- **Deviation distance**: the carver is biased toward continuing straight
  (`straight_bias` = 0.45 Medium / 0.65 Hard), so false paths run farther
  before dead-ending.
- **Looping paths (Hard only)**: ~1.2% of maze cells get an extra wall
  opened between two already-mazed, grid-adjacent cells the spanning tree
  didn't connect directly, braiding in short loops that defeat simple
  wall-following.
- **Varied wall thickness (Hard only)**: each wall segment gets a
  per-segment random stroke width for visual noise that doesn't change the
  maze's actual topology.

All 19 boroughs remain fully connected and covered at both Medium and Hard.
