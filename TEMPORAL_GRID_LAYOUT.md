# Grid Overlay Tool - TEMPORAL GRID LAYOUT (FINAL)
## Matrix View: All Treatments × All Dates

---

## Concept: Treatment-Date Matrix

**Layout:** Grid with treatments as COLUMNS and dates as ROWS
**Each cell:** Contains 4 replicate plot images (2×2 mini-grid)
**Purpose:** See entire trial at a glance

---

## Visual Layout

### Example: 10 Treatments × 5 Dates = 50 Cells

```
┌───────────────────────────────────────────────────────────────────────────────┐
│                        TEMPORAL PROGRESSION - GRID VIEW                        │
├───────────────────────────────────────────────────────────────────────────────┤
│                                                                                │
│        CONTROL   TREATMENT A  TREATMENT B  TREATMENT C  ...  TREATMENT J       │
│        ──────────────────────────────────────────────────────────────────────  │
│ Jan 8  ┌──────┐  ┌──────┐    ┌──────┐    ┌──────┐         ┌──────┐          │
│        │ R1 R2│  │ R1 R2│    │ R1 R2│    │ R1 R2│         │ R1 R2│          │
│        │ R3 R4│  │ R3 R4│    │ R3 R4│    │ R3 R4│    ...  │ R3 R4│          │
│        └──────┘  └──────┘    └──────┘    └──────┘         └──────┘          │
│                                                                                │
│ Jan 20 ┌──────┐  ┌──────┐    ┌──────┐    ┌──────┐         ┌──────┐          │
│        │ R1 R2│  │ R1 R2│    │ R1 R2│    │ R1 R2│         │ R1 R2│          │
│        │ R3 R4│  │ R3 R4│    │ R3 R4│    │ R3 R4│    ...  │ R3 R4│          │
│        └──────┘  └──────┘    └──────┘    └──────┘         └──────┘          │
│                                                                                │
│ Feb 5  ┌──────┐  ┌──────┐    ┌──────┐    ┌──────┐         ┌──────┐          │
│        │ R1 R2│  │ R1 R2│    │ R1 R2│    │ R1 R2│         │ R1 R2│          │
│        │ R3 R4│  │ R3 R4│    │ R3 R4│    │ R3 R4│    ...  │ R3 R4│          │
│        └──────┘  └──────┘    └──────┘    └──────┘         └──────┘          │
│                                                                                │
│ Feb 21 ┌──────┐  ┌──────┐    ┌──────┐    ┌──────┐         ┌──────┐          │
│        │ R1 R2│  │ R1 R2│    │ R1 R2│    │ R1 R2│         │ R1 R2│          │
│        │ R3 R4│  │ R3 R4│    │ R3 R4│    │ R3 R4│    ...  │ R3 R4│          │
│        └──────┘  └──────┘    └──────┘    └──────┘         └──────┘          │
│                                                                                │
│ Mar 15 ┌──────┐  ┌──────┐    ┌──────┐    ┌──────┐         ┌──────┐          │
│        │ R1 R2│  │ R1 R2│    │ R1 R2│    │ R1 R2│         │ R1 R2│          │
│        │ R3 R4│  │ R3 R4│    │ R3 R4│    │ R3 R4│    ...  │ R3 R4│          │
│        └──────┘  └──────┘    └──────┘    └──────┘         └──────┘          │
│                                                                                │
└───────────────────────────────────────────────────────────────────────────────┘

Footer: [Screenshot Entire Grid] [Export as PDF]
```

**Reading the grid:**
- **Vertical (down a column):** See one treatment's progression over 5 dates
- **Horizontal (across a row):** Compare all 10 treatments at one date
- **One cell:** 4 replicates at one treatment × date combination

---

## Detailed Cell Design

### Cell with Data
```
┌─────────────────────────┐
│ Control - Jan 8, 2025   │ ← Cell header
├───────────┬─────────────┤
│           │             │
│  [Rep 1]  │  [Rep 2]    │ ← Top row: R1, R2
│   plot    │   plot      │
│           │             │
├───────────┼─────────────┤
│           │             │
│  [Rep 3]  │  [Rep 4]    │ ← Bottom row: R3, R4
│   plot    │   plot      │
│           │             │
└───────────┴─────────────┘
```

