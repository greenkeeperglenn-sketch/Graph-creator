# Grid Overlay Tool - BREAKDOWN VIEW MODES
## Addendum to Final Plan - Display Options

---

## Two Breakdown View Modes

### Mode 1: Single Date View (By Treatment)
**Purpose:** See all treatments at one point in time

**Layout:**
```
Date: Jan 8, 2025

CONTROL
┌────┐ ┌────┐ ┌────┐ ┌────┐
│ R1 │ │ R2 │ │ R3 │ │ R4 │  ← 4 replicates in a row
└────┘ └────┘ └────┘ └────┘

TREATMENT A
┌────┐ ┌────┐ ┌────┐ ┌────┐
│ R1 │ │ R2 │ │ R3 │ │ R4 │
└────┘ └────┘ └────┘ └────┘

TREATMENT B
┌────┐ ┌────┐ ┌────┐ ┌────┐
│ R1 │ │ R2 │ │ R3 │ │ R4 │
└────┘ └────┘ └────┘ └────┘

... (all 10 treatments)
```

**Use case:** Compare treatments at a specific timepoint

---

### Mode 2: Temporal View (By Treatment Over Time) ⭐ NEW
**Purpose:** See progression of each treatment over the entire trial period

**Layout:**
```
CONTROL - Progression Over Time

Jan 8     Jan 20    Feb 5     Feb 21    Mar 15
┌────┐    ┌────┐    ┌────┐    ┌────┐    ┌────┐
│    │    │    │    │    │    │    │    │    │  ← Rep 1 across all dates
└────┘    └────┘    └────┘    └────┘    └────┘

┌────┐    ┌────┐    ┌────┐    ┌────┐    ┌────┐
│    │    │    │    │    │    │    │    │    │  ← Rep 2 across all dates
└────┘    └────┘    └────┘    └────┘    └────┘

┌────┐    ┌────┐    ┌────┐    ┌────┐    ┌────┐
│    │    │    │    │    │    │    │    │    │  ← Rep 3 across all dates
└────┘    └────┘    └────┘    └────┘    └────┘

┌────┐    ┌────┐    ┌────┐    ┌────┐    ┌────┐
│    │    │    │    │    │    │    │    │    │  ← Rep 4 across all dates
└────┘    └────┘    └────┘    └────┘    └────┘

────────────────────────────────────────────────

TREATMENT A - Progression Over Time

Jan 8     Jan 20    Feb 5     Feb 21    Mar 15
┌────┐    ┌────┐    ┌────┐    ┌────┐    ┌────┐
│    │ → │    │ → │    │ → │    │ → │    │  ← Rep 1 timeline
└────┘    └────┘    └────┘    └────┘    └────┘

┌────┐    ┌────┐    ┌────┐    ┌────┐    ┌────┐
│    │ → │    │ → │    │ → │    │ → │    │  ← Rep 2 timeline
└────┘    └────┘    └────┘    └────┘    └────┘

┌────┐    ┌────┐    ┌────┐    ┌────┐    ┌────┐
│    │ → │    │ → │    │ → │    │ → │    │  ← Rep 3 timeline
└────┘    └────┘    └────┘    └────┘    └────┘

┌────┐    ┌────┐    ┌────┐    ┌────┐    ┌────┐
│    │ → │    │ → │    │ → │    │ → │    │  ← Rep 4 timeline
└────┘    └────┘    └────┘    └────┘    └────┘

────────────────────────────────────────────────

... (all 10 treatments)
```

**Use case:** See how each treatment progresses over time, identify growth patterns

---

## Updated Breakdown View UI

### View Selector
```
┌────────────────────────────────────────────────────────────────┐
│ « Back to Grid    Trial: Jan-Mar 2025                          │
├────────────────────────────────────────────────────────────────┤
│ View Mode:  ◉ Single Date   ○ Temporal Progression             │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│ [Content changes based on selected mode]                       │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

---

## Mode 1: Single Date View (Detailed)

### Header
```
View Mode: ◉ Single Date   ○ Temporal Progression

