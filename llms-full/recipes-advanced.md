# Advanced Recipes for Custom Visualizations

## Purpose
This file is for cases where LayerChart does not expose a ready-made mark and you need to build directly on LayerCake primitives/context.

---

## Recipe 1: Belief Ellipse (covariance ellipse) with LayerCake + Svg

### Problem
Given points with covariance stats (`meanX`, `meanY`, `varX`, `varY`, `covXY`), draw uncertainty ellipses that align with chart scales.

### First Principles
An ellipse from covariance is defined by:
1. eigenvalues of covariance matrix → axis lengths
2. eigenvectors (or equivalent angle formula) → rotation
3. confidence multiplier `k` (e.g., 1, 2, 2.4477 for ~95% in 2D normal)

Covariance matrix:

`[ varX   covXY ]`
`[ covXY  varY  ]`

Axis lengths in data units:
- `a = k * sqrt(lambdaMax)`
- `b = k * sqrt(lambdaMin)`

Rotation angle:
- `theta = 0.5 * atan2(2*covXY, varX - varY)`

### Implementation Pattern
```svelte
<script>
  import { LayerCake, Svg } from 'layercake';

  export let rows; // raw observations
  export let ellipses; // [{meanX, meanY, varX, varY, covXY, label}]

  const k = 2.4477; // ~95% confidence radius in 2D Gaussian

  function ellipsePath(e, xGet, yGet, steps = 64) {
    const trace = e.varX + e.varY;
    const det = e.varX * e.varY - e.covXY * e.covXY;
    const root = Math.sqrt(Math.max(0, trace * trace / 4 - det));

    const lambdaMax = trace / 2 + root;
    const lambdaMin = trace / 2 - root;

    const a = k * Math.sqrt(Math.max(0, lambdaMax));
    const b = k * Math.sqrt(Math.max(0, lambdaMin));
    const theta = 0.5 * Math.atan2(2 * e.covXY, e.varX - e.varY);

    let d = '';
    for (let i = 0; i <= steps; i += 1) {
      const t = (i / steps) * Math.PI * 2;
      const ex = a * Math.cos(t);
      const ey = b * Math.sin(t);

      // Rotate in data space around origin
      const rx = ex * Math.cos(theta) - ey * Math.sin(theta);
      const ry = ex * Math.sin(theta) + ey * Math.cos(theta);

      // Translate to mean and project with chart scales
      const px = xGet({ x: e.meanX + rx });
      const py = yGet({ y: e.meanY + ry });

      d += i === 0 ? `M ${px} ${py}` : ` L ${px} ${py}`;
    }
    return d + ' Z';
  }
</script>

<div style="width:800px;height:480px;">
  <LayerCake x="x" y="y" data={rows} yDomain={[0, null]}>
    <Svg let:xGet let:yGet>
      {#each ellipses as e}
        <path d={ellipsePath(e, xGet, yGet)} fill="none" stroke="#7c3aed" stroke-width="2" />
      {/each}
    </Svg>
  </LayerCake>
</div>
```

### Why this is the correct LayerCake usage
- Scale mapping happens through `xGet/yGet`, not manual pixel math.
- Ellipse geometry is solved in data space, then projected, so zoom/domain changes stay correct.
- Works with any compatible x/y scales (linear/time/log with valid domain values).

### Common Failure Modes
- Negative determinant due to noisy covariance estimates → clamp sqrt input with `Math.max(0, ...)`.
- Passing non-flat rows while relying on computed domains → set `flatData`.
- Mixing pixel-space and data-space math in same function.

---

## Recipe 2: Multi-layer hybrid rendering (complex charts)

Use this when marks count is high and labels/axes remain vector/text:

1. `Svg` for axes and annotations.
2. `Canvas` for dense points/heat layers.
3. `Html` for interaction overlays/tooltips.

This split is usually required for advanced charts where LayerChart high-level marks are either missing or too opinionated.

---

## Recipe 3: Manual transforms for novel geometry

For custom geometry (confidence cones, convex hull bundles, radar envelopes):
1. Precompute geometry in JS helper functions.
2. Keep geometry in data units.
3. Project every output coordinate through `xGet/yGet` right before rendering.
4. Never cache projected pixels across domain/size changes without invalidation.