**Rep Identity:**
- Rep 1 = Same physical plot position across all dates
- Rep 2 = Same physical plot position across all dates
- Rep 3 = Same physical plot position across all dates
- Rep 4 = Same physical plot position across all dates

**Example:**
- Cell (Control, Jan 8) → Rep 1 shows plot from field position [Row 0, Col 1]
- Cell (Control, Feb 5) → Rep 1 shows SAME plot from field position [Row 0, Col 1]
- User can track the exact same physical plot over time

---

### Cell with Missing Data
```
┌─────────────────────────┐
│ Control - Jan 8, 2025   │
├─────────────────────────┤
│                         │
│   ⚠️ Missing Data        │
│                         │
│   No drone image for    │
│   this date             │
│                         │
│  ┌───────────────────┐  │
│  │  + Add Image      │  │ ← Click to upload for this date
│  └───────────────────┘  │
│                         │
│  ┌───────────────────┐  │
│  │  🗑️ Delete Date    │  │ ← Or remove date from timeline
│  └───────────────────┘  │
│                         │
└─────────────────────────┘
```

**User Actions:**
1. **Add Image:**
   - Click "+ Add Image"
   - Upload drone image for Jan 8
   - Tool extracts plots and populates this cell
   - Grid configuration already exists (no reconfiguration needed)

2. **Delete Date:**
   - Click "Delete Date"
   - Confirms: "Remove Jan 8 from all treatments?"
   - Deletes entire row from grid
   - Jan 8 removed from timeline

---

## Updated UI Layout - Temporal Grid View

```
┌──────────────────────────────────────────────────────────────────────────────┐
│ « Back to Field Map    Trial: Jan-Mar 2025                                    │
├──────────────────────────────────────────────────────────────────────────────┤
│ View Mode:  ○ Single Date   ◉ Temporal Grid                                  │
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                               │
│  [Scrollable Grid Container]                                                 │
│                                                                               │
│  Column Headers (Fixed):                                                     │
│  ┌─────────┬─────────┬─────────┬─────────┬─────────┬─────────┐              │
│  │ Date ▼  │ CONTROL │ TRTMT A │ TRTMT B │ TRTMT C │ ...     │              │
│  └─────────┴─────────┴─────────┴─────────┴─────────┴─────────┘              │
│                                                                               │
│  Row 1: Jan 8, 2025                                                          │
│  ┌─────────┬─────────┬─────────┬─────────┬─────────┬─────────┐              │
│  │ Jan 8   │ [4 plot]│ [4 plot]│ [4 plot]│ [4 plot]│ ...     │              │
│  │         │  images │  images │  images │  images │         │              │
│  └─────────┴─────────┴─────────┴─────────┴─────────┴─────────┘              │
│                                                                               │
│  Row 2: Jan 20, 2025                                                         │
│  ┌─────────┬─────────┬─────────┬─────────┬─────────┬─────────┐              │
│  │ Jan 20  │ [4 plot]│ [4 plot]│ [MISSING│ [4 plot]│ ...     │              │
│  │         │  images │  images │  DATA]  │  images │         │              │
│  └─────────┴─────────┴─────────┴─────────┴─────────┴─────────┘              │
│                                                                               │
│  Row 3: Feb 5, 2025                                                          │
│  ┌─────────┬─────────┬─────────┬─────────┬─────────┬─────────┐              │
│  │ Feb 5   │ [4 plot]│ [4 plot]│ [4 plot]│ [4 plot]│ ...     │              │
│  │         │  images │  images │  images │  images │         │              │
│  └─────────┴─────────┴─────────┴─────────┴─────────┴─────────┘              │
│                                                                               │
│  ... (more dates)                                                            │
│                                                                               │
├──────────────────────────────────────────────────────────────────────────────┤
│ Footer: [Screenshot Entire Grid] [Export as PDF] [Print]                     │
└──────────────────────────────────────────────────────────────────────────────┘
```

**Features:**
- Fixed column headers (scroll with content)
- Fixed row date labels (left column)
- Scrollable grid area
- Each cell clickable to zoom/view full resolution

---

## Data Structure for Replicate Tracking

