# WFP Schedule Planner - New Event Types Implementation Plan

## Overview
This document tracks the implementation of new event types and related functionality in the WFP Schedule Planner application.

## Implementation Phases

### Phase 1: Core Logic Implementation
- [x] 1.1 Update `calculateDatesForPeriod` function
  - [x] Add calculations for new event types
  - [x] Implement date adjustments and working day logic
  - [x] Add error handling and validation (Initial implementation for calculations done)

- [ ] 1.2 Add helper functions
  - [ ] Create `applyNoOverlapRule` function
  - [ ] Add any additional date utility functions if needed (Utilized existing functions)

### Phase 2: Settings Integration
- [x] 2.1 Update `appSettings` defaults
  - [x] Add default values for new event type offsets
  - [x] Add default visibility settings
  - [ ] Add color settings for new event types

- [ ] 2.2 Update Settings UI
  - [ ] Add input fields for new offset values
  - [ ] Add color pickers for new event types
  - [ ] Add toggles for visibility settings

### Phase 3: Calendar and Display Updates
- [ ] 3.1 Update Calendar Rendering
  - [ ] Add new event types to calendar view
  - [ ] Apply appropriate styling and colors
  - [ ] Update tooltips and event displays

- [ ] 3.2 Update Results Table
  - [ ] Add columns for new event dates
  - [ ] Ensure proper date formatting
  - [ ] Update sorting functionality

### Phase 4: Testing and Validation
- [ ] 4.1 Unit Testing
  - [ ] Test each new date calculation in isolation
  - [ ] Verify "No Overlap" rule behavior
  - [ ] Test edge cases (month boundaries, weekends, holidays)

- [ ] 4.2 Integration Testing
  - [ ] Test with existing bid periods
  - [ ] Verify calendar display
  - [ ] Check persistence of settings

- [ ] 4.3 User Acceptance Testing
  - [ ] Verify all calculations match business rules
  - [ ] Check UI/UX for new elements
  - [ ] Test on different screen sizes

### Phase 5: Documentation and Cleanup
- [ ] 5.1 Update Documentation
  - [ ] Add comments to new code
  - [ ] Update user documentation
  - [ ] Document new settings and their effects

- [ ] 5.2 Code Review and Cleanup
  - [ ] Remove debug code
  - [ ] Optimize performance if needed
  - [ ] Ensure consistent code style

## New Event Types and Logic

### 1. Training Handover (Tech & Cabin Crew)
- **Logic**: `Final SSIM Handover Date + appSettings.trainingHandoverOffset calendar days` (Default offset: 7)
- **Month Label**: Next Month (M%) (UI/Display concern)
- **Color Group**: Training Handover

### 2. CB Pairing Publish (Tech Crew)
- **Logic**: `Bidding Closure Date (Tech Crew) + appSettings.cbPairingPublishTechOffset working days` (Default offset: -5)
- **Month Label**: Next Month (M%) (UI/Display concern)
- **Color Group**: CB Pairing Publish

### 3. CB Pairing Publish (Cabin Crew)
- **Logic**: `Bidding Closure Date (Cabin Crew) + appSettings.cbPairingPublishCabinOffset working days` (Default offset: -5)
- **Month Label**: Next Month (M%) (UI/Display concern)
- **Color Group**: CB Pairing Publish

### 4. Bidding Closure (Tech Crew)
- **Logic**: `Roster Build Commence Date (Tech Crew) + appSettings.biddingClosureTechOffset calendar days` (Default offset: -5)
- **Month Label**: Next Month (M%) (UI/Display concern)
- **Color Group**: Bidding Closure

### 5. Bidding Closure (Cabin Crew)
- **Logic**: `Roster Build Commence Date (Cabin Crew) + appSettings.biddingClosureCabinOffset calendar days` (Default offset: -3)
- **Month Label**: No "Next Month" labeling (UI/Display concern)
- **Color Group**: Bidding Closure

### 6. Roster Build Commence (Tech Crew)
- **Logic**: `Tech Crew Publish Date + appSettings.rosterBuildCommenceTechOffset calendar days` (Default offset: -5)
- **Month Label**: Next Month (M%) (UI/Display concern)
- **Color Group**: Roster Build Commence

### 7. Roster Build Commence (Cabin Crew)
- **Logic**: `Cabin Crew Publish Date + appSettings.rosterBuildCommenceCabinOffset calendar days` (Default offset: -5)
- **Month Label**: Next Month (M%) (UI/Display concern)
- **Color Group**: Roster Build Commence

## Settings Configuration

### App Settings Updates
```javascript
{
  // Event Offsets (from initializeAppSettings)
  trainingHandoverOffset: 7,          // Calendar days from Final SSIM Handover
  cbPairingPublishTechOffset: -5,     // Working days from Bidding Closure Tech
  cbPairingPublishCabinOffset: -5,    // Working days from Bidding Closure Cabin
  biddingClosureTechOffset: -5,       // Calendar days from Roster Build Commence Tech
  biddingClosureCabinOffset: -3,      // Calendar days from Roster Build Commence Cabin
  rosterBuildCommenceTechOffset: -5,  // Calendar days from Tech Crew Publish
  rosterBuildCommenceCabinOffset: -5, // Calendar days from Cabin Crew Publish

  // Event Visibility (from initializeAppSettings)
  showTrainingHandover: true,
  showCbPairingPublishTech: true,
  showCbPairingPublishCabin: true,
  showBiddingClosureTech: true,
  showBiddingClosureCabin: true,
  showRosterBuildCommenceTech: true,
  showRosterBuildCommenceCabin: true
  // Note: Other existing settings from the application are not listed here for brevity.
}
```

### Color Groups
1. Training Handover
2. CB Pairing Publish (Tech & Cabin share 1 color)
3. Bidding Closure (Tech & Cabin share 1 color)
4. Roster Build Commence (Tech & Cabin share 1 color)

## Notes
- All dates should be normalized to UTC midnight
- Working days exclude weekends and public holidays
- The "No Overlap" rule ensures significant events are at least one working day apart