Current Date: Jan 8, 2025
◀ Previous Date | Next Date ▶
```

### Layout (10 treatments × 4 replicates = 40 plots)
```
┌────────────────────────────────────────────────────────────────┐
│ CONTROL (4 replicates)                                          │
│ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐           │
│ │          │ │          │ │          │ │          │           │
│ │  Plot    │ │  Plot    │ │  Plot    │ │  Plot    │           │
│ │  Image   │ │  Image   │ │  Image   │ │  Image   │           │
│ │          │ │          │ │          │ │          │           │
│ │ Control  │ │ Control  │ │ Control  │ │ Control  │           │
│ └──────────┘ └──────────┘ └──────────┘ └──────────┘           │
├────────────────────────────────────────────────────────────────┤
│ TREATMENT A (4 replicates)                                      │
│ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐           │
│ │  Plot    │ │  Plot    │ │  Plot    │ │  Plot    │           │
│ │Treatment │ │Treatment │ │Treatment │ │Treatment │           │
│ │    A     │ │    A     │ │    A     │ │    A     │           │
│ └──────────┘ └──────────┘ └──────────┘ └──────────┘           │
├────────────────────────────────────────────────────────────────┤
│ ... (8 more treatments)                                         │
└────────────────────────────────────────────────────────────────┘

Footer: [Screenshot Current Date]
```

**Features:**
- Navigate between dates
- See all treatments at selected date
- 4 plots per treatment (horizontal row)
- Screenshot captures all treatments from one date

---

## Mode 2: Temporal View (Detailed) ⭐

### Header
```
View Mode: ○ Single Date   ◉ Temporal Progression

Showing: 5 dates (Jan 8 → Mar 15)
Filter: ☑ Show all dates  □ Select date range
```

### Layout (Each treatment gets a section showing progression)
```
┌────────────────────────────────────────────────────────────────┐
│ CONTROL - Temporal Progression                                 │
│ ───────────────────────────────────────────────────────────────│
│         Jan 8      Jan 20      Feb 5      Feb 21     Mar 15    │
│ Rep 1: ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐      │
│        │     │→  │     │→  │     │→  │     │→  │     │      │
│        │Plot │   │Plot │   │Plot │   │Plot │   │Plot │      │
│        └─────┘   └─────┘   └─────┘   └─────┘   └─────┘      │
│                                                                 │
│ Rep 2: ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐      │
│        │Plot │→  │Plot │→  │Plot │→  │Plot │→  │Plot │      │
│        └─────┘   └─────┘   └─────┘   └─────┘   └─────┘      │
│                                                                 │
│ Rep 3: ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐      │
│        │Plot │→  │Plot │→  │Plot │→  │Plot │→  │Plot │      │
│        └─────┘   └─────┘   └─────┘   └─────┘   └─────┘      │
│                                                                 │
│ Rep 4: ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐      │
│        │Plot │→  │Plot │→  │Plot │→  │Plot │→  │Plot │      │
│        └─────┘   └─────┘   └─────┘   └─────┘   └─────┘      │
├────────────────────────────────────────────────────────────────┤
│ TREATMENT A - Temporal Progression                              │
│ ───────────────────────────────────────────────────────────────│
│         Jan 8      Jan 20      Feb 5      Feb 21     Mar 15    │
│ Rep 1: ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐      │
│        │Plot │→  │Plot │→  │Plot │→  │Plot │→  │Plot │      │
│        └─────┘   └─────┘   └─────┘   └─────┘   └─────┘      │
│                                                                 │
│ Rep 2: ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐   ┌─────┐      │
│        │Plot │→  │Plot │→  │Plot │→  │Plot │→  │Plot │      │
│        └─────┘   └─────┘   └─────┘   └─────┘   └─────┘      │
│                                                                 │
│ ... (reps 3-4)                                                  │
├────────────────────────────────────────────────────────────────┤
│ TREATMENT B - Temporal Progression                              │
│ ... (reps 1-4 across dates)                                     │
├────────────────────────────────────────────────────────────────┤
│ ... (8 more treatments)                                         │
└────────────────────────────────────────────────────────────────┘

