# Versioning & Stability

- **Semver**: yes (with documented major-version breaks).
- **Release cadence**: active; multiple releases in 2024-2025.
- **Stability**: stable core API with incremental breaking changes in majors.

## Deprecated APIs (still present in source)
- `extents` LayerCake prop — marked deprecated in changelog v9.0.0; use per-channel domains (`xDomain`, `yDomain`, etc.) and computed extents.

## Known Breaking Changes
- **10.0.0 (2025-08-18)**: `Svg` `innerElement` prop removed; layout `overflow` prop added.
- **9.0.0 (2025-07-24)**: `Svg`/`ScaledSvg` `title` prop renamed to `titleText`; title snippet behavior changed; Canvas/WebGL fallback slot removed.
- **8.0.0 (2023-09-19)**: ordinal unique-domain behavior sorted by default (later configurable with domain sort props).
- **5.0.0 (2021-06-20)**: `calcExtents` second argument changed from array descriptors to object map.

## Compatibility notes
- Svelte 5+: current line (`10.x`) targets Svelte `>=5`.
- Svelte 3/4 users should stay on `8.4.4`.
