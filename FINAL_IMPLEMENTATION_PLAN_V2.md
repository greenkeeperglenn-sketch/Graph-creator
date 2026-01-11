# Grid Overlay Tool - FINAL IMPLEMENTATION PLAN (v2)
## Column-Based Breakdown Views - Ready for Development

---

## Overview

Field map tool with two breakdown view modes:
1. **Single Date View:** All treatments in columns (navigate between dates)
2. **Treatment Timeline View:** All dates in columns (navigate between treatments)

Both views show 4 replicates stacked vertically in each column.

---

## View 1: Single Date View

### Layout
**Shows:** All treatments at one date, each treatment in its own column

```
┌────────────────────────────────────────────────────────────────┐
│ Single Date View                                                │
│                                                                 │
│ Date: Jan 8, 2025          [◀ Prev Date] [Next Date ▶]        │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  CONTROL   TREATMENT A  TREATMENT B  TREATMENT C  ... (10 cols)│
│  ┌─────┐   ┌─────┐     ┌─────┐     ┌─────┐                   │
│  │ R1  │   │ R1  │     │ R1  │     │ R1  │                   │
│  │     │   │     │     │     │     │     │                   │
│  └─────┘   └─────┘     └─────┘     └─────┘                   │
│  ┌─────┐   ┌─────┐     ┌─────┐     ┌─────┐                   │
│  │ R2  │   │ R2  │     │ R2  │     │ R2  │                   │
│  │     │   │     │     │     │     │     │                   │
│  └─────┘   └─────┘     └─────┘     └─────┘                   │
│  ┌─────┐   ┌─────┐     ┌─────┐     ┌─────┐                   │
│  │ R3  │   │ R3  │     │ R3  │     │ R3  │                   │
│  │     │   │     │     │     │     │     │                   │
│  └─────┘   └─────┘     └─────┘     └─────┘                   │
│  ┌─────┐   ┌─────┐     ┌─────┐     ┌─────┐                   │
│  │ R4  │   │ R4  │     │ R4  │     │ R4  │                   │
│  │     │   │     │     │     │     │     │                   │
│  └─────┘   └─────┘     └─────┘     └─────┘                   │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
Footer: [Screenshot Current View] [Switch to Treatment Timeline]
```

**Dimensions:**
- **Columns:** 10 (number of treatments)
- **Rows:** 4 (replicates per treatment)
- **Total images visible:** 40 (10 treatments × 4 reps)

**Use case:** Compare all treatments at a specific date

---

## View 2: Treatment Timeline View

### Layout
**Shows:** One treatment across all dates, each date in its own column

```
┌────────────────────────────────────────────────────────────────┐
│ Treatment Timeline View                                         │
│                                                                 │
│ Treatment: Control    [◀ Prev Treatment] [Next Treatment ▶]   │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Jan 8      Jan 20      Feb 5       Feb 21      Mar 15        │
│  ┌─────┐   ┌─────┐    ┌─────┐     ┌─────┐     ┌─────┐       │
│  │ R1  │   │ R1  │    │ R1  │     │ R1  │     │ R1  │       │
│  │     │   │     │    │     │     │     │     │     │       │
│  └─────┘   └─────┘    └─────┘     └─────┘     └─────┘       │
│  ┌─────┐   ┌─────┐    ┌─────┐     ┌─────┐     ┌─────┐       │
│  │ R2  │   │ R2  │    │ R2  │     │ R2  │     │ R2  │       │
│  │     │   │     │    │     │     │     │     │     │       │
│  └─────┘   └─────┘    └─────┘     └─────┘     └─────┘       │
│  ┌─────┐   ┌─────┐    ┌─────┐     ┌─────┐     ┌─────┐       │
│  │ R3  │   │ R3  │    │ R3  │     │ R3  │     │ R3  │       │
│  │     │   │     │    │     │     │     │     │     │       │
│  └─────┘   └─────┘    └─────┘     └─────┘     └─────┘       │
│  ┌─────┐   ┌─────┐    ┌─────┐     ┌─────┐     ┌─────┐       │
│  │ R4  │   │ R4  │    │ R4  │     │ R4  │     │ R4  │       │
│  │     │   │     │    │     │     │     │     │     │       │
│  └─────┘   └─────┘    └─────┘     └─────┘     └─────┘       │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
Footer: [Screenshot Current View] [Switch to Single Date View]
```

