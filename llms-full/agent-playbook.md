# Agent Playbook: Building New Custom Marks with LayerCake

## Why this file exists
This file is a step-by-step execution plan for coding agents. Use it when the requested chart does not exist as a ready-made LayerChart component.

---

## Step 1: Decide if LayerCake is required
Use LayerCake directly when at least one is true:
1. You need custom geometry (belief ellipse, hull, cone, stream envelope, custom glyph).
2. You need mixed rendering layers (SVG + Canvas + HTML) with one shared coordinate system.
3. You need low-level control over scale/domain behavior beyond a high-level chart wrapper.

If none are true, use a higher-level chart component instead.

---

## Step 2: Define data-space math first
Never start from pixels.

1. Write formulas in data units.
2. Define each intermediate variable clearly.
3. Handle invalid numeric cases (`NaN`, negative square-root input, empty arrays).

For uncertainty ellipses:
- Inputs: `meanX`, `meanY`, `varX`, `varY`, `covXY`, `k`.
- Derived: `lambdaMin`, `lambdaMax`, `theta`, radii `a`, `b`.
- Output: parametric `(x(t), y(t))` points in **data space**.

---

## Step 3: Project to screen using LayerCake getters
Convert each data-space coordinate through LayerCake context getters:
- `px = xGet({ x })`
- `py = yGet({ y })`

Do not compute pixel transforms manually if LayerCake already owns scales.

---

## Step 4: Choose rendering layer intentionally
- `Svg`: axes, paths, labels, precise vector output.
- `Canvas`: very high point count / heatmaps / dense strokes.
- `Html`: tooltips and DOM-rich overlays.
- `WebGL`: extreme density or GPU workflows.

For most custom marks, start in `Svg` for debuggability.

---

## Step 5: Domain/range correctness checks
Before final output, verify:
1. Parent container has explicit width/height.
2. `flatData` is set if `data` is nested.
3. Domain overrides are intentional (`[0, null]` means lock min only).
4. `yReverse` behavior is expected for your scale type.

---

## Step 6: Minimal implementation template
```svelte
<script>
  import { LayerCake, Svg } from 'layercake';
  export let data;

  function makePath(d, xGet, yGet) {
    // 1) compute geometry in data space
    // 2) project with xGet/yGet
    // 3) return SVG path string
    return '';
  }
</script>

<div style="width:800px;height:420px;">
  <LayerCake x="x" y="y" {data}>
    <Svg let:xGet let:yGet let:data>
      {#each data as d}
        <path d={makePath(d, xGet, yGet)} fill="none" stroke="currentColor" />
      {/each}
    </Svg>
  </LayerCake>
</div>
```

---

## Step 7: Validation checklist for agents
- [ ] No invented API names; every prop/helper exists in `src/lib/index.js`.
- [ ] Geometry computed in data units before projection.
- [ ] No manual scale duplication when `xGet/yGet` is available.
- [ ] Works if chart resizes (no stale cached pixel coordinates).
- [ ] Handles degenerate cases (zero variance, null rows, invalid covariance).

---

## Step 8: Output contract for generated code
When producing final chart code, include:
1. Imports.
2. Data shape assumptions.
3. Scale/domain choices.
4. Geometry helper function.
5. Render layer choice and reason.
6. Edge-case handling strategy.

If any of these are missing, the output is incomplete.
