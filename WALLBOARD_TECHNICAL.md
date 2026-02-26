# Fleet Repair Wallboard - Technical Documentation

## Architecture Overview

The wallboard dashboard is a real-time monitoring interface built with vanilla HTML/CSS/JavaScript and Firebase Firestore. It's designed for minimal overhead while maintaining live data synchronization.

### Technology Stack

- **Frontend:** HTML5, CSS3, Vanilla JavaScript (ES6)
- **Backend:** Firebase Firestore (NoSQL)
- **Authentication:** Firebase Auth (inherits session from beta.html)
- **Real-time:** Firebase onSnapshot listeners
- **Storage:** Browser localStorage for preferences
- **Theming:** CSS custom properties (variables)

### System Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                     Browser Tab                              │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌──────────────────────────────────────────────────────┐   │
│  │        wallboard.html (Client Application)           │   │
│  ├──────────────────────────────────────────────────────┤   │
│  │                                                       │   │
│  │  ┌─────────────────┐  ┌──────────────────────────┐  │   │
│  │  │   UI Layer      │  │   Preferences Store      │  │   │
│  │  │ - Header        │  │ (localStorage)           │  │   │
│  │  │ - Settings Pnl  │  │ - Field visibility       │  │   │
│  │  │ - Vehicle Grid  │  │ - Filter selections      │  │   │
│  │  │ - Toast notify  │  │ - Theme preference       │  │   │
│  │  └────────┬────────┘  └──────────────────────────┘  │   │
│  │           │                                          │   │
│  │  ┌────────▼────────────────────────────────────┐    │   │
│  │  │    Render & Display Logic                   │    │   │
│  │  │ - createVehicleCardHTML()                   │    │   │
│  │  │ - renderVehicleGrid()                       │    │   │
│  │  │ - getFilteredVehicles()                     │    │   │
│  │  │ - calculateDaysInShop()                     │    │   │
│  │  └────────┬────────────────────────────────────┘    │   │
│  │           │                                          │   │
│  │  ┌────────▼────────────────────────────────────┐    │   │
│  │  │    State Management                         │    │   │
│  │  │ - fleetData[] (vehicle array)               │    │   │
│  │  │ - displayPrefs (user preferences)           │    │   │
│  │  │ - currentTheme (dark/light)                 │    │   │
│  │  │ - isConnected (status flag)                 │    │   │
│  │  └────────┬────────────────────────────────────┘    │   │
│  │           │                                          │   │
│  │  ┌────────▼────────────────────────────────────┐    │   │
│  │  │    Firebase Listener                        │    │   │
│  │  │ - db.collection('vehicles')                 │    │   │
│  │  │   .onSnapshot(snapshot => {...})            │    │   │
│  │  │ - Real-time updates on data change          │    │   │
│  │  └────────┬────────────────────────────────────┘    │   │
│  │           │                                          │   │
│  └───────────┼──────────────────────────────────────────┘   │
│              │                                               │
└──────────────┼───────────────────────────────────────────────┘
               │
        ┌──────▼──────────────────────────────────────────┐
        │    Firebase Firestore (Cloud)                  │
        ├───────────────────────────────────────────────┤
        │                                                 │
        │  ┌──────────────────────────────────────────┐  │
        │  │  Collection: vehicles                     │  │
        │  │  ├─ Documents: DLX-001, DLX-002, etc   │  │
        │  │  └─ Fields: id, status, priority, etc   │  │
        │  │                                           │  │
        │  └──────────────────────────────────────────┘  │
        │                                                 │
        └─────────────────────────────────────────────────┘
```

---

## File Structure

```
repairtracker/
├── wallboard.html              ← Main wallboard application
├── beta.html                   ← Data entry & management
├── index.html                  ← Legacy Google Sheets version
├── mobile.html                 ← Mobile optimized version
├── WALLBOARD_GUIDE.md          ← User guide
└── WALLBOARD_TECHNICAL.md      ← This file
```

---

## Code Organization

### HTML Structure

```html
<!DOCTYPE html>
<html>
<head>
    <!-- Fonts, Firebase, Basic Styles -->