### Enhanced Cell Mapping
```javascript
// When mapping treatments to cells, track replicate identity
cellMapping: [
  {
    cellIndex: 0,
    row: 0,
    col: 0,
    treatmentName: "Control",
    replicateId: "rep1"  // NEW: Permanent ID for this replicate
  },
  {
    cellIndex: 1,
    row: 0,
    col: 1,
    treatmentName: "Control",
    replicateId: "rep2"  // This is rep 2 of Control
  },
  {
    cellIndex: 4,
    row: 0,
    col: 4,
    treatmentName: "Control",
    replicateId: "rep3"  // This is rep 3 of Control
  },
  {
    cellIndex: 23,
    row: 2,
    col: 7,
    treatmentName: "Control",
    replicateId: "rep4"  // This is rep 4 of Control
  },
  // ... more mappings
]
```

### Extracted Plots Structure for Grid View
```javascript
extractedPlots = {
  gridData: {
    // Organized by [treatment][replicate][date]
    "Control": {
      "rep1": {
        "2025-01-08": { imageData: "data:image/png...", cellIndex: 0 },
        "2025-01-20": { imageData: "data:image/png...", cellIndex: 0 },
        "2025-02-05": { imageData: "data:image/png...", cellIndex: 0 },
        "2025-02-21": { imageData: "data:image/png...", cellIndex: 0 },
        "2025-03-15": { imageData: "data:image/png...", cellIndex: 0 }
      },
      "rep2": {
        "2025-01-08": { imageData: "data:image/png...", cellIndex: 1 },
        "2025-01-20": { imageData: "data:image/png...", cellIndex: 1 },
        // ... all dates
      },
      "rep3": { /* ... */ },
      "rep4": { /* ... */ }
    },
    "Treatment A": {
      "rep1": { /* dates */ },
      "rep2": { /* dates */ },
      "rep3": { /* dates */ },
      "rep4": { /* dates */ }
    },
    // ... all treatments
  },

  dates: ["2025-01-08", "2025-01-20", "2025-02-05", "2025-02-21", "2025-03-15"],
  treatments: ["Control", "Treatment A", "Treatment B", /* ... */]
}
```

---

## Technical Implementation

### 1. Render Temporal Grid View

```javascript
function renderTemporalGridView(extractedPlots) {
  const container = document.getElementById('temporalGridContainer');
  container.innerHTML = '';

  const { gridData, dates, treatments } = extractedPlots;

  // Create grid table
  const table = document.createElement('table');
  table.className = 'temporal-grid';

  // Header row (treatment names)
  const headerRow = document.createElement('tr');
  headerRow.innerHTML = `
    <th class="date-column">Date</th>
    ${treatments.map(t => `<th class="treatment-column">${t}</th>`).join('')}
  `;
  table.appendChild(headerRow);

  // Data rows (one per date)
  for (const date of dates) {
    const row = document.createElement('tr');

    // Date label cell
    const dateCell = document.createElement('td');
    dateCell.className = 'date-label-cell';
    dateCell.textContent = formatDate(date);
    row.appendChild(dateCell);

    // Treatment cells (one per treatment)
    for (const treatment of treatments) {
      const cell = document.createElement('td');
      cell.className = 'treatment-cell';

      const treatmentData = gridData[treatment];

      if (treatmentData) {
        // Create 2×2 mini-grid for 4 replicates
        const miniGrid = document.createElement('div');
        miniGrid.className = 'replicate-mini-grid';

        for (let rep = 1; rep <= 4; rep++) {
          const repId = `rep${rep}`;
          const plotData = treatmentData[repId]?.[date];

          const plotDiv = document.createElement('div');
          plotDiv.className = 'replicate-plot';

          if (plotData) {
            const img = document.createElement('img');
            img.src = plotData.imageData;
            img.alt = `${treatment} Rep ${rep}`;
            img.onclick = () => zoomPlot(plotData);
            plotDiv.appendChild(img);
          } else {
            // Missing data for this replicate
            plotDiv.className += ' missing-data';
            plotDiv.innerHTML = '<span class="no-data-label">—</span>';
          }

          miniGrid.appendChild(plotDiv);
        }

        cell.appendChild(miniGrid);
      } else {
        // Treatment not mapped
        cell.className += ' no-treatment';
        cell.innerHTML = '<div class="no-data">No mapping</div>';
      }

      // Add cell header
      const cellHeader = document.createElement('div');
      cellHeader.className = 'cell-header';
      cellHeader.textContent = `${treatment} - ${formatDate(date)}`;
      cell.insertBefore(cellHeader, cell.firstChild);

      row.appendChild(cell);
    }

    table.appendChild(row);
  }

  container.appendChild(table);
}
```

