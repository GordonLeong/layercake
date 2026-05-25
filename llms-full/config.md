# Configuration

## Config Method
Programmatic via Svelte component props on `LayerCake` and layout components.

## LayerCake Reference (high-impact keys)
| Key | Type | Default | Description |
|---|---|---|---|
| `data` | `Array<Object>\|Object` | `[]` | Source dataset. |
| `flatData` | `Array<Object>` | `data` | Flat rows used for scale calculations. |
| `x`,`y`,`z`,`r` | `string\|number\|Function\|Array` | `undefined` | Channel accessors. |
| `xScale`,`yScale`,`zScale`,`rScale` | D3 scale factory/instance | linear/linear/linear/sqrt | Per-channel scale selection. |
| `xDomain`...`rDomain` | `Array\|Function\|undefined` | auto | Domain overrides (supports partial `null`). |
| `xRange`...`rRange` | `Array\|Function\|undefined` | auto | Range overrides. |
| `xPadding`...`rPadding` | `[number, number]` | `undefined` | Domain padding in pixels (converted through scale). |
| `xNice`,`yNice`,`zNice`,`rNice` | `boolean\|number` | `false` | Calls scale `.nice()` when available. |
| `xReverse`,`yReverse`,`zReverse`,`rReverse` | `boolean` | `false`,`dynamic`,`false`,`false` | Reverses default range direction. |
| `xDomainSort`...`rDomainSort` | `boolean` | `false` | Sorts calculated unique domains for ordinal scales. |
| `padding` | `{top,right,bottom,left}` | all `0` | Inner chart padding. |
| `percentRange` | `boolean` | `false` | Forces default ranges to `[0,100]`. |
| `width`,`height` | `number` | measured | Override measured container dimensions. |
| `ssr` | `boolean` | `false` | Enables server-safe rendering path. |
| `debug` | `boolean` | `false` | Debounced console diagnostics for scales and box. |
| `verbose` | `boolean` | `true` | Enables warning logs (e.g., zero-size container). |

## Runtime/package constraints
- Node engine: `^20.17.0 || >=22.9.0`.
- Peer deps: `svelte >=5`, `typescript ^5.0.2`.

## Minimal Example
```svelte
<LayerCake x="x" y="y" {data} />
```

## Production Example
```svelte
<script>
  import { LayerCake } from 'layercake';
  import { scaleBand, scaleLinear } from 'd3-scale';
</script>

<LayerCake
  data={rows}
  flatData={rows}
  x="category"
  y="value"
  xScale={scaleBand().padding(0.2)}
  yScale={scaleLinear()}
  yDomain={[0, null]}
  yNice={true}
  xDomainSort={true}
  padding={{ top: 10, right: 16, bottom: 24, left: 40 }}
  verbose={true}
/>
```
