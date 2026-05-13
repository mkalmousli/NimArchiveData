# openlayers-nim

Nim JS bindings for OpenLayers. It follows typescript bidings mostly. Mostly generated with Codex, but guided to provide mostly sane API choices. PRs for more bindings / typed APIs accepted.

The best "docs" are to follow the OpenLayers docs and the ported examples below.

## Examples

The interactive example app and individual example modules are in `examples/`. It uses Karax to switch between the examples.

- Main router app: `examples/app.nim`
- Per-example modules:
  - `examples/simple.nim`
  - `examples/semiTransparentLayer.nim`
  - `examples/selectFeatures.nim`
  - `examples/selectHoverFeatures.nim`
  - `examples/drawFeatures.nim`
  - `examples/earthquakesHeatmap.nim`
  - `examples/drawModifyTraceSnap.nim`
  - `examples/layerOpacity.nim`
  - `examples/attributions.nim`
  - `examples/center.nim`
  - `examples/iconSymbolizer.nim`
  - `examples/markerAnimation.nim`
  - `examples/geoJSON.nim`
  - `examples/layerGroups.nim`

Build the app:

- `nim js -d:nodejs examples/app.nim`

Serve examples locally:

- `nim r examples/server.nim`
- Open `http://127.0.0.1:8080/examples/examples.html`

You can also build any individual example module directly, for example:

- `nim js -d:nodejs examples/drawFeatures.nim`

## Options

This module assumes ESM style modules, which can be opted out of by passing `-d:openlayers.noEsmModules`.