### 2. Handle Missing Data

```javascript
function renderMissingDataCell(treatment, date) {
  return `
    <div class="missing-data-cell">
      <div class="missing-icon">⚠️</div>
      <div class="missing-text">Missing Data</div>
      <div class="missing-subtext">No drone image for ${formatDate(date)}</div>
      <div class="missing-actions">
        <button class="add-image-btn" onclick="addImageForDate('${date}')">
          + Add Image
        </button>
        <button class="delete-date-btn" onclick="deleteDateFromTimeline('${date}')">
          🗑️ Delete Date
        </button>
      </div>
    </div>
  `;
}

async function addImageForDate(date) {
  // Open file picker
  const input = document.createElement('input');
  input.type = 'file';
  input.accept = 'image/*';

  input.onchange = async (e) => {
    const file = e.target.files[0];
    if (!file) return;

    // Upload and associate with this specific date
    await uploadDroneImage(file, date);  // Force date association

    // Re-extract plots
    await extractPlotsAllDates();

    // Re-render grid
    renderTemporalGridView(extractedPlots);

    showNotification(`Added image for ${formatDate(date)} ✓`);
  };

  input.click();
}

function deleteDateFromTimeline(date) {
  if (!confirm(`Delete ${formatDate(date)} from timeline? This will remove the date from all treatments.`)) {
    return;
  }

  const tab = tabs[currentTabId];
  const fieldMap = tab.fieldMap;

  // Remove image from fieldMap
  fieldMap.images = fieldMap.images.filter(img => img.date !== date);

  // Delete from IndexedDB
  const imageToDelete = fieldMap.images.find(img => img.date === date);
  if (imageToDelete) {
    imageDB.delete(imageToDelete.imageId);
  }

  // Re-render
  renderTemporalGridView(extractedPlots);

  showNotification(`Deleted ${formatDate(date)} from timeline`);
}
```

### 3. Track Replicate Identity

When user maps treatments, assign replicate IDs in order:

```javascript
function assignTreatmentToCell(cellIndex, treatmentName) {
  const tab = tabs[currentTabId];
  const fieldMap = tab.fieldMap;

  // Count how many cells already assigned to this treatment
  const assignedCount = fieldMap.cellMapping.filter(m => m.treatmentName === treatmentName).length;

  // Assign next replicate ID (rep1, rep2, rep3, rep4)
  const replicateId = `rep${assignedCount + 1}`;

  // Calculate row/col
  const row = Math.floor(cellIndex / fieldMap.gridCols);
  const col = cellIndex % fieldMap.gridCols;

  // Add to mapping
  fieldMap.cellMapping.push({
    cellIndex: cellIndex,
    row: row,
    col: col,
    treatmentName: treatmentName,
    replicateId: replicateId  // CRITICAL: Track which replicate this is
  });

  saveProjectToLocalStorage();
}
```

### 4. Extract Plots with Replicate Tracking

```javascript
async function extractPlotsAllDates() {
  const tab = tabs[currentTabId];
  const fieldMap = tab.fieldMap;

  const gridData = {};

  // Initialize structure
  for (const treatment of tab.treatments) {
    gridData[treatment.name] = {
      rep1: {},
      rep2: {},
      rep3: {},
      rep4: {}
    };
  }

  // Extract from each image
  for (const imageRef of fieldMap.images) {
    const imageRecord = await imageDB.get(imageRef.imageId);
    const plots = await extractPlotsFromImage(imageRecord, fieldMap);

    // Organize by treatment and replicate ID
    for (const plot of plots) {
      const mapping = fieldMap.cellMapping.find(m => m.cellIndex === plot.cellIndex);

      if (mapping) {
        gridData[mapping.treatmentName][mapping.replicateId][imageRef.date] = {
          imageData: plot.imageData,
          cellIndex: plot.cellIndex,
          row: mapping.row,
          col: mapping.col
        };
      }
    }
  }

  return {
    gridData: gridData,
    dates: fieldMap.images.map(img => img.date).sort(),
    treatments: tab.treatments.map(t => t.name)
  };
}
```

