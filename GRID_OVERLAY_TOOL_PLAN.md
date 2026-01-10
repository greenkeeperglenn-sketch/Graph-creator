# Grid Overlay Tool for Drone Images - Implementation Plan

## Overview
A new tool integrated into the existing Graph Generator application that allows users to overlay a configurable grid on drone images of agricultural trials, map treatments to grid cells based on trial plans, and extract individual plot images for presentation.

---

## 1. Feature Requirements

### Core Functionality
1. **Image Upload & Display**
   - Upload drone image of trial field
   - Display image in a zoomable/pannable viewer
   - Extract image date from EXIF metadata automatically

2. **Grid Configuration**
   - Input: Number of replicates (e.g., 4)
   - Auto-calculate grid dimensions: `treatments.length × replicates`
   - Example: 8 treatments × 4 replicates = 32 cells (8 rows × 4 columns OR 4 rows × 8 columns)
   - Allow user to choose grid orientation (treatments in rows vs columns)

3. **Grid Overlay**
   - Draggable/resizable grid overlay on the image
   - Visual grid with labeled cells
   - Adjustable grid opacity, color, line thickness
   - Corner handles for resizing
   - Rotation capability for angled plots

4. **Treatment Mapping**
   - Drag-and-drop interface to assign treatments to grid positions
   - Visual indication of which cells have which treatments
   - Color-coding by treatment
   - Automatic replication (e.g., Treatment A appears in 4 cells for 4 replicates)

5. **Plot Extraction**
   - Extract individual plot images from grid cells
   - Display all plots in a organized layout grouped by treatment
   - Show treatment name + replicate number on each extracted image
   - Screenshot capability for the entire breakdown view

6. **Data Persistence**
   - Save grid configuration with trial data
   - Store drone image reference/data
   - Persist treatment-to-cell mappings

---

## 2. UI/UX Design

### New UI Section: "Field Map" Tab
Add a new top-level tab alongside the existing graph tabs.

#### Layout Structure
```
┌─────────────────────────────────────────────────────────────┐
│ Header: Logo | Project Name | [Graph Tabs] | [Field Map Tab]│
├─────────────────────────────────────────────────────────────┤
│ Left Panel          │ Center Panel      │ Right Panel       │
│ (280px)             │ (flex-grow)       │ (280px)           │
│                     │                   │                   │
│ IMAGE CONFIG        │ DRONE IMAGE       │ TREATMENT MAPPER  │
│ ───────────────     │ ───────────────   │ ───────────────   │
│ • Upload Image      │                   │ Available:        │
│ • Image Date: auto  │   [Drone Photo    │ □ Control         │
│ • Extracted from    │    with draggable │ □ Treatment A     │
│   EXIF              │    grid overlay]  │ □ Treatment B     │
│                     │                   │ □ Treatment C     │
│ GRID CONFIG         │                   │                   │
│ ───────────────     │ Grid Controls:    │ Assigned:         │
│ • # Replicates: [4] │ • Pan/Zoom        │ [Grid view with   │
│ • # Treatments: 8   │ • Reset           │  color-coded      │
│   (auto from data)  │ • Fit to image    │  treatments]      │
│ • Orientation:      │                   │                   │
│   ○ Rows            │                   │                   │
│   ● Columns         │                   │                   │
│ • Grid: 8 × 4       │                   │                   │
│                     │                   │                   │
│ GRID STYLE          │                   │                   │
│ ───────────────     │                   │                   │
│ • Opacity: [75%]    │                   │                   │
│ • Color: [#10b981]  │                   │                   │
│ • Line Width: [2px] │                   │                   │
│ • Rotation: [0°]    │                   │                   │
│                     │                   │                   │
│ [Extract Plots]     │                   │ [Clear Mapping]   │
└─────────────────────┴───────────────────┴───────────────────┘
│ Footer: [Screenshot] [Save Project] [Export Breakdown]      │
└─────────────────────────────────────────────────────────────┘
```

### Plot Breakdown View
After clicking "Extract Plots", display extracted images:

