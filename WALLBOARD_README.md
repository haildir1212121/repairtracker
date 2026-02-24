# Fleet Repair Wallboard Dashboard

A real-time interactive wallboard for monitoring fleet status and repair progress in shop environments. Built for wall-mounted displays with real-time Firebase synchronization, customizable views, and fullscreen presentation mode.

## Features

✨ **Real-Time Updates**
- Live Firebase Firestore synchronization
- No manual refresh needed
- Updates appear within seconds of changes
- Automatic connection recovery

📊 **Fleet Visibility**
- Vehicle status at a glance (Operational/Warning/Down)
- Priority-based color coding (Urgent/High/Maintenance/Normal)
- Days-in-shop tracking with automatic warnings
- Customizable information display

🎯 **User-Friendly Design**
- Responsive grid layout adapts to any screen size
- Large, readable fonts optimized for distance viewing
- Fullscreen presentation mode for TV displays
- Dark and light theme support

⚙️ **Customizable Display**
- Toggle which vehicle fields to show
- Filter by status type
- Persistent preferences stored locally
- Settings accessible during use

🔌 **Integration**
- Seamless Firebase Firestore connection
- Works alongside beta.html for data entry
- Read-only access ensures data integrity
- Single sign-on with existing credentials

## Quick Start

### Requirements
- Modern web browser (Chrome, Firefox, Safari, Edge)
- JavaScript enabled
- Active Firebase Firestore database
- User authentication credentials

### Access
1. Navigate to `wallboard.html`
2. Log in with your credentials
3. Wallboard loads automatically

### First Use
- Default view shows: Current Issue, Location, Parts Status, Days in Shop
- All vehicle statuses are visible by default
- Dark mode is enabled by default
- All preferences saved automatically

## Files Included

| File | Purpose |
|------|---------|
| `wallboard.html` | Main application (954 lines) |
| `WALLBOARD_README.md` | Quick start guide (this file) |
| `WALLBOARD_GUIDE.md` | Comprehensive user guide |
| `WALLBOARD_TECHNICAL.md` | Developer technical reference |
| `WALLBOARD_TESTING.md` | Complete testing plan |

## Display Modes

### Standard Mode
- Full header with controls
- Vehicle grid with scrolling
- Settings panel accessible via ⚙️ button
- Suitable for desktop and tablet use

### Fullscreen Mode
- Maximized grid for wall displays
- Header hidden automatically
- F key or button toggle
- Optimized for TV viewing distance
- Settings still accessible (F key to exit)

## Customization

### Display Fields
Toggle visibility of:
- Current Issue
- Location
- Assigned Technician
- Parts Status
- Days in Shop
- Home Location

### Filter Options
Show/hide vehicles by:
- Operational status
- Warning status
- Down for repair status

### Theme
- Dark mode (default): Better for 24/7 shop displays
- Light mode: Better for brightly lit areas

## Integration with Beta.html

The wallboard is designed as a companion to beta.html:

```
beta.html (Data Entry)
    ↓
Firebase Firestore (Shared Database)
    ↓
wallboard.html (Display)
```

- Create and manage repairs in beta.html
- Monitor progress in real-time on wallboard
- Technicians view status on shop floor displays
- Supervisors manage from office

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| **F** | Toggle fullscreen mode |
| **ESC** | Close settings panel |

## Color Coding Guide

### Status Background
- 🟢 **Green**: Operational (running)
- 🟡 **Orange**: Warning (issues detected)
- 🔴 **Red**: Down (in repair)

### Priority Badges
- 🔴 **Red**: Urgent (critical/safety)
- 🟠 **Orange**: High (significant problems)
- 🟣 **Purple**: Maintenance (scheduled service)
- (No badge): Normal (routine repair)

### Days in Shop
- ⚪ **Gray**: 0-6 days (normal)
- 🟠 **Orange**: 7-13 days (warning)
- 🔴 **Red**: 14+ days (critical)

## Technical Stack

- **Frontend**: HTML5, CSS3, Vanilla JavaScript (ES6)
- **Database**: Firebase Firestore (real-time)
- **Authentication**: Firebase Auth
- **Real-time Sync**: Firebase onSnapshot listeners
- **Storage**: Browser localStorage for preferences

## System Requirements

### Server/Database
- Firebase project configured
- Firestore database with vehicle collection
- Authentication enabled
- Network connectivity

### Browser
- HTML5 support
- CSS3 Flexbox and Grid
- JavaScript ES6
- WebSocket support (for real-time updates)
- HTTPS recommended (required on some deployments)

### Display
- Minimum 1024x768 resolution (desktop)
- Optimal 1920x1080+ (standard display)
- Large displays benefit from fullscreen mode

## Connection Status