---

## CSS Styling

```css
/* Temporal grid table */
.temporal-grid {
  width: 100%;
  border-collapse: separate;
  border-spacing: 12px;
  margin: 20px 0;
}

/* Header cells */
.temporal-grid th {
  background: rgba(16, 185, 129, 0.2);
  color: #10b981;
  font-weight: 600;
  padding: 12px;
  border-radius: 8px;
  text-align: center;
  position: sticky;
  top: 0;
  z-index: 10;
}

.date-column {
  min-width: 100px;
}

.treatment-column {
  min-width: 250px;
  max-width: 300px;
}

/* Date label cell */
.date-label-cell {
  background: rgba(99, 102, 241, 0.2);
  color: #6366f1;
  font-weight: 600;
  padding: 12px;
  border-radius: 8px;
  text-align: center;
  vertical-align: middle;
  position: sticky;
  left: 0;
  z-index: 5;
}

/* Treatment cells */
.treatment-cell {
  background: rgba(26, 26, 46, 0.6);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 12px;
  vertical-align: top;
  position: relative;
}

.cell-header {
  font-size: 11px;
  color: #94a3b8;
  margin-bottom: 8px;
  text-align: center;
  font-weight: 500;
}

/* 2×2 mini-grid for replicates */
.replicate-mini-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 1fr;
  gap: 6px;
  width: 100%;
  aspect-ratio: 1;
}

.replicate-plot {
  background: rgba(0, 0, 0, 0.3);
  border-radius: 6px;
  overflow: hidden;
  position: relative;
  cursor: zoom-in;
}

.replicate-plot img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.replicate-plot.missing-data {
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: default;
}

.no-data-label {
  color: #64748b;
  font-size: 24px;
}

/* Missing data cell */
.missing-data-cell {
  padding: 20px;
  text-align: center;
  min-height: 200px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.missing-icon {
  font-size: 32px;
  margin-bottom: 12px;
}

.missing-text {
  font-weight: 600;
  color: #f59e0b;
  margin-bottom: 4px;
}

.missing-subtext {
  font-size: 12px;
  color: #94a3b8;
  margin-bottom: 16px;
}

.missing-actions {
  display: flex;
  flex-direction: column;
  gap: 8px;
  width: 100%;
  max-width: 180px;
}

.add-image-btn,
.delete-date-btn {
  padding: 8px 16px;
  border-radius: 6px;
  border: none;
  font-size: 13px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
}

.add-image-btn {
  background: #10b981;
  color: white;
}

.add-image-btn:hover {
  background: #059669;
}

.delete-date-btn {
  background: rgba(239, 68, 68, 0.2);
  color: #ef4444;
  border: 1px solid rgba(239, 68, 68, 0.3);
}

.delete-date-btn:hover {
  background: rgba(239, 68, 68, 0.3);
}
```

---

## Screenshot Handling

### Entire Grid Screenshot

**Challenge:** Grid is very wide (10 treatments) and scrolls horizontally

**Solutions:**

#### Option 1: Wide PNG (Recommended for Digital)
```javascript
async function screenshotTemporalGrid() {
  const gridContainer = document.getElementById('temporalGridContainer');

  // Capture full scrollable width
  const canvas = await html2canvas(gridContainer, {
    scale: 2,
    backgroundColor: '#1a1a2e',
    width: gridContainer.scrollWidth,   // Full width
    height: gridContainer.scrollHeight, // Full height
    windowWidth: gridContainer.scrollWidth,
    windowHeight: gridContainer.scrollHeight,
    logging: false
  });

  // Copy to clipboard or download
  canvas.toBlob(async (blob) => {
    try {
      await navigator.clipboard.write([
        new ClipboardItem({ 'image/png': blob })
      ]);
      showNotification('Grid screenshot copied to clipboard! ✓');
    } catch (err) {
      // Fallback: download
      downloadBlob(blob, `temporal_grid_${formatDate(new Date())}.png`);
      showNotification('Grid screenshot downloaded');
    }
  });
}
```