```
┌─────────────────────────────────────────────────────────────┐
│ Header: « Back to Field Map | Trial: [Name] | Date: [Date]  │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│ CONTROL (4 replicates)                                        │
│ ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐                         │
│ │Plot  │ │Plot  │ │Plot  │ │Plot  │                         │
│ │R1    │ │R2    │ │R3    │ │R4    │                         │
│ └──────┘ └──────┘ └──────┘ └──────┘                         │
│                                                               │
│ TREATMENT A (4 replicates)                                    │
│ ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐                         │
│ │Plot  │ │Plot  │ │Plot  │ │Plot  │                         │
│ │R1    │ │R2    │ │R3    │ │R4    │                         │
│ └──────┘ └──────┘ └──────┘ └──────┘                         │
│                                                               │
│ ... (continues for all treatments)                           │
│                                                               │
├─────────────────────────────────────────────────────────────┤
│ Footer: [Screenshot All] [Export Individual Images]          │
└─────────────────────────────────────────────────────────────┘
```

---

## 3. Data Structure Design

### Extended Tab Object
```javascript
{
  id: "tab_1",
  name: "Graph 1",
  graphTitle: "Trial Results",
  yAxisLabel: "Green Coverage (%)",
  xAxisLabel: "Assessment Date",
  yAxisMin: 0,
  yAxisMax: 100,
  yAxisPercent: true,
  treatments: [...],
  dates: [...],
  currentDateIndex: 0,
  legendAscending: true,

  // NEW: Field Map Data
  fieldMap: {
    imageId: "image_uuid_123",           // Reference to stored image
    imageDate: "2025-01-15",              // Extracted from EXIF
    imageFileName: "field_jan_15.jpg",
    replicates: 4,
    orientation: "columns",               // "rows" or "columns"
    gridRows: 8,
    gridColumns: 4,

    // Grid overlay position/size (as % of image dimensions)
    grid: {
      x: 10,           // % from left
      y: 5,            // % from top
      width: 80,       // % of image width
      height: 90,      // % of image height
      rotation: 0      // degrees
    },

    // Grid styling
    gridStyle: {
      color: "#10b981",
      opacity: 0.75,
      lineWidth: 2
    },

    // Treatment mapping: array indexed by cell position
    // For 8 rows × 4 columns, array has 32 elements
    cellMapping: [
      { row: 0, col: 0, treatmentName: "Control", replicate: 1 },
      { row: 0, col: 1, treatmentName: "Control", replicate: 2 },
      { row: 0, col: 2, treatmentName: "Control", replicate: 3 },
      { row: 0, col: 3, treatmentName: "Control", replicate: 4 },
      { row: 1, col: 0, treatmentName: "Treatment A", replicate: 1 },
      // ... continues for all cells
    ],

    // Extracted plot images (base64 or references)
    extractedPlots: null  // Generated on-demand, not saved
  }
}
```

### Image Storage Options (Choose One)

#### **Option A: IndexedDB (RECOMMENDED)**
- **Pros:**
  - Handles large binary files efficiently
  - No file size bloat in JSON
  - Automatic browser storage
  - Works offline
  - Can store multiple images per project
- **Cons:**
  - More complex implementation
  - Browser-specific storage limits (typically 50MB+)
- **Implementation:**
  ```javascript
  // Create IndexedDB database
  const dbName = "stri_graph_generator_images";
  const storeName = "droneImages";

  // Store structure:
  {
    id: "image_uuid_123",
    projectId: "project_name",
    tabId: "tab_1",
    fileName: "field_jan_15.jpg",
    mimeType: "image/jpeg",
    imageData: Blob,        // Binary image data
    exifData: {...},        // Extracted EXIF metadata
    uploadedAt: "2025-01-10T12:00:00Z"
  }
  ```

#### **Option B: Base64 in JSON**
- **Pros:**
  - Simple implementation
  - Portable (single file export)
  - No additional storage mechanism needed
- **Cons:**
  - ~33% larger file size
  - Slower JSON parsing for large images
  - Not ideal for high-res drone images (5-20MB typical)
- **Implementation:**
  ```javascript
  fieldMap: {
    imageDataUrl: "data:image/jpeg;base64,/9j/4AAQSkZJRg...",
    // ... rest of config
  }
  ```

#### **Option C: File System Access API + Path Reference**
- **Pros:**
  - No file size duplication
  - Keeps original image quality
  - User maintains file organization
- **Cons:**
  - Requires modern browser (Chrome 86+, Edge 86+)
  - User must manage file locations
  - Not portable across devices
  - Requires user permission each session
- **Implementation:**
  ```javascript
  fieldMap: {
    imageFilePath: "/Users/john/trials/field_jan_15.jpg",
    imageFileHandle: FileSystemFileHandle  // For re-access
  }
  ```

