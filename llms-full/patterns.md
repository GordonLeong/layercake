# Common Patterns

## 1) Core chart composition: one orchestrator, many layers
**When to use**: Most charts.
```svelte
<script>
  import { LayerCake, Svg, Canvas, Html } from 'layercake';
  export let data;
</script>

<LayerCake x="x" y="y" {data}>
  <Svg><!-- axes, lines --></Svg>
  <Canvas><!-- dense points --></Canvas>
  <Html><!-- labels/tooltips --></Html>
</LayerCake>
```
**Why this works**: all layers read identical scale/domain context.

## 2) Nested data input with explicit flattening
**When to use**: grouped/nested datasets (`[{key, values:[...]}]`).
```svelte
<script>
  import { LayerCake, flatten } from 'layercake';
  export let groups;
  const flatData = flatten(groups, 'values');
</script>

<LayerCake data={groups} {flatData} x="date" y="value" />
```
**Why this works**: scale math runs against `flatData`, rendering logic can still use original grouped `data`.

## 3) Partial domain override while preserving computed max
**When to use**: force baseline but keep dynamic upper bound.
```svelte
<LayerCake
  x="date"
  y="amount"
  yDomain={[0, null]}
  yNice={true}
  {data}
/>
```
**Why this works**: `null` tells domain merge logic to keep computed extent side.

## 4) Domain transformation callback
**When to use**: clamp/sort/transform computed domains programmatically.
```svelte
<LayerCake
  x="category"
  xDomain={(computed) => computed.slice().sort()}
  {data}
/>
```
**Why this works**: domain prop function receives computed domain before scale application.

## 5) Interval data via array accessors
**When to use**: spans, ranges, start/end bars.
```js
import { calcExtents } from 'layercake';

const extents = calcExtents(data, {
  y: d => [d.start, d.end]
});
```
**Why this works**: extent engine handles arrays from accessor return values.

## 6) Ordinal stability with explicit sorting
**When to use**: categorical color legends must be deterministic.
```svelte
<script>
  import { scaleBand } from 'd3-scale';
</script>

<LayerCake
  x="category"
  xScale={scaleBand()}
  xDomainSort={true}
  {data}
/>
```
**Why this works**: unique-domain calculation sorts when sort flag is enabled.

## 7) Stack preprocessing with custom value access
**When to use**: wide data and non-primitive nested values.
```js
import { stack } from 'layercake';
import { stackOrderAscending, stackOffsetSilhouette } from 'd3-shape';

const stacked = stack(data, ['apples', 'bananas', 'cherries'], {
  value: (row, key) => row[key].x,
  order: stackOrderAscending,
  offset: stackOffsetSilhouette
});
```
**Why this works**: wrapper forwards options directly to D3 stack generator.
