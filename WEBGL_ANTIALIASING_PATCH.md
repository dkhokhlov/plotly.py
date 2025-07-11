# WebGL Antialiasing Disable Patch

## Overview

This patch disables antialiasing in all WebGL contexts for the plotly.py library by modifying the JavaScript bundle files. Antialiasing can sometimes cause rendering issues or performance problems, and this patch ensures all WebGL contexts are created with `antialias: false`.

## Changes Made

The following JavaScript bundle files have been modified to disable antialiasing:

1. **plotly/package_data/plotly.min.js** - Main plotly.js bundle
2. **plotly/package_data/widgetbundle.js** - Widget bundle for Jupyter environments  
3. **plotly/labextension/static/340.2a23c8275d47a2531dae.js** - JupyterLab extension bundle
4. **js/lib/mimeExtension.js** - MIME extension JavaScript file

## Technical Details

### What was changed:

- `antialias:!0` → `antialias:!1` (antialias: true → antialias: false)
- `antialias:!o.pick` → `antialias:!1` (conditional → always false)
- `antialias:!a.pick` → `antialias:!1` (conditional → always false)  
- `antialias:!r.pick` → `antialias:!1` (conditional → always false)
- `antialias:this._antialias||!1` → `antialias:!1` (conditional → always false)

### Files affected:

All changes target WebGL context creation calls in the plotly.js codebase, specifically:
- `getContext("webgl", options)` calls
- `getContext("experimental-webgl", options)` calls

## Verification

Run the test script to verify the changes:

```bash
python test_simple_antialiasing.py
```

This will check all JavaScript bundles and confirm that:
- No `antialias:!0` (enabled) occurrences remain
- Multiple `antialias:!1` (disabled) occurrences exist

## Impact

- **Performance**: May improve performance on some devices by reducing GPU load
- **Rendering**: Removes antialiasing smoothing effects, resulting in sharper but potentially more pixelated edges
- **Compatibility**: Ensures consistent behavior across different devices and browsers
- **Memory**: May reduce GPU memory usage

## Rollback

To rollback these changes, restore the backup files:

```bash
cp plotly/package_data/plotly.min.js.backup plotly/package_data/plotly.min.js
cp plotly/package_data/widgetbundle.js.backup plotly/package_data/widgetbundle.js  
cp plotly/labextension/static/340.2a23c8275d47a2531dae.js.backup plotly/labextension/static/340.2a23c8275d47a2531dae.js
cp js/lib/mimeExtension.js.backup js/lib/mimeExtension.js
```

## Notes

- This patch affects all WebGL-based plot types including:
  - `scattergl` (WebGL scatter plots)
  - `scatter3d` (3D scatter plots)
  - `surface` (3D surface plots)
  - `mesh3d` (3D mesh plots)
  - And other WebGL-accelerated visualizations

- The patch is applied at the JavaScript level, so it affects all plotting contexts (browser, Jupyter notebooks, JupyterLab, etc.)

- This change is global and cannot be overridden on a per-plot basis with this patch approach.