</head>
<body>
    <!-- Header -->
    <header class="wallboard-header">
        <!-- Title, Status Indicator, Controls -->
    </header>

    <!-- Settings Panel (Drawer) -->
    <div class="settings-panel" id="settingsPanel">
        <!-- Preferences for field & filter display -->
    </div>

    <!-- Main Content -->
    <main class="wallboard-content">
        <div class="vehicle-grid" id="vehicleGrid">
            <!-- Vehicle cards injected here via JavaScript -->
        </div>
    </main>

    <!-- Fullscreen Indicator -->
    <div class="fullscreen-indicator">
        <!-- Press F hint -->
    </div>

    <!-- JavaScript -->
    <script>
        // All functionality here
    </script>
</body>
</html>
```

### CSS Architecture

**CSS Custom Properties (Dark Mode Defaults):**
```css
:root {
    --bg-app: #111111;           /* Main background */
    --bg-header: #161616;        /* Header background */
    --bg-card: #232323;          /* Card background */
    --text-main: #ededed;        /* Primary text */
    --text-muted: #8b8b8b;       /* Secondary text */
    --border: #2c2c2c;           /* Border color */

    /* Status colors */
    --status-operational: #4ade80;  /* Green */
    --status-warning: #f97316;      /* Orange */
    --status-down: #ef4444;         /* Red */

    /* Priority colors */
    --c-urgent: #ef4444;            /* Red */
    --c-high: #f97316;              /* Orange */
    --c-normal: #3b82f6;            /* Blue */
    --c-maint: #a855f7;             /* Purple */

    /* Layout */
    --radius-lg: 12px;
    --radius-md: 8px;
    --transition: 0.3s cubic-bezier(0.4,0,0.2,1);
}
```

**Responsive Grid:**
```css
.vehicle-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 1.5rem;
}
```

**Status-based Styling:**
```css
.vehicle-card.status-operational { /* Green tint */ }
.vehicle-card.status-warning      { /* Orange tint */ }
.vehicle-card.status-down         { /* Red tint */ }
```

---

## JavaScript API

### State Management

```javascript
// Fleet data (synced from Firebase)
let fleetData = [];                    // Array of vehicle objects

// Display preferences (persisted in localStorage)
let displayPrefs = {
    fields: {
        issue: true,                  // Show issue description
        location: true,               // Show work location
        technician: false,            // Show assigned tech
        parts: true,                  // Show parts status
        days: true,                   // Show days in shop
        homeLocation: false           // Show home location
    },
    filters: {
        operational: true,            // Include operational vehicles
        warning: true,                // Include warning status
        down: true                    // Include down for repair
    }
};

// Connection state
let isConnected = false;               // Firebase connection status

// Theme
let currentTheme = 'dark';             // 'dark' or 'light'
```

### Core Functions

#### Initialization
```javascript
initializeWallboard()
  ├─ applyTheme()                    // Apply saved theme
  ├─ loadPreferences()               // Restore display settings
  └─ auth.onAuthStateChanged()       // Check authentication
      └─ startRealtimeListener()      // Begin Firebase sync
```

#### Real-time Data Sync
```javascript
startRealtimeListener()
  │
  └─ db.collection('vehicles').onSnapshot(snapshot => {
       fleetData = snapshot.docs.map(...)
       renderVehicleGrid()
       setStatus('connected', 'Live')
     })
```

**Firebase Listener Behavior:**
- Triggers on initial load with all documents
- Re-triggers whenever ANY document in collection changes
- Provides added, modified, and removed documents (via snapshot)
- Maintains live connection with automatic reconnection

#### Rendering
```javascript
renderVehicleGrid()
  ├─ getFilteredVehicles()          // Apply filters & sort
  └─ map(vehicle =>
       createVehicleCardHTML(vehicle)
     )

createVehicleCardHTML(vehicle)
  ├─ Extract: status, priority, days
  ├─ Build: badges, details sections
  └─ Return: HTML string
```

**Card Content Logic:**
```javascript
if (displayPrefs.fields.issue && vehicle.issue) {
    // Show issue section
}
if (displayPrefs.fields.location && vehicle.location) {
    // Show location detail
}
// ... repeat for all configurable fields
```

#### Filtering & Sorting
```javascript
getFilteredVehicles()
  ├─ Filter by status (operational, warning, down)
  └─ Sort by:
      1. Priority (Urgent → High → Maint → Normal)
      2. Days in shop (most recent first)

calculateDaysInShop(dateStr)
  └─ Math: (now - dateStr) / millisPerDay
