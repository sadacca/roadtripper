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