**Dimensions:**
- **Columns:** 5 (number of dates)
- **Rows:** 4 (replicates per treatment)
- **Total images visible:** 20 (5 dates × 4 reps for one treatment)

**Use case:** See progression of one treatment over time

---

## View Switcher UI

```
┌────────────────────────────────────────────────────────────────┐
│ « Back to Field Map    Trial: Jan-Mar 2025                      │
├────────────────────────────────────────────────────────────────┤
│ View:  ◉ Single Date   ○ Treatment Timeline                    │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│ [Content changes based on selected view]                       │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

---

## Technical Implementation

### Data Structure

```javascript
// Extract plots organized for both views
extractedPlots = {
  // For Single Date View - organized by [date][treatment][replicate]
  byDate: {
    "2025-01-08": {
      "Control": {
        rep1: { imageData: "data:image/png...", cellIndex: 0 },
        rep2: { imageData: "data:image/png...", cellIndex: 1 },
        rep3: { imageData: "data:image/png...", cellIndex: 4 },
        rep4: { imageData: "data:image/png...", cellIndex: 23 }
      },
      "Treatment A": { rep1: {...}, rep2: {...}, rep3: {...}, rep4: {...} },
      // ... all 10 treatments
    },
    "2025-01-20": { /* same structure */ },
    "2025-02-05": { /* same structure */ },
    "2025-02-21": { /* same structure */ },
    "2025-03-15": { /* same structure */ }
  },

  // For Treatment Timeline View - organized by [treatment][date][replicate]
  byTreatment: {
    "Control": {
      "2025-01-08": {
        rep1: { imageData: "data:image/png...", cellIndex: 0 },
        rep2: { imageData: "data:image/png...", cellIndex: 1 },
        rep3: { imageData: "data:image/png...", cellIndex: 4 },
        rep4: { imageData: "data:image/png...", cellIndex: 23 }
      },
      "2025-01-20": { /* reps */ },
      "2025-02-05": { /* reps */ },
      "2025-02-21": { /* reps */ },
      "2025-03-15": { /* reps */ }
    },
    "Treatment A": { /* all dates */ },
    // ... all 10 treatments
  },

  dates: ["2025-01-08", "2025-01-20", "2025-02-05", "2025-02-21", "2025-03-15"],
  treatments: ["Control", "Treatment A", "Treatment B", /* ... 10 total */]
}
```

### View 1: Single Date View Renderer

```javascript
let currentDateIndex = 0;

function renderSingleDateView() {
  const container = document.getElementById('breakdownContainer');
  container.innerHTML = '';

  const date = extractedPlots.dates[currentDateIndex];
  const dateData = extractedPlots.byDate[date];

  // Header with navigation
  const header = document.createElement('div');
  header.className = 'view-header';
  header.innerHTML = `
    <h2>Single Date View</h2>
    <div class="date-nav">
      <h3>Date: ${formatDate(date)}</h3>
      <button onclick="previousDate()" ${currentDateIndex === 0 ? 'disabled' : ''}>
        ◀ Prev Date
      </button>
      <button onclick="nextDate()" ${currentDateIndex === extractedPlots.dates.length - 1 ? 'disabled' : ''}>
        Next Date ▶
      </button>
    </div>
  `;
  container.appendChild(header);

  // Treatment columns
  const columnsContainer = document.createElement('div');
  columnsContainer.className = 'treatment-columns';

  for (const treatment of extractedPlots.treatments) {
    const treatmentData = dateData[treatment];

    const column = document.createElement('div');
    column.className = 'treatment-column';

    // Column header
    const columnHeader = document.createElement('h4');
    columnHeader.className = 'column-header';
    columnHeader.textContent = treatment.toUpperCase();
    column.appendChild(columnHeader);

    // 4 replicates stacked vertically
    if (treatmentData) {
      for (let rep = 1; rep <= 4; rep++) {
        const repData = treatmentData[`rep${rep}`];

        const plotCard = document.createElement('div');
        plotCard.className = 'plot-card';

        if (repData) {
          plotCard.innerHTML = `
            <img src="${repData.imageData}" alt="${treatment} Rep ${rep}">
            <div class="plot-label">Rep ${rep}</div>
          `;
        } else {
          plotCard.innerHTML = '<div class="no-data">No data</div>';
        }

        column.appendChild(plotCard);
      }
    } else {
      column.innerHTML += '<div class="no-treatment">No mapping</div>';
    }

    columnsContainer.appendChild(column);
  }

  container.appendChild(columnsContainer);
}

