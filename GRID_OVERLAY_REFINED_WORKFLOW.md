# Grid Overlay Tool - REFINED WORKFLOW & DESIGN
## Based on User Requirements (2026-01-10)

---

## Key User Requirements Confirmed

1. **Grid Orientation:** Auto-detect from image orientation OR user choice
2. **Treatment Assignment:** 4× draggable labels for each treatment (if 4 replicates)
3. **Plot Labels:** Treatment name only (no replicate numbers)
4. **Multiple Dates:** YES - Upload multiple images, configure grid once, apply to all
5. **Export:** Composite screenshot for presentations (scrollable view on computer)
6. **File Management:** Easy drag-and-drop for adding/removing images

---

## CRITICAL INSIGHT: Multi-Date Temporal Workflow

The tool is designed for **time-series analysis** where:
- User has drone images from the **same field** on **different dates**
- Grid and treatment positions **remain constant** across all dates
- User configures grid/treatments **once**, then navigates through dates
- Each date shows the **same plots** at different growth stages

**This mirrors the existing graph behavior:**
- Graph tab has multiple dates → timeline navigation
- Field map tab has multiple images → same timeline navigation
- Same trial, different timepoints, consistent layout

---

## Revised Data Structure

### Tab Object with Multi-Date Field Map
```javascript
{
  id: "tab_1",
  name: "Trial Jan-Feb 2025",
  graphTitle: "Green Coverage Over Time",

  // GRAPH DATA (existing)
  treatments: [
    { name: "Control", values: [45, 52, 61, 68], letters: ["a", "ab", "b", "c"] },
    { name: "Treatment A", values: [48, 58, 72, 80], letters: ["a", "b", "c", "d"] },
    // ... 6 more treatments
  ],
  dates: ["2025-01-05", "2025-01-15", "2025-02-01", "2025-02-15"],
  currentDateIndex: 0,

  // FIELD MAP DATA (new) - Single configuration for ALL dates
  fieldMap: {
    enabled: true,

    // GRID CONFIGURATION (applies to all images)
    replicates: 4,
    orientation: "auto",  // "auto", "rows", or "columns"
    gridRows: 8,          // Auto-calculated: 8 treatments
    gridCols: 4,          // Auto-calculated: 4 replicates

    // GRID POSITION/STYLE (applies to all images - same plots across dates)
    grid: {
      x: 10,              // % from left
      y: 5,               // % from top
      width: 85,          // % of image width
      height: 90,         // % of image height
      rotation: -2        // degrees (if field is slightly angled)
    },

    gridStyle: {
      color: "#10b981",
      opacity: 0.75,
      lineWidth: 2
    },

    // TREATMENT MAPPING (applies to all dates - plots don't move)
    cellMapping: [
      { row: 0, col: 0, treatmentName: "Control" },
      { row: 0, col: 1, treatmentName: "Control" },
      { row: 0, col: 2, treatmentName: "Control" },
      { row: 0, col: 3, treatmentName: "Control" },
      { row: 1, col: 0, treatmentName: "Treatment A" },
      { row: 1, col: 1, treatmentName: "Treatment A" },
      // ... all 32 cells (8 treatments × 4 reps)
    ],

    // IMAGES - One per date (indexed by date)
    images: [
      {
        date: "2025-01-05",      // Matches dates[0]
        imageId: "img_uuid_001",
        fileName: "drone_jan05.jpg",
        uploadedAt: "2025-01-10T10:00:00Z",
        exifData: {
          dateTaken: "2025-01-05T14:32:00Z",
          camera: "DJI Mavic 3",
          gps: { lat: 9.073928, lon: -79.634042, alt: 120 },
          dimensions: { width: 5280, height: 3956 }
        }
      },
      {
        date: "2025-01-15",      // Matches dates[1]
        imageId: "img_uuid_002",
        fileName: "drone_jan15.jpg",
        uploadedAt: "2025-01-10T10:05:00Z",
        exifData: { /* ... */ }
      },
      {
        date: "2025-02-01",      // Matches dates[2]
        imageId: "img_uuid_003",
        fileName: "drone_feb01.jpg",
        uploadedAt: "2025-01-10T10:10:00Z",
        exifData: { /* ... */ }
      },
      {
        date: "2025-02-15",      // Matches dates[3]
        imageId: "img_uuid_004",
        fileName: "drone_feb15.jpg",
        uploadedAt: "2025-01-10T10:15:00Z",
        exifData: { /* ... */ }
      }
    ]
  }
}
```

