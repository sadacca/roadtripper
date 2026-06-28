# Road Tripper

Supporting materials (games, checklists, maps, language tools, etc.) for
road trips, organized by trip.

## Structure

```
trips/
  <trip-slug>/
    README.md        trip route, dates, regions covered
    bingo/            bingo boards for this trip
    checklists/       packing/prep/stop checklists
    maps/             route maps, stop lists
    language/         phrasebooks, language-learning aids
    <other-theme>/    add a theme folder as needed
```

Each theme folder holds one subfolder per piece of material, e.g.
`trips/<trip-slug>/bingo/<game-slug>/`. A material with reviewable input
data (board content, checklist items, vocab lists, etc.) keeps that data in
an `inputs/` subfolder, separate from the rendered/playable file, e.g.:

```
trips/<trip-slug>/bingo/<game-slug>/
  index.html          the playable/printable material
  inputs/
    items.json        reviewable source data
    README.md         notes on the input format
```

Not every material needs `inputs/` — only ones with data worth reviewing
independently of the rendering.

## Browsing by theme

To see all materials of one theme across every trip, see
[`THEMES.md`](THEMES.md).

## Trips

- [`mid-atlantic-to-montreal`](trips/mid-atlantic-to-montreal/) — Mid-Atlantic
  (MD/DE/NJ) up to Montréal, QC, via NY.