function previousDate() {
  if (currentDateIndex > 0) {
    currentDateIndex--;
    renderSingleDateView();
  }
}

function nextDate() {
  if (currentDateIndex < extractedPlots.dates.length - 1) {
    currentDateIndex++;
    renderSingleDateView();
  }
}
```

### View 2: Treatment Timeline Renderer

```javascript
let currentTreatmentIndex = 0;

function renderTreatmentTimelineView() {
  const container = document.getElementById('breakdownContainer');
  container.innerHTML = '';

  const treatment = extractedPlots.treatments[currentTreatmentIndex];
  const treatmentData = extractedPlots.byTreatment[treatment];

  // Header with navigation
  const header = document.createElement('div');
  header.className = 'view-header';
  header.innerHTML = `
    <h2>Treatment Timeline View</h2>
    <div class="treatment-nav">
      <h3>Treatment: ${treatment}</h3>
      <button onclick="previousTreatment()" ${currentTreatmentIndex === 0 ? 'disabled' : ''}>
        ◀ Prev Treatment
      </button>
      <button onclick="nextTreatment()" ${currentTreatmentIndex === extractedPlots.treatments.length - 1 ? 'disabled' : ''}>
        Next Treatment ▶
      </button>
    </div>
  `;
  container.appendChild(header);

  // Date columns
  const columnsContainer = document.createElement('div');
  columnsContainer.className = 'date-columns';

  for (const date of extractedPlots.dates) {
    const dateData = treatmentData[date];

    const column = document.createElement('div');
    column.className = 'date-column';

    // Column header
    const columnHeader = document.createElement('h4');
    columnHeader.className = 'column-header';
    columnHeader.textContent = formatDate(date);
    column.appendChild(columnHeader);

    // 4 replicates stacked vertically
    if (dateData) {
      for (let rep = 1; rep <= 4; rep++) {
        const repData = dateData[`rep${rep}`];

        const plotCard = document.createElement('div');
        plotCard.className = 'plot-card';

        if (repData) {
          plotCard.innerHTML = `
            <img src="${repData.imageData}" alt="${treatment} Rep ${rep}">
            <div class="plot-label">Rep ${rep}</div>
          `;
        } else {
          plotCard.innerHTML = '<div class="no-data">No data</div>';
        }

        column.appendChild(plotCard);
      }
    } else {
      column.innerHTML += '<div class="no-date">Missing image</div>';
    }

    columnsContainer.appendChild(column);
  }

  container.appendChild(columnsContainer);
}

function previousTreatment() {
  if (currentTreatmentIndex > 0) {
    currentTreatmentIndex--;
    renderTreatmentTimelineView();
  }
}

function nextTreatment() {
  if (currentTreatmentIndex < extractedPlots.treatments.length - 1) {
    currentTreatmentIndex++;
    renderTreatmentTimelineView();
  }
}
```

### View Mode Switcher

```javascript
let breakdownViewMode = 'single-date';  // or 'treatment-timeline'

function switchBreakdownView(mode) {
  breakdownViewMode = mode;

  // Update radio buttons
  document.querySelectorAll('input[name="breakdownView"]').forEach(input => {
    input.checked = input.value === mode;
  });

  // Render appropriate view
  if (mode === 'single-date') {
    renderSingleDateView();
  } else {
    renderTreatmentTimelineView();
  }
}