---

## Revised UI/UX - Multi-Date Design

### Layout: Field Map View

```
┌────────────────────────────────────────────────────────────────────┐
│ Header: [Logo] Trial Jan-Feb 2025 [Graph Tab] [Field Map Tab ✓]   │
├────────────────────────────────────────────────────────────────────┤
│ Timeline: Jan 5 ← [●    ] → Feb 15             Date: Jan 5, 2025   │
├─────────────────┬──────────────────────────┬───────────────────────┤
│ LEFT PANEL      │ CENTER PANEL             │ RIGHT PANEL           │
│ (280px)         │ (flex-grow)              │ (280px)               │
│                 │                          │                       │
│ IMAGE LIBRARY   │  DRONE IMAGE             │ TREATMENT MAPPER      │
│ ───────────     │  ──────────────          │ ──────────────        │
│ Current Date:   │                          │ Drag treatments to    │
│ ✓ Jan 5, 2025   │  [Image with grid        │ grid cells:           │
│   Jan 15, 2025  │   overlay showing        │                       │
│   Feb 1, 2025   │   treatment colors]      │ Control (4×)          │
│   Feb 15, 2025  │                          │ ┌─────┐┌─────┐       │
│                 │  Grid overlaid with      │ │Ctrl ││Ctrl │       │
│ [+ Add Image]   │  color-coded cells       │ └─────┘└─────┘       │
│ [Remove Image]  │                          │ ┌─────┐┌─────┐       │
│                 │  Controls:               │ │Ctrl ││Ctrl │       │
│ GRID CONFIG     │  • Pan/Zoom              │ └─────┘└─────┘       │
│ ───────────     │  • Fit Image             │                       │
│ Replicates: [4] │  • Reset Grid            │ Treatment A (4×)      │
│ Treatments: 8   │                          │ ┌─────┐┌─────┐       │
│ Grid: 8×4       │                          │ │Trt A││Trt A│       │
│                 │                          │ └─────┘└─────┘       │
│ Orientation:    │                          │ ┌─────┐┌─────┐       │
│ ◉ Auto-detect   │                          │ │Trt A││Trt A│       │
│ ○ Rows          │                          │ └─────┘└─────┘       │
│ ○ Columns       │                          │                       │
│                 │                          │ ... (more treatments) │
│ GRID STYLE      │                          │                       │
│ ───────────     │                          │ Status:               │
│ Opacity: [75%]  │                          │ ✓ 32/32 cells mapped  │
│ Color: [■]      │                          │                       │
│ Width: [2px]    │                          │ [Clear All]           │
│ Rotation: [-2°] │                          │                       │
│                 │                          │                       │
│ [Configure Grid]│                          │                       │
└─────────────────┴──────────────────────────┴───────────────────────┘
│ Footer: [◀ Previous Date] [View Breakdown] [Next Date ▶] [Screenshot]│
└────────────────────────────────────────────────────────────────────┘
```

### Breakdown View (for current date)

```
┌────────────────────────────────────────────────────────────────────┐
│ « Back to Grid View      Trial: Jan-Feb 2025      Date: Jan 5 2025 │
├────────────────────────────────────────────────────────────────────┤
│ Timeline: Jan 5 ← [●    ] → Feb 15                                 │
├────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  CONTROL                                                            │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐                  │
│  │ [plot]  │ │ [plot]  │ │ [plot]  │ │ [plot]  │                  │
│  │         │ │         │ │         │ │         │                  │
│  │ Control │ │ Control │ │ Control │ │ Control │                  │
│  └─────────┘ └─────────┘ └─────────┘ └─────────┘                  │
│                                                                     │
│  TREATMENT A                                                        │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐                  │
│  │ [plot]  │ │ [plot]  │ │ [plot]  │ │ [plot]  │                  │
│  │         │ │         │ │         │ │         │                  │
│  │Treatment│ │Treatment│ │Treatment│ │Treatment│                  │
│  │    A    │ │    A    │ │    A    │ │    A    │                  │
│  └─────────┘ └─────────┘ └─────────┘ └─────────┘                  │
│                                                                     │
│  TREATMENT B                                                        │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐                  │
│  │ [plot]  │ │ [plot]  │ │ [plot]  │ │ [plot]  │                  │
│  └─────────┘ └─────────┘ └─────────┘ └─────────┘                  │
│                                                                     │
│  ... (continues for all 8 treatments)                              │
│                                                                     │
├────────────────────────────────────────────────────────────────────┤
│ Footer: [◀ Previous Date] [Screenshot to Clipboard] [Next Date ▶]  │
└────────────────────────────────────────────────────────────────────┘
```

