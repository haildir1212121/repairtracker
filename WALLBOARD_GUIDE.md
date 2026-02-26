# Fleet Repair Wallboard Dashboard - User Guide

## Overview

The Fleet Repair Wallboard Dashboard is a dedicated display interface designed for wall-mounted TV screens in shop environments. It provides real-time visibility into fleet status and repair progress with automatic updates, no manual refresh required.

**Key Features:**
- 🎯 Real-time fleet status updates via Firebase
- 📊 Vehicle grid with customizable field display
- 🌙 Dark/Light theme support
- 🖥️ Fullscreen presentation mode for TV displays
- ⚙️ Customizable display preferences with persistent storage
- 🔌 Live connection status indicator

---

## Getting Started

### Accessing the Wallboard

1. Navigate to `wallboard.html` in your browser
   ```
   https://yourrepairtracker.com/wallboard.html
   ```

2. You'll be redirected to login if not already authenticated (uses same credentials as beta.html)

3. Once authenticated, the dashboard loads automatically and begins receiving real-time data

### First-Time Setup

The wallboard comes with sensible defaults:
- **Fields Shown:** Current Issue, Location, Parts Status, Days in Shop
- **Filters Active:** All vehicle statuses (Operational, Warning, Down)
- **Theme:** Dark mode
- **Sort Order:** Priority (Urgent → High → Maintenance → Normal), then by days in shop

---

## Display Modes

### Standard Mode (Default)
The wallboard displays in a responsive grid layout:
- Header with connection status and controls
- Vehicle cards in customizable grid
- Settings panel accessible from the right
- Real-time updates as data changes

### Fullscreen Mode
Optimized for wall-mounted displays:

**How to Enable:**
- Click the **Fullscreen** button in the header
- OR press the **F** key on your keyboard

**What Changes:**
- Header hides automatically
- Grid expands to fill entire screen
- Larger fonts for distance viewing
- Settings panel still accessible via keyboard

**How to Exit:**
- Click fullscreen button again
- OR press **F** key
- OR press **ESC** key

---

## Vehicle Cards Explained

### Card Layout

```
┌─────────────────────────────────┐
│ DLX-001      [URGENT] [Van]     │  ← Vehicle ID, Priority Badge, Type
│ ✓ Down                          │  ← Status Indicator
│ Status: Repair in Progress      │  ← Shop Status
│ Issue: Transmission slipping     │  ← Current Problem
│ Location: Bay 3                 │  ← Work Location
│ Parts: On order (Feb 28)        │  ← Parts Status
│ 12 days in shop                 │  ← Days Tracker
└─────────────────────────────────┘
```

### Color Coding

**Status Backgrounds:**
- 🟢 **Green tint** = Operational (vehicle running)
- 🟡 **Orange tint** = Warning (issues detected)
- 🔴 **Red tint** = Down (in repair)

**Priority Badges:**
- 🔴 **Red** = Urgent (critical/safety issue)
- 🟠 **Orange** = High (significant problems)
- 🟣 **Purple** = Maintenance (scheduled service)
- 🔵 **Blue** = Normal (routine repair)

**Days in Shop Warning:**
- ⚪ **Gray** = 0-6 days (normal)
- 🟠 **Orange** = 7-13 days (warning)
- 🔴 **Red** = 14+ days (critical - needs attention)

---

## Customization

### Display Settings

Click the **⚙️ Settings** button to open the settings panel.

#### Vehicle Fields
Toggle which information appears on each card:

- **Current Issue** - Shows the reported problem/complaint
- **Location** - Current work area or bay
- **Assigned Technician** - Staff member working on the repair
- **Parts Status** - Ordering/delivery status of needed parts
- **Days in Shop** - How long the vehicle has been in repair
- **Home Location** - Base/home station for the vehicle

#### Display Filters
Show/hide vehicle statuses:

- **Operational Vehicles** - Running, deployed vehicles
- **Warning Status** - Vehicles with detected issues
- **Down for Repair** - Vehicles actively in the shop

**Example Use Cases:**
- Filter to "Down for Repair" only to focus on in-shop work
- Keep all filters on for complete fleet visibility
- Hide "Operational" vehicles for after-hours shop status