**Result:** Very wide PNG (e.g., 3000px × 1200px)
**Use case:** Paste into PowerPoint, auto-fits to slide width

#### Option 2: PDF Export (Recommended for Print)
```javascript
// Using jsPDF library
async function exportGridAsPDF() {
  const { jsPDF } = window.jspdf;
  const doc = new jsPDF({
    orientation: 'landscape',
    unit: 'mm',
    format: 'a3'  // Large format for grid
  });

  const gridContainer = document.getElementById('temporalGridContainer');

  // Render to PDF
  await doc.html(gridContainer, {
    callback: (doc) => {
      doc.save(`temporal_grid_${formatDate(new Date())}.pdf`);
      showNotification('PDF exported! ✓');
    },
    x: 10,
    y: 10,
    width: 400,
    windowWidth: gridContainer.scrollWidth
  });
}
```

**Dependency:**
```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
```

---

## User Workflow Examples

### Scenario 1: Complete Trial Documentation

**Goal:** Screenshot entire grid for final report

1. Navigate to "Temporal Grid" view
2. All treatments × all dates displayed in matrix
3. Click "Screenshot Entire Grid"
4. Paste into Word/PowerPoint → One comprehensive view
5. Result: Entire trial visible in one image

---

### Scenario 2: Identify Treatment Effect Timeline

**Goal:** See when Treatment A started outperforming Control

1. View temporal grid
2. Look at "Treatment A" column (read down)
3. Compare visually to "Control" column
4. Jan 8: Similar appearance
5. Jan 20: Slight difference
6. Feb 5: Clear difference visible ← Treatment effect onset
7. Screenshot grid → Annotate in PowerPoint to highlight this progression

---

### Scenario 3: Handle Missing Data

**Goal:** Realize Feb 5 image is missing, add it later

1. View temporal grid
2. Feb 5 row shows missing data cells
3. Click "+ Add Image" in Feb 5 / Control cell
4. Upload `drone_feb05.jpg`
5. Tool extracts all plots automatically
6. Feb 5 row now populated ✓
7. Grid complete

---

### Scenario 4: Remove Bad Image Date

**Goal:** Jan 20 image was blurry, remove it from analysis

1. View temporal grid
2. See Jan 20 row has poor quality images
3. Click "Delete Date" in any Jan 20 cell
4. Confirm deletion
5. Jan 20 row removed from grid
6. Timeline now: Jan 8, Feb 5, Feb 21, Mar 15 (4 dates)

---

## Implementation Summary

### Grid Dimensions
- **Columns:** Number of treatments (e.g., 10)
- **Rows:** Number of dates (e.g., 5)
- **Cells:** Columns × Rows (e.g., 50)
- **Each cell:** 2×2 mini-grid = 4 replicate images

### Key Features
- ✓ Treatment columns with headers
- ✓ Date rows with labels
- ✓ 2×2 replicate mini-grid per cell
- ✓ Replicate identity maintained across dates (rep1 = same plot always)
- ✓ Missing data cells with add/delete options
- ✓ Scrollable grid (horizontal + vertical)
- ✓ Screenshot entire grid (wide PNG or PDF)
- ✓ Zoom individual plots

### Data Requirements
- Replicate ID tracking in cellMapping
- Extract plots indexed by [treatment][replicate][date]
- Missing data detection and handling

---

## Updated Timeline

**Phase 4: Plot Extraction & Views (Updated)**
- Day 7: Single date view (by treatment)
- Day 8: Temporal grid view (matrix layout)
  - Grid renderer
  - 2×2 replicate mini-grids
  - Missing data UI
  - Add/delete date functionality
- Day 9: Screenshot & export
  - Wide PNG capture
  - PDF export

**No time added** - temporal grid replaces previous "temporal progression" implementation

---

## Final Confirmation

This design gives you:

✓ **One comprehensive view** of entire trial
✓ **Read down** a column → See one treatment over time
✓ **Read across** a row → Compare all treatments at one date
✓ **Same replicate tracking** → Rep 1 is always same physical plot
✓ **Missing data handling** → Add images or delete dates interactively
✓ **Perfect for presentations** → One screenshot shows everything

**Ready to implement?** 🚀
