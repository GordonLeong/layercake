# Core API

## Overview
This package exports one orchestration component (`LayerCake`), five layout components, and data/scale helpers. Import from `layercake` package root.

## Exports

### `LayerCake`
- **Signature**: `LayerCake(props)` Svelte component.
- **Returns**: rendered container + context stores.
- **Behavior**: Measures container, normalizes accessors, computes extents/domains/scales/ranges, and exposes these via slot props and context key `LayerCake`.
- **Key props**: `data`, `flatData`, `x|y|z|r`, `xDomain|yDomain|zDomain|rDomain`, `xScale|yScale|zScale|rScale`, `xRange|yRange|zRange|rRange`, `padding`, `percentRange`, `xNice|yNice|zNice|rNice`, `xDomainSort|yDomainSort|zDomainSort|rDomainSort`, `debug`, `verbose`, `ssr`.

### Layout Components

#### `Svg`
- **Signature**: `Svg(props)`.
- **Behavior**: Absolute-positioned `<svg>` aligned to chart padding; supports `titleText`/`title` snippet, `defs`, accessibility attrs, `overflow`.

#### `ScaledSvg`
- **Signature**: `ScaledSvg(props)`.
- **Behavior**: SVG variant that scales with chart dimensions and context coordinates.

#### `Html`
- **Signature**: `Html(props)`.
- **Behavior**: HTML overlay layer aligned to same chart box.

#### `Canvas`
- **Signature**: `Canvas(props)`.
- **Behavior**: Creates 2D context, pixel-ratio scales it with `scaleCanvas`, and provides `canvas` context store (`ctx`).

#### `WebGL`
- **Signature**: `WebGL(props)`.
- **Behavior**: Creates WebGL context (`webgl`/fallback names) and provides `gl` context store.

### Helper Functions

#### `scaleCanvas`
- **Signature**: `scaleCanvas(context: CanvasRenderingContext2D, width: number, height: number): void`
- **Behavior**: Scales canvas for high-DPI rendering.

#### `flatten`
- **Signature**: `flatten(list: Array<any>, accessor?: string|Function): Array<any>`
- **Behavior**: Flattens exactly one level if accessor result is an array; otherwise returns input list unchanged.

#### `uniques`
- **Signature**: `uniques(data: Array<any>, accessor?: string|Function): Array<any>`
- **Behavior**: Returns unique values preserving encounter order.

#### `calcExtents`
- **Signature**: `calcExtents(data: Array<object>, fields: Record<string, Function>): Record<string,[any,any]>`
- **Behavior**: Computes min/max per field, skipping `false`, `undefined`, `null`, and `NaN`; supports array-valued accessors.

#### `raise`
- **Signature**: `raise(el: SVGElement): void`
- **Behavior**: Reorders an SVG element to front.

#### `takeEvery`
- **Signature**: `takeEvery(list: Array<any>, n: number): Array<any>`
- **Behavior**: Returns every nth element.

#### `bin`
- **Signature**: `bin<T>(data: T[], value?: string|number|((d:T)=>number)|null, options?: {domain?: [number,number], thresholds?: number|number[]|Function}): Array<T[] & {x0:number;x1:number}>`
- **Behavior**: Wrapper over `d3-array` binning.

#### `stack`
- **Signature**: `stack(data: Array<any>, keys: Array<string>, options?: {value?: string|number|Function, order?: Array<any>|Function, offset?: Function}): Array<any>`
- **Behavior**: Wrapper over `d3-shape` stack for stacked charts.

#### `groupLonger`
- **Signature**: `groupLonger(data, keys, keyName?, valueName?)`
- **Behavior**: Converts wide rows into long form key/value rows.
