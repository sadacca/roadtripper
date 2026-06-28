# Inputs

`shapes.json` holds the source silhouette for each maze: a simplified,
stylized polygon (not survey-accurate) per region, plus the maze grid
resolution (`cols` x `rows`) used to carve it. Montréal/Québec uses a maple
leaf silhouette as a stand-in shape, since the actual province border is too
large and irregular to carve a fun maze into.

Regenerating `index.html` from this data re-runs the maze carve (a
randomized recursive-backtracker over grid cells whose centers fall inside
the polygon), so re-running without a fixed random seed will produce a
different maze layout each time.