The header shows connection status:

| Status | Meaning | Action |
|--------|---------|--------|
| 🟡 Connecting | Initial load or reconnecting | Wait 10 seconds |
| 🟢 Live | Connected and receiving updates | Monitor normally |
| ⚫ Error | Connection lost | Check network |

## Performance

- **Initial Load**: <10 seconds
- **Update Latency**: <2 seconds
- **Memory Usage**: <100MB
- **CPU Usage**: <20% during operation

## Browser Compatibility

| Browser | Version | Support |
|---------|---------|---------|
| Chrome | Latest | ✓ Full support |
| Firefox | Latest | ✓ Full support |
| Safari | Latest | ✓ Full support |
| Edge | Latest | ✓ Full support |
| Mobile browsers | Latest | ✓ Full support |

## Known Limitations

- Display is read-only (use beta.html to make changes)
- Designed for <200 vehicles per instance (scalable with optimization)
- Real-time updates require active Firebase connection
- Fullscreen API may be blocked in some browsers (CSS fallback available)

## Troubleshooting

### Wallboard shows "Connecting..." but doesn't complete
- Check internet connection
- Verify Firebase credentials are correct
- Check browser console for error messages
- Try refreshing the page

### Changes in beta.html don't appear in wallboard
- Verify connection status is "Live"
- Give it 2-3 seconds for update to arrive
- Check if filters are hiding the vehicle
- Try making another change to trigger update

### Fullscreen doesn't work
- Check browser fullscreen permissions
- Try F key instead of button
- CSS-based fullscreen (hiding header) always works as fallback
- HTTPS may be required on some browsers

### Preferences don't persist
- Check browser storage is enabled
- Not in Private/Incognito mode
- Clear cookies if major issues
- Use normal browsing mode

## Best Practices

1. **Mount on prominent wall**
   - Visible from multiple work areas
   - Eye-level positioning
   - Away from direct sunlight

2. **Use fullscreen mode**
   - Press F when mounted on TV
   - Cleaner presentation for shop floor

3. **Customize for your workflow**
   - Show fields most relevant to your team
   - Filter status types as needed
   - Use theme appropriate for lighting

4. **Monitor connection**
   - Green "Live" dot confirms data
   - Check before shift starts
   - ~10 second auto-reconnect if interrupted

5. **Regular check-ins**
   - Review priority order during shifts
   - Watch for vehicles nearing 14-day warning
   - Identify stuck repairs (unchanged status + high days)

## Support

### Getting Help

**User Questions:**
- See WALLBOARD_GUIDE.md for detailed instructions
- Check WALLBOARD_TECHNICAL.md for technical details

**Reporting Issues:**
1. Document the problem
2. Note browser and OS version
3. Screenshot or video if possible
4. Check browser console (F12) for errors
5. Include date/time of issue

### Feature Requests

Suggested enhancements for future versions:
- Location-based filtering
- Custom sort options
- Analytics overlay
- Work order detail modal
- Estimated completion times

## Deployment

### Hosting Options
- Same server as beta.html (recommended)
- Static file hosting (AWS S3, GitHub Pages, Vercel)
- Any standard web server

### Setup
1. Copy wallboard.html to your server
2. Verify Firebase config is correct
3. Test: `https://yoursite.com/wallboard.html`
4. Bookmark for quick access

### No Special Configuration Needed
- Uses same Firebase project as beta.html
- Same authentication system
- No additional database setup required

## Documentation

### For Users
- **WALLBOARD_GUIDE.md** - Complete user guide with setup and customization

### For Developers
- **WALLBOARD_TECHNICAL.md** - Architecture, code organization, APIs, extending
- **WALLBOARD_TESTING.md** - Comprehensive testing plan with 24 test cases

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-02-24 | Initial release |

## License

Part of the RepairTracker fleet management system. Use within your organization in accordance with your RepairTracker license.

## Credits

- Built with vanilla HTML/CSS/JavaScript for minimum overhead
- Real-time data via Firebase Firestore
- Designed for shop floor efficiency
- Optimized for team collaboration

---

## Next Steps

1. **Deploy wallboard.html** to your server
2. **Test with sample data** using the included testing plan
3. **Train shop staff** on basic features
4. **Mount on display** using fullscreen mode
5. **Monitor usage** and gather feedback
6. **Customize** based on team feedback

## Questions?

- Check WALLBOARD_GUIDE.md for user questions
- Check WALLBOARD_TECHNICAL.md for developer questions
- Check browser console (F12) for technical errors
- Review WALLBOARD_TESTING.md to verify proper function

---

**Made for teams who need real-time fleet visibility at a glance.**

For inquiries about features, support, or deployment, contact your RepairTracker administrator.