Footer: [Screenshot All Temporal Progressions]
```

**Features:**
- Each treatment section shows all replicates across all dates
- Horizontal timeline for each replicate
- See growth/change patterns
- Screenshot captures entire temporal view (all treatments, all dates)

---

## Alternative Temporal Layout (Compact)

### Option: All Replicates Combined (Average View)
If user doesn't need to see individual replicates in temporal view:

```
┌────────────────────────────────────────────────────────────────┐
│ CONTROL - Temporal Progression (All Replicates)                │
│                                                                 │
│  Jan 8         Jan 20        Feb 5         Feb 21      Mar 15  │
│ ┌────────┐   ┌────────┐   ┌────────┐   ┌────────┐   ┌────────┐│
│ │        │   │        │   │        │   │        │   │        ││
│ │ 4 plots│→  │ 4 plots│→  │ 4 plots│→  │ 4 plots│→  │ 4 plots││
│ │combined│   │combined│   │combined│   │combined│   │combined││
│ │        │   │        │   │        │   │        │   │        ││
│ └────────┘   └────────┘   └────────┘   └────────┘   └────────┘│
├────────────────────────────────────────────────────────────────┤
│ TREATMENT A - Temporal Progression (All Replicates)            │
│  Jan 8         Jan 20        Feb 5         Feb 21      Mar 15  │
│ ┌────────┐   ┌────────┐   ┌────────┐   ┌────────┐   ┌────────┐│
│ │4 plots │→  │4 plots │→  │4 plots │→  │4 plots │→  │4 plots ││
│ └────────┘   └────────┘   └────────┘   └────────┘   └────────┘│
└────────────────────────────────────────────────────────────────┘
```

**Question:** Should temporal view show:
- **Option A:** Individual replicate timelines (4 rows per treatment)
- **Option B:** Combined replicates (1 row per treatment, all 4 plots grouped)
- **Option C:** User choice (toggle between A and B)

---

## Data Structure for Both Views

### Extended Plot Extraction Structure
```javascript
// When extracting plots, create indexed structure
extractedPlots = {
  byDate: {
    "2025-01-08": {
      "Control": [plotImg1, plotImg2, plotImg3, plotImg4],
      "Treatment A": [plotImg1, plotImg2, plotImg3, plotImg4],
      // ... all treatments
    },
    "2025-01-20": { /* same structure */ },
    "2025-02-05": { /* same structure */ },
    // ... all dates
  },

  byTreatment: {
    "Control": {
      rep1: {
        "2025-01-08": plotImg,
        "2025-01-20": plotImg,
        "2025-02-05": plotImg,
        // ... all dates
      },
      rep2: { /* same */ },
      rep3: { /* same */ },
      rep4: { /* same */ }
    },
    "Treatment A": { /* same structure */ },
    // ... all treatments
  }
}
```

---

## Technical Implementation

### Mode Switcher
```javascript
let breakdownViewMode = 'single-date';  // or 'temporal'