#### **Option D: Hybrid - Thumbnail + External File**
- **Pros:**
  - Fast preview loading
  - Portable project files
  - Original quality maintained externally
- **Cons:**
  - User must manage both files
  - More complex workflow
- **Implementation:**
  ```javascript
  fieldMap: {
    thumbnailDataUrl: "data:image/jpeg;base64,...",  // Small preview
    originalFileName: "field_jan_15.jpg",             // User manages
    // ... config
  }
  ```

**RECOMMENDATION: Option A (IndexedDB)** for best balance of functionality and user experience.

---

## 4. Technical Implementation Plan

### Phase 1: Image Upload & Display
**Files to modify:** `index.html` (add new section)

**Components to build:**
1. **Image Upload Handler**
   - Accept: JPG, PNG, TIFF
   - File size validation (warn if > 20MB)
   - EXIF data extraction library (e.g., `exif-js`)
   - Extract: date taken, GPS coordinates, camera model

2. **Image Viewer**
   - Canvas-based or `<img>` with transform controls
   - Pan: Click and drag
   - Zoom: Mouse wheel or pinch gesture
   - Fit-to-view button
   - Use existing glass-morphism styling

3. **IndexedDB Manager**
   - Create database on first use
   - Store/retrieve image blobs
   - Link images to tab IDs
   - Clean up orphaned images

**Dependencies:**
```html
<!-- EXIF extraction -->
<script src="https://cdn.jsdelivr.net/npm/exif-js@2.3.0/exif.min.js"></script>
```

### Phase 2: Grid Overlay System
**Components to build:**

1. **Grid Calculator**
   ```javascript
   function calculateGrid(numTreatments, numReplicates, orientation) {
     if (orientation === 'rows') {
       return { rows: numTreatments, cols: numReplicates };
     } else {
       return { rows: numReplicates, cols: numTreatments };
     }
   }
   ```

2. **Grid Renderer (SVG Overlay)**
   - Similar to existing `addTimelineLine()` pattern
   - SVG with `<rect>` for each cell
   - Drag handles on corners for positioning
   - Resize handles for scaling
   - Rotation handle at top-center
   - Cell labels (optional: row/col numbers)

3. **Grid Interaction Controller**
   - Mouse down on grid: start drag
   - Mouse down on corner: start resize
   - Mouse down on rotation handle: start rotate
   - Update grid coordinates in real-time
   - Snap-to-edge option for alignment

**SVG Structure:**
```html
<svg id="gridOverlay" class="grid-overlay">
  <g id="gridGroup" transform="translate(x,y) rotate(angle)">
    <!-- Grid cells -->
    <rect class="grid-cell" data-row="0" data-col="0" />
    <rect class="grid-cell" data-row="0" data-col="1" />
    <!-- ... more cells ... -->

    <!-- Grid lines -->
    <line class="grid-line" x1="..." y1="..." x2="..." y2="..." />

    <!-- Control handles -->
    <circle class="grid-handle" data-type="resize-tl" />
    <circle class="grid-handle" data-type="resize-tr" />
    <circle class="grid-handle" data-type="resize-bl" />
    <circle class="grid-handle" data-type="resize-br" />
    <circle class="grid-handle" data-type="rotate" />
  </g>
</svg>
```

### Phase 3: Treatment Mapping Interface
**Components to build:**

1. **Treatment List Panel**
   - Display all treatments from current tab
   - Drag source for treatment assignment
   - Color indicator for each treatment
   - Show count: "Assigned: 4/4 replicates"

2. **Grid Cell Assignment**
   - Drop target: each grid cell
   - Visual feedback on hover
   - Color-fill cell with treatment color
   - Display treatment name in cell (small text)
   - Click to unassign

3. **Auto-Fill Patterns** (Optional enhancement)
   - Randomized complete block design (RCBD)
   - Latin square
   - Serpentine pattern
   - Custom patterns

**Drag & Drop Logic:**
```javascript
// Drag start: Treatment panel
treatmentElement.addEventListener('dragstart', (e) => {
  e.dataTransfer.setData('treatment', treatmentName);
});

// Drop: Grid cell
gridCell.addEventListener('drop', (e) => {
  const treatmentName = e.dataTransfer.getData('treatment');
  const row = e.target.dataset.row;
  const col = e.target.dataset.col;
  assignTreatmentToCell(row, col, treatmentName);
});
```

### Phase 4: Plot Extraction
**Components to build:**

