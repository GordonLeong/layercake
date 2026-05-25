# Common Patterns

## Explicitly provide `flatData` for nested datasets
**When to use**: `data` is hierarchical/grouped but scales should run on flattened rows.
```svelte
<script>
  import { LayerCake, flatten } from 'layercake';
  export let grouped; // [{ key, values: [...] }]
  const flatData = flatten(grouped, 'values');
</script>

<LayerCake data={grouped} {flatData} x="date" y="value" />
```
**Why this works**: `LayerCake` uses `flatData` for extent/domain calculation.

## Mix rendering layers under one scale context
**When to use**: You need SVG axes and Canvas/WebGL marks using identical coordinate transforms.
```svelte
<LayerCake x="x" y="y" {data}>
  <Svg><!-- axes/labels --></Svg>
  <Canvas><!-- many points --></Canvas>
</LayerCake>
```
**Why this works**: All layout components read the same `LayerCake` context stores.

## Use array-valued accessors for interval/span channels
**When to use**: rows contain start/end pairs.
```js
import { calcExtents } from 'layercake';

const ext = calcExtents(data, {
  y: d => [d.start, d.end]
});
```
**Why this works**: `calcExtents` inspects each array entry and updates min/max.

## Keep ordinal domain ordering deterministic
**When to use**: categorical colors/order should be stable regardless of row order.
```svelte
<LayerCake
  x="category"
  xScale={scaleBand()}
  xDomainSort={true}
  {data}
/>
```
**Why this works**: unique-value domain computation sorts when `*DomainSort` is true.

## Pass custom stack/bin options through wrappers
**When to use**: You need D3 stack/bin behavior but want layercake helper API.
```js
import { stack, bin } from 'layercake';
import { stackOrderAscending } from 'd3-shape';

const series = stack(data, ['a', 'b'], { order: stackOrderAscending });
const bins = bin(data, d => d.value, { domain: [0, 100] });
```
**Why this works**: wrappers forward options to underlying D3 generators.
