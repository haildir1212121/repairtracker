# Fleet Repair Wallboard - Testing Plan

## Overview

This document outlines the complete testing strategy for the Fleet Repair Wallboard dashboard, covering functional testing, integration testing, performance testing, and user acceptance testing.

---

## Pre-Testing Checklist

- [ ] wallboard.html is accessible at the correct URL
- [ ] Firebase Firestore database contains test vehicle data
- [ ] Users can authenticate with Firebase
- [ ] beta.html is accessible for creating test data
- [ ] Test devices/browsers are available
- [ ] Network connection is stable
- [ ] Browser developer tools are accessible

---

## Test Environment Setup

### Test Data Preparation

Create test vehicles in beta.html with various statuses:

```
Vehicle ID    | Status      | Priority | Days | Issue
─────────────────────────────────────────────────────────────
DLX-TEST-001  | Down        | Urgent   | 12   | Transmission slip
DLX-TEST-002  | Down        | High     | 7    | Engine overheating
DLX-TEST-003  | Warning     | High     | 3    | Oil level low
DLX-TEST-004  | Operational | Normal   | 1    | Routine service
DLX-TEST-005  | Down        | Maint    | 5    | Scheduled inspection
```

### Test Browsers

| Browser | Version | Desktop | Tablet | Mobile |
|---------|---------|---------|--------|--------|
| Chrome  | Latest  | ✓       | ✓      | ✓      |
| Firefox | Latest  | ✓       | ✓      | ✓      |
| Safari  | Latest  | ✓       | ✓      | ✓      |
| Edge    | Latest  | ✓       | ✓      | -      |

### Test Devices

