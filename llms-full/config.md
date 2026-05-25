# Configuration

## Config Method
LayerCake configuration is **prop-driven**. There are no config files or env vars for chart behavior.

## LayerCake Props Reference

### Data + Accessors
| Key | Type | Default | Description |
|---|---|---|---|
| `data` | `Array<Object>\|Object` | `[]` | Source dataset used in chart composition. |
| `flatData` | `Array<Object>` | `data` | Flat rows for extent/domain calculations. |
| `x`,`y`,`z`,`r` | `string\|number\|Function\|Array` | `undefined` | Channel definitions converted to accessors. |

### Scale Selection
| Key | Type | Default | Description |
|---|---|---|---|
| `xScale` | D3 scale | `scaleLinear` | X channel scale. |
| `yScale` | D3 scale | `scaleLinear` | Y channel scale. |
| `zScale` | D3 scale | `scaleLinear` | Z channel scale. |
| `rScale` | D3 scale | `scaleSqrt` | Radius channel scale. |

### Domain Control
| Key | Type | Default | Description |
|---|---|---|---|
| `xDomain`,`yDomain`,`zDomain`,`rDomain` | `Array\|Function\|undefined` | computed | Manual, partial (`null`), or computed-domain transform override. |
| `xDomainSort`,`yDomainSort`,`zDomainSort`,`rDomainSort` | `boolean` | `false` | Sort computed unique domains for categorical channels. |

### Range + Direction
| Key | Type | Default | Description |
|---|---|---|---|
| `xRange`,`yRange`,`zRange`,`rRange` | `Array\|Function\|undefined` | computed | Override default ranges or derive from `{width,height}`. |
| `xReverse` | `boolean` | `false` | Reverse default x range. |
| `yReverse` | `boolean\|undefined` | dynamic | Defaults to `true` for non-bandwidth scales, else `false`. |
| `zReverse` | `boolean` | `false` | Reverse z range. |
| `rReverse` | `boolean` | `false` | Reverse r range. |
| `percentRange` | `boolean` | `false` | Use `[0,100]` base ranges. |

### Domain/Scale Post-processing
| Key | Type | Default | Description |
|---|---|---|---|
| `xPadding`,`yPadding`,`zPadding`,`rPadding` | `[number, number]` | `undefined` | Pixel padding converted into domain expansion. |
| `xNice`,`yNice`,`zNice`,`rNice` | `boolean\|number` | `false` | Apply `.nice()` when supported by scale. |

### Layout + Runtime
| Key | Type | Default | Description |
|---|---|---|---|
| `padding` | `{top,right,bottom,left}` | all `0` | Inner chart margin. |
| `width`,`height` | `number` | measured | Manual dimensions if container measurement is not desired. |
| `pointerEvents` | `boolean` | `true` | Wrapper pointer-events control. |
| `position` | `string` | `'relative'` | Wrapper positioning (`'absolute'` used for stacking cakes). |
| `ssr` | `boolean` | `false` | SSR-safe render path. |
| `debug` | `boolean` | `false` | Debounced console diagnostics. |
| `verbose` | `boolean` | `true` | Enable warnings (e.g., zero-size container). |
| `custom` | `Object` | `{}` | User-defined values passed through context. |

## Layout Component Config Highlights

### `Svg`
- `titleText`, `title` snippet, `defs` snippet.
- accessibility props (`label`, `labelledBy`, `describedBy`).
- `overflow` prop for clipping behavior.

### `Canvas`
- `fallback` text prop.
- bindables: `element`, `context` (2D context).

### `WebGL`
- `contextAttributes` for context creation options.
- bindables: `element`, `context` (WebGL context).

## Runtime/Package Constraints
- Node: `^20.17.0 || >=22.9.0`
- Peer dependencies:
  - `svelte >=5`
  - `typescript ^5.0.2`

## Minimal Config Example
```svelte
<LayerCake x="x" y="y" {data} />
```

## Production-style Example
```svelte
<script>
  import { LayerCake, Svg, Canvas } from 'layercake';
  import { scaleBand, scaleLinear } from 'd3-scale';

  export let rows;
</script>

<div style="width:100%;height:420px;">
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
    padding={{ top: 10, right: 16, bottom: 30, left: 44 }}
    verbose={true}
  >
    <Svg />
    <Canvas />
  </LayerCake>
</div>
```
