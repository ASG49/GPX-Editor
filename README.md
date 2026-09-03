# GPXWeaver

A powerful, web-based GPX track editor for simplifying, editing, splitting, and joining GPS tracks. Perfect for cleaning up recorded tracks from apps like GeoTracker before sharing or analyzing.

🔗 **Live App**: [https://asg49.github.io/GPXWeaver/](https://asg49.github.io/GPXWeaver/)

![GPXWeaver](https://img.shields.io/badge/version-6.7-blue) ![License](https://img.shields.io/badge/license-MIT-green)

---

## Features

### ⚡ Performance
- **Fast load** for massive files — tested with 500,000+ points in 120MB+ GPX files
- **Toggle-only marker visibility** — no automatic recalculation on zoom, fast zooming at any scale
- **Clean default** — only the track line is shown on load; markers appear only when you ask for them

### 📉 Track Simplification
- **Douglas-Peucker algorithm** for intelligent point reduction
- **Manual simplification** at any percentage (1–95%) — no auto-simplification
- **Restore Original** to undo simplification at any time
- Simplification respects your current marker visibility setting

### 📍 Points Visibility
- **Manual toggle only** — "●○ Points OFF / ● Points ON" button controls all marker display
- No automatic zoom-based marker loading — zoom freely without performance hits
- **Marker colors**: 🔴 Red (normal/delete mode) · 🟠 Orange (split mode)

### ✏️ Track Editing
- **Drag points** — move any point to adjust position (coordinates rounded to 6 decimal places)
- **Add points** — tap the track line to insert new waypoints with interpolated ele, time, speed, and course
- **Delete points** — long-press (2.6s) on any point to mark for deletion
- **Rectangle Selection** — draw a rectangle to bulk-select and delete multiple points (works on mobile and desktop)
- All edits update the track line in real time

### ✂️ Track Splitting
- **Split mode** — tap any point to divide the track at that location
- Save individual segments as separate GPX files
- Split markers auto-clean after save

### 🔗 Track Joining
- **Smart multi-file loading** — select 2+ files and they join automatically
- **Geometric chaining** — tracks are ordered by geographic proximity of endpoints, not by filename or selection order
- Starting track auto-identified as the one with no incoming connection
- Greedy nearest-neighbor chain eliminates stray connecting lines
- Select 1 file = load normally; select multiple = auto-chain and join

### 🌐 Gap Detection
- **Logarithmic gap threshold slider** (10m – 1,000km)
- Prevents unwanted connector lines between segments
- Adjustable in real time

### 🗺️ Map Layers
- OpenStreetMap
- OpenTopoMap (topographic contours)
- Google Terrain *(default)*
- Google Satellite
- Google Hybrid
- Esri World Imagery

### 📊 Navigation Controls

**Desktop:**
- Zoom display + Points toggle — bottom-left
- Scale bar — bottom-right
- Layer selector — top-right

**Mobile:**
- Zoom display — top-left (below +/− buttons)
- Points toggle — top-right
- Scale bar — bottom-right

### 💾 File Handling
- **Save filename** uses the actual disk filename you loaded, not the internal GPX track name
- Edited files get `_X` suffix (e.g. `MyHike_X.gpx`)
- Current filename displayed in header bar (updates to `_X` on first edit)
- Saved GPX always includes a `<metadata>` block with save filename and UTC timestamp
- All original `<trkpt>` data preserved on save: elevation, time, speed, course, extensions
- **Coordinate precision**: always 6 decimal places (~0.11m)
- **GeoTracker extensions** (`<geotracker:meta c="course" s="speed"/>`) fully preserved and interpolated for added points

### 🔒 Security
- HTML sanitization on all track name display (`sanitizeHTML()`)
- XML sanitization on all GPX output (`sanitizeXML()`)
- Protects against malicious GPX files with injected content

### 📱 Mobile-Optimized
- Responsive layout for phones and tablets
- Touch-friendly controls including rectangle selection
- Compact header with current filename displayed

---

## Usage

### Getting Started

1. **Load a GPX file** — Menu (☰) → Load GPX File → select file(s)
   - Select multiple files to auto-chain and join them geometrically
2. **View your track** — blue line shown immediately; no markers until you ask
3. **Show markers** — click "●○ Points OFF" → becomes "● Points ON"
4. **Edit** — drag points, tap line to add, long-press to delete, or draw a rectangle to bulk-delete
5. **Simplify** — Menu → adjust slider → Apply Simplification → Restore Original if needed
6. **Save** — Menu → Save GPX → file saves with `_X` suffix using your original filename

### Joining Multiple Google Maps Routes
1. Generate your route segments in Google Maps (max 10 waypoints each)
2. Convert each to GPX using [maptogpx.com](https://maptogpx.com)
3. Menu → Load GPX File → select all segment files at once
4. GPXWeaver automatically chains them in the correct geographic order
5. Review the join list, then Join All Tracks

### Rectangle Selection (Bulk Delete)
1. Menu → Enable "Rectangle Select"
2. Draw a rectangle over the points you want to remove
3. Tap "Delete Selected" to remove them all at once

### Splitting a Track
1. Menu → Enable "Split Mode"
2. Tap the point where you want to split
3. Save → downloads as separate GPX files

### Gap Detection (Joined Tracks)
- Menu → adjust Gap Detection slider
- Increase to hide connector lines between segments
- Works in real time

---

## File Format

- **Input**: `.gpx` files (GPX 1.0 and 1.1)
- **Output**: GPX 1.1, all original `<trkpt>` data preserved (ele, time, speed, course, extensions)
- **Metadata**: `<metadata>` block added to every saved file with name and timestamp
- **Coordinates**: 6 decimal places in all output
- **Namespaces**: GeoTracker and other extension namespaces declared at root level

---

## Map Controls Summary

| Control | Desktop | Mobile |
|---------|---------|--------|
| Zoom +/− | top-left | top-left |
| Zoom display | bottom-left | top-left |
| Points toggle | bottom-left | top-right |
| Layer selector | top-right | top-right |
| Scale bar | bottom-right | bottom-right |

---

## Technical Details

- **Single HTML file** — no dependencies to install, works offline after first load
- **Leaflet.js** mapping library
- **Douglas-Peucker** simplification algorithm
- **Haversine** distance formula for accurate geographic calculations
- **Geometric nearest-neighbor chaining** for correct multi-track join order
- All event listeners properly registered inside `DOMContentLoaded`
- XSS protection on all user-generated content inserted into HTML or XML

---

## Recent Improvements (v6.0–v6.7)

✅ **v6.7 — Geometric chaining bug fix**
- Critical fix: `maxIncomingDist` was initialized to `Infinity` instead of `-Infinity`
- Condition `closestIncoming > Infinity` is always false — starting track never updated
- Algorithm silently did nothing; tracks were always joined in browser file order
- One-character fix restores correct geometric chaining behavior ✓

✅ **v6.6 — Geometric track chaining**
- Replaced alphabetical sort with nearest-neighbor geographic chaining
- `haversineDistance()` for accurate endpoint distance calculation
- Starting track = the one whose first point has no close incoming endpoint
- Eliminates stray connecting lines from wrong join order
- Works regardless of browser file selection order or filename

✅ **v6.5 — Gap slider initialization fix**
- Gap Detection slider thumb was rendering at far right on load
- Added explicit initialization inside `DOMContentLoaded`
- Slider and label now correctly show 1.0 km on load

✅ **v6.4 — Slider visibility**
- `accent-color: #3498db` on both sliders for visibility on dark background

✅ **v6.3 — Namespace fix**
- Browser `innerHTML` was injecting redundant `xmlns=` on every `<trkpt>` child element
- Added `cleanInnerXML()` to strip redundant declarations before GPX output
- `xmlns:geotracker` declared at root `<gpx>` level (correct XML practice)
- Saved files now load cleanly in all GPX applications

✅ **v6.2 — Event listeners & metadata**
- All event listeners moved inside `DOMContentLoaded` (was causing menu/layer failures)
- GPX `<metadata>` block (filename + UTC timestamp) added to all saved files
- Coordinate precision fixed to 6 decimal places in all three GPX generators

✅ **v6.1 — Coordinate precision**
- Dragged and added points stored and output at 6 decimal places (was 15+)

✅ **v6.0 — GeoTracker data preservation**
- Full `<trkpt>` innerHTML preserved through all edit operations
- `interpolateSpeed()` and `interpolateCourse()` for added points
- Comprehensive code audit: XSS sanitization, dead code removal, logic fixes

---

## Changelog

### v6.7 (2026-07-23)
- Fixed geometric chaining: `maxIncomingDist` initialized to `-Infinity` (was `Infinity`)
- Starting track now correctly identified; join order now truly geometric ✓

### v6.6 (2026-07-23)
- Geometric nearest-neighbor track chaining replaces alphabetical sort
- `haversineDistance()` and `geometricChainTracks()` functions added
- 500m endpoint threshold handles GPS rounding between shared waypoints

### v6.5 (2026-07-22)
- Gap Detection slider thumb now correctly initialized inside `DOMContentLoaded`
- `accent-color` added to both sliders for dark-background visibility

### v6.4 (2026-07-22)
- Slider accent color fix

### v6.3 (2026-07-20)
- `cleanInnerXML()` strips redundant `xmlns` from trkpt innerHTML before output
- `xmlns:geotracker` declared at root `<gpx>` element in all three generators
- Fixed read-back failure for files saved by v6.2

### v6.2 (2026-07-19)
- All event listeners inside `DOMContentLoaded` (was causing menu/layer control failures)
- `<metadata>` block (filename + UTC timestamp) in all saved GPX files
- Coordinate precision fixed to `toFixed(6)` in `generateGPXForTrack()`
- CSS duplicate `#header` rule fixed (was hiding header bar)

### v6.1 (2026-07-19)
- Dragged and added point coordinates rounded to 6 decimal places

### v6.0 (2026-07-15)
- GeoTracker `<geotracker:meta c="course" s="speed"/>` extensions parsed and preserved
- `interpolateSpeed()` and `interpolateCourse()` for added points (0°/360° wrap handling)
- Major code audit: XSS sanitization, duplicate function removed, dead code cleaned

### v5.99 (2026-07-15)
- All `<trkpt>` data preserved via `innerHTML` capture; all three GPX generators output it verbatim

### v5.98 (2026-07-15)
- Filename displayed in header bar; updates on first edit; clears on track clear

### v5.97 (2026-07-15)
- Save uses actual disk filename not internal GPX `<name>` tag

### v5.96 (2026-07-15)
- `sanitizeHTML()` and `sanitizeXML()` added throughout
- Dead code, duplicate functions, unused constants removed
- Toggle-only rule enforced in all `createMarkers()` call sites

### v5.95 (2026-07-15)
- Toggle-only point visibility; `zoomend` does safety cleanup only
- Fast zooming at any zoom level, no recalculation overhead

### v5.94 (2026-06-24)
- Rectangle selection fixed on mobile (`changedTouches` instead of `touches`)

### v5.87 (2026-06-18)
- Mobile controls: zoom top-left, toggle top-right, scale bottom-right

---

## License

MIT License — see [LICENSE](LICENSE) file for details.

## Author

**Tony Gozdz** © 2026  
Battery R&D Engineer · Serious GPX Geek 🗺️

---

*Tested with files from 100 to 500,000+ points (120MB+ GPX). Handles extreme files efficiently. All editing operations — drag, add, delete, rectangle select, split, join, simplify — work reliably on both mobile and desktop.*