1. **Canvas Image Slicer**
   ```javascript
   function extractPlot(imageElement, gridConfig, row, col) {
     const canvas = document.createElement('canvas');
     const ctx = canvas.getContext('2d');

     // Calculate cell dimensions
     const cellWidth = gridConfig.width / gridConfig.cols;
     const cellHeight = gridConfig.height / gridConfig.rows;

     // Calculate source coordinates (accounting for rotation)
     const sx = gridConfig.x + (col * cellWidth);
     const sy = gridConfig.y + (row * cellHeight);

     // Set canvas size
     canvas.width = cellWidth;
     canvas.height = cellHeight;

     // Draw image slice
     ctx.drawImage(imageElement, sx, sy, cellWidth, cellHeight,
                   0, 0, cellWidth, cellHeight);

     return canvas.toDataURL('image/png');
   }
   ```

2. **Breakdown View Generator**
   - Group plots by treatment
   - Display in grid layout (4 columns typical)
   - Add labels: Treatment name, replicate number, date
   - Apply consistent styling (glass-morphism cards)

3. **Batch Export**
   - Individual images: ZIP file download
   - Composite screenshot: Single PNG via html2canvas
   - File naming: `{Treatment}_{Rep}_{Date}.png`

**Dependencies:**
```html
<!-- ZIP generation for batch export -->
<script src="https://cdn.jsdelivr.net/npm/jszip@3.10.1/dist/jszip.min.js"></script>
```

### Phase 5: Data Persistence
**Components to build:**

1. **IndexedDB CRUD Functions**
   ```javascript
   async function saveImageToDB(imageFile, metadata) { ... }
   async function loadImageFromDB(imageId) { ... }
   async function deleteImageFromDB(imageId) { ... }
   async function listAllImages() { ... }
   ```

2. **Extended Save/Load Functions**
   - Modify existing `saveProjectToLocalStorage()` to include fieldMap
   - Modify `exportProjectAsJSON()` to handle image references
   - On import: Check if referenced images exist in IndexedDB
   - Prompt user to re-upload missing images if needed

3. **Project Export Options**
   - **Light Export:** JSON only (references images by ID)
   - **Full Export:** JSON + embedded base64 images (portable but large)
   - **Archive Export:** ZIP containing JSON + image files

---

## 5. File Storage Strategy - RECOMMENDATION

### Recommended Approach: IndexedDB + Optional Export

**Development Storage:**
- Use IndexedDB for active project work
- Automatic persistence in browser
- Fast access, no file management

**Export Options:**
1. **Light Project File (.json)**
   - Contains all graph data + grid configs
   - Image references only (IDs)
   - Small file size (~10-50KB)
   - Requires images to remain in browser storage

2. **Full Project Archive (.zip)**
   - JSON file + all drone images
   - Fully portable
   - Can share with others
   - Import extracts images back to IndexedDB

**Implementation:**
```javascript
// Save to IndexedDB
async function saveProject() {
  // Save JSON to localStorage (existing)
  saveProjectToLocalStorage();

  // Images already in IndexedDB from upload
  // No additional action needed
}

// Export with images
async function exportFullProject() {
  const zip = new JSZip();
  const projectData = getCurrentProjectData();

  // Add JSON
  zip.file('project.json', JSON.stringify(projectData, null, 2));

  // Add images from IndexedDB
  for (const tab of projectData.tabs) {
    if (tab.fieldMap?.imageId) {
      const imageBlob = await loadImageFromDB(tab.fieldMap.imageId);
      zip.file(`images/${tab.fieldMap.imageFileName}`, imageBlob);
    }
  }

  // Download ZIP
  const blob = await zip.generateAsync({ type: 'blob' });
  downloadFile(blob, `${projectData.projectName}_full.zip`);
}
```

**Why This Approach:**
- No JSON bloat from base64 encoding
- Works entirely in browser (matches current architecture)
- User can choose lightweight or full export
- Imported projects automatically restore images to IndexedDB
- Clean separation: JSON = data structure, IndexedDB = media storage

---

## 6. Image Metadata Extraction

### EXIF Data to Extract
```javascript
{
  dateTaken: "2025-01-15 14:32:00",    // EXIF DateTimeOriginal
  camera: "DJI Mavic 3",                // EXIF Model
  gpsLatitude: 9.073928,                // EXIF GPSLatitude
  gpsLongitude: -79.634042,             // EXIF GPSLongitude
  altitude: 120,                        // EXIF GPSAltitude (meters)
  imageWidth: 5280,                     // EXIF ExifImageWidth
  imageHeight: 3956,                    // EXIF ExifImageLength
  orientation: 1                        // EXIF Orientation
}
```

