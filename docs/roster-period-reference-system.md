# Roster Period Reference System

This document describes how the roster period reference data is built and managed in the SSIM Timeline Planning application.

---

## Overview

The Roster Period Reference system provides a configurable framework for defining when each monthly roster period starts and ends. This data is displayed in the Settings page under the "Roster Definitions" tab and is used throughout the application when creating new bid periods.

---

## 1. Month Definitions Structure

### Location
`planning-dates-calculator.html` - Line 1571

### Data Structure
The `standardRosterPeriodDefinitions` array contains 12 objects (one per month):

```javascript
const standardRosterPeriodDefinitions = [
    { period: "Jan", startRule: (year) => `${year}-01-01`, endRule: (year) => `${year}-01-30`, days: "30" },
    { period: "Feb", startRule: (year) => `${year}-01-31`, endRule: (year) => `${year}-03-01`, days: (year) => new Date(year, 1, 29).getDate() === 29 ? "30/31 (leap)" : "30" },
    { period: "Mar", startRule: (year) => `${year}-03-02`, endRule: (year) => `${year}-03-31`, days: "30" },
    { period: "Apr", startRule: (year) => `${year}-04-01`, endRule: (year) => `${year}-04-30`, days: "30" },
    { period: "May", startRule: (year) => `${year}-05-01`, endRule: (year) => `${year}-05-31`, days: "31" },
    { period: "Jun", startRule: (year) => `${year}-06-01`, endRule: (year) => `${year}-06-30`, days: "30" },
    { period: "Jul", startRule: (year) => `${year}-07-01`, endRule: (year) => `${year}-07-31`, days: "31" },
    { period: "Aug", startRule: (year) => `${year}-08-01`, endRule: (year) => `${year}-08-31`, days: "31" },
    { period: "Sep", startRule: (year) => `${year}-09-01`, endRule: (year) => `${year}-09-30`, days: "30" },
    { period: "Oct", startRule: (year) => `${year}-10-01`, endRule: (year) => `${year}-10-31`, days: "31" },
    { period: "Nov", startRule: (year) => `${year}-11-01`, endRule: (year) => `${year}-11-30`, days: "30" },
    { period: "Dec", startRule: (year) => `${year}-12-01`, endRule: (year) => `${year}-12-31`, days: "31" }
];
```

### Property Definitions

| Property | Type | Description |
|----------|------|-------------|
| `period` | String | 3-letter month abbreviation ("Jan", "Feb", etc.) |
| `startRule` | Function | Takes `year` as parameter, returns start date as `YYYY-MM-DD` string |
| `endRule` | Function | Takes `year` as parameter, returns end date as `YYYY-MM-DD` string |
| `days` | String or Function | Number of days in the period (can be dynamic for leap years) |

### Why Functions?

The `startRule` and `endRule` are implemented as functions rather than static strings because:

1. **Year flexibility** - The same definition works for any year (2024, 2025, 2026, etc.)
2. **Leap year handling** - February's `days` property uses a function to detect leap years:
   ```javascript
   days: (year) => new Date(year, 1, 29).getDate() === 29 ? "30/31 (leap)" : "30"
   ```

---

## 2. Roster Period Boundaries

The following table shows the actual start and end dates for each roster period:

| Period | Start Date | End Date | Days | Notes |
|--------|------------|----------|------|-------|
| **Jan** | January 1 | January 30 | 30 | Starts on 1st of the month |
| **Feb** | January 31 | March 1 | 30/31 | Crosses month boundary; extra day in leap years |
| **Mar** | March 2 | March 31 | 30 | Starts on 2nd (immediately after Feb ends) |
| **Apr** | April 1 | April 30 | 30 | Standard calendar month |
| **May** | May 1 | May 31 | 31 | Standard calendar month |
| **Jun** | June 1 | June 30 | 30 | Standard calendar month |
| **Jul** | July 1 | July 31 | 31 | Standard calendar month |
| **Aug** | August 1 | August 31 | 31 | Standard calendar month |
| **Sep** | September 1 | September 30 | 30 | Standard calendar month |
| **Oct** | October 1 | October 31 | 31 | Standard calendar month |
| **Nov** | November 1 | November 30 | 30 | Standard calendar month |
| **Dec** | December 1 | December 31 | 31 | Standard calendar month |

### Important Notes

- **January, February, and March** have non-standard boundaries to ensure continuous coverage without gaps
- **February** is unique as it spans from January 31 to March 1, accommodating the variable length of the calendar month
- **April through December** follow standard calendar month boundaries

---

## 3. Reference Table Builder

### State Management

```javascript
// Line 1386
let rosterReferenceCurrentYear = new Date().getFullYear();
```

This variable tracks which year is currently being displayed in the reference table.

### Builder Function

**Location:** Lines 2704-2720

```javascript
function populateRosterPeriodsReference(yearToDisplay = new Date().getFullYear()) {
    const rosterBody = document.getElementById('rosterPeriodsReferenceBody');
    const yearDisplaySpan = document.getElementById('rosterReferenceYearDisplay');
    if (!rosterBody || !yearDisplaySpan) return;

    rosterBody.innerHTML = '';  // Clear existing rows
    yearDisplaySpan.textContent = yearToDisplay;  // Update year display

    standardRosterPeriodDefinitions.forEach(def => {
        const row = rosterBody.insertRow();
        row.insertCell().textContent = def.period;
        row.insertCell().textContent = formatDateForInput(parseDate(def.startRule(yearToDisplay)));
        row.insertCell().textContent = formatDateForInput(parseDate(def.endRule(yearToDisplay)));
        row.insertCell().textContent = typeof def.days === 'function'
            ? def.days(yearToDisplay)
            : def.days;
        Array.from(row.cells).forEach(cell => cell.className = 'px-2 py-2');
    });
}
```

