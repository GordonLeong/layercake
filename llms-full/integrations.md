# Integration Points

## Required Dependencies
- `d3-array` — extent/unique/bin primitives.
- `d3-scale` — default x/y/z/r scales and scale behavior.
- `d3-shape` — stack helper.
- `svelte` — component runtime, context, stores.

## Optional / User-supplied Dependencies
- Additional D3 scales (`scaleBand`, `scaleTime`, `scaleOrdinal`, etc.) — pass as `xScale`/`yScale`/etc.
- `d3-shape` stack order/offset utilities — pass into `stack(..., { order, offset })`.
- WebGL toolkits (for example `regl`) — consume `gl` context from `WebGL` layout.

## Framework Integrations

### Svelte context usage inside mark components
```svelte
<script>
  import { getContext } from 'svelte';
  const { data, xGet, yGet } = getContext('LayerCake');
</script>

{#each $data as d}
  <div style={`left:${$xGet(d)}px;top:${$yGet(d)}px;position:absolute;`} />
{/each}
```

### Canvas integration
```svelte
<script>
  import { getContext } from 'svelte';
  const { ctx } = getContext('canvas');
</script>
```

### WebGL integration
```svelte
<script>
  import { getContext } from 'svelte';
  const { gl } = getContext('gl');
</script>
```