### Library: exif-js
```javascript
function extractImageMetadata(file) {
  return new Promise((resolve) => {
    EXIF.getData(file, function() {
      const exifData = EXIF.getAllTags(this);

      // Parse date
      const dateStr = EXIF.getTag(this, 'DateTimeOriginal') ||
                     EXIF.getTag(this, 'DateTime');
      const imageDate = parseDateFromEXIF(dateStr);

      resolve({
        date: imageDate,
        camera: EXIF.getTag(this, 'Model'),
        gps: {
          lat: EXIF.getTag(this, 'GPSLatitude'),
          lon: EXIF.getTag(this, 'GPSLongitude'),
          alt: EXIF.getTag(this, 'GPSAltitude')
        },
        dimensions: {
          width: EXIF.getTag(this, 'ExifImageWidth'),
          height: EXIF.getTag(this, 'ExifImageLength')
        },
        raw: exifData
      });
    });
  });
}
```

---

## 7. CSS Styling (Match Existing Design)

### Grid Overlay Styles
```css
/* Grid overlay container */
.grid-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: all;
  z-index: 10;
}

/* Grid cells */
.grid-cell {
  fill: transparent;
  stroke: var(--grid-color, #10b981);
  stroke-width: var(--grid-line-width, 2);
  opacity: var(--grid-opacity, 0.75);
  transition: fill 0.2s ease;
}

.grid-cell:hover {
  fill: rgba(16, 185, 129, 0.1);
  cursor: pointer;
}

.grid-cell.assigned {
  fill: var(--treatment-color);
  opacity: 0.4;
}

/* Grid handles */
.grid-handle {
  fill: #10b981;
  stroke: white;
  stroke-width: 2;
  cursor: move;
  r: 8;
}

.grid-handle[data-type^="resize"] {
  cursor: nwse-resize;
}

.grid-handle[data-type="rotate"] {
  cursor: grab;
  fill: #6366f1;
}

/* Field map panel */
.field-map-panel {
  background: rgba(26, 26, 46, 0.6);
  backdrop-filter: blur(20px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 16px;
  padding: 24px;
}

/* Plot cards in breakdown view */
.plot-card {
  background: rgba(26, 26, 46, 0.8);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 8px;
  text-align: center;
}

.plot-card img {
  width: 100%;
  height: auto;
  border-radius: 8px;
  margin-bottom: 8px;
}

.plot-label {
  font-family: 'Montserrat', sans-serif;
  font-size: 11px;
  color: #94a3b8;
  font-weight: 500;
}
```

---

## 8. Integration Points with Existing Code

### Files to Modify
**Primary file:** `/home/user/Graph-creator/index.html`

### Sections to Add/Modify

1. **HTML Structure** (around line 100-200)
   - Add Field Map tab button in header
   - Add field map panel sections (hidden by default)

2. **CSS** (around line 20-600)
   - Add grid overlay styles
   - Add field map panel styles
   - Add plot breakdown styles

3. **JavaScript - Data Structure** (around line 1066-1093)
   - Extend tab object with `fieldMap` property
   - Update `createNewTab()` function

4. **JavaScript - Tab Management** (around line 1094-1150)
   - Add field map tab activation logic
   - Show/hide field map panels

5. **JavaScript - New Functions** (add after line 1810)
   - Image upload and EXIF extraction
   - IndexedDB management
   - Grid overlay rendering and interaction
   - Treatment mapping
   - Plot extraction and breakdown view
   - Extended save/load/export functions

6. **HTML - Dependencies** (in `<head>`)
   - Add exif-js library
   - Add JSZip library (for exports)

---

## 9. User Workflow Example

### Scenario: User has 8 treatments, 4 replicates, and a drone image

**Step 1: Setup Trial Data** (existing functionality)
- Create new tab "Field Trial Jan 2025"
- Paste TSV data with 8 treatments
- Generate graph

**Step 2: Upload Drone Image**
- Click "Field Map" tab
- Click "Upload Image" button
- Select `drone_field_jan15.jpg`
- Tool extracts date: "2025-01-15" from EXIF
- Image displays in center panel