- Desktop (1920x1080, 2560x1440)
- Tablet (iPad, Samsung Tab)
- Large Display (TV or monitor >40")
- Mobile (iPhone, Android)

---

## Functional Testing

### Test 1: Initial Load & Authentication

**Steps:**
1. Open wallboard.html in browser
2. Observe authentication redirect
3. Log in with valid credentials
4. Verify wallboard loads after auth

**Expected Results:**
- ✓ Redirects to login if not authenticated
- ✓ Accepts valid email/password
- ✓ Loads wallboard.html after authentication
- ✓ Shows "Connecting..." status initially
- ✓ Shows "Live" status within 10 seconds
- ✓ Fleet data populated with vehicles

**Fail Criteria:**
- ✗ Blank page or unresponsive loading
- ✗ Login error with valid credentials
- ✗ Connection status shows error after 30 seconds
- ✗ No vehicles displayed despite database having data

---

### Test 2: Vehicle Grid Display

**Steps:**
1. On loaded wallboard, observe the vehicle grid
2. Count visible vehicles
3. Check vehicle card layout
4. Verify all expected fields are visible

**Expected Results:**
- ✓ Grid displays all vehicles from database
- ✓ Cards arranged in responsive grid (4-5 columns on desktop)
- ✓ Each card shows: ID, Status, Shop Status
- ✓ Default visible fields: Issue, Location, Parts, Days
- ✓ Card height consistent
- ✓ Proper spacing and padding

**Fail Criteria:**
- ✗ Vehicles missing from display
- ✗ Cards overlapping or misaligned
- ✗ Text truncation without ellipsis
- ✗ Inconsistent card sizing

**Test Data Validation:**
```javascript
// Run in browser console
console.log('Total vehicles:', fleetData.length);
console.log('Grid items:', document.querySelectorAll('.vehicle-card').length);
console.log('Match:', fleetData.length === document.querySelectorAll('.vehicle-card').length);
```

---

### Test 3: Status Color Coding

**Steps:**
1. Observe cards for different statuses
2. Check operational vehicles (should have green tint)
3. Check warning vehicles (should have orange tint)
4. Check down vehicles (should have red tint)

**Expected Results:**
- ✓ Operational: Green gradient border, subtle glow on hover
- ✓ Warning: Orange gradient border, visible glow on hover
- ✓ Down: Red gradient border, strong glow on hover
- ✓ Status dot color matches border
- ✓ Colors visible from typical TV viewing distance

**Fail Criteria:**
- ✗ Colors indistinguishable at viewing distance
- ✗ Wrong color assigned to status
- ✗ No visual indicator for status

---

### Test 4: Priority Badges

**Steps:**
1. Find vehicles with different priorities
2. Verify badge appears on card
3. Check badge color matches priority
4. Verify text is readable

**Expected Results:**
- ✓ Urgent: Red badge, all urgent vehicles marked
- ✓ High: Orange badge, all high priority marked
- ✓ Maintenance: Purple badge
- ✓ Normal: No badge (or minimal indicator)
- ✓ Badge text uppercase and legible

**Fail Criteria:**
- ✗ Wrong badge color for priority
- ✗ Badge missing on high-priority vehicles
- ✗ Badge text too small to read

---

### Test 5: Days-in-Shop Calculation

**Steps:**
1. Create test vehicle with known date (e.g., 5 days ago)
2. Verify days displayed on card
3. Check warning color triggers at 7 days
4. Check critical color triggers at 14 days

**Expected Results:**
- ✓ Calculated days matches expected (allow ±1 day for timezone)
- ✓ 0-6 days: Normal gray badge
- ✓ 7-13 days: Orange warning badge
- ✓ 14+ days: Red critical badge
- ✓ Badge displays prominently at bottom of card

**Fail Criteria:**
- ✗ Days calculation off by >2 days
- ✗ Warning threshold not triggering at 7 days
- ✗ Critical threshold not triggering at 14 days
- ✗ Days not displayed when issue is recent

---

### Test 6: Display Preferences

**Settings Tests:**

#### Test 6a: Field Visibility Toggles

**Steps:**
1. Open Settings panel (click ⚙️)
2. Uncheck "Current Issue" checkbox
3. Verify issue sections disappear from all cards
4. Check "Current Issue" again
5. Verify issue sections reappear
6. Repeat for other field checkboxes

**Expected Results:**
- ✓ Settings panel opens smoothly
- ✓ Each checkbox toggle immediately affects cards
- ✓ Changes persist across page reload

**Fail Criteria:**
- ✗ Settings panel doesn't open
- ✗ Checkbox changes don't affect display
- ✗ Changes don't persist after reload

#### Test 6b: Filter Toggles

**Steps:**
1. Open Settings panel
2. Uncheck "Down for Repair"
3. Verify down status vehicles disappear
4. Check "Down for Repair" again
5. Verify vehicles reappear

**Expected Results:**
- ✓ Filter applied immediately
- ✓ Correct vehicles hidden/shown
- ✓ Other filters work independently
- ✓ Multiple filters can be active simultaneously

**Fail Criteria:**
- ✗ Filter doesn't apply
- ✗ Wrong vehicles hidden
- ✗ Filters interfere with each other

#### Test 6c: Preferences Persistence

**Steps:**
1. Open Settings and customize field visibility
2. Close browser tab completely
3. Open wallboard.html again
4. Verify same preferences are active

**Expected Results:**
- ✓ Preferences load from localStorage
- ✓ Field visibility matches previous session
- ✓ Filter selections match previous session

**Fail Criteria:**
- ✗ Preferences reset on page reload
- ✗ Some preferences don't persist
- ✗ Settings revert to defaults

---

### Test 7: Theme Toggle

**Steps:**
1. Click theme button (🌙 icon in header)
2. Verify theme switches to light mode
3. Check all text remains readable
4. Check all colors remain distinguishable
5. Click theme button again to return to dark
6. Close and reopen wallboard
7. Verify theme persisted

**Expected Results:**
- ✓ Icon changes to ☀️ in light mode, 🌙 in dark mode
- ✓ All backgrounds switch colors appropriately
- ✓ Text contrast ratio ≥ 4.5:1 (WCAG AA)
- ✓ Card colors remain distinguishable
- ✓ Theme persists across sessions

**Fail Criteria:**
- ✗ Text becomes unreadable in light mode
- ✗ Colors lose distinction
- ✗ Icon doesn't change
- ✗ Theme resets on reload

---

### Test 8: Fullscreen Mode

**Steps:**
1. Click Fullscreen button in header
2. Verify header disappears
3. Verify grid expands to fill screen
4. Press F key to verify it toggles fullscreen
5. Press ESC to exit fullscreen
6. Verify header returns

**Expected Results:**
- ✓ Header hidden in fullscreen mode
- ✓ Grid maximizes to use full screen
- ✓ F key toggles fullscreen
- ✓ ESC key exits fullscreen
- ✓ Content remains fully functional in fullscreen
- ✓ Cards still hover/interact in fullscreen
- ✓ Settings panel still accessible (ESC focused)

**Fail Criteria:**
- ✗ Header still visible in fullscreen
- ✗ Grid doesn't expand properly
- ✗ F key doesn't work
- ✗ Content cut off in fullscreen
- ✗ Cards unresponsive in fullscreen

**Browser Fullscreen API Note:**
- Some browsers require user gesture to enter fullscreen
- May show "Press F11 to exit" indicator
- CSS-based fullscreen always works as fallback

---

### Test 9: Real-Time Updates

**Steps:**
1. Open wallboard.html in Tab A
2. Open beta.html in Tab B
3. In Tab B, create a new vehicle
4. In Tab A, observe if new vehicle appears
5. In Tab B, change a vehicle's status
6. In Tab A, verify status color change appears
7. In Tab B, change a vehicle's priority
8. In Tab A, verify priority badge updates

**Expected Results:**
- ✓ New vehicle appears within 2 seconds
- ✓ Status changes reflected within 2 seconds
- ✓ Priority badge updates without page refresh
- ✓ No console errors during updates
- ✓ Connection status remains "Live"

**Fail Criteria:**
- ✗ Changes don't appear without manual refresh
- ✗ Updates take >5 seconds
- ✗ Connection status shows error
- ✗ Console shows Firebase errors
- ✗ Wallboard reverts to "Connecting..."

**Note:** Use browser DevTools to watch Network tab during updates (should see WebSocket messages to firebase).

---

### Test 10: Sorting Order

**Steps:**
1. Observe vehicle card order on wallboard
2. Check that Urgent priority vehicles appear first
3. Check High priority after Urgent
4. Check that within same priority, older repairs (more days) appear first

**Expected Results:**
- ✓ Top-left card is Urgent priority
- ✓ Urgent vehicles sorted by days (oldest first)
- ✓ High priority vehicles after all Urgent
- ✓ Within High, sorted by days
- ✓ Consistent order across refreshes

**Fail Criteria:**
- ✗ Urgent vehicles not at top
- ✗ Random or inconsistent order
- ✗ Days sorting ignored

---

### Test 11: Responsive Layout

**Tests at Different Resolutions:**

#### Desktop (1920x1080)
- [ ] 5-6 columns visible
- [ ] No horizontal scroll
- [ ] All text readable at arm's length

#### Tablet (1024x768)
- [ ] 3-4 columns visible
- [ ] Grid reflows smoothly
- [ ] Touch interactions work

#### Large Display (2560x1440)
- [ ] 6-7 columns visible
- [ ] Large fonts appropriate for distance
- [ ] No wasted whitespace

#### Mobile (375x667)
- [ ] 2 columns visible
- [ ] No horizontal scroll
- [ ] Touch friendly spacing

**Expected Results:**
- ✓ Grid adapts to all resolutions
- ✓ No text overflow
- ✓ No horizontal scrolling on any resolution
- ✓ Cards maintain aspect ratio
- ✓ Touch targets ≥44px for mobile

**Fail Criteria:**
- ✗ Horizontal scrollbar appears
- ✗ Text overflows or truncates improperly
- ✗ Layout breaks on any tested resolution
- ✗ Touch targets too small on mobile

---

## Integration Testing

### Test 12: Beta.html Integration

**Setup:**
- Open beta.html in one window
- Open wallboard.html in another
- Position side-by-side

**Test Steps:**

#### 12a: Create Vehicle in beta.html
1. Click "Add Unit" in beta.html
2. Fill in form: ID=TEST-INT-001, Status=Down, Priority=Urgent
3. Save the ticket
4. Observe wallboard.html

**Expected Results:**
- ✓ New vehicle appears in wallboard within 2 seconds
- ✓ Correct status color (red for Down)
- ✓ Correct priority badge (Urgent/red)
- ✓ No page refresh needed

#### 12b: Update Status in beta.html
1. In beta.html, click on the vehicle
2. Change Shop Status to "Ready for Pickup"
3. Save
4. Observe wallboard.html

**Expected Results:**
- ✓ Shop Status updates in wallboard card
- ✓ Card color may change if status affects it
- ✓ Update appears within 2 seconds

#### 12c: Update Priority in beta.html
1. In beta.html, change Priority to "Normal"
2. Save
3. Observe wallboard.html

**Expected Results:**
- ✓ Priority badge disappears (Normal priority shows no badge)
- ✓ Card position may change if resorting
- ✓ Update appears within 2 seconds

#### 12d: Add Parts Information
1. In beta.html, change status to "Awaiting part order"
2. Fill in Parts Ordered date and Parts ETA
3. Save
4. Observe wallboard.html

**Expected Results:**
- ✓ Parts status displays on card
- ✓ Shows "On order (ETA date)"
- ✓ Updated within 2 seconds

---

### Test 13: Cross-Browser Compatibility

**Test on Each Browser:**

| Feature | Chrome | Firefox | Safari | Edge |
|---------|--------|---------|--------|------|
| Initial Load | [ ] | [ ] | [ ] | [ ] |
| Auth Flow | [ ] | [ ] | [ ] | [ ] |
| Grid Display | [ ] | [ ] | [ ] | [ ] |
| Color Coding | [ ] | [ ] | [ ] | [ ] |
| Settings Panel | [ ] | [ ] | [ ] | [ ] |
| Theme Toggle | [ ] | [ ] | [ ] | [ ] |
| Fullscreen | [ ] | [ ] | [ ] | [ ] |
| Real-time Updates | [ ] | [ ] | [ ] | [ ] |
| Responsive Layout | [ ] | [ ] | [ ] | [ ] |

**Expected Results:**
- ✓ All features work identically across all browsers
- ✓ No console errors in any browser
- ✓ Performance consistent across browsers

**Fail Criteria:**
- ✗ Feature works on Chrome but not Firefox
- ✗ Styling different between browsers
- ✗ Console errors in any browser

---

## Performance Testing

### Test 14: Load Time

**Steps:**
1. Open wallboard.html
2. Measure time from page load to "Live" status
3. Measure time from page load to first card visible
4. Record CPU usage and memory usage

**Expected Results:**
- ✓ First paint: <2 seconds
- ✓ Interactive (Live status): <5 seconds
- ✓ All vehicles rendered: <10 seconds
- ✓ CPU usage: <20% during load
- ✓ Memory usage: <100MB

**Fail Criteria:**
- ✗ Load time >15 seconds
- ✗ CPU spikes >80%
- ✗ Memory usage >200MB
- ✗ Browser becomes unresponsive

---

### Test 15: Update Performance

**Steps:**
1. Open wallboard with 20+ vehicles visible
2. In beta.html, make a change to a vehicle
3. Measure time from change to visible update in wallboard
4. Monitor for lag or stuttering

**Expected Results:**
- ✓ Update appears within 1-2 seconds
- ✓ No page flicker or reload
- ✓ No "janky" animations
- ✓ Smooth 60fps transitions

**Fail Criteria:**
- ✗ Update takes >5 seconds
- ✗ Visible lag or stuttering
- ✗ Frame rate drops below 30fps
- ✗ Page appears to freeze during update

---

### Test 16: Memory Leak Detection

**Steps:**
1. Open wallboard.html
2. In DevTools Memory tab, take heap snapshot (baseline)
3. Keep wallboard open for 30 minutes
4. Make frequent changes in beta.html (every 30 seconds)
5. Check memory usage periodically

**Expected Results:**
- ✓ Memory usage stable (±10MB variation)
- ✓ Heap size doesn't grow continuously
- ✓ No console errors or warnings
- ✓ Connection remains "Live"

**Fail Criteria:**
- ✗ Memory continuously increases
- ✗ Heap size doubles during test
- ✗ Console errors appear
- ✗ Browser becomes slow/unresponsive

---

## User Acceptance Testing

### Test 17: Shop Staff Usability

**Target Users:** Technicians and supervisors (non-technical)

**Test Scenario:**
"You're a shop supervisor. You need to quickly see which vehicles are down and how long they've been in the shop."

**Steps:**
1. Open wallboard.html
2. Identify all Down status vehicles
3. Find the oldest repair (most days)
4. Identify all Urgent priority repairs
5. Set the display to show only Down vehicles

**Expected Results:**
- ✓ User can find all Down vehicles within 10 seconds
- ✓ Days-in-shop number is clearly visible
- ✓ Red color makes urgent items stand out
- ✓ Filtering settings are intuitive
- ✓ User doesn't need to consult documentation

**Fail Criteria:**
- ✗ User confused about what they're looking at
- ✗ Takes >30 seconds to find Down vehicles
- ✗ Can't identify days-in-shop without asking
- ✗ Settings terminology is confusing

---

### Test 18: Multi-Location Usage

**Test Scenario:**
"Our shop has 3 different bays. We want to put the wallboard on a display in each area."

**Steps:**
1. Open wallboard.html on Monitor 1 (display)
2. Make changes in beta.html for vehicles
3. Observe both monitors show updates simultaneously

**Expected Results:**
- ✓ Multiple instances sync in real-time
- ✓ Each display is independent (can customize separately)
- ✓ No conflicts or synchronization issues
- ✓ Settings don't override each other

**Fail Criteria:**
- ✗ Updates don't appear on one display
- ✗ Settings on one display affect another
- ✗ Connection drops on any display

---

## Accessibility Testing

### Test 19: Color Contrast

**Steps:**
1. Use WebAIM Contrast Checker (https://webaim.org/resources/contrastchecker/)
2. Check each color combination:
   - Text on card background
   - Status dots
   - Priority badges
   - Warning colors

**Expected Results:**
- ✓ All text: Contrast ratio ≥ 4.5:1 (WCAG AA)
- ✓ Decorative elements: Contrast ratio ≥ 3:1 (WCAG AA)
- ✓ Dark mode and light mode both pass

**Fail Criteria:**
- ✗ Any contrast ratio <3:1
- ✗ Gray text on gray background
- ✗ Light yellow on white

---

### Test 20: Keyboard Navigation

**Steps:**
1. Use Tab key to navigate through all interactive elements
2. Verify focus indicator is visible
3. Test all keyboard shortcuts:
   - F: Fullscreen toggle
   - ESC: Close settings
4. Verify form inputs are accessible

**Expected Results:**
- ✓ Tab cycles through buttons
- ✓ Focus indicator clearly visible
- ✓ Settings button accessible via Tab
- ✓ Checkboxes focusable and toggleable with Space
- ✓ All shortcuts work as documented

**Fail Criteria:**
- ✗ Elements skipped by Tab
- ✗ No focus indicator
- ✗ Keyboard shortcuts don't work
- ✗ Hidden elements are focusable

---

## Edge Case Testing

### Test 21: Missing Data

**Test Cases:**

#### 21a: Vehicle with no issue description
1. Create vehicle in beta.html with empty issue field
2. Observe wallboard

**Expected:** Card displays without issue section, no errors

#### 21b: Vehicle with no location
1. Create vehicle with empty location field

**Expected:** Location row not displayed, card still shows properly

#### 21c: Vehicle with no date (days calculation impossible)
1. Create vehicle with empty date field

**Expected:** Days-in-shop not displayed, card shows other info

**Fail Criteria:**
- ✗ Card layout breaks with missing data
- ✗ Console shows errors
- ✗ Empty fields display as "null" or "undefined"

---

### Test 22: Large Data Sets

**Steps:**
1. Simulate large database by creating 100+ vehicles in test Firebase project
2. Open wallboard.html
3. Measure load time
4. Scroll through all vehicles
5. Verify all vehicles eventually visible

**Expected Results:**
- ✓ Load time <10 seconds
- ✓ Scrolling is smooth
- ✓ All vehicles accessible
- ✓ No console memory warnings

**Fail Criteria:**
- ✗ Load time >30 seconds
- ✗ Lag when scrolling
- ✗ Grid stops loading at certain count
- ✗ Memory warnings in console

---

### Test 23: Rapid Updates

**Steps:**
1. In beta.html, rapidly update same vehicle status 10 times
2. Observe wallboard for correct final state
3. Check for console errors

**Expected Results:**
- ✓ All updates received
- ✓ Final state matches latest change
- ✓ No duplicate cards
- ✗ No missing updates
- ✓ No console errors

**Fail Criteria:**
- ✗ Some updates missed
- ✗ Duplicate vehicle cards appear
- ✗ Final state doesn't match
- ✗ Console errors

---

### Test 24: Connection Recovery

**Steps:**
1. Open wallboard.html and verify "Live" status
2. Disconnect network (disconnect wifi or use DevTools Network throttling)
3. Wait 10 seconds
4. Observe status indicator
5. Reconnect network
6. Verify wallboard recovers

**Expected Results:**
- ✓ Status changes to error/disconnected
- ✓ Toast notification appears
- ✓ After reconnecting, status returns to "Live"
- ✓ Any updates made during disconnect are received

**Fail Criteria:**
- ✗ Wallboard doesn't indicate disconnection
- ✗ Doesn't auto-reconnect after network restored
- ✗ Requires manual page refresh to recover

---

## Regression Testing Checklist

After any changes to wallboard.html, verify:

- [ ] Firebase connection works
- [ ] All vehicles display
- [ ] Color coding correct
- [ ] Priority badges show
- [ ] Days calculation accurate
- [ ] Settings persist
- [ ] Theme toggle works
- [ ] Fullscreen mode works
- [ ] Real-time updates work
- [ ] No console errors
- [ ] Responsive layout works
- [ ] Cross-browser compatible
- [ ] Performance acceptable
- [ ] Keyboard shortcuts work

---

## Bug Report Template

When filing bugs, include:

```
TITLE: [Component] Brief description

SEVERITY: Critical | High | Medium | Low

ENVIRONMENT:
- Browser: Chrome 120
- OS: macOS 14
- Device: MacBook Pro 16"
- Date/Time: 2026-02-24 14:30 UTC

STEPS TO REPRODUCE:
1. Open wallboard.html
2. ...
3. ...

EXPECTED:
What should have happened

ACTUAL:
What actually happened

SCREENSHOT/VIDEO:
[Attach if applicable]

CONSOLE ERRORS:
[Paste from DevTools Console if applicable]

NETWORK ACTIVITY:
[Note any failed requests or WebSocket issues]
```

---

## Sign-Off Checklist

### Development Team
- [ ] All test cases executed
- [ ] No critical bugs remaining
- [ ] Documentation complete
- [ ] Code reviewed and approved
- [ ] Performance within acceptable limits

### QA Team
- [ ] Integration testing complete
- [ ] Cross-browser testing complete
- [ ] Edge cases tested
- [ ] User acceptance testing passed
- [ ] Accessibility requirements met

### Product Team
- [ ] Feature meets requirements
- [ ] User feedback incorporated
- [ ] Deployment plan approved
- [ ] Documentation reviewed
- [ ] Support plan in place

---

## Test Execution Log

| Test # | Description | Date | Tester | Result | Notes |
|--------|-------------|------|--------|--------|-------|
| 1 | Initial Load | _ | _ | _ | _ |
| 2 | Vehicle Grid | _ | _ | _ | _ |
| 3 | Status Colors | _ | _ | _ | _ |
| 4 | Priority Badges | _ | _ | _ | _ |
| 5 | Days Calculation | _ | _ | _ | _ |
| 6 | Preferences | _ | _ | _ | _ |
| 7 | Theme Toggle | _ | _ | _ | _ |
| 8 | Fullscreen | _ | _ | _ | _ |
| 9 | Real-time | _ | _ | _ | _ |
| 10 | Sorting | _ | _ | _ | _ |
| 11 | Responsive | _ | _ | _ | _ |
| 12 | Integration | _ | _ | _ | _ |
| 13 | Compatibility | _ | _ | _ | _ |
| 14 | Load Time | _ | _ | _ | _ |
| 15 | Update Perf | _ | _ | _ | _ |
| 16 | Memory | _ | _ | _ | _ |
| 17 | Usability | _ | _ | _ | _ |
| 18 | Multi-location | _ | _ | _ | _ |
| 19 | Contrast | _ | _ | _ | _ |
| 20 | Keyboard | _ | _ | _ | _ |
| 21 | Missing Data | _ | _ | _ | _ |
| 22 | Large Data | _ | _ | _ | _ |
| 23 | Rapid Updates | _ | _ | _ | _ |
| 24 | Connection | _ | _ | _ | _ |

---

## Continuous Testing

### Post-Launch Monitoring

- Monitor error logs from Firebase
- Check browser console for JavaScript errors
- Monitor performance metrics (load time, update latency)
- Collect user feedback on usability
- Track feature usage patterns

### Regular Testing Schedule

- **Weekly:** Quick smoke test (basic functionality)
- **Monthly:** Full regression test suite
- **Quarterly:** Performance and load testing
- **As-needed:** Edge case and compatibility testing

---

**Version:** 1.0
**Last Updated:** February 24, 2026
**Maintained by:** QA Team
