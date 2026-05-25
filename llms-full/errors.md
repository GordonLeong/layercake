# Error Handling

## Error Model
Most validation errors are synchronous `TypeError` throws in helper functions. Component-level invalid combinations usually degrade with console warnings/errors instead of throwing.

## Error Types

### `TypeError` from `calcExtents`
- **Raised when**: first arg not array, or second arg missing/array/null.
- **Common cause**: passing nested object as `data` without flattening.
- **Recovery**: pass flat rows and object accessor map (`{ x: d => ... }`).

### `TypeError` from `calcUniques`
- **Raised when**: same shape violations as `calcExtents`.
- **Common cause**: using deprecated array-of-field descriptors.
- **Recovery**: convert to object map syntax.

### `TypeError` from `bin`
- **Raised when**: first arg not object/array.
- **Common cause**: accidentally passing `undefined` dataset.
- **Recovery**: validate data load before calling `bin`.

### `TypeError` from `stack`
- **Raised when**: data missing, or `keys` is not array.
- **Common cause**: passing string key list instead of array.
- **Recovery**: pass `['seriesA', 'seriesB']`.

## Runtime warnings/errors (non-throwing)

### Zero/negative container dimensions
- **Raised when**: measured chart box width/height <= 0 after mount and `verbose=true`.
- **Common cause**: parent element has no explicit size.
- **Recovery**: set CSS width and height on container.

### `*Nice` on incompatible scale
- **Raised when**: `xNice/yNice/...` true but scale has no `.nice()`.
- **Common cause**: using ordinal/threshold scale with nice enabled.
- **Recovery**: remove `*Nice` or use continuous scale.