**Step 3: Configure Grid**
- Input: 4 replicates
- Tool calculates: 8 treatments × 4 reps = 32 cells
- Choose orientation: "Treatments in rows" → 8 rows × 4 columns
- Grid appears on image (semi-transparent green)

**Step 4: Position Grid**
- Drag grid overlay to align with field plots
- Resize corners to match plot boundaries
- Rotate if plots are angled
- Adjust opacity/color if needed for visibility

**Step 5: Map Treatments**
- Drag "Control" from treatment panel
- Drop on row 1, columns 1-4 (auto-assigns 4 replicates)
- Drag "Treatment A" to row 2
- Continue for all 8 treatments
- Grid cells now color-coded

**Step 6: Extract Plots**
- Click "Extract Plots" button
- Tool slices image into 32 individual plot images
- Breakdown view displays:
  - **Control:** 4 plot images labeled R1, R2, R3, R4
  - **Treatment A:** 4 plot images
  - ... (all 8 treatments)

**Step 7: Screenshot & Export**
- Click "Screenshot All" → captures breakdown view to clipboard
- Paste into PowerPoint for presentation
- OR click "Export Individual Images" → downloads ZIP with 32 PNGs

**Step 8: Save Project**
- Click "Save Project"
- Project JSON saved to localStorage (includes grid config)
- Drone image saved to IndexedDB
- Later: Load project, all mappings restored

---

## 10. Development Phases & Priorities

### MVP (Minimum Viable Product)
**Goal:** Basic grid overlay and plot extraction

1. ✓ Image upload and display
2. ✓ IndexedDB storage
3. ✓ Static grid overlay (no drag, manual position input)
4. ✓ Simple treatment assignment (dropdown per cell)
5. ✓ Plot extraction
6. ✓ Basic breakdown view
7. ✓ Save/load with field map data

**Estimated complexity:** Medium (2-3 days of focused dev)

### Phase 2: Enhanced Interaction
1. ✓ Draggable/resizable grid
2. ✓ Drag-and-drop treatment mapping
3. ✓ EXIF date extraction
4. ✓ Grid rotation
5. ✓ Improved breakdown view layout

**Estimated complexity:** Medium-High (2 days)

### Phase 3: Polish & Advanced Features
1. ✓ Auto-fill patterns (RCBD, etc.)
2. ✓ Batch image export (ZIP)
3. ✓ Full project archive export
4. ✓ GPS visualization (optional: map overlay)
5. ✓ Plot statistics integration (link to graph data)
6. ✓ Multi-date field maps (temporal comparison)

**Estimated complexity:** High (3-4 days)

---

## 11. Potential Challenges & Solutions

### Challenge 1: Large Image Performance
**Problem:** High-resolution drone images (20+ MP) may slow down canvas operations

**Solutions:**
- Generate downscaled preview for grid overlay interaction (e.g., 2000px max width)
- Use full resolution only for final plot extraction
- Implement lazy loading for breakdown view
- Consider Web Workers for image processing

### Challenge 2: Grid Rotation Math
**Problem:** Calculating rotated cell boundaries is complex

**Solutions:**
- Use SVG transforms (browser handles rotation)
- For extraction: transform coordinates via rotation matrix
```javascript
function rotatePoint(x, y, cx, cy, angle) {
  const rad = angle * Math.PI / 180;
  const cos = Math.cos(rad);
  const sin = Math.sin(rad);
  return {
    x: cos * (x - cx) - sin * (y - cy) + cx,
    y: sin * (x - cx) + cos * (y - cy) + cy
  };
}
```

### Challenge 3: Browser Storage Limits
**Problem:** IndexedDB has browser-specific limits (~50MB Chrome, ~unlimited Firefox)

**Solutions:**
- Warn user if image > 10MB
- Suggest image compression before upload
- Implement storage usage indicator
- Fallback: Prompt to export full archive and clear local storage

### Challenge 4: EXIF Data Missing
**Problem:** Some images lack EXIF metadata

**Solutions:**
- Fallback to file modification date
- Allow manual date entry
- Parse date from filename if follows pattern (e.g., `field_2025-01-15.jpg`)

### Challenge 5: Irregular Plot Shapes
**Problem:** Not all trial layouts are perfect grids

**Solutions:**
- MVP: Rectangular grid only
- Future: Support for:
  - Skipped cells (blank plots)
  - Irregular shapes via polygon selection
  - Free-form regions (Lasso tool)

---

## 12. Testing Checklist