---

## User Workflow (Multi-Date)

### Initial Setup (One-time)

**Step 1: Create Trial & Graph Data**
- Create new tab "Trial Jan-Feb 2025"
- Paste TSV data with 8 treatments, 4 dates
- Generate graph ✓

**Step 2: Upload All Drone Images**
- Click "Field Map" tab
- Upload 4 images:
  - `drone_jan05.jpg` → Auto-matched to date "2025-01-05"
  - `drone_jan15.jpg` → Auto-matched to date "2025-01-15"
  - `drone_feb01.jpg` → Auto-matched to date "2025-02-01"
  - `drone_feb15.jpg` → Auto-matched to date "2025-02-15"
- Tool extracts EXIF dates and associates with graph dates

**Step 3: Configure Grid (Once for All Images)**
- Set replicates: 4
- Choose orientation: Auto-detect (or manual if needed)
- Grid appears: 8 rows × 4 columns

**Step 4: Position Grid (On First Image)**
- Viewing: Jan 5 image
- Drag grid to align with plots
- Resize corners to match field boundaries
- Rotate if needed (e.g., -2° for slight angle)
- **This grid configuration applies to ALL dates**

**Step 5: Map Treatments (Once for All Images)**
- Right panel shows:
  - **Control** with 4 draggable tiles
  - **Treatment A** with 4 draggable tiles
  - **Treatment B** with 4 draggable tiles
  - ... (all 8 treatments)
- Drag first "Control" tile → drop on row 0, col 0
- Drag second "Control" tile → drop on row 0, col 1
- Drag third "Control" tile → drop on row 0, col 2
- Drag fourth "Control" tile → drop on row 0, col 3
- Row 0 now shows 4 cells colored for "Control"
- Repeat for Treatment A (row 1), Treatment B (row 2), etc.
- **This treatment mapping applies to ALL dates**

### Daily Usage (View & Screenshot)

**Step 6: Navigate Through Dates**
- Use timeline slider or arrow buttons
- Click "Jan 5" → Shows Jan 5 drone image with grid overlay
- Click "Jan 15" → Shows Jan 15 image with **same grid/treatments**
- Grid position, rotation, and treatment assignments are **identical**
- Only the underlying image changes

**Step 7: View Breakdown for Each Date**
- Select date: "Jan 5"
- Click "View Breakdown" button
- Breakdown view shows:
  - Control: 4 plot images from Jan 5
  - Treatment A: 4 plot images from Jan 5
  - ... (all treatments)
- Navigate to "Jan 15"
- Click "View Breakdown" button again
- Breakdown now shows **same plots, from Jan 15 image**

**Step 8: Screenshot for Presentation**
- In breakdown view (e.g., Jan 5)
- Click "Screenshot to Clipboard"
- Paste into PowerPoint slide 1
- Navigate to "Jan 15"
- Click "Screenshot to Clipboard"
- Paste into PowerPoint slide 2
- Repeat for Feb 1, Feb 15
- Result: 4 slides showing treatment progression over time

### Adding/Removing Images

**Add New Date:**
- User collects drone image on "2025-03-01"
- In graph tab: Add new date column "2025-03-01" with data
- In field map tab: Click "+ Add Image"
- Upload `drone_mar01.jpg`
- Tool auto-matches to "2025-03-01"
- Grid and treatments **automatically applied** (no re-configuration needed)