```

#### Preferences Management
```javascript
savePreferences()
  ├─ Read checkbox values
  ├─ Update displayPrefs object
  ├─ localStorage.setItem('wallboard-prefs', JSON.stringify(...))
  └─ renderVehicleGrid()           // Re-render with new settings

loadPreferences()
  ├─ Retrieve from localStorage
  ├─ Parse and merge with defaults
  └─ Update UI checkboxes
```

#### UI Controls
```javascript
toggleSettings()
  └─ settingsPanel.classList.toggle('open')

toggleTheme()
  ├─ currentTheme = currentTheme === 'dark' ? 'light' : 'dark'
  ├─ localStorage.setItem('wallboard-theme', currentTheme)
  ├─ applyTheme()                   // Update CSS variables
  └─ Update icon (🌙 ↔️ ☀️)

toggleFullscreen()
  ├─ document.body.classList.toggle('fullscreen')
  └─ Try requestFullscreen() API

applyTheme()
  └─ Set CSS variables based on currentTheme
     (Light: whites/blacks; Dark: grays/whites)
```

#### Status Updates
```javascript
setStatus(state, text)
  ├─ Update indicator dot class (loading, connected, error)
  ├─ Update text display
  └─ Set isConnected flag

showToast(message)
  ├─ Create toast div
  ├─ Append to body
  └─ Auto-remove after 3s
```

---

## Firebase Schema

### vehicles Collection

```javascript
{
  // Document ID = Vehicle ID (e.g., "DLX-001")
  id: "DLX-001",
  docId: "DLX-001",

  // Vehicle Info
  vehicleType: "Van",              // Van, Prius, Scion, Other
  homeLocation: "Dispatch Lot",
  mileage: 45000,

  // Repair Status
  status: "Down",                  // Operational, Warning, Down
  priority: "Urgent",              // Urgent, High, Maint, Normal
  issue: "Transmission slipping",
  shopStatus: "Repair in Progress",
  location: "Bay 3",
  onTheRoad: false,

  // Timeline
  date: "2026-02-20",              // Date reported
  statusUpdatedAt: "2026-02-20T10:30:00Z",
  inspectionDate: "2026-03-30",

  // Parts
  partsOrdered: "2026-02-21",
  partsEta: "2026-02-28",

  // Metadata
  requestedBy: "Supervisor Name",
  notes: "Customer noted noise",
  history: "[{ts:..., user:..., action:..., desc:...}]",
  sortOrder: 1708939800000,
  lastUpdated: "2026-02-24T14:30:00Z",

  // Flags
  isBaseVehicle: false,
  isTicket: true,
  baseVehicleId: "DLX-001",

  // Fleetio Integration
  fleetioIssueId: "123456"
}
```

**Data Flow for Wallboard:**

1. **Initial Load:**
   - Firebase retrieves all documents from `vehicles` collection
   - Maps to fleetData array with all fields
   - Renders grid with available data

2. **Ongoing Updates:**
   - Firebase listener detects changes
   - Affected documents included in snapshot
   - fleetData updated
   - Re-render affected cards only

3. **Efficiency:**
   - Only fields in card HTML are read
   - Missing fields skip rendering gracefully
   - No requests for unneeded data

---

## Event Flow

### Initial Page Load
```
1. DOMContentLoaded
2. initializeWallboard()
   └─ Check localStorage theme
   └─ Load preferences from localStorage
   └─ Check Firebase auth
3. auth.onAuthStateChanged()
   └─ User authenticated? → startRealtimeListener()
   └─ Not authenticated? → Redirect to beta.html
4. startRealtimeListener()
   └─ Firebase connects
   └─ Initial snapshot loads all vehicles
   └─ renderVehicleGrid()
   └─ setStatus('connected', 'Live')
```

### User Changes Display Setting
```
1. User clicks checkbox in settings panel
2. pref-* onchange event triggers
3. savePreferences()
   └─ Update displayPrefs object
   └─ Save to localStorage
   └─ renderVehicleGrid()
4. Grid re-renders with new field visibility/filters
```

### Firebase Data Changes
```
1. User updates vehicle in beta.html
2. Firebase updates document
3. wallboard's onSnapshot listener triggers
4. fleetData array updated
5. renderVehicleGrid()
6. Grid updates to show new values
   (User sees change within ~1 second)
