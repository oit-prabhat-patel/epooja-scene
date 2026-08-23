# ePooja 3D scene (visuals-only)

Self-contained three.js page for the Dharmayana **ePooja** feature. In production it
is embedded in the app via a `BridgeWebView` and driven entirely by the Flutter app
over a small JSON bridge; it renders visuals only (no audio, no timeline — those live
in the app).

**This repo is a development deploy** served via GitHub Pages so the scene can be
iterated on and previewed without app infrastructure. Source of truth during active
development currently lives alongside the Flutter code; this repo mirrors it.

## Live preview

- Bare page (waits for the app to send `loadMurti`): `./`
- Standalone demo with a sample deity: **`./?murti=sample.glb`**

`sample.glb` is a dev-only sample murti (Tripo-generated). In production the app sends
the real murti + prop (`lamp`/`flower`) GLB URLs from the catalogue; lit diyas and the
flower shower appear once those prop GLBs are supplied.

## Bridge protocol

App → scene (native: `window.__appWebBridge.receive(jsonStr)`; web: a `postMessage`
from the parent), each a `{v,type,payload?}` envelope:

- `loadMurti {murtiUrl}` · `addProps {props:[{type:'lamp'|'flower',url}]}`
- `cue {propType,on}` · `setOrbitEnabled {enabled}` · `pause` · `resume`

Scene → app (native: `window.AppWebBridge.postMessage(jsonStr)`; web:
`parent.postMessage(jsonStr,'*')`):

- `sceneReady` · `murtiProgress {pct}` · `murtiLoaded` · `propsLoaded`
- `fpsReport {avg}` · `error {reason}`

## Notes

- three.js is pinned to r0.137.5 (classic `examples/js` globals).
- Assets (murti + props) are fetched from the URLs the app supplies — they must be
  CORS-accessible from this origin.
- Pre-production hardening: self-host three.js/DRACO/HDRI (with SRI), pin the HDRI to a
  first-party origin, and restrict inbound `postMessage` to the app origin.
