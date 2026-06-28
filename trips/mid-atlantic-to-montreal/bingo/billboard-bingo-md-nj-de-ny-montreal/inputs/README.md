# Inputs

`items.json` mirrors the `ITEMS` data object embedded in `../index.html`. It's
the thing to review/edit when changing board content (labels, colors, icons,
regions) — review it here, then port approved changes into the `ITEMS`
constant in `index.html`.

Structure: a `national` array (appears on every card) plus one array per
region code (`md`, `de`, `nj`, `ny`, `qc`). Each item: `label`, `sub`, `icon`
(emoji or `null`), `bg`, `color`, `strip` (hex colors).
