# Grid Overlay Tool - FINAL IMPLEMENTATION PLAN
## Ready for Development - 2026-01-10

---

## Executive Summary

A field map tool integrated into the existing Graph Generator that allows users to:
1. Upload drone images of agricultural trial fields
2. Overlay a configurable grid matching the field plot layout
3. Map randomized treatments to grid cells via drag-and-drop
4. Extract individual plot images organized by treatment
5. Navigate through multiple dates via timeline
6. Screenshot breakdowns for presentations

**Key Feature:** Configure grid and treatment mapping ONCE, then apply to multiple drone images from different dates.

---

## User Requirements (Confirmed)

### Core Workflow
- ✓ One drone image per assessment date contains ALL plots (e.g., 10 treatments × 4 replicates = 40 plots in one image)
- ✓ Plots are randomized across the field (standard RCBD or similar design)
- ✓ Grid configuration and treatment mapping applies to all images (plots don't move)
- ✓ Multiple drone images with independent dates (don't need to match assessment dates)
- ✓ Timeline navigation through dates (independent from graph timeline)

### UI/UX Requirements
- ✓ Bulk assign mode for faster treatment mapping
- ✓ Reusable treatment labels with countdown (drag same label multiple times)
- ✓ Auto-detect grid orientation from image dimensions
- ✓ Treatment name labels only (no replicate numbers)
- ✓ Composite screenshot for presentations
- ✓ Scrollable breakdown view on computer

### File Management
- ✓ Drag-and-drop raw drone images (JPG, PNG, TIFF)
- ✓ Drag-and-drop project ZIP archives for import/export
- ✓ IndexedDB for browser storage
- ✓ Full ZIP export for portability

---

## Data Structure

### Extended Tab Object
```javascript
{
  id: "tab_1",
  name: "Trial Jan-Mar 2025",

  // GRAPH DATA (existing)
  graphTitle: "Green Coverage Over Time",
  yAxisLabel: "Green Coverage (%)",
  xAxisLabel: "Assessment Date",
  treatments: [
    { name: "Control", values: [45, 52, 61, 68], letters: ["a", "ab", "b", "c"] },
    { name: "Treatment A", values: [48, 58, 72, 80], letters: ["a", "b", "c", "d"] },
    // ... 8 more treatments
  ],
  dates: ["2025-01-10", "2025-01-24", "2025-02-07", "2025-02-21"],  // Assessment dates
  currentDateIndex: 0,

  // FIELD MAP DATA (new)
  fieldMap: {
    enabled: true,

    // GRID CONFIGURATION (applies to all images)
    replicates: 4,
    treatmentCount: 10,  // Auto from treatments.length

    // Grid dimensions (user can override)
    gridRows: 5,          // Could be 5×8, 8×5, 10×4, 4×10, etc.
    gridCols: 8,
    totalCells: 40,

    orientation: "auto",  // "auto", "landscape", "portrait"

    // GRID POSITION & STYLE (applies to all images - same field layout)
    grid: {
      x: 8,               // % from left
      y: 5,               // % from top
      width: 84,          // % of image width
      height: 88,         // % of image height
      rotation: 0         // degrees
    },

    gridStyle: {
      color: "#10b981",
      opacity: 0.75,
      lineWidth: 2,
      showCellNumbers: false  // Optional: show 1-40 in cells
    },

    // TREATMENT MAPPING (applies to all images - randomized plot positions)
    // Each cell maps to one treatment
    cellMapping: [
      { cellIndex: 0, row: 0, col: 0, treatmentName: "Treatment C" },
      { cellIndex: 1, row: 0, col: 1, treatmentName: "Control" },
      { cellIndex: 2, row: 0, col: 2, treatmentName: "Treatment A" },
      { cellIndex: 3, row: 0, col: 3, treatmentName: "Treatment F" },
      { cellIndex: 4, row: 0, col: 4, treatmentName: "Control" },
      { cellIndex: 5, row: 0, col: 5, treatmentName: "Treatment B" },
      { cellIndex: 6, row: 0, col: 6, treatmentName: "Treatment D" },
      { cellIndex: 7, row: 0, col: 7, treatmentName: "Treatment E" },
      // ... 32 more cells (total 40)
      { cellIndex: 39, row: 4, col: 7, treatmentName: "Treatment A" }
    ],

    // DRONE IMAGES (independent dates from assessment dates)
    currentImageIndex: 0,  // Which image is currently displayed
    images: [
      {
        imageId: "img_uuid_001",
        date: "2025-01-08",              // Drone flight date (2 days before assessment)
        fileName: "drone_jan08.jpg",
        uploadedAt: "2025-01-10T09:00:00Z",
        exifData: {
          dateTaken: "2025-01-08T13:45:00Z",
          camera: "DJI Mavic 3",
          gps: { lat: 9.073928, lon: -79.634042, alt: 120 },
          dimensions: { width: 5280, height: 3956 }
        }
      },
      {
        imageId: "img_uuid_002",
        date: "2025-01-20",              // Between assessments
        fileName: "drone_jan20.jpg",
        uploadedAt: "2025-01-22T10:00:00Z",
        exifData: { /* ... */ }
      },
      {
        imageId: "img_uuid_003",
        date: "2025-02-05",
        fileName: "drone_feb05.jpg",
        uploadedAt: "2025-02-06T14:30:00Z",
        exifData: { /* ... */ }
      },
      {
        imageId: "img_uuid_004",
        date: "2025-02-21",              // Matches assessment date
        fileName: "drone_feb21.jpg",
        uploadedAt: "2025-02-21T16:00:00Z",
        exifData: { /* ... */ }
      },
      {
        imageId: "img_uuid_005",
        date: "2025-03-15",              // After all assessments
        fileName: "drone_mar15.jpg",
        uploadedAt: "2025-03-15T11:00:00Z",
        exifData: { /* ... */ }
      }
    ]
  }
}
```

---

## UI Layout

### Field Map View (Grid Configuration Mode)

```
┌──────────────────────────────────────────────────────────────────────┐
│ Header: [Logo] Trial Jan-Mar 2025  [Graph] [Field Map ✓]             │
├──────────────────────────────────────────────────────────────────────┤
│ Image Timeline: Jan 8 ← [●        ] → Mar 15    Date: Jan 8, 2025    │
├─────────────────┬────────────────────────────┬───────────────────────┤
│ LEFT PANEL      │ CENTER PANEL               │ RIGHT PANEL           │
│ (300px)         │ (flex-grow)                │ (320px)               │
│                 │                            │                       │
│ 📁 IMAGE LIBRARY│  🖼️ DRONE IMAGE            │ 🎯 TREATMENT MAPPER   │
│ ───────────────│  ────────────────          │ ────────────────      │
│                 │                            │                       │
│ Drop Zone:      │  [Drone image with         │ Drag treatments to    │
│ ┌─────────────┐│   grid overlay showing     │ grid cells:           │
│ │ Drop images ││   color-coded cells        │                       │
│ │ or ZIP here ││   for assigned treatments] │ Mapping Mode:         │
│ │             ││                            │ ◉ Drag & Drop         │
│ │ 📷 JPG PNG  ││  Grid with:                │ ○ Click to Assign     │
│ │ 📦 ZIP      ││  - Draggable corners       │                       │
│ └─────────────┘│  - Resize handles          │ ─────────────────     │
│                 │  - Rotation handle         │ Control               │
│ Current Images: │  - Cell highlighting       │ ┌──────────────┐    │
│ ✓ Jan 8, 2025   │                            │ │   Control    │4× │
│   Jan 20, 2025  │  Controls:                 │ └──────────────┘    │
│   Feb 5, 2025   │  • Pan (click-drag)        │ Assigned: 4/4 ✓     │
│   Feb 21, 2025  │  • Zoom (wheel)            │                       │
│   Mar 15, 2025  │  • Fit to View             │ Treatment A           │
│                 │  • Reset Grid              │ ┌──────────────┐    │
│ [+ Add Images]  │  • Lock Grid               │ │ Treatment A  │4× │
│ [Remove]        │                            │ └──────────────┘    │
│                 │                            │ Assigned: 3/4         │
│ GRID CONFIG     │                            │                       │
│ ───────────────│                            │ Treatment B           │
│ Replicates: [4] │                            │ ┌──────────────┐    │
│ Treatments: 10  │                            │ │ Treatment B  │4× │
│ Total Cells: 40 │                            │ └──────────────┘    │
│                 │                            │ Assigned: 4/4 ✓     │
│ Grid Dimensions:│                            │                       │
│ Rows: [5] ▴▾    │                            │ ... (7 more)          │
│ Cols: [8] ▴▾    │                            │                       │
│                 │                            │ ─────────────────     │
│ Orientation:    │                            │ Progress:             │
│ ◉ Auto-detect   │                            │ 37/40 cells assigned  │
│ ○ Custom        │                            │ [████████░░] 92%      │
│                 │                            │                       │
│ GRID STYLE      │                            │ [Clear All]           │
│ ───────────────│                            │ [Auto-Fill RCBD]      │
│ Opacity: [75%]  │                            │                       │
│ Color: [■]      │                            │                       │
│ Width: [2px]    │                            │                       │
│ Rotation: [0°]  │                            │                       │
│ □ Show cell #s  │                            │                       │
│                 │                            │                       │
│ [Lock Grid]     │                            │                       │
└─────────────────┴────────────────────────────┴───────────────────────┘
│ Footer: [◀ Prev Image] [View Breakdown] [Next Image ▶] [Screenshot]  │
└──────────────────────────────────────────────────────────────────────┘
```

### Breakdown View (Plot Extraction Display)

```
┌──────────────────────────────────────────────────────────────────────┐
│ « Back to Grid    Trial: Jan-Mar 2025    Drone Image: Jan 8, 2025    │
├──────────────────────────────────────────────────────────────────────┤
│ Image Timeline: Jan 8 ← [●        ] → Mar 15                          │
├──────────────────────────────────────────────────────────────────────┤
│                                                                        │
│  CONTROL (4 replicates)                                                │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐                 │
│  │          │ │          │ │          │ │          │                 │
│  │  [plot   │ │  [plot   │ │  [plot   │ │  [plot   │                 │
│  │   image] │ │   image] │ │   image] │ │   image] │                 │
│  │          │ │          │ │          │ │          │                 │
│  │ Control  │ │ Control  │ │ Control  │ │ Control  │                 │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘                 │
│                                                                        │
│  TREATMENT A (4 replicates)                                            │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐                 │
│  │          │ │          │ │          │ │          │                 │
│  │  [plot   │ │  [plot   │ │  [plot   │ │  [plot   │                 │
│  │   image] │ │   image] │ │   image] │ │   image] │                 │
│  │          │ │          │ │          │ │          │                 │
│  │Treatment │ │Treatment │ │Treatment │ │Treatment │                 │
│  │    A     │ │    A     │ │    A     │ │    A     │                 │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘                 │
│                                                                        │
│  TREATMENT B (4 replicates)                                            │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐                 │
│  │  [plot]  │ │  [plot]  │ │  [plot]  │ │  [plot]  │                 │
│  │Treatment │ │Treatment │ │Treatment │ │Treatment │                 │
│  │    B     │ │    B     │ │    B     │ │    B     │                 │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘                 │
│                                                                        │
│  ... (7 more treatments, scrollable)                                  │
│                                                                        │
│                                                                        │
├──────────────────────────────────────────────────────────────────────┤
│ Footer: [◀ Prev Image] [Screenshot to Clipboard] [Next Image ▶]      │
└──────────────────────────────────────────────────────────────────────┘
```

---

## User Workflow

### Phase 1: Initial Setup (One-Time Configuration)

**Step 1: Create Trial and Graph Data**
- Create new tab "Trial Jan-Mar 2025"
- Paste TSV data with 10 treatments
- Generate graph
- Graph has 4 assessment dates

**Step 2: Upload Drone Images**
- Click "Field Map" tab
- Drop zone appears
- Drag and drop 5 drone images:
  - `drone_jan08.jpg` → EXIF date: Jan 8, 2025
  - `drone_jan20.jpg` → EXIF date: Jan 20, 2025
  - `drone_feb05.jpg` → EXIF date: Feb 5, 2025
  - `drone_feb21.jpg` → EXIF date: Feb 21, 2025
  - `drone_mar15.jpg` → EXIF date: Mar 15, 2025
- Tool extracts EXIF dates automatically
- Images appear in library list (sorted by date)

**Step 3: Configure Grid**
- Left panel shows:
  - Replicates: [4]
  - Treatments: 10 (auto-detected)
  - Total Cells: 40
- Tool auto-detects image is landscape (5280×3956)
- Suggests grid: 8 cols × 5 rows (landscape orientation)
- User accepts or manually adjusts
- Grid overlay appears on image

**Step 4: Position Grid**
- Viewing first image (Jan 8)
- Drag grid to align with field plots
- Resize corners to match plot boundaries
- Rotate if field is angled (use rotation handle)
- Adjust opacity/color for better visibility
- Click "Lock Grid" when satisfied

**Step 5: Map Treatments to Cells**
- Right panel shows treatment mapper
- Each treatment has reusable draggable label + "4×" counter
- User has field randomization plan (on paper or separate file)

**Drag & Drop Method:**
```
Looking at field plan: Control is in positions 1, 4, 12, 23

1. Drag "Control" label → drop on cell 1 (row 0, col 1)
   - Cell fills with color
   - Counter updates: "Control: 3/4"
2. Drag "Control" label again → drop on cell 4 (row 0, col 4)
   - Counter: "Control: 2/4"
3. Drag "Control" label → drop on cell 12 (row 1, col 4)
   - Counter: "Control: 1/4"
4. Drag "Control" label → drop on cell 23 (row 2, col 7)
   - Counter: "Control: 0/4" ✓ (checkmark appears)

Repeat for all 10 treatments...
```

**Bulk Assign Method (Alternative):**
```
1. Click "Bulk Assign" button for "Control"
2. Cursor changes to paint brush mode
3. Click cells: 1, 4, 12, 23
4. After 4 clicks, mode exits automatically
5. All 4 cells assigned to "Control" ✓
```

**Step 6: Verify Mapping**
- Progress bar shows: 40/40 cells assigned ✓
- Visual check: All cells color-coded
- Save auto-saves to localStorage

---

### Phase 2: Daily Usage (Viewing & Screenshots)

**View Different Dates:**
- Image timeline shows: Jan 8, Jan 20, Feb 5, Feb 21, Mar 15
- Click "Jan 20" → Shows Jan 20 image with same grid overlay
- Grid position, rotation, and treatment mapping are identical
- Only the underlying image changes (different growth stage)

**Generate Breakdown:**
- Select image: "Jan 8"
- Click "View Breakdown" button
- Tool extracts all 40 plot images from Jan 8 drone image
- Breakdown view displays:
  - **Control:** 4 plot images (from cells 1, 4, 12, 23)
  - **Treatment A:** 4 plot images
  - **Treatment B:** 4 plot images
  - ... (all 10 treatments)
- Plots grouped by treatment, scrollable

**Screenshot for Presentation:**
- In breakdown view (currently viewing Jan 8)
- Click "Screenshot to Clipboard"
- Breakdown view captured to clipboard (1400px wide)
- Open PowerPoint → Paste → Slide 1 has Jan 8 breakdown
- Navigate to "Feb 5" using timeline
- Click "Screenshot to Clipboard"
- Paste → Slide 2 has Feb 5 breakdown
- Repeat for all dates → 5 slides showing temporal progression

**Compare Dates:**
- Navigate between dates to visually compare growth
- Same plots, different timepoints
- Easy to see treatment effects over time

---

### Phase 3: Project Management

**Save Project:**
- Auto-save: Happens automatically after every change
  - localStorage: JSON data (grid config, mappings)
  - IndexedDB: Drone images (binary)
- Manual export: Click "Export Project"
  - **Light JSON:** Small file with image references
  - **Full ZIP:** Complete archive with images

**Export Full Archive:**
```
trial_jan_mar_2025_full.zip
├── project.json                 (All data)
├── images/
│   ├── drone_jan08.jpg
│   ├── drone_jan20.jpg
│   ├── drone_feb05.jpg
│   ├── drone_feb21.jpg
│   └── drone_mar15.jpg
└── README.txt                   (Instructions)
```

**Load Project (Different Computer):**
- Drop `trial_jan_mar_2025_full.zip` into Field Map tab
- Tool extracts:
  - Reads project.json
  - Extracts all 5 images
  - Saves images to IndexedDB
  - Restores grid configuration
  - Restores treatment mappings
- Result: Exact restoration ✓

**Add New Image Later:**
- New drone flight on Apr 5, 2025
- Drop `drone_apr05.jpg` into image library
- Grid and treatment mapping automatically applied
- New image appears in timeline
- View breakdown immediately (no reconfiguration)

**Remove Image:**
- Click "Jan 20, 2025" in library
- Click "Remove" button
- Confirm deletion
- Image removed from IndexedDB
- Timeline updates (Jan 20 no longer shown)
- Grid config and mappings remain intact

---

## Technical Implementation

### Phase 1: Image Upload & Management

#### 1.1 Drop Zone Handler
```javascript
function setupDropZone() {
  const dropZone = document.getElementById('fieldMapDropZone');

  dropZone.addEventListener('drop', async (e) => {
    e.preventDefault();
    const files = Array.from(e.dataTransfer.files);

    for (const file of files) {
      if (file.name.endsWith('.zip')) {
        // Project import
        await importProjectArchive(file);
      } else if (file.type.startsWith('image/')) {
        // Drone image upload
        await uploadDroneImage(file);
      }
    }
  });
}
```

#### 1.2 Image Upload & EXIF Extraction
```javascript
async function uploadDroneImage(file) {
  // Extract EXIF metadata
  const exifData = await extractEXIF(file);
  const imageDate = exifData.dateTaken || new Date(file.lastModified);

  // Generate thumbnail for library preview
  const thumbnail = await generateThumbnail(file, 200);

  // Save to IndexedDB
  const imageId = generateUUID();
  await saveToIndexedDB({
    id: imageId,
    projectName: currentProjectName,
    tabId: currentTabId,
    date: formatDate(imageDate),
    fileName: file.name,
    mimeType: file.type,
    imageData: file,
    thumbnail: thumbnail,
    uploadedAt: new Date().toISOString(),
    exifData: {
      dateTaken: imageDate,
      camera: exifData.model,
      gps: exifData.gps,
      dimensions: { width: exifData.width, height: exifData.height }
    }
  });

  // Add to fieldMap
  const tab = tabs[currentTabId];
  if (!tab.fieldMap) {
    tab.fieldMap = initializeFieldMap();
  }

  tab.fieldMap.images.push({
    imageId: imageId,
    date: formatDate(imageDate),
    fileName: file.name,
    uploadedAt: new Date().toISOString(),
    exifData: { /* ... */ }
  });

  // Auto-save
  saveProjectToLocalStorage();

  // Update UI
  renderImageLibrary();

  // If first image, auto-configure grid
  if (tab.fieldMap.images.length === 1) {
    autoConfigureGrid(exifData.dimensions);
  }

  showNotification(`Uploaded: ${file.name} (${formatDate(imageDate)})`);
}
```

#### 1.3 IndexedDB Manager
```javascript
class ImageDatabase {
  constructor() {
    this.dbName = 'stri_graph_generator_images';
    this.version = 1;
    this.db = null;
  }

  async open() {
    return new Promise((resolve, reject) => {
      const request = indexedDB.open(this.dbName, this.version);

      request.onerror = () => reject(request.error);
      request.onsuccess = () => {
        this.db = request.result;
        resolve(this.db);
      };

      request.onupgradeneeded = (event) => {
        const db = event.target.result;

        // Create object store
        const store = db.createObjectStore('droneImages', { keyPath: 'id' });

        // Create indexes
        store.createIndex('projectName', 'projectName', { unique: false });
        store.createIndex('tabId', 'tabId', { unique: false });
        store.createIndex('date', 'date', { unique: false });
      };
    });
  }

  async save(imageRecord) {
    const db = await this.open();
    return new Promise((resolve, reject) => {
      const tx = db.transaction('droneImages', 'readwrite');
      const store = tx.objectStore('droneImages');
      const request = store.put(imageRecord);

      request.onsuccess = () => resolve(request.result);
      request.onerror = () => reject(request.error);
    });
  }

  async get(imageId) {
    const db = await this.open();
    return new Promise((resolve, reject) => {
      const tx = db.transaction('droneImages', 'readonly');
      const store = tx.objectStore('droneImages');
      const request = store.get(imageId);

      request.onsuccess = () => resolve(request.result);
      request.onerror = () => reject(request.error);
    });
  }

  async delete(imageId) {
    const db = await this.open();
    return new Promise((resolve, reject) => {
      const tx = db.transaction('droneImages', 'readwrite');
      const store = tx.objectStore('droneImages');
      const request = store.delete(imageId);

      request.onsuccess = () => resolve();
      request.onerror = () => reject(request.error);
    });
  }

  async getAllByTab(tabId) {
    const db = await this.open();
    return new Promise((resolve, reject) => {
      const tx = db.transaction('droneImages', 'readonly');
      const store = tx.objectStore('droneImages');
      const index = store.index('tabId');
      const request = index.getAll(tabId);

      request.onsuccess = () => resolve(request.result);
      request.onerror = () => reject(request.error);
    });
  }
}

const imageDB = new ImageDatabase();
```

#### 1.4 EXIF Extraction
```html
<!-- Add to <head> -->
<script src="https://cdn.jsdelivr.net/npm/exif-js@2.3.0/exif.min.js"></script>
```

```javascript
function extractEXIF(file) {
  return new Promise((resolve) => {
    EXIF.getData(file, function() {
      const dateStr = EXIF.getTag(this, 'DateTimeOriginal') ||
                     EXIF.getTag(this, 'DateTime');

      resolve({
        dateTaken: parseEXIFDate(dateStr),
        model: EXIF.getTag(this, 'Model'),
        width: EXIF.getTag(this, 'PixelXDimension') || EXIF.getTag(this, 'ExifImageWidth'),
        height: EXIF.getTag(this, 'PixelYDimension') || EXIF.getTag(this, 'ExifImageLength'),
        gps: {
          lat: EXIF.getTag(this, 'GPSLatitude'),
          lon: EXIF.getTag(this, 'GPSLongitude'),
          alt: EXIF.getTag(this, 'GPSAltitude')
        }
      });
    });
  });
}

function parseEXIFDate(dateStr) {
  if (!dateStr) return new Date();
  // EXIF format: "2025:01:08 13:45:30"
  const parts = dateStr.split(' ');
  const datePart = parts[0].replace(/:/g, '-');
  const timePart = parts[1] || '00:00:00';
  return new Date(`${datePart}T${timePart}`);
}
```

---

### Phase 2: Grid Overlay System

#### 2.1 Auto-Configure Grid
```javascript
function autoConfigureGrid(imageDimensions) {
  const tab = tabs[currentTabId];
  const numTreatments = tab.treatments.length;
  const numReplicates = tab.fieldMap.replicates || 4;
  const totalCells = numTreatments * numReplicates;

  // Determine orientation
  const imageAspect = imageDimensions.width / imageDimensions.height;
  const isLandscape = imageAspect > 1;

  // Calculate grid dimensions
  let rows, cols;
  if (isLandscape) {
    // Wider grid for landscape image
    cols = Math.max(numTreatments, numReplicates);
    rows = Math.min(numTreatments, numReplicates);
  } else {
    // Taller grid for portrait image
    rows = Math.max(numTreatments, numReplicates);
    cols = Math.min(numTreatments, numReplicates);
  }

  // Update fieldMap
  tab.fieldMap.gridRows = rows;
  tab.fieldMap.gridCols = cols;
  tab.fieldMap.totalCells = totalCells;
  tab.fieldMap.orientation = 'auto';

  // Initialize grid position (centered, 85% coverage)
  tab.fieldMap.grid = {
    x: 7.5,
    y: 7.5,
    width: 85,
    height: 85,
    rotation: 0
  };

  // Render grid
  renderGrid();
}
```

#### 2.2 Grid Renderer (SVG)
```javascript
function renderGrid() {
  const tab = tabs[currentTabId];
  const fieldMap = tab.fieldMap;
  const imageContainer = document.getElementById('fieldMapImageContainer');

  // Remove existing grid
  const existingGrid = document.getElementById('gridOverlay');
  if (existingGrid) existingGrid.remove();

  // Create SVG overlay
  const svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
  svg.id = 'gridOverlay';
  svg.style.position = 'absolute';
  svg.style.top = '0';
  svg.style.left = '0';
  svg.style.width = '100%';
  svg.style.height = '100%';
  svg.style.pointerEvents = 'none';

  // Get image dimensions
  const img = document.getElementById('fieldMapImage');
  const imgRect = img.getBoundingClientRect();

  // Calculate grid pixel coordinates from percentages
  const gridX = (fieldMap.grid.x / 100) * imgRect.width;
  const gridY = (fieldMap.grid.y / 100) * imgRect.height;
  const gridWidth = (fieldMap.grid.width / 100) * imgRect.width;
  const gridHeight = (fieldMap.grid.height / 100) * imgRect.height;

  // Cell dimensions
  const cellWidth = gridWidth / fieldMap.gridCols;
  const cellHeight = gridHeight / fieldMap.gridRows;

  // Create grid group (for rotation)
  const gridGroup = document.createElementNS('http://www.w3.org/2000/svg', 'g');
  gridGroup.setAttribute('transform',
    `translate(${gridX + gridWidth/2}, ${gridY + gridHeight/2})
     rotate(${fieldMap.grid.rotation})
     translate(${-gridWidth/2}, ${-gridHeight/2})`
  );

  // Draw cells
  for (let row = 0; row < fieldMap.gridRows; row++) {
    for (let col = 0; col < fieldMap.gridCols; col++) {
      const cellIndex = row * fieldMap.gridCols + col;
      const mapping = fieldMap.cellMapping.find(m => m.cellIndex === cellIndex);

      const rect = document.createElementNS('http://www.w3.org/2000/svg', 'rect');
      rect.setAttribute('x', col * cellWidth);
      rect.setAttribute('y', row * cellHeight);
      rect.setAttribute('width', cellWidth);
      rect.setAttribute('height', cellHeight);
      rect.setAttribute('class', 'grid-cell');
      rect.setAttribute('data-cell-index', cellIndex);
      rect.setAttribute('data-row', row);
      rect.setAttribute('data-col', col);
      rect.style.pointerEvents = 'all';

      // Color if assigned
      if (mapping) {
        const treatment = tab.treatments.find(t => t.name === mapping.treatmentName);
        const colorIndex = tab.treatments.indexOf(treatment);
        rect.style.fill = getTreatmentColor(colorIndex);
        rect.style.opacity = '0.4';

        // Add text label
        const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
        text.setAttribute('x', col * cellWidth + cellWidth/2);
        text.setAttribute('y', row * cellHeight + cellHeight/2);
        text.setAttribute('text-anchor', 'middle');
        text.setAttribute('dominant-baseline', 'middle');
        text.style.fontSize = '10px';
        text.style.fill = 'white';
        text.style.pointerEvents = 'none';
        text.textContent = mapping.treatmentName.substring(0, 4);
        gridGroup.appendChild(text);
      } else {
        rect.style.fill = 'transparent';
      }

      rect.style.stroke = fieldMap.gridStyle.color;
      rect.style.strokeWidth = fieldMap.gridStyle.lineWidth;

      // Drop handler
      rect.addEventListener('drop', (e) => handleCellDrop(e, cellIndex));
      rect.addEventListener('dragover', (e) => e.preventDefault());
      rect.addEventListener('click', (e) => handleCellClick(e, cellIndex));

      gridGroup.appendChild(rect);
    }
  }

  // Add resize handles (if not locked)
  if (!fieldMap.gridLocked) {
    addGridHandles(gridGroup, gridWidth, gridHeight);
  }

  svg.appendChild(gridGroup);
  imageContainer.appendChild(svg);
}
```

#### 2.3 Grid Interaction (Drag/Resize/Rotate)
```javascript
let gridDragState = null;

function addGridHandles(gridGroup, gridWidth, gridHeight) {
  const handlePositions = [
    { type: 'resize-tl', x: 0, y: 0 },
    { type: 'resize-tr', x: gridWidth, y: 0 },
    { type: 'resize-bl', x: 0, y: gridHeight },
    { type: 'resize-br', x: gridWidth, y: gridHeight },
    { type: 'rotate', x: gridWidth/2, y: -20 }
  ];

  handlePositions.forEach(pos => {
    const handle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
    handle.setAttribute('cx', pos.x);
    handle.setAttribute('cy', pos.y);
    handle.setAttribute('r', 8);
    handle.setAttribute('class', 'grid-handle');
    handle.setAttribute('data-type', pos.type);
    handle.style.pointerEvents = 'all';

    if (pos.type === 'rotate') {
      handle.style.fill = '#6366f1';
    } else {
      handle.style.fill = '#10b981';
    }

    handle.addEventListener('mousedown', (e) => startGridDrag(e, pos.type));

    gridGroup.appendChild(handle);
  });

  // Grid body drag (move entire grid)
  gridGroup.style.cursor = 'move';
  gridGroup.addEventListener('mousedown', (e) => {
    if (e.target.classList.contains('grid-cell')) return;
    startGridDrag(e, 'move');
  });
}

function startGridDrag(e, type) {
  e.stopPropagation();
  gridDragState = {
    type: type,
    startX: e.clientX,
    startY: e.clientY,
    startGrid: { ...tabs[currentTabId].fieldMap.grid }
  };

  document.addEventListener('mousemove', handleGridDrag);
  document.addEventListener('mouseup', endGridDrag);
}

function handleGridDrag(e) {
  if (!gridDragState) return;

  const dx = e.clientX - gridDragState.startX;
  const dy = e.clientY - gridDragState.startY;
  const img = document.getElementById('fieldMapImage');
  const imgRect = img.getBoundingClientRect();

  const fieldMap = tabs[currentTabId].fieldMap;

  switch (gridDragState.type) {
    case 'move':
      fieldMap.grid.x = gridDragState.startGrid.x + (dx / imgRect.width) * 100;
      fieldMap.grid.y = gridDragState.startGrid.y + (dy / imgRect.height) * 100;
      break;

    case 'resize-br':
      fieldMap.grid.width = gridDragState.startGrid.width + (dx / imgRect.width) * 100;
      fieldMap.grid.height = gridDragState.startGrid.height + (dy / imgRect.height) * 100;
      break;

    case 'rotate':
      const centerX = imgRect.width / 2;
      const centerY = imgRect.height / 2;
      const angle = Math.atan2(e.clientY - centerY, e.clientX - centerX) * 180 / Math.PI;
      fieldMap.grid.rotation = angle;
      break;
  }

  renderGrid();
}

function endGridDrag() {
  gridDragState = null;
  document.removeEventListener('mousemove', handleGridDrag);
  document.removeEventListener('mouseup', endGridDrag);
  saveProjectToLocalStorage();
}
```

---

### Phase 3: Treatment Mapping

#### 3.1 Treatment Mapper Panel
```javascript
function renderTreatmentMapper() {
  const tab = tabs[currentTabId];
  const fieldMap = tab.fieldMap;
  const mapperPanel = document.getElementById('treatmentMapperPanel');

  mapperPanel.innerHTML = `
    <h3>Treatment Mapper</h3>
    <div class="mapper-mode">
      <label>
        <input type="radio" name="mapperMode" value="drag" checked> Drag & Drop
      </label>
      <label>
        <input type="radio" name="mapperMode" value="bulk"> Bulk Assign
      </label>
    </div>
    <div class="treatment-list" id="treatmentList"></div>
    <div class="mapper-progress">
      <p>Progress: <span id="mappingProgress">0/${fieldMap.totalCells}</span></p>
      <progress id="mappingProgressBar" max="${fieldMap.totalCells}" value="0"></progress>
    </div>
    <button onclick="clearAllMappings()">Clear All</button>
  `;

  const treatmentListDiv = document.getElementById('treatmentList');

  tab.treatments.forEach((treatment, index) => {
    const assignedCount = fieldMap.cellMapping.filter(
      m => m.treatmentName === treatment.name
    ).length;
    const remaining = fieldMap.replicates - assignedCount;

    const treatmentDiv = document.createElement('div');
    treatmentDiv.className = 'treatment-item';
    treatmentDiv.innerHTML = `
      <div class="treatment-header">
        <span class="treatment-color" style="background: ${getTreatmentColor(index)}"></span>
        <span class="treatment-name">${treatment.name}</span>
      </div>
      <div class="treatment-tile"
           draggable="true"
           data-treatment="${treatment.name}"
           style="background: ${getTreatmentColor(index)}">
        ${treatment.name}
        <span class="treatment-counter">${remaining}×</span>
      </div>
      <div class="treatment-status">
        Assigned: ${assignedCount}/${fieldMap.replicates}
        ${assignedCount === fieldMap.replicates ? '✓' : ''}
      </div>
    `;

    const tile = treatmentDiv.querySelector('.treatment-tile');

    // Disable dragging if all assigned
    if (remaining === 0) {
      tile.draggable = false;
      tile.style.opacity = '0.5';
      tile.style.cursor = 'not-allowed';
    }

    tile.addEventListener('dragstart', (e) => {
      e.dataTransfer.setData('treatment', treatment.name);
      e.dataTransfer.effectAllowed = 'copy';
    });

    treatmentListDiv.appendChild(treatmentDiv);
  });

  updateMappingProgress();
}

function updateMappingProgress() {
  const fieldMap = tabs[currentTabId].fieldMap;
  const assigned = fieldMap.cellMapping.length;
  const total = fieldMap.totalCells;

  document.getElementById('mappingProgress').textContent = `${assigned}/${total}`;
  document.getElementById('mappingProgressBar').value = assigned;
}
```

#### 3.2 Cell Drop Handler
```javascript
function handleCellDrop(e, cellIndex) {
  e.preventDefault();
  const treatmentName = e.dataTransfer.getData('treatment');

  if (!treatmentName) return;

  const tab = tabs[currentTabId];
  const fieldMap = tab.fieldMap;

  // Check if cell already assigned
  const existing = fieldMap.cellMapping.find(m => m.cellIndex === cellIndex);
  if (existing) {
    if (confirm(`Cell already assigned to ${existing.treatmentName}. Replace?`)) {
      // Remove existing
      fieldMap.cellMapping = fieldMap.cellMapping.filter(m => m.cellIndex !== cellIndex);
    } else {
      return;
    }
  }

  // Check if treatment quota reached
  const assignedCount = fieldMap.cellMapping.filter(m => m.treatmentName === treatmentName).length;
  if (assignedCount >= fieldMap.replicates) {
    showNotification(`${treatmentName} already assigned to ${fieldMap.replicates} cells`);
    return;
  }

  // Calculate row/col from cellIndex
  const row = Math.floor(cellIndex / fieldMap.gridCols);
  const col = cellIndex % fieldMap.gridCols;

  // Add mapping
  fieldMap.cellMapping.push({
    cellIndex: cellIndex,
    row: row,
    col: col,
    treatmentName: treatmentName
  });

  // Update UI
  renderGrid();
  renderTreatmentMapper();
  saveProjectToLocalStorage();

  showNotification(`Assigned ${treatmentName} to cell ${cellIndex + 1}`);
}
```

#### 3.3 Bulk Assign Mode
```javascript
let bulkAssignState = null;

document.addEventListener('change', (e) => {
  if (e.target.name === 'mapperMode') {
    if (e.target.value === 'bulk') {
      enableBulkAssignMode();
    } else {
      disableBulkAssignMode();
    }
  }
});

function enableBulkAssignMode() {
  const treatmentList = document.getElementById('treatmentList');
  const tiles = treatmentList.querySelectorAll('.treatment-tile');

  tiles.forEach(tile => {
    tile.draggable = false;
    tile.style.cursor = 'pointer';

    tile.onclick = () => {
      const treatmentName = tile.dataset.treatment;
      startBulkAssign(treatmentName);
    };
  });
}

function startBulkAssign(treatmentName) {
  const fieldMap = tabs[currentTabId].fieldMap;
  const assignedCount = fieldMap.cellMapping.filter(m => m.treatmentName === treatmentName).length;
  const remaining = fieldMap.replicates - assignedCount;

  if (remaining === 0) {
    showNotification(`${treatmentName} already fully assigned`);
    return;
  }

  bulkAssignState = {
    treatmentName: treatmentName,
    remaining: remaining,
    assigned: []
  };

  document.body.style.cursor = 'crosshair';
  showNotification(`Click ${remaining} cells to assign ${treatmentName}`);
}

function handleCellClick(e, cellIndex) {
  if (!bulkAssignState) return;

  e.stopPropagation();

  const tab = tabs[currentTabId];
  const fieldMap = tab.fieldMap;

  // Check if already assigned in this bulk session
  if (bulkAssignState.assigned.includes(cellIndex)) {
    showNotification('Cell already selected in this session');
    return;
  }

  // Check if cell occupied
  const existing = fieldMap.cellMapping.find(m => m.cellIndex === cellIndex);
  if (existing) {
    showNotification(`Cell occupied by ${existing.treatmentName}`);
    return;
  }

  // Calculate row/col
  const row = Math.floor(cellIndex / fieldMap.gridCols);
  const col = cellIndex % fieldMap.gridCols;

  // Add mapping
  fieldMap.cellMapping.push({
    cellIndex: cellIndex,
    row: row,
    col: col,
    treatmentName: bulkAssignState.treatmentName
  });

  bulkAssignState.assigned.push(cellIndex);
  bulkAssignState.remaining--;

  // Update UI
  renderGrid();

  // Check if done
  if (bulkAssignState.remaining === 0) {
    endBulkAssign();
  } else {
    showNotification(`${bulkAssignState.remaining} more cells for ${bulkAssignState.treatmentName}`);
  }
}

function endBulkAssign() {
  showNotification(`${bulkAssignState.treatmentName} fully assigned ✓`);
  bulkAssignState = null;
  document.body.style.cursor = 'default';
  renderTreatmentMapper();
  saveProjectToLocalStorage();
}
```

---

### Phase 4: Plot Extraction & Breakdown View

#### 4.1 Extract Plots from Grid
```javascript
async function extractPlots() {
  const tab = tabs[currentTabId];
  const fieldMap = tab.fieldMap;
  const currentImage = fieldMap.images[fieldMap.currentImageIndex];

  if (!currentImage) {
    showNotification('No image selected');
    return;
  }

  // Load image from IndexedDB
  const imageRecord = await imageDB.get(currentImage.imageId);
  const imageBlob = imageRecord.imageData;

  // Create image element
  const img = new Image();
  const imgURL = URL.createObjectURL(imageBlob);

  img.onload = () => {
    const plots = [];

    // Create canvas for extraction
    const canvas = document.createElement('canvas');
    const ctx = canvas.getContext('2d');

    // Calculate grid dimensions in pixels
    const gridX = (fieldMap.grid.x / 100) * img.width;
    const gridY = (fieldMap.grid.y / 100) * img.height;
    const gridWidth = (fieldMap.grid.width / 100) * img.width;
    const gridHeight = (fieldMap.grid.height / 100) * img.height;

    const cellWidth = gridWidth / fieldMap.gridCols;
    const cellHeight = gridHeight / fieldMap.gridRows;

    // Extract each cell
    for (const mapping of fieldMap.cellMapping) {
      const row = mapping.row;
      const col = mapping.col;

      // Calculate cell position
      const cellX = gridX + (col * cellWidth);
      const cellY = gridY + (row * cellHeight);

      // Set canvas size to cell size
      canvas.width = cellWidth;
      canvas.height = cellHeight;

      // Handle rotation if needed
      if (fieldMap.grid.rotation !== 0) {
        // TODO: Apply rotation transform
        // For now, assuming no rotation
      }

      // Draw the cell portion of the image
      ctx.drawImage(
        img,
        cellX, cellY, cellWidth, cellHeight,  // Source
        0, 0, cellWidth, cellHeight            // Destination
      );

      // Convert to data URL
      const plotDataURL = canvas.toDataURL('image/png');

      plots.push({
        treatmentName: mapping.treatmentName,
        cellIndex: mapping.cellIndex,
        row: row,
        col: col,
        imageData: plotDataURL
      });
    }

    // Clean up
    URL.revokeObjectURL(imgURL);

    // Display breakdown
    displayBreakdown(plots, currentImage.date);
  };

  img.src = imgURL;
}
```

#### 4.2 Breakdown View
```javascript
function displayBreakdown(plots, imageDate) {
  const tab = tabs[currentTabId];

  // Switch to breakdown view
  document.getElementById('fieldMapGridView').style.display = 'none';
  document.getElementById('fieldMapBreakdownView').style.display = 'block';

  const breakdownContainer = document.getElementById('breakdownContainer');
  breakdownContainer.innerHTML = '';

  // Update header
  document.getElementById('breakdownHeader').innerHTML = `
    <button onclick="backToGridView()">« Back to Grid View</button>
    <h2>Trial: ${tab.name}</h2>
    <h3>Drone Image: ${formatDate(imageDate)}</h3>
  `;

  // Group plots by treatment
  const plotsByTreatment = {};
  for (const plot of plots) {
    if (!plotsByTreatment[plot.treatmentName]) {
      plotsByTreatment[plot.treatmentName] = [];
    }
    plotsByTreatment[plot.treatmentName].push(plot);
  }

  // Render each treatment group
  for (const treatment of tab.treatments) {
    const treatmentPlots = plotsByTreatment[treatment.name] || [];

    if (treatmentPlots.length === 0) continue;

    const treatmentSection = document.createElement('div');
    treatmentSection.className = 'treatment-section';

    const header = document.createElement('h3');
    header.textContent = `${treatment.name.toUpperCase()} (${treatmentPlots.length} replicates)`;
    treatmentSection.appendChild(header);

    const plotGrid = document.createElement('div');
    plotGrid.className = 'plot-grid';

    for (const plot of treatmentPlots) {
      const plotCard = document.createElement('div');
      plotCard.className = 'plot-card';

      plotCard.innerHTML = `
        <img src="${plot.imageData}" alt="${plot.treatmentName}">
        <div class="plot-label">${treatment.name}</div>
      `;

      plotGrid.appendChild(plotCard);
    }

    treatmentSection.appendChild(plotGrid);
    breakdownContainer.appendChild(treatmentSection);
  }
}

function backToGridView() {
  document.getElementById('fieldMapBreakdownView').style.display = 'none';
  document.getElementById('fieldMapGridView').style.display = 'block';
}
```

#### 4.3 Screenshot Breakdown
```javascript
async function screenshotBreakdown() {
  const breakdownView = document.getElementById('fieldMapBreakdownView');

  // Use existing html2canvas (already loaded for graph screenshots)
  const canvas = await html2canvas(breakdownView, {
    scale: 2,                        // High DPI
    backgroundColor: '#1a1a2e',      // Match app background
    width: 1400,                     // Fixed width for consistency
    logging: false,
    imageTimeout: 0,
    useCORS: true
  });

  // Copy to clipboard
  canvas.toBlob(async (blob) => {
    try {
      await navigator.clipboard.write([
        new ClipboardItem({ 'image/png': blob })
      ]);
      showNotification('Screenshot copied to clipboard! ✓');
    } catch (err) {
      console.error('Clipboard error:', err);
      // Fallback: download
      downloadBlob(blob, `breakdown_${new Date().toISOString().split('T')[0]}.png`);
      showNotification('Screenshot downloaded');
    }
  });
}
```

---

### Phase 5: Timeline Navigation

#### 5.1 Image Timeline
```javascript
function renderImageTimeline() {
  const tab = tabs[currentTabId];
  const fieldMap = tab.fieldMap;

  if (!fieldMap.images || fieldMap.images.length === 0) {
    document.getElementById('imageTimeline').innerHTML = '<p>No images uploaded</p>';
    return;
  }

  const timeline = document.getElementById('imageTimeline');
  timeline.innerHTML = '';

  // Create timeline slider
  const slider = document.createElement('input');
  slider.type = 'range';
  slider.min = 0;
  slider.max = fieldMap.images.length - 1;
  slider.value = fieldMap.currentImageIndex || 0;
  slider.className = 'image-timeline-slider';

  slider.addEventListener('input', (e) => {
    fieldMap.currentImageIndex = parseInt(e.target.value);
    displayCurrentImage();
    updateTimelineLabel();
  });

  timeline.appendChild(slider);

  // Date labels
  const labelsDiv = document.createElement('div');
  labelsDiv.className = 'timeline-labels';

  fieldMap.images.forEach((img, index) => {
    const label = document.createElement('span');
    label.textContent = formatDate(img.date);
    label.className = 'timeline-label';
    if (index === fieldMap.currentImageIndex) {
      label.classList.add('active');
    }
    label.onclick = () => {
      fieldMap.currentImageIndex = index;
      slider.value = index;
      displayCurrentImage();
      updateTimelineLabel();
    };
    labelsDiv.appendChild(label);
  });

  timeline.appendChild(labelsDiv);

  // Navigation buttons
  const navDiv = document.createElement('div');
  navDiv.className = 'timeline-nav';
  navDiv.innerHTML = `
    <button onclick="previousImage()" ${fieldMap.currentImageIndex === 0 ? 'disabled' : ''}>
      ◀ Previous
    </button>
    <button onclick="nextImage()" ${fieldMap.currentImageIndex === fieldMap.images.length - 1 ? 'disabled' : ''}>
      Next ▶
    </button>
  `;
  timeline.appendChild(navDiv);
}

function previousImage() {
  const fieldMap = tabs[currentTabId].fieldMap;
  if (fieldMap.currentImageIndex > 0) {
    fieldMap.currentImageIndex--;
    displayCurrentImage();
    renderImageTimeline();
  }
}

function nextImage() {
  const fieldMap = tabs[currentTabId].fieldMap;
  if (fieldMap.currentImageIndex < fieldMap.images.length - 1) {
    fieldMap.currentImageIndex++;
    displayCurrentImage();
    renderImageTimeline();
  }
}

async function displayCurrentImage() {
  const tab = tabs[currentTabId];
  const fieldMap = tab.fieldMap;
  const currentImage = fieldMap.images[fieldMap.currentImageIndex];

  if (!currentImage) return;

  // Load from IndexedDB
  const imageRecord = await imageDB.get(currentImage.imageId);
  const imageBlob = imageRecord.imageData;
  const imageURL = URL.createObjectURL(imageBlob);

  // Display
  const img = document.getElementById('fieldMapImage');
  img.src = imageURL;

  // Re-render grid overlay
  img.onload = () => {
    renderGrid();
    URL.revokeObjectURL(imageURL);
  };

  // Update date display
  document.getElementById('currentImageDate').textContent = formatDate(currentImage.date);
}
```

---

### Phase 6: Export & Import

#### 6.1 Export Full Archive
```html
<!-- Add to <head> -->
<script src="https://cdn.jsdelivr.net/npm/jszip@3.10.1/dist/jszip.min.js"></script>
```

```javascript
async function exportFullArchive() {
  const zip = new JSZip();
  const projectData = getCurrentProjectData();

  // Add project JSON
  zip.file('project.json', JSON.stringify(projectData, null, 2));

  // Add README
  zip.file('README.txt', `
Graph Generator Project Archive
================================

Project: ${projectData.projectName}
Exported: ${new Date().toISOString()}

Contents:
- project.json: Project configuration and data
- images/: Drone field images

To import:
1. Open Graph Generator in browser
2. Drag and drop this ZIP file into the Field Map tab
3. Project will be restored with all images

For support: https://github.com/your-repo
  `.trim());

  // Add all images
  const imagesFolder = zip.folder('images');

  for (const tab of projectData.tabs) {
    if (tab.fieldMap?.images) {
      for (const imgRef of tab.fieldMap.images) {
        const imageRecord = await imageDB.get(imgRef.imageId);
        if (imageRecord) {
          imagesFolder.file(imgRef.fileName, imageRecord.imageData);
        }
      }
    }
  }

  // Generate and download
  showNotification('Generating archive...');
  const blob = await zip.generateAsync({
    type: 'blob',
    compression: 'DEFLATE',
    compressionOptions: { level: 6 }
  });

  const filename = `${projectData.projectName.replace(/[^a-z0-9]/gi, '_')}_${formatDate(new Date())}.zip`;
  downloadBlob(blob, filename);

  showNotification(`Exported: ${filename}`);
}
```

#### 6.2 Import Archive
```javascript
async function importProjectArchive(zipFile) {
  try {
    showNotification('Importing project...');

    const zip = await JSZip.loadAsync(zipFile);

    // Read project.json
    const projectJSON = await zip.file('project.json').async('text');
    const projectData = JSON.parse(projectJSON);

    // Extract and save images to IndexedDB
    const imagesFolder = zip.folder('images');
    const imageFiles = Object.keys(imagesFolder.files).filter(name => !name.endsWith('/'));

    for (const imagePath of imageFiles) {
      const imageBlob = await zip.file(imagePath).async('blob');
      const fileName = imagePath.split('/').pop();

      // Find corresponding image reference in project data
      for (const tab of projectData.tabs) {
        if (tab.fieldMap?.images) {
          const imgRef = tab.fieldMap.images.find(img => img.fileName === fileName);
          if (imgRef) {
            // Save to IndexedDB with original ID
            await imageDB.save({
              id: imgRef.imageId,
              projectName: projectData.projectName,
              tabId: tab.id,
              date: imgRef.date,
              fileName: fileName,
              mimeType: imageBlob.type,
              imageData: imageBlob,
              uploadedAt: imgRef.uploadedAt,
              exifData: imgRef.exifData
            });
          }
        }
      }
    }

    // Load project data
    loadProject(projectData);

    showNotification(`Imported: ${projectData.projectName} ✓`);
  } catch (err) {
    console.error('Import error:', err);
    showNotification('Import failed. Check console for details.');
  }
}

function loadProject(projectData) {
  // Clear current project
  if (confirm('Loading project will replace current work. Continue?')) {
    // Restore tabs
    tabs = projectData.tabs;
    tabCounter = projectData.tabCounter;
    activeTabId = projectData.activeTabId || tabs[0]?.id;
    currentProjectName = projectData.projectName;

    // Re-render UI
    renderTabs();
    switchTab(activeTabId);

    // Save to localStorage
    saveProjectToLocalStorage();
  }
}
```

---

## CSS Styling

### Grid Overlay Styles
```css
/* Grid overlay */
.grid-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 10;
}

.grid-cell {
  fill: transparent;
  stroke: var(--grid-color, #10b981);
  stroke-width: 2;
  opacity: 0.75;
  transition: fill 0.2s ease;
  cursor: pointer;
}

.grid-cell:hover {
  fill: rgba(16, 185, 129, 0.15);
}

.grid-cell.assigned {
  opacity: 0.5;
}

.grid-handle {
  fill: #10b981;
  stroke: white;
  stroke-width: 2;
  cursor: grab;
}

.grid-handle:active {
  cursor: grabbing;
}

.grid-handle[data-type="rotate"] {
  fill: #6366f1;
}

/* Treatment mapper */
.treatment-item {
  background: rgba(26, 26, 46, 0.6);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 12px;
  margin-bottom: 12px;
}

.treatment-tile {
  padding: 12px;
  border-radius: 8px;
  text-align: center;
  color: white;
  font-weight: 600;
  cursor: grab;
  user-select: none;
  position: relative;
}

.treatment-tile:active {
  cursor: grabbing;
}

.treatment-counter {
  position: absolute;
  top: 4px;
  right: 8px;
  background: rgba(0, 0, 0, 0.3);
  padding: 2px 6px;
  border-radius: 4px;
  font-size: 11px;
}

.treatment-status {
  margin-top: 8px;
  font-size: 12px;
  color: #94a3b8;
}

/* Breakdown view */
.treatment-section {
  margin-bottom: 40px;
}

.plot-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 16px;
  margin-top: 16px;
}

.plot-card {
  background: rgba(26, 26, 46, 0.8);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 12px;
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
  font-size: 13px;
  color: #e2e8f0;
  font-weight: 500;
}

/* Timeline */
.image-timeline-slider {
  width: 100%;
  margin: 16px 0;
}

.timeline-labels {
  display: flex;
  justify-content: space-between;
  margin-top: 8px;
}

.timeline-label {
  font-size: 12px;
  color: #94a3b8;
  cursor: pointer;
  padding: 4px 8px;
  border-radius: 4px;
}

.timeline-label:hover {
  background: rgba(16, 185, 129, 0.1);
  color: #10b981;
}

.timeline-label.active {
  background: rgba(16, 185, 129, 0.2);
  color: #10b981;
  font-weight: 600;
}
```

---

## Implementation Checklist

### Phase 1: Foundation (Days 1-2)
- [ ] Add Field Map tab to header
- [ ] Create left/center/right panel layout
- [ ] Implement drop zone for images/ZIP
- [ ] IndexedDB setup and CRUD functions
- [ ] EXIF extraction (exif-js library)
- [ ] Image upload and storage
- [ ] Image library display with thumbnails
- [ ] Basic image display in center panel

### Phase 2: Grid System (Days 3-4)
- [ ] Auto-configure grid from image dimensions
- [ ] SVG grid renderer
- [ ] Grid cell creation (rows × cols)
- [ ] Grid drag/move functionality
- [ ] Grid resize handles
- [ ] Grid rotation handle
- [ ] Grid style controls (opacity, color, width)
- [ ] Lock grid feature

### Phase 3: Treatment Mapping (Days 5-6)
- [ ] Treatment mapper panel UI
- [ ] Generate draggable treatment tiles with counters
- [ ] Drag-and-drop to grid cells
- [ ] Cell assignment logic
- [ ] Reusable drag (same label multiple times)
- [ ] Bulk assign mode
- [ ] Progress tracking
- [ ] Clear all mappings

### Phase 4: Plot Extraction (Days 7-8)
- [ ] Extract plots from grid cells
- [ ] Handle rotation in extraction
- [ ] Group plots by treatment
- [ ] Breakdown view layout
- [ ] Plot card display
- [ ] Treatment section headers
- [ ] Switch between grid/breakdown views

### Phase 5: Timeline & Navigation (Day 9)
- [ ] Image timeline UI (slider + labels)
- [ ] Previous/Next buttons
- [ ] Display current image
- [ ] Apply grid to all images
- [ ] Sync timeline with current index

### Phase 6: Screenshot & Export (Day 10)
- [ ] Breakdown screenshot to clipboard
- [ ] Export light JSON
- [ ] Export full ZIP archive (JSZip)
- [ ] Import ZIP archive
- [ ] Extract images from ZIP
- [ ] Restore to IndexedDB
- [ ] Load project data

### Phase 7: Polish & Testing (Days 11-12)
- [ ] Error handling and validation
- [ ] Responsive layout adjustments
- [ ] Loading indicators
- [ ] Notifications/toasts
- [ ] Keyboard shortcuts
- [ ] Browser compatibility testing
- [ ] Performance optimization for large images
- [ ] Documentation and tooltips

---

## Estimated Development Time

- **Phase 1 (Foundation):** 2 days
- **Phase 2 (Grid):** 2 days
- **Phase 3 (Mapping):** 2 days
- **Phase 4 (Extraction):** 2 days
- **Phase 5 (Timeline):** 1 day
- **Phase 6 (Export):** 1 day
- **Phase 7 (Polish):** 2 days

**Total:** ~12 days for full implementation

**MVP (Phases 1-4 only):** ~8 days

---

## Dependencies to Add

```html
<!-- In <head> section of index.html -->

<!-- EXIF extraction -->
<script src="https://cdn.jsdelivr.net/npm/exif-js@2.3.0/exif.min.js"></script>

<!-- ZIP generation/extraction -->
<script src="https://cdn.jsdelivr.net/npm/jszip@3.10.1/dist/jszip.min.js"></script>

<!-- html2canvas already included for graph screenshots -->
```

---

## Browser Compatibility

### Required Features
- IndexedDB (all modern browsers ✓)
- Drag & Drop API (all modern browsers ✓)
- Clipboard API (Chrome 76+, Firefox 87+, Safari 13.1+)
- File API (all modern browsers ✓)

### Fallbacks
- Clipboard: Download file if clipboard.write() fails
- IndexedDB: Clear error messages if quota exceeded
- Large files: Warn if image > 10MB

---

## Success Criteria

### Core Functionality
✓ Upload multiple drone images
✓ Configure grid once, apply to all
✓ Map treatments via drag-and-drop
✓ Extract plots grouped by treatment
✓ Navigate through dates
✓ Screenshot breakdowns
✓ Export/import projects

### Performance
✓ Handle 5-10 images (5-10MB each) smoothly
✓ Grid rendering < 100ms
✓ Plot extraction < 3 seconds for 40 plots
✓ Screenshot generation < 2 seconds

### User Experience
✓ Intuitive drag-and-drop
✓ Clear progress indicators
✓ No data loss (auto-save)
✓ Portable projects (ZIP export)

---

**END OF FINAL PLAN**

Ready for implementation! 🚀