**Remove Date:**
- Click date in image library (e.g., "Feb 15, 2025")
- Click "Remove Image"
- Image deleted from IndexedDB
- Date remains in graph data (just no field map for that date)

---

## Drag & Drop Treatment Assignment - Refined

### Treatment Panel Design

For **8 treatments** and **4 replicates**, the right panel shows:

```
TREATMENT MAPPER
────────────────

Available Tiles:

┌─────────────────┐
│ Control         │
├────┬────┬────┬──┤
│Ctrl│Ctrl│Ctrl│Ct│ ← 4 draggable tiles
└────┴────┴────┴──┘

┌─────────────────┐
│ Treatment A     │
├────┬────┬────┬──┤
│TrtA│TrtA│TrtA│Tr│ ← 4 draggable tiles
└────┴────┴────┴──┘

┌─────────────────┐
│ Treatment B     │
├────┬────┬────┬──┤
│TrtB│TrtB│TrtB│Tr│ ← 4 draggable tiles
└────┴────┴────┴──┘

... (6 more treatments)

────────────────
Status: 12/32 cells assigned
```

### Drag Behavior

1. **Drag Tile:**
   - User grabs one "Ctrl" tile
   - Tile becomes semi-transparent (visual feedback)
   - Grid cells highlight on hover

2. **Drop on Cell:**
   - User drops on grid cell (row 0, col 0)
   - Cell fills with treatment color
   - "Ctrl" text appears in cell
   - Tile disappears from panel (used up)
   - Panel shows: "Control: 3 remaining"

3. **Unassign:**
   - User clicks assigned cell
   - Cell clears
   - Tile reappears in panel
   - Panel shows: "Control: 4 remaining"

4. **Visual Feedback:**
   - Assigned cells: Filled with treatment color + text
   - Unassigned cells: Empty (just grid lines)
   - Progress: "Status: 32/32 cells assigned ✓"

### Alternative: Bulk Assignment

**Option for faster workflow:**

Instead of dragging 4× individual tiles, provide "Assign All" option:

```
┌─────────────────────────┐
│ Control                 │
│ [Assign to 4 cells]     │ ← Click, then click 4 grid cells
└─────────────────────────┘
```

**Workflow:**
1. Click "Assign to 4 cells" button
2. Cursor changes to "paint brush" mode
3. Click grid cells: row 0 col 0, row 0 col 1, row 0 col 2, row 0 col 3
4. All 4 cells assigned to "Control"
5. Mode exits automatically

**User's choice:** Implement both (drag individual OR bulk assign) for flexibility.

---

## Grid Orientation Auto-Detection

### Logic

When user uploads first image:

```javascript
function determineOrientation(imageWidth, imageHeight, numTreatments, numReplicates) {
  const imageAspectRatio = imageWidth / imageHeight;

  // Calculate grid aspect ratios for both orientations
  const treatmentsInRows = numReplicates / numTreatments;  // 4/8 = 0.5 (tall)
  const treatmentsInCols = numTreatments / numReplicates;  // 8/4 = 2.0 (wide)

  // Match grid orientation to image orientation
  if (imageAspectRatio > 1) {
    // Wide image (landscape)
    return 'columns';  // Wide grid (8 cols × 4 rows)
  } else {
    // Tall image (portrait)
    return 'rows';  // Tall grid (8 rows × 4 cols)
  }
}
```

**Example:**
- Drone image: 5280 × 3956 (aspect ratio = 1.33, landscape)
- Auto-select: Treatments in COLUMNS → 8 cols × 4 rows
- Result: Grid is wider than tall, matches image

**User Override:**
- Radio buttons: ◉ Auto-detect  ○ Rows  ○ Columns
- If user selects "Rows", grid becomes 8 rows × 4 cols (ignoring auto)

---

## Image-to-Date Association

### Automatic Matching

When user uploads an image:

```javascript
async function uploadImage(file) {
  // Extract EXIF date
  const exifData = await extractImageMetadata(file);
  const imageDate = exifData.dateTaken;  // "2025-01-05T14:32:00Z"

  // Get trial dates from graph data
  const trialDates = tabs[currentTabId].dates;  // ["2025-01-05", "2025-01-15", ...]

  // Find closest matching date
  const matchedDate = findClosestDate(imageDate, trialDates);

  if (matchedDate) {
    // Auto-associate
    assignImageToDate(file, matchedDate);
    showNotification(`Image associated with ${matchedDate}`);
  } else {
    // No match - prompt user
    showDatePicker(`Image date: ${imageDate}. Which trial date?`, trialDates);
  }
}

function findClosestDate(imageDate, trialDates) {
  // Convert to timestamps
  const imgTime = new Date(imageDate).getTime();

  let closestDate = null;
  let minDiff = Infinity;

  for (const trialDate of trialDates) {
    const trialTime = new Date(trialDate).getTime();
    const diff = Math.abs(imgTime - trialTime);

    // If within 3 days, consider it a match
    if (diff < 3 * 24 * 60 * 60 * 1000 && diff < minDiff) {
      closestDate = trialDate;
      minDiff = diff;
    }
  }

  return closestDate;
}
```

### Manual Association

If auto-match fails or user wants to override:

```
┌─────────────────────────────────┐
│ Upload Image                    │
├─────────────────────────────────┤
│ File: drone_jan05.jpg           │
│ EXIF Date: Jan 5, 2025 2:32 PM  │
│                                 │
│ Associate with trial date:      │
│ ○ Jan 5, 2025                   │
│ ○ Jan 15, 2025                  │
│ ○ Feb 1, 2025                   │
│ ○ Feb 15, 2025                  │
│                                 │
│ [Confirm] [Cancel]              │
└─────────────────────────────────┘
```

---

## File Management

### Easy Add/Remove

**Add Images:**
- Drag multiple files onto "Image Library" panel
- Tool processes each:
  1. Extract EXIF
  2. Auto-match to trial date
  3. Store in IndexedDB
  4. Add to images array
  5. Show thumbnail in list

**Remove Images:**
- Click image in library list
- Click "Remove" button (or Delete key)
- Confirm: "Remove image for Jan 5, 2025?"
- Delete from IndexedDB
- Remove from images array
- Grid/treatment config remains intact

**Replace Image:**
- Upload new image with same date
- Tool detects existing image for that date
- Prompt: "Replace existing image for Jan 5, 2025?"
- If yes: Delete old, store new

---

## IndexedDB Schema (Updated)

### Database: `stri_graph_generator_images`

**Store: `droneImages`**

```javascript
{
  id: "img_uuid_001",                    // Primary key
  projectId: "trial_jan_feb_2025",       // Links to project
  tabId: "tab_1",                        // Links to specific tab
  date: "2025-01-05",                    // Which trial date
  fileName: "drone_jan05.jpg",
  mimeType: "image/jpeg",
  imageData: Blob,                       // Binary image data
  thumbnail: "data:image/jpeg;base64,", // Small preview (200px wide)
  uploadedAt: "2025-01-10T10:00:00Z",
  exifData: {
    dateTaken: "2025-01-05T14:32:00Z",
    camera: "DJI Mavic 3",
    gps: { lat: 9.073928, lon: -79.634042, alt: 120 },
    dimensions: { width: 5280, height: 3956 }
  }
}
```

**Indexes:**
- `projectId` - Query all images for a project
- `tabId` - Query all images for a tab
- `date` - Fast lookup by date

---

## Screenshot Workflow

### Breakdown View Screenshot

**Goal:** Capture clean, presentation-ready image of all plots for one date

**Implementation (using existing html2canvas):**

```javascript
async function screenshotBreakdown() {
  const breakdownContainer = document.getElementById('breakdownView');

  // Same approach as existing graph screenshot
  const canvas = await html2canvas(breakdownContainer, {
    scale: 2,                           // High DPI
    backgroundColor: '#1a1a2e',          // Solid background
    width: 1400,                         // Fixed width for consistency
    height: null,                        // Auto height based on content
    logging: false
  });

  // Copy to clipboard
  canvas.toBlob(async (blob) => {
    await navigator.clipboard.write([
      new ClipboardItem({ 'image/png': blob })
    ]);
    showNotification('Screenshot copied to clipboard!');
  });
}
```