### Theme Selection

Click the **🌙 Theme** button to toggle between dark and light modes:

- **Dark Mode** (default) - Better for 24/7 shop displays
- **Light Mode** - Better for brightly lit areas

Your selection persists across sessions.

---

## Monitoring the Fleet

### Real-Time Updates

The wallboard automatically updates when:
- A vehicle status changes
- A new repair ticket is created
- Parts information is updated
- Vehicle location changes
- Priority levels change

**No manual refresh needed** - changes appear within seconds.

### Connection Status

The header shows connection status:
- 🟡 **Orange pulsing dot + "Connecting..."** - Initial load, wait for connection
- 🟢 **Green dot + "Live"** - Connected and receiving updates
- ⚠️ **Gray dot + "Connection Error"** - Lost connection, will auto-retry

### Interpreting the Display

**Quick Glance Assessment:**
1. **Look at card positions** - Cards are sorted by priority (top-left = most urgent)
2. **Check color intensity** - Red borders = highest priority
3. **Scan day numbers** - Orange/red badges = oldest repairs needing attention
4. **Count vehicles by status** - Color tints show at-a-glance fleet health

---

## Using with Beta.html

The wallboard is designed to work alongside `beta.html` for data entry and management:

### Workflow

1. **Data Entry Team (beta.html):**
   - Create new repair tickets
   - Update status as work progresses
   - Manage vehicle database
   - Assign technicians and parts

2. **Shop Floor (wallboard.html):**
   - Displays live status on TV monitor
   - Technicians and supervisors monitor progress
   - Helps coordinate resource allocation
   - Identifies bottlenecks and urgent items

### Synchronization

- Both systems share the same Firebase database
- Changes in beta.html appear in wallboard within seconds
- Wallboard is read-only for data integrity
- To make changes, use beta.html's forms

---

## Best Practices

### For Shop Environments

1. **Mount on prominent wall**
   - Visible from multiple work bays
   - Eye level for best engagement
   - Away from direct sunlight

2. **Use fullscreen mode**
   - Press F when displaying on TV
   - Cleaner presentation
   - Removes UI distractions

3. **Customize for your workflow**
   - Adjust field visibility based on team needs
   - Filter status types to match shift focus
   - Use theme appropriate for lighting conditions

4. **Monitor connection**
   - Check status indicator regularly
   - Green dot confirms live data
   - If gray, give it 10 seconds to reconnect

5. **Regular check-ins**
   - Review priority order during shifts
   - Note vehicles nearing 14-day warning
   - Identify stuck repairs (high days + status unchanged)

### For Multi-Location Shops

- Create bookmarks or tabs for different monitor locations
- Open wallboard on each shop floor monitor
- Data syncs across all instances in real-time
- No need for manual updates between locations

---

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| **F** | Toggle fullscreen mode |
| **ESC** | Close settings panel |
| **⚙️** | Click button to open settings |
| **🌙** | Click button to toggle theme |

---

## Troubleshooting

### "Connecting..." stays displayed

**Possible Causes:**
- Browser tab needs focus
- Firebase connection issue
- Firewall blocking WebSocket

**Solutions:**
- Click on the browser window to focus it
- Check internet connection
- Wait up to 30 seconds for retry
- Refresh page (F5) if needed

### Vehicle data not updating

**Possible Causes:**
- Settings filters are hiding the vehicle
- No changes made since wallboard opened
- User session expired

**Solutions:**
- Check settings filters match your view needs
- Make a change in beta.html to test
- Refresh page if session expired (browser will redirect)

### Fullscreen not working

**Possible Causes:**
- Browser fullscreen API disabled
- Right-click + fullscreen not allowed
- HTTPS required on some browsers

**Solutions:**
- CSS-based fullscreen still works (hides header)
- Check browser security settings
- Ensure HTTPS connection if required

### Theme not persisting

**Possible Causes:**
- Browser cookies/localStorage disabled
- Private/Incognito mode in use

**Solutions:**
- Enable localStorage in browser settings
- Use normal browsing mode for persistence

