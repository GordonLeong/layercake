# Antipatterns & Gotchas

## Passing non-array input into extent/unique helpers
**What you might do**:
```js
calcExtents({ not: 'an array' }, { x: d => d.x });
```
**Why it breaks**: `calcExtents` and `calcUniques` throw `TypeError` when first arg is not an array.
**Do this instead**:
```js
calcExtents(rowsArray, { x: d => d.x });
```

## Using legacy `fields` array API for `calcExtents`/`calcUniques`
**What you might do**:
```js
calcUniques(data, [{ field: 'x', accessor: d => d.x }]);
```
**Why it breaks**: helpers now require an object map `{ x: accessor }`, not array descriptors.
**Do this instead**:
```js
calcUniques(data, { x: d => d.x });
```

## Expecting `yReverse` default to always be true
**What you might do**:
```svelte
<LayerCake y="value" yScale={scaleBand()} {data} />
```
**Why it breaks**: default `yReverse` becomes `false` when scale has `bandwidth` (ordinal/band), so orientation differs from linear scale defaults.
**Do this instead**:
```svelte
<LayerCake y="value" yScale={scaleBand()} yReverse={true} {data} />
```

## Setting `*Nice` on scales without `.nice()`
**What you might do**:
```svelte
<LayerCake xNice={true} xScale={scaleOrdinal()} {data} />
```
**Why it breaks**: no crash, but logs error and ignores nice because scale lacks `.nice()`.
**Do this instead**:
```svelte
<LayerCake xScale={scaleLinear()} xNice={true} {data} />
```
or keep ordinal scale and remove `xNice`.

## Continuing to use removed/renamed layout props
**What you might do**:
```svelte
<Svg title="My chart" bind:innerElement={g} />
```
**Why it breaks**: `title` prop renamed to `titleText` and `innerElement` removed in v10.
**Do this instead**:
```svelte
<Svg titleText="My chart" />
```