**Result:**
- Clean image with all treatments × replicates
- Date shown in header
- Treatment names on each plot
- Ready to paste into PowerPoint

---

## Timeline Navigation (Synchronized)

### Behavior

The Field Map timeline **mirrors** the Graph timeline:

**Synchronized:**
- Graph tab shows timeline: [Jan 5] [Jan 15] [Feb 1] [Feb 15]
- Field Map tab shows timeline: [Jan 5] [Jan 15] [Feb 1] [Feb 15]
- Clicking date in either tab updates `currentDateIndex`
- Both tabs show the same date

**Independent Option:**
- If user prefers, make timelines independent
- Graph can show "Jan 5" while Field Map shows "Feb 1"
- Useful for comparing graph data from one date with field image from another

**Recommendation:** Start with **synchronized** for simplicity.

---

## Implementation Adjustments

### Phase 1: Multi-Date Image Upload
1. Upload multiple images
2. EXIF extraction for each
3. Auto-match to trial dates (with manual override)
4. IndexedDB storage with date indexing
5. Image library panel showing all dates

### Phase 2: Grid Configuration (Once for All)
1. Grid calculator (auto-orientation)
2. SVG overlay with drag/resize/rotate
3. Grid config stored in fieldMap (NOT per-image)
4. Applying grid to any selected date image

### Phase 3: Treatment Mapping (Once for All)
1. Generate N× draggable tiles per treatment (N = replicates)
2. Drag-and-drop to grid cells
3. Cell mapping stored in fieldMap (NOT per-image)
4. Visual feedback and progress tracking
5. Optional: Bulk assign mode

### Phase 4: Multi-Date Plot Extraction
1. For selected date, extract all plots from that date's image
2. Same grid/mapping, different underlying image
3. Group by treatment (no replicate numbers on labels)
4. Breakdown view with date in header

### Phase 5: Timeline & Screenshot
1. Timeline navigation (matches graph timeline)
2. Previous/Next date buttons
3. Breakdown screenshot for current date
4. Synchronized or independent timeline option

---

## Export Options (Updated)

### Option 1: Light JSON Export
- Project structure + grid config + treatment mapping
- Image references only (IDs + dates)
- File size: ~20-50 KB

### Option 2: Full Archive Export (ZIP)
```
trial_jan_feb_2025_full.zip
├── project.json                 (Full config)
├── images/
│   ├── 2025-01-05.jpg          (Original drone image)
│   ├── 2025-01-15.jpg
│   ├── 2025-02-01.jpg
│   └── 2025-02-15.jpg
└── README.txt                   (Instructions for import)
```

### Option 3: Presentation Package (ZIP)
```
trial_jan_feb_2025_presentation.zip
├── breakdown_2025-01-05.png    (Screenshot)
├── breakdown_2025-01-15.png
├── breakdown_2025-02-01.png
└── breakdown_2025-02-15.png
```
- All breakdown screenshots pre-generated
- User can drag all PNGs into PowerPoint at once

---

## Summary of Key Changes

### From Original Plan → Refined Plan

| Aspect | Original | Refined |
|--------|----------|---------|
| **Images** | Single image per tab | Multiple images (one per date) |
| **Grid Config** | Per image | Once for all images |
| **Treatment Mapping** | Per image | Once for all images |
| **Navigation** | None | Timeline (like graph) |
| **Drag Items** | One per treatment | N× per treatment (N = reps) |
| **Plot Labels** | Treatment + Rep | Treatment only |
| **Orientation** | User choice | Auto-detect + override |
| **Export** | Individual plots | Composite screenshots |
| **Workflow** | Configure each time | Configure once, navigate dates |

---

## Next Steps

1. **Confirm this refined workflow** aligns with your vision
2. **Choose bulk vs. individual** drag-and-drop (or both)
3. **Decide on timeline sync** (synchronized with graph vs. independent)
4. **Proceed with implementation** once approved

**This design provides:**
- ✓ Easy multi-date management
- ✓ One-time configuration (grid + treatments)
- ✓ Temporal navigation (like existing graphs)
- ✓ Clean presentation screenshots
- ✓ Simple file management (add/remove images)

Ready to build when you approve this approach!