---

## Technical Details

### System Requirements

- Modern web browser (Chrome, Firefox, Safari, Edge)
- JavaScript enabled
- WebSocket support for real-time updates
- Access to Firebase project (authenticated user)

### Firebase Integration

The wallboard connects to the same Firebase Firestore database as beta.html:
- Real-time listeners on `vehicles` collection
- Automatic sync without polling
- Efficient data transmission
- Live connection state monitoring

### Data Refresh

- Initial load: All vehicles from Firestore
- Ongoing: Firebase onSnapshot listeners trigger on any change
- No scheduled refresh intervals
- True real-time updates

### Storage

Display preferences stored in browser localStorage:
- Field visibility choices
- Filter selections
- Theme preference
- Auto-saved on each change
- Cleared if browser storage is cleared

---

## Advanced Usage

### Multiple Displays

You can run the wallboard on multiple monitors/TVs:

```
Monitor 1 (Shop Floor):    wallboard.html in fullscreen
Monitor 2 (Office):        wallboard.html with settings visible
Monitor 3 (Waiting Area):  wallboard.html on wall mount
```

Each instance:
- Connects independently to Firebase
- Syncs with same data
- Can have different display preferences
- Uses separate localStorage per browser

### Responsive Grid

The wallboard adapts to screen size:
- **Desktop (>1200px):** 4-5 cards per row
- **Tablet (768-1200px):** 3-4 cards per row
- **Mobile (<768px):** 2-3 cards per row

Designed for TV displays at typical viewing distances.

### Custom Data Fields

To add new fields to vehicle cards:

1. **In beta.html:** Add field to vehicle form and save
2. **In wallboard.html:** Add corresponding checkbox in settings
3. **Update:** `displayPrefs.fields` object with new field
4. **Render:** Add `card-detail` div in `createVehicleCardHTML()`

See code comments for `// Vehicle Fields` section.

---

## Support & Feedback

### Reporting Issues

If you experience problems:

1. Note the time and what you were doing
2. Check browser console (F12) for errors
3. Report with:
   - Browser and OS version
   - Steps to reproduce
   - Screenshot if applicable
   - Console errors (paste from F12)

### Feature Requests

Suggested enhancements for future versions:
- Location-based filtering
- Custom sort options
- Bulk status updates
- Analytics overlay
- Estimated completion times
- Work order details modal

---

## Data Displayed

The wallboard displays the following vehicle information (when available):

| Field | Source | Purpose |
|-------|--------|---------|
| Vehicle ID | Base record | Unique vehicle identifier |
| Status | Repair ticket | Operational / Warning / Down |
| Shop Status | Repair ticket | Current repair stage |
| Priority | Repair ticket | Urgency level |
| Issue | Repair ticket | Problem description |
| Location | Repair ticket | Current work area |
| Days in Shop | Calculated | Time since repair started |
| Vehicle Type | Base record | Van / Prius / Scion / Other |
| Parts Status | Repair ticket | Order/delivery status |
| Home Location | Base record | Primary vehicle station |

All data sourced from Firebase real-time collection.

---

## Quick Reference

### Default Display
Shows: Issue, Location, Parts Status, Days in Shop
Hides: Technician, Home Location
Filters: All statuses visible
Theme: Dark

### Change What's Shown
Click ⚙️ Settings → Toggle field checkboxes → Changes apply immediately

### Change Theme
Click 🌙 Theme → Toggles between dark and light

### Enter Fullscreen
Click 🖥️ Fullscreen button OR press F key → Header hides, grid expands

### Exit Fullscreen
Press F key OR ESC OR click 🖥️ again

### Check Connection
Look at colored dot next to "FLEET WALLBOARD" title
- 🟡 Connecting
- 🟢 Live
- ⚪ Error

---

## Version & Updates

**Current Version:** 1.0
**Release Date:** February 2026
**Last Updated:** February 24, 2026

The wallboard is maintained alongside beta.html and receives updates as part of the RepairTracker system maintenance.

---

**Made for shop teams who need real-time fleet visibility at a glance.** 🚚🔧