### Year Navigation

**Location:** Lines 2722-2725

```javascript
function changeRosterReferenceYear(offset) {
    rosterReferenceCurrentYear += offset;
    populateRosterPeriodsReference(rosterReferenceCurrentYear);
}
```

The user can navigate between years using arrow buttons that call this function with `+1` or `-1`.

---

## 4. When the Table is Populated

The reference table is populated in the following scenarios:

| Trigger | Location | Description |
|---------|----------|-------------|
| Settings page initialization | Line 3155 | Called during `initializeSettingsPage()` |
| Tab selection | Line 4049 | Called when user clicks "Roster Definitions" tab |
| Year navigation | Line 2724 | Called when user clicks ← or → buttons |

---

## 5. Integration with Period Creation

### Lookup Function

**Location:** Lines 1813-1830

```javascript
function getStandardRosterDates(bidMonthName, bidYear) {
    const monthIdx = monthMap[bidMonthName];
    if (monthIdx === undefined) {
        console.error("Invalid bidMonthName passed to getStandardRosterDates:", bidMonthName);
        return null;
    }

    const definition = standardRosterPeriodDefinitions.find(
        def => def.period.toLowerCase() === bidMonthName.toLowerCase()
    );
    if (!definition) {
        console.error("No roster definition found for:", bidMonthName);
        return null;
    }

    return {
        startDateStr: definition.startRule(bidYear),
        endDateStr: definition.endRule(bidYear)
    };
}
```

### Usage Points

This function is called when:

1. **Auto-adding next period** - `autoAddAndCalculateNextBidPeriod()` (line 1994)
2. **Manual period creation** - `addBidPeriodFromForm()` (line 2134)
3. **Auto-generating missing periods** - `createPeriodFromDefinition()` (line 4350)

---

## 6. Data Flow Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                    User Opens Settings Page                      │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│              Clicks "Roster Definitions" Tab                     │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│         populateRosterPeriodsReference(currentYear)              │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│           Loop through standardRosterPeriodDefinitions           │
│                                                                  │
│   For each month definition:                                     │
│   ├── Execute startRule(year) → "2025-01-01"                    │
│   ├── Execute endRule(year) → "2025-01-30"                      │
│   ├── Evaluate days (string or function)                        │
│   └── Insert row into HTML table                                │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Table Rendered in UI                          │
│                                                                  │
│   ┌──────────┬────────────┬────────────┬──────┐                 │
│   │ Period   │ Start Date │ End Date   │ Days │                 │
│   ├──────────┼────────────┼────────────┼──────┤                 │
│   │ Jan      │ 01/01/2025 │ 30/01/2025 │ 30   │                 │
│   │ Feb      │ 31/01/2025 │ 01/03/2025 │ 30   │                 │
│   │ ...      │ ...        │ ...        │ ...  │                 │
│   └──────────┴────────────┴────────────┴──────┘                 │
└─────────────────────────────────────────────────────────────────┘
                                │
                    ┌───────────┴───────────┐
                    ▼                       ▼
        ┌───────────────────┐   ┌───────────────────┐
        │  User clicks ←    │   │  User clicks →    │
        └───────────────────┘   └───────────────────┘
                    │                       │
                    ▼                       ▼
        ┌───────────────────────────────────────────┐
        │   changeRosterReferenceYear(offset)       │
        │   rosterReferenceCurrentYear += offset    │
        │   populateRosterPeriodsReference(year)    │
        └───────────────────────────────────────────┘
```

---

## 7. HTML Structure

The reference table is rendered in the Settings page:

```html
<div id="rosterDefinitions" class="tab-content">
    <div class="settings-section">
        <div class="flex justify-between items-center mb-3">
            <h3 class="element-title">Standard Roster Periods</h3>
            <div class="flex items-center space-x-2">
                <button onclick="changeRosterReferenceYear(-1)">←</button>
                <span id="rosterReferenceYearDisplay"></span>
                <button onclick="changeRosterReferenceYear(1)">→</button>
            </div>
        </div>
        <table>
            <thead>
                <tr>
                    <th>Roster Period</th>
                    <th>Start Date</th>
                    <th>End Date</th>
                    <th>Days</th>
                </tr>
            </thead>
            <tbody id="rosterPeriodsReferenceBody">
                <!-- Rows populated dynamically -->
            </tbody>
        </table>
    </div>
</div>
```

---

## 8. Customization

To modify roster period boundaries, edit the `standardRosterPeriodDefinitions` array at line 1571. Each definition follows this pattern:

```javascript
{
    period: "MonthAbbr",                           // 3-letter abbreviation
    startRule: (year) => `${year}-MM-DD`,          // Start date rule
    endRule: (year) => `${year}-MM-DD`,            // End date rule
    days: "N" or (year) => calculateDays(year)    // Day count
}
```

### Example: Changing January to End on the 31st

```javascript
{ period: "Jan", startRule: (year) => `${year}-01-01`, endRule: (year) => `${year}-01-31`, days: "31" }
```

> **Note:** Modifying one period's boundaries may require adjusting adjacent periods to maintain continuous coverage.

---

## Summary

The Roster Period Reference system is a dynamic, year-agnostic framework that:

1. **Defines** roster period boundaries using rule functions
2. **Displays** the computed dates in a navigable reference table
3. **Provides** lookup functionality for period creation workflows
4. **Handles** edge cases like leap years automatically

The system ensures consistency across the application by centralizing all roster period definitions in a single data structure.