```

### User Keyboard Shortcut
```
1. User presses 'F' key
2. toggleFullscreen() called
3. body.classList.toggle('fullscreen')
4. CSS media query applies fullscreen styles
5. Header hidden, grid expanded
6. User sees full-screen presentation
```

---

## Performance Considerations

### Optimization Strategies

1. **Firebase Listener:**
   - Single listener on entire vehicles collection
   - Efficient delta updates (only changed docs)
   - Auto-reconnect on network failure
   - Minimal bandwidth for text-based data

2. **DOM Rendering:**
   - Full re-render on each data change (acceptable for vehicle count <100)
   - Could optimize with virtual scrolling for 1000+ vehicles
   - String concatenation for HTML (fast for small datasets)

3. **CSS Transitions:**
   - GPU-accelerated transforms (scale, translateY)
   - Smooth 0.3s transitions on hover/update
   - No animation blocking (uses `will-change` implicitly)

4. **localStorage:**
   - Minimal keys: `wallboard-prefs`, `wallboard-theme`
   - Immediate writes on preference change
   - No sync overhead

### Scalability

**Current Limits:**
- Comfortable with 100-200 vehicles per instance
- Grid layout auto-adjusts based on screen size
- Firebase Firestore handles 100k+ documents easily

**If Scaling Beyond 200 Vehicles:**
- Implement virtual scrolling with React-window
- Add pagination or location-based filtering
- Consider materialized views in Firestore
- Implement data aggregation queries

---

## Integration with Other Components

### Relationship to beta.html

| Aspect | wallboard.html | beta.html |
|--------|---|---|
| **Purpose** | Display | Data Entry |
| **Auth** | Inherits session | Manages login |
| **Database** | Read-only (via listener) | Read-write (forms) |
| **UI** | Fullscreen-friendly | Feature-rich forms |
| **Users** | Shop floor staff | Office/supervisors |

**Data Flow:**
```
beta.html (edits)
    ↓
Firebase Firestore (syncs)
    ↓
wallboard.html (displays)
```

### Authentication

The wallboard relies on Firebase Auth from beta.html:
- Uses `firebase.auth()` from parent project
- Checks `auth.currentUser` on load
- Redirects to beta.html if not authenticated
- No separate login form needed

**Session Persistence:**
- Firebase SDK handles auth token refresh
- Browser storage keeps user logged in
- Logout on beta.html affects wallboard too

### Shared Data Model

Both apps use identical Firestore schema:
- Same vehicle documents
- Same field names and types
- Same status enumerations
- Changes in one visible in the other

---

## Extending the Wallboard

### Adding a New Display Field

**Step 1: Add Checkbox to Settings HTML**
```html
<div class="pref-item">
    <input type="checkbox" id="pref-custom-field" checked onchange="savePreferences()">
    <label for="pref-custom-field">Custom Field Name</label>
</div>
```

**Step 2: Add to Display Preferences Object**
```javascript
let displayPrefs = {
    fields: {
        customField: true,  // Add here
        // ... existing fields
    },
    // ...
};
```

**Step 3: Load/Save Preference**
```javascript
function loadPreferences() {
    // Existing code...
    document.getElementById('pref-custom-field').checked = displayPrefs.fields.customField;
}