### Functional Tests
- [ ] Upload JPG, PNG, TIFF images
- [ ] EXIF date extraction works
- [ ] Grid calculates correctly for various treatment/replicate counts
- [ ] Grid drag/resize/rotate functions smoothly
- [ ] Treatment assignment via drag-and-drop
- [ ] Plot extraction produces correct image slices
- [ ] Breakdown view displays all plots
- [ ] Screenshot captures breakdown view
- [ ] ZIP export contains all plots with correct names
- [ ] Save/load preserves all field map data
- [ ] IndexedDB stores and retrieves images
- [ ] Full archive export/import works

### Edge Cases
- [ ] Image with no EXIF data
- [ ] Very large image (>20MB)
- [ ] Image with extreme aspect ratio (panorama)
- [ ] Grid with 1 replicate
- [ ] Grid with 20+ treatments
- [ ] Delete image from project
- [ ] Switch between tabs (field map ↔ graph)
- [ ] Browser storage quota exceeded

### Browser Compatibility
- [ ] Chrome/Edge (primary target)
- [ ] Firefox
- [ ] Safari (IndexedDB support)

---

## 13. Future Enhancements

### Advanced Features (Post-MVP)
1. **Multi-Date Comparison**
   - Upload multiple drone images from different dates
   - Align grids across dates
   - Animate temporal changes (GIF/video export)

2. **GPS Integration**
   - Display field location on map
   - Verify plot positions via GPS coordinates
   - Export KML file for Google Earth

3. **Plot-Level Statistics**
   - Link extracted plots to graph data
   - Overlay green coverage % on plot images
   - Highlight plots with significant differences

4. **AI-Assisted Gridding**
   - Auto-detect plot boundaries via image segmentation
   - Suggest grid alignment
   - Computer vision for plot identification

5. **Collaborative Features**
   - Share field maps with team
   - Cloud storage integration
   - Version history

6. **Mobile Support**
   - Responsive design for tablets
   - Touch gestures for grid manipulation
   - Camera upload from mobile device

---

## 14. Summary & Next Steps

### What This Tool Provides
✓ Seamless integration with existing graph generator
✓ Visual mapping of treatments to field plots
✓ Automated plot extraction from drone imagery
✓ Professional presentation-ready output
✓ Efficient data management with IndexedDB
✓ Portable project exports

### Recommended Implementation Order
1. Start with **Phase 1** (Image Upload) - Validate storage approach
2. Build **Phase 2** (Grid Overlay) - Core visual functionality
3. Implement **Phase 3** (Treatment Mapping) - Connect to existing data
4. Develop **Phase 4** (Plot Extraction) - Deliver value to user
5. Finalize **Phase 5** (Persistence) - Production-ready

### Key Decisions to Confirm
1. **Storage:** IndexedDB + ZIP export (recommended) vs. alternatives?
2. **Grid Orientation:** Default to treatments-in-rows or treatments-in-columns?
3. **Cell Assignment:** Drag-and-drop (recommended) vs. click-select?
4. **Export Format:** Individual PNGs in ZIP (recommended) vs. single composite image?

### Estimated Total Development Time
- **MVP:** 2-3 days
- **Full Feature Set:** 7-9 days
- **Polish & Testing:** 2-3 days
- **Total:** ~12-15 days for complete implementation

---

## Questions for Clarification

1. **Grid Layout:** Should treatments be in rows or columns by default? Or user choice?

2. **Treatment Assignment:** Do you want to manually assign each cell, or have presets like:
   - Auto-fill sequentially (T1-R1, T1-R2, T1-R3, T1-R4, T2-R1...)
   - Randomized block design
   - Custom pattern entry

3. **Plot Labels:** What info on extracted plots?
   - Treatment name only
   - Treatment + Replicate number
   - Treatment + Replicate + Date
   - Treatment + Replicate + Date + Statistics (from graph data)

4. **Multiple Images:** Do you need to handle multiple drone images per trial (different dates)?
   - If yes: Store all images and select which to view?
   - If yes: Compare changes over time?

5. **Export Priority:** What's more important?
   - Individual plot images (for detailed analysis)
   - Composite breakdown screenshot (for presentations)
   - Both equally important

6. **Grid Precision:** How precise does grid alignment need to be?
   - Rough approximation OK (faster)
   - Pixel-perfect alignment required (more complex UI)

---

**END OF PLAN**

Ready to start implementation once you confirm the approach and answer clarifying questions!
