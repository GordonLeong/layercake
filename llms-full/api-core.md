# Core API

## Overview
`layercake` exposes one stateful chart orchestrator (`LayerCake`), five render-layer components, and utility functions for data transformation and scale preparation. The central mental model is: **you own marks, LayerCake owns chart math and context wiring**.

---

## Package Root Exports
Import all public APIs from:

```js
import { ... } from 'layercake';
```

### Component Exports
- `LayerCake`
- `Html`
- `Svg`
- `ScaledSvg`
- `Canvas`
- `WebGL`

### Utility Exports
- `scaleCanvas`
- `flatten`
- `uniques`
- `calcExtents`
- `raise`
- `takeEvery`
- `bin`
- `stack`
- `groupLonger`

---

## `LayerCake` Component

### Role
The chart orchestrator. It:
1. Measures container width/height.
2. Converts `x/y/z/r` definitions into accessors.
3. Computes extents and domains.
4. Instantiates and configures D3 scales.
5. Exposes stores/context/slot-props to child layers.

### Important Props (grouped by responsibility)

#### Data and Accessors
- `data: Array<Object> | Object` — source data.
- `flatData?: Array<Object>` — flat rows for scale computation when `data` is nested.
- `x | y | z | r` — accessor definitions (`string | number | function | array`), normalized internally.

#### Domains
- `xDomain | yDomain | zDomain | rDomain`
  - accepts explicit arrays, partial arrays with `null` (`[0, null]`), or a function `(computedDomain) => newDomain`.

#### Scales
- `xScale | yScale | zScale | rScale`
  - defaults: linear, linear, linear, sqrt.
  - pass D3 scale factories/instances to override.

#### Ranges and Direction
- `xRange | yRange | zRange | rRange` — explicit range array or range function receiving `{ width, height }`.
- `xReverse | yReverse | zReverse | rReverse` — reverse default ranges.
- `percentRange` — force default ranges to `[0, 100]` (except `r` min remains logic-compatible through range handling).

#### Scale Post-processing
- `xPadding | yPadding | zPadding | rPadding` — pixel-based domain expansion via scale inversion logic.
- `xNice | yNice | zNice | rNice` — run `.nice()` when available.
- `xDomainSort | yDomainSort | zDomainSort | rDomainSort` — sorting behavior for unique categorical domains.

#### Layout/Runtime
- `padding` — chart inner margin object.
- `width | height` — manual dimension overrides.
- `ssr` — SSR-safe rendering mode.
- `debug` — debounced debug logging.
- `verbose` — runtime warnings (like zero-sized container).
- `pointerEvents`, `position` — wrapper-level behavior.

### What `LayerCake` Produces for Children
Child components access stores/context such as:
- geometry: `width`, `height`, `containerWidth`, `containerHeight`, `aspectRatio`, `padding`
- data: `data`, `flatData`, `extents`
- scales/domains/ranges: `xScale`, `yScale`, `zScale`, `rScale`, `xDomain`, ..., `xRange`, ...
- mapped getters: `xGet`, `yGet`, `zGet`, `rGet`

### Minimal Example
```svelte
<script>
  import { LayerCake, Svg } from 'layercake';
  const data = [{ x: 0, y: 1 }, { x: 1, y: 3 }];
</script>

<div style="width:600px;height:300px;">
  <LayerCake x="x" y="y" {data}>
    <Svg let:data let:xGet let:yGet>
      {#each data as d}
        <circle cx={xGet(d)} cy={yGet(d)} r="3" />
      {/each}
    </Svg>
  </LayerCake>
</div>
```

---

## Layout Components

## `Svg`
Use for vector rendering (axes, lines, labels, defs, markers).
- Accessibility props: `label`, `labelledBy`, `describedBy`.
- Title options:
  - `titleText` prop (simple).
  - `title` snippet (advanced; takes precedence).
- `defs` snippet for SVG defs.
- `overflow` prop (`'visible'` or `'hidden'`).

## `ScaledSvg`
SVG-oriented layer variant for scaled output contexts. Use when you need the same chart context with scaled-svg behavior (for example export-oriented composition).

## `Html`
Absolute-positioned HTML layer aligned to chart box. Use for tooltip portals, rich labels, or DOM interactions that are easier in HTML than SVG.

## `Canvas`
Canvas rendering layer.
- Initializes 2D context on mount.
- Runs `scaleCanvas(context, width, height)` for DPI correctness.
- Exposes context via Svelte context key `canvas` with store `ctx`.

## `WebGL`
WebGL rendering layer.
- Tries context names in order: `webgl`, `experimental-webgl`, `moz-webgl`, `webkit-3d`.
- Accepts `contextAttributes`.
- Exposes context via key `gl` with store `gl`.

---

## Utility APIs

### `flatten(list, accessor = d => d)`
Flattens one level only when accessor returns arrays.
- If input is not array: returns input unchanged.
- Accessor can be string key or function.

### `uniques(list, accessor, transform = true)`
Returns unique values in encounter order (used for categorical workflows).

### `calcExtents(data, fields)`
Computes min/max per field.
- `fields` is an object map: `{ x: d => d.x }`.
- Skips `false`, `undefined`, `null`, and `NaN`.
- If accessor returns arrays, all elements are considered.

### `bin(data, value?, options?)`
Wrapper over `d3-array/bin`.
- Supports `value` accessor as function/string/number.
- Supports `domain` and `thresholds` pass-through.
- Returns bins with `x0` and `x1` boundaries.

### `stack(data, keys, options?)`
Wrapper over `d3-shape/stack`.
- `keys` must be array of series names.
- Optional `value`, `order`, `offset` pass-through.

### `groupLonger(data, keys, keyName?, valueName?)`
Transforms wide rows to long-form rows for grouped/stacked workflows.

### `takeEvery(list, n)`
Sampling helper (every nth item).

### `raise(el)`
Reorders SVG nodes for hover/focus layering.

### `scaleCanvas(ctx, width, height)`
Canvas DPR scaling helper.