function renderBreakdownView() {
  const modeSelector = document.getElementById('breakdownViewMode');
  modeSelector.innerHTML = `
    <label>
      <input type="radio" name="viewMode" value="single-date"
             ${breakdownViewMode === 'single-date' ? 'checked' : ''}>
      Single Date
    </label>
    <label>
      <input type="radio" name="viewMode" value="temporal"
             ${breakdownViewMode === 'temporal' ? 'checked' : ''}>
      Temporal Progression
    </label>
  `;

  modeSelector.addEventListener('change', (e) => {
    breakdownViewMode = e.target.value;
    renderBreakdownContent();
  });

  renderBreakdownContent();
}
```

### Single Date View Renderer
```javascript
function renderSingleDateView(plots, currentDate) {
  const container = document.getElementById('breakdownContent');
  container.innerHTML = '';

  // Header
  const header = document.createElement('div');
  header.className = 'breakdown-header';
  header.innerHTML = `
    <h3>Date: ${formatDate(currentDate)}</h3>
    <div class="date-nav">
      <button onclick="previousDate()">◀ Previous Date</button>
      <button onclick="nextDate()">Next Date ▶</button>
    </div>
  `;
  container.appendChild(header);

  // Group by treatment
  const plotsByTreatment = groupPlotsByTreatment(plots);

  for (const treatment of treatments) {
    const treatmentPlots = plotsByTreatment[treatment.name] || [];
    if (treatmentPlots.length === 0) continue;

    const section = document.createElement('div');
    section.className = 'treatment-section';

    section.innerHTML = `
      <h4>${treatment.name.toUpperCase()} (${treatmentPlots.length} replicates)</h4>
      <div class="plot-row">
        ${treatmentPlots.map(plot => `
          <div class="plot-card">
            <img src="${plot.imageData}" alt="${treatment.name}">
            <div class="plot-label">${treatment.name}</div>
          </div>
        `).join('')}
      </div>
    `;

    container.appendChild(section);
  }
}
```

### Temporal View Renderer
```javascript
function renderTemporalView(plotsByTreatment, dates) {
  const container = document.getElementById('breakdownContent');
  container.innerHTML = '';

  // Header
  const header = document.createElement('div');
  header.className = 'breakdown-header';
  header.innerHTML = `
    <h3>Temporal Progression</h3>
    <p>Showing ${dates.length} dates: ${formatDate(dates[0])} → ${formatDate(dates[dates.length-1])}</p>
  `;
  container.appendChild(header);

  // For each treatment
  for (const treatment of treatments) {
    const section = document.createElement('div');
    section.className = 'temporal-section';

    const treatmentData = plotsByTreatment[treatment.name];
    if (!treatmentData) continue;

    // Section header
    const sectionHeader = document.createElement('h4');
    sectionHeader.textContent = `${treatment.name.toUpperCase()} - Temporal Progression`;
    section.appendChild(sectionHeader);

    // Date headers
    const dateHeaderRow = document.createElement('div');
    dateHeaderRow.className = 'date-header-row';
    dateHeaderRow.innerHTML = `
      <span class="rep-label"></span>
      ${dates.map(d => `<span class="date-label">${formatDate(d)}</span>`).join('')}
    `;
    section.appendChild(dateHeaderRow);

    // For each replicate
    for (let rep = 1; rep <= 4; rep++) {
      const replicateRow = document.createElement('div');
      replicateRow.className = 'temporal-row';

      // Replicate label
      const repLabel = document.createElement('span');
      repLabel.className = 'rep-label';
      repLabel.textContent = `Rep ${rep}:`;
      replicateRow.appendChild(repLabel);

      // Plot images across dates
      for (const date of dates) {
        const plotData = treatmentData[`rep${rep}`]?.[date];

        const plotCard = document.createElement('div');
        plotCard.className = 'temporal-plot-card';

        if (plotData) {
          plotCard.innerHTML = `
            <img src="${plotData.imageData}" alt="${treatment.name} Rep ${rep}">
          `;
        } else {
          plotCard.innerHTML = '<div class="no-data">No data</div>';
        }

        replicateRow.appendChild(plotCard);

        // Add arrow between dates (except last)
        if (dates.indexOf(date) < dates.length - 1) {
          const arrow = document.createElement('span');
          arrow.className = 'temporal-arrow';
          arrow.textContent = '→';
          replicateRow.appendChild(arrow);
        }
      }

      section.appendChild(replicateRow);
    }

    container.appendChild(section);
  }
}
```

### Extract Plots for Both Views
```javascript
async function extractPlotsAllDates() {
  const tab = tabs[currentTabId];
  const fieldMap = tab.fieldMap;

  const plotsByDate = {};
  const plotsByTreatment = {};

  // Initialize treatment structure
  for (const treatment of tab.treatments) {
    plotsByTreatment[treatment.name] = {
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

    plotsByDate[imageRef.date] = {};

    // Organize plots
    for (const plot of plots) {
      // By date
      if (!plotsByDate[imageRef.date][plot.treatmentName]) {
        plotsByDate[imageRef.date][plot.treatmentName] = [];
      }
      plotsByDate[imageRef.date][plot.treatmentName].push(plot);

      // By treatment (assign to replicate number based on cell index order)
      const treatmentPlots = plotsByDate[imageRef.date][plot.treatmentName];
      const repNumber = treatmentPlots.length;  // 1, 2, 3, 4
      plotsByTreatment[plot.treatmentName][`rep${repNumber}`][imageRef.date] = plot;
    }
  }

  return {
    byDate: plotsByDate,
    byTreatment: plotsByTreatment,
    dates: fieldMap.images.map(img => img.date)
  };
}
```

---

## CSS Styling for Temporal View

```css
/* Temporal view specific */
.temporal-section {
  margin-bottom: 48px;
  background: rgba(26, 26, 46, 0.4);
  border-radius: 16px;
  padding: 24px;
}

.date-header-row {
  display: grid;
  grid-template-columns: 80px repeat(auto-fit, minmax(150px, 1fr));
  gap: 8px;
  margin-bottom: 16px;
  font-weight: 600;
  color: #10b981;
}

.temporal-row {
  display: grid;
  grid-template-columns: 80px repeat(auto-fit, minmax(150px, 1fr));
  gap: 8px;
  margin-bottom: 16px;
  align-items: center;
}

.rep-label {
  font-size: 13px;
  color: #94a3b8;
  font-weight: 500;
}

.date-label {
  text-align: center;
  font-size: 12px;
}

.temporal-plot-card {
  background: rgba(26, 26, 46, 0.8);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  padding: 8px;
  text-align: center;
  min-height: 120px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.temporal-plot-card img {
  width: 100%;
  height: auto;
  border-radius: 6px;
}

.temporal-arrow {
  color: #6366f1;
  font-size: 18px;
  padding: 0 4px;
}

.no-data {
  color: #64748b;
  font-size: 11px;
  font-style: italic;
}

/* Single date view */
.plot-row {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
  gap: 16px;
  margin-top: 16px;
}
```

---

## Screenshot Behavior

### Single Date View
- Captures all treatments from selected date
- File name: `breakdown_single_2025-01-08.png`

### Temporal View
- Captures all treatments across all dates
- Likely very tall image (scrollable)
- File name: `breakdown_temporal_all_dates.png`
- May need to use multiple screenshots or PDF export for very long views

---

## User Workflow Examples

### Workflow 1: Compare Treatments at Peak (Single Date View)
1. Navigate to "Feb 21" (peak growth date)
2. Switch to "Single Date" view
3. See all 10 treatments side-by-side
4. Screenshot → Paste into PowerPoint for treatment comparison slide

### Workflow 2: Show Treatment Efficacy Over Time (Temporal View)
1. Switch to "Temporal Progression" view
2. Scroll to "Treatment A" section
3. See 4 replicates progressing from Jan 8 → Mar 15
4. Visually identify when treatment effect became apparent
5. Screenshot Treatment A section → Presentation slide showing efficacy timeline

### Workflow 3: Full Trial Documentation
1. Single Date View: Screenshot each date (5 screenshots)
   - PowerPoint Section 1: "Snapshots at Each Assessment"
2. Temporal View: Screenshot entire view (1 tall screenshot)
   - PowerPoint Section 2: "Complete Trial Progression"

---

## Implementation Priority

### Phase 1: Single Date View (MVP)
- ✓ Required for basic functionality
- ✓ Simpler implementation
- ✓ 1 day development

### Phase 2: Temporal View (Enhancement)
- ⭐ High value for trial analysis
- ⭐ Differentiation feature
- ⭐ 1-2 days development

**Recommendation:** Implement both modes in sequence during Phase 4 (Plot Extraction) of main timeline.

---

## Questions for Confirmation

1. **Temporal View Layout:**
   - **Option A:** Show all 4 replicates separately (4 rows per treatment)
   - **Option B:** Show replicates combined (1 row per treatment, 4 plots grouped at each date)
   - **Preferred:** A or B?

2. **Replicate Ordering:**
   - Should replicate 1 always be the same physical plot across dates?
   - Or just label them 1-4 arbitrarily at each treatment?
   - (Matters for temporal view consistency)

3. **Missing Dates:**
   - If you have 5 images but one treatment wasn't mapped at a certain date, show:
     - **Option A:** "No data" placeholder
     - **Option B:** Skip that cell entirely
     - **Preferred:** A or B?

4. **Screenshot Size:**
   - Temporal view might be very tall (10 treatments × 4 reps = 40 rows)
   - Should we:
     - **Option A:** Single tall image (might be 5000px+ height)
     - **Option B:** Option to screenshot individual treatment sections
     - **Option C:** Export as PDF instead of PNG for long views
     - **Preferred?**

Let me know your preferences and I'll update the final plan!
