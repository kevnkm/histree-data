# 3D Models

Locale-agnostic binary 3D assets (`.glb`) and their registry. A **model** is the historical
thing it depicts — a person, structure, object, vehicle, or document. Organized per country:

```
data/models/<country>/
  models.json        # registry + node↔model join (this is the source of truth)
  <modelId>.glb      # one .glb file per model, stored once
```

## Serving

Load `.glb` binaries via **jsDelivr** (a real CDN with correct binary content-types and
edge caching) — not `raw.githubusercontent.com`:

```
https://cdn.jsdelivr.net/gh/kevnkm/histree-data@main/data/models/<country>/<modelId>.glb
```

## `models.json` schema

A map of `modelId` → model entry. The model is the first-class thing (e.g. King Sejong, the
Silla gold crown, Gyeongbokgung palace); a single model may be referenced by **multiple**
nodes, and a node may reference multiple models. That many-to-many link lives here in
`nodeIds`, so it is stored once and the `ko/` and `en/` node files stay untouched.

```jsonc
{
  "king_sejong": {
    "file": "king_sejong.glb",               // .glb filename within this folder
    "type": "person",                         // person | structure | object | vehicle | document
    "title": { "ko": "세종대왕", "en": "King Sejong" },
    "nodeIds": ["sejong_ascension", "hangul"], // nodes this model belongs to (many-to-many)
    "credit": null,                           // attribution — required by most CC licenses
    "license": null,                          // e.g. "CC-BY-4.0"
    "sourceUrl": null,
    "scale": 1.0                              // optional display scale hint
  }
}
```

### Field notes
- `file` is keyed to the **model**, not a node — the binary is stored once.
- `type` lets the UI pick an icon/label and filter (person, structure, object, vehicle, document).
- `nodeIds` is the many-to-many join. The app builds a reverse index (`nodeId → modelId[]`)
  on load, so opening a node detail modal shows every model tied to it.
- `title` is a localized object; the model has its own caption independent of node titles.
- Date/era are intentionally **not** stored: a model's temporal context comes from the
  node(s) it's attached to via `nodeIds`. (Only add an intrinsic `date` if you later build
  an artifact-first view — e.g. a chronological 3D gallery — that shows models outside a
  single node's context.)
- Every listed entry is meant to render — to hide a model, remove its entry (don't gate it
  with a flag). The catalog grows by adding entries + their `.glb`.
- Always fill `credit` / `license` / `sourceUrl` for real models — CC-licensed models
  require attribution.