// Initialize view switcher
document.addEventListener('DOMContentLoaded', () => {
  const viewSwitcher = document.getElementById('breakdownViewSwitcher');
  viewSwitcher.innerHTML = `
    <label>
      <input type="radio" name="breakdownView" value="single-date" checked>
      Single Date
    </label>
    <label>
      <input type="radio" name="breakdownView" value="treatment-timeline">
      Treatment Timeline
    </label>
  `;

  viewSwitcher.addEventListener('change', (e) => {
    switchBreakdownView(e.target.value);
  });
});
```

---

## CSS Styling

```css
/* View header */
.view-header {
  margin-bottom: 24px;
  text-align: center;
}

.date-nav,
.treatment-nav {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 16px;
  margin-top: 12px;
}

.date-nav button,
.treatment-nav button {
  padding: 8px 16px;
  background: rgba(16, 185, 129, 0.2);
  color: #10b981;
  border: 1px solid rgba(16, 185, 129, 0.3);
  border-radius: 6px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.2s ease;
}

.date-nav button:hover:not(:disabled),
.treatment-nav button:hover:not(:disabled) {
  background: rgba(16, 185, 129, 0.3);
}

.date-nav button:disabled,
.treatment-nav button:disabled {
  opacity: 0.3;
  cursor: not-allowed;
}

/* Column layouts */
.treatment-columns,
.date-columns {
  display: grid;
  grid-auto-flow: column;
  grid-auto-columns: minmax(200px, 1fr);
  gap: 20px;
  padding: 20px;
  overflow-x: auto;
}

.treatment-column,
.date-column {
  background: rgba(26, 26, 46, 0.4);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 16px;
  min-width: 200px;
}

.column-header {
  color: #10b981;
  font-size: 14px;
  font-weight: 600;
  text-align: center;
  margin-bottom: 16px;
  padding-bottom: 12px;
  border-bottom: 1px solid rgba(16, 185, 129, 0.3);
}

/* Plot cards */
.plot-card {
  background: rgba(26, 26, 46, 0.8);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  padding: 8px;
  margin-bottom: 12px;
  text-align: center;
}

.plot-card:last-child {
  margin-bottom: 0;
}

.plot-card img {
  width: 100%;
  height: auto;
  border-radius: 6px;
  margin-bottom: 6px;
  cursor: zoom-in;
}

.plot-label {
  font-size: 11px;
  color: #94a3b8;
  font-weight: 500;
}

.no-data,
.no-treatment,
.no-date {
  padding: 40px 20px;
  text-align: center;
  color: #64748b;
  font-size: 13px;
  font-style: italic;
}

/* View switcher */
#breakdownViewSwitcher {
  display: flex;
  justify-content: center;
  gap: 24px;
  margin-bottom: 24px;
  padding: 16px;
  background: rgba(26, 26, 46, 0.4);
  border-radius: 12px;
}

#breakdownViewSwitcher label {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  color: #94a3b8;
  font-weight: 500;
}

#breakdownViewSwitcher input[type="radio"] {
  cursor: pointer;
}