function savePreferences() {
    // Existing code...
    displayPrefs.fields.customField = document.getElementById('pref-custom-field').checked;
    // ... rest of save
}
```

**Step 4: Add to Card Rendering**
```javascript
function createVehicleCardHTML(vehicle) {
    // ... existing code...

    if (displayPrefs.fields.customField && vehicle.customField) {
        html += `<div class="card-detail">
            <span class="card-detail-label">Custom Field:</span>
            <span class="card-detail-value">${vehicle.customField}</span>
        </div>`;
    }

    // ... rest of function
}
```

### Adding a New Status Filter

Follow same pattern as above, but modify `getFilteredVehicles()`:

```javascript
function getFilteredVehicles() {
    return fleetData.filter(v => {
        // ... existing filters...

        const newStatusFilter = v.someNewStatus;
        if (newStatusFilter && !displayPrefs.filters.newStatus) return false;

        return true;
    });
}
```

### Custom Theme Colors

Modify CSS variables in `:root`:
```css
:root {
    --c-urgent: #your-red-color;
    --c-high: #your-orange-color;
    /* etc */
}
```

Or in `applyTheme()` function:
```javascript
if (isDark) {
    document.body.style.setProperty('--c-urgent', '#your-dark-red');
} else {
    document.body.style.setProperty('--c-urgent', '#your-light-red');
}
```

---

## Debugging

### Browser Console Errors

**Common Issues:**

1. **"Firebase not defined"**
   - Firebase libraries didn't load
   - Check CDN URLs are correct

2. **"Cannot read property 'collection' of undefined"**
   - Firebase not initialized
   - Check firebaseConfig is correct

3. **Blank grid with "Loading fleet data..."**
   - Firebase listener not receiving data
   - Check: Auth status, Firestore permissions, Network

### Debug Logging

Add to `startRealtimeListener()`:
```javascript
unsubscribe = db.collection('vehicles').onSnapshot(
    snapshot => {
        console.log('📊 Firestore snapshot received');
        console.log('  Total vehicles:', snapshot.docs.length);
        console.log('  First vehicle:', snapshot.docs[0]?.data());
        fleetData = snapshot.docs.map(...);
        renderVehicleGrid();
    },
    error => {
        console.error('🔴 Firebase error:', error);
    }
);
```

### Testing Display Logic

```javascript
// Test in browser console
console.table(fleetData);              // View all vehicle data
console.table(getFilteredVehicles()); // View filtered/sorted data
console.log(displayPrefs);             // View current preferences
```

### Connection Status

Check in browser DevTools Network tab:
- WebSocket connection to Firebase
- Should show `wss://` (secure WebSocket)
- May see multiple connections (normal for Firebase)

---

## Deployment

### Hosting Options

1. **Same server as beta.html**
   - No additional setup needed
   - Files served from same domain
   - Credentials/auth shared

2. **Static file hosting**
   - AWS S3
   - GitHub Pages
   - Vercel
   - Netlify
   - Any HTTP server

### Prerequisites

- Firebase project (shared with beta.html)
- Valid firebaseConfig credentials
- User authentication credentials
- HTTPS recommended (required for some browser APIs)

### Setup Steps

1. Copy `wallboard.html` to your server
2. Verify Firebase config is correct
3. Test in browser: `https://yoursite.com/wallboard.html`
4. Bookmark for quick access
5. Share URL with shop staff

---

## Monitoring & Maintenance

### Health Checks

**Weekly:**
- Open wallboard.html
- Verify "Live" status appears
- Check a few vehicle cards display correctly
- Test updating a vehicle in beta.html (should appear in wallboard)

**Monthly:**
- Review for data accuracy
- Check theme/preferences work correctly
- Test on different browsers/devices
- Monitor browser console for errors

### Performance Monitoring

Monitor these in DevTools:
- **Network:** Firebase WebSocket should remain open
- **Memory:** Check for memory leaks (grow over time)
- **CPU:** Should be idle when no data changing

### Logging & Support

For bug reports include:
- Browser and version
- Exact steps to reproduce
- Screenshot of issue
- Browser console errors (F12 → Console)
- Current date/time when issue occurred

---

## Future Enhancement Ideas

1. **Analytics Overlay**
   - Key metrics on wallboard
   - Units down, urgent count, avg repair time

2. **Location-Based View**
   - Filter by work location
   - Bay assignment display

3. **Technician Assignment**
   - Show assigned staff on cards
   - Technician workload at a glance

4. **Estimated Completion**
   - Prediction based on repair type/parts status
   - Countdown to expected pickup

5. **Drag-to-Update**
   - Quick status changes from wallboard
   - Drag card between visual status zones

6. **Mobile Companion App**
   - Native app for technician updates
   - Direct sync with wallboard

7. **Alert System**
   - Notifications for urgent items
   - Color pulse on critical repairs
   - Audio alerts option

8. **Historical View**
   - Replay fleet status changes
   - Analyze repair bottlenecks
   - Trending analysis

---

## References

- [Firebase Documentation](https://firebase.google.com/docs)
- [Firestore Real-time Updates](https://firebase.google.com/docs/firestore/query-data/listen)
- [CSS Grid Layout](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout)
- [Fullscreen API](https://developer.mozilla.org/en-US/docs/Web/API/Fullscreen_API)
- [LocalStorage API](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)

---

**Last Updated:** February 24, 2026
**Maintained by:** RepairTracker Development Team
**Version:** 1.0