#breakdownViewSwitcher input[type="radio"]:checked + span {
  color: #10b981;
}
```

---

## User Workflows

### Workflow 1: Compare Treatments at Peak Growth

**Goal:** See all treatments at Feb 21 (peak) for comparison

1. Click "View Breakdown"
2. Select "Single Date" view
3. Navigate to Feb 21 using "Next Date" button
4. See all 10 treatments side-by-side in columns
5. Each treatment shows 4 replicates stacked
6. Screenshot → Paste into PowerPoint

**Result:** One image showing all treatments at peak growth

---

### Workflow 2: Track Treatment A Progression

**Goal:** See how Treatment A performed over entire trial

1. Click "View Breakdown"
2. Select "Treatment Timeline" view
3. Navigate to "Treatment A" using "Next Treatment" button
4. See Treatment A across all 5 dates in columns
5. Each date shows 4 replicates stacked
6. Visually identify when treatment effect appeared
7. Screenshot → Paste into PowerPoint

**Result:** One image showing Treatment A's progression from Jan 8 → Mar 15

---

### Workflow 3: Full Trial Documentation

**Goal:** Document entire trial with screenshots

**Single Date View:**
- Screenshot Jan 8 (all treatments)
- Next Date → Screenshot Jan 20
- Next Date → Screenshot Feb 5
- Next Date → Screenshot Feb 21
- Next Date → Screenshot Mar 15
- **Result:** 5 slides, each showing all treatments at one date

**Treatment Timeline View:**
- Screenshot Control (all dates)
- Next Treatment → Screenshot Treatment A
- Continue through all 10 treatments
- **Result:** 10 slides, each showing one treatment over time

**Total:** 15 comprehensive screenshots covering entire trial

---

## Screenshot Handling

### Single Date View Screenshot
```javascript
async function screenshotSingleDateView() {
  const container = document.getElementById('breakdownContainer');

  const canvas = await html2canvas(container, {
    scale: 2,
    backgroundColor: '#1a1a2e',
    width: 1600,  // Wide enough for 10 columns
    logging: false
  });

  canvas.toBlob(async (blob) => {
    await navigator.clipboard.write([
      new ClipboardItem({ 'image/png': blob })
    ]);

    const date = extractedPlots.dates[currentDateIndex];
    showNotification(`Screenshot: All treatments on ${formatDate(date)} ✓`);
  });
}
```

### Treatment Timeline Screenshot
```javascript
async function screenshotTreatmentTimeline() {
  const container = document.getElementById('breakdownContainer');

  const canvas = await html2canvas(container, {
    scale: 2,
    backgroundColor: '#1a1a2e',
    width: 1200,  // Wide enough for 5 date columns
    logging: false
  });

  canvas.toBlob(async (blob) => {
    await navigator.clipboard.write([
      new ClipboardItem({ 'image/png': blob })
    ]);

    const treatment = extractedPlots.treatments[currentTreatmentIndex];
    showNotification(`Screenshot: ${treatment} timeline ✓`);
  });
}
```

---

## Keyboard Shortcuts (Optional Enhancement)

```javascript
document.addEventListener('keydown', (e) => {
  // Only in breakdown view
  if (!isBreakdownViewActive()) return;

  if (breakdownViewMode === 'single-date') {
    if (e.key === 'ArrowLeft') previousDate();
    if (e.key === 'ArrowRight') nextDate();
  } else {
    if (e.key === 'ArrowLeft') previousTreatment();
    if (e.key === 'ArrowRight') nextTreatment();
  }

  // Screenshot shortcut
  if (e.key === 's' && (e.ctrlKey || e.metaKey)) {
    e.preventDefault();
    if (breakdownViewMode === 'single-date') {
      screenshotSingleDateView();
    } else {
      screenshotTreatmentTimeline();
    }
  }
});
```

**Shortcuts:**
- **← →** Navigate prev/next
- **Ctrl/Cmd + S** Screenshot current view

---

## Implementation Timeline

### Phase 4: Plot Extraction & Views (Updated)

**Day 7-8: Core Extraction & Views**
- Extract plots from all images with replicate tracking
- Organize data for both views (byDate and byTreatment)
- Build Single Date View with prev/next navigation
- Build Treatment Timeline View with prev/next navigation
- View mode switcher

**Day 9: Polish**
- Screenshot functions for both views
- Keyboard shortcuts
- Loading states
- Missing data handling
- Smooth transitions between views

**Total:** 3 days (no change from original estimate)

---

## Complete Feature Set

### View 1: Single Date
✓ All treatments in columns (10 columns)
✓ 4 replicates stacked per column
✓ Prev/Next date navigation
✓ Screenshot current date

### View 2: Treatment Timeline
✓ All dates in columns (5 columns)
✓ 4 replicates stacked per column
✓ Prev/Next treatment navigation
✓ Screenshot current treatment

### Shared Features
✓ Clean column-based layout
✓ Replicate identity maintained (rep1 = same plot always)
✓ View mode toggle (radio buttons)
✓ Keyboard shortcuts
✓ High-quality screenshots

---

## Summary

**Two focused views:**
1. **Compare treatments** at specific dates (navigate dates)
2. **Track treatment progression** over time (navigate treatments)

**Simple navigation:**
- Prev/Next buttons
- Keyboard shortcuts (arrows + Ctrl+S)

**Clean layout:**
- Columns for treatments or dates
- 4 replicates stacked vertically
- Horizontal scrolling if needed

**Perfect for presentations:**
- One screenshot = all treatments at one date
- One screenshot = one treatment across all dates
- Clean, professional output

**Ready to build!** 🚀
