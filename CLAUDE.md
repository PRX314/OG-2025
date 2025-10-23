# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Olimpiadi Goliardiche 2025** is a web-based scoring and management system for a multi-bar competition event. The application consists of three main pages designed with a Greek/Olympic aesthetic (gold/bronze color palette inspired by ancient Greek vases).

This is a **vanilla JavaScript, HTML, and CSS** project with **no build process or dependencies**. All code runs directly in the browser.

## Key Files

- **`index.html`** - Main scoreboard/leaderboard page (Classifica Finale)
- **`hub-capitani.html`** - Team captain management dashboard
- **`homepage.html`** - Landing page with event information, temples, and rules
- **`script.js`** - JavaScript for captain hub functionality
- **`styles.css`** - Shared CSS styling across all pages
- **`template_classifica.csv`** - CSV template for Google Sheets integration
- **`Loghi Bar/`** - Directory containing bar logos and event branding images

## Application Architecture

### Three-Page Application

1. **Homepage (`homepage.html`)**: Entry point showing:
   - Event information (60 days, 14 temples, teams of 4-8 members)
   - List of participating bars/temples with hours
   - Challenge types (Quiz, Theater, Creativity, Penalties, Riddles, Grand Finale)
   - Basic rules

2. **Classifica Finale (`index.html`)**: Main scoreboard featuring:
   - Desktop view: Full table with all team scores across 14 bars + bonus
   - Mobile view: Two modes - Card-based team view and Per-Bar view
   - Google Sheets integration via JSONP for live data sync
   - Real-time score calculation and ranking
   - Export functionality
   - LocalStorage persistence

3. **Hub Capitani (`hub-capitani.html`)**: Team management dashboard with:
   - Desktop and mobile responsive versions
   - Team creation and member management (1-8 members)
   - Challenge logging system
   - Captain action buttons (Rally, Strategy Meeting, Victory Horn)
   - Team statistics (morale, energy, strategy meters)
   - Strategy notes with auto-save
   - LocalStorage persistence

### Data Storage

**LocalStorage Keys:**
- `olimpiadiClassifica` - Scoreboard data (team names, scores, rankings)
- `olympicCaptainHubData` - Captain hub data (team info, members, challenges, stats)

**Data Structure (Captain Hub):**
```javascript
{
  teamName: string,
  captainName: string,
  members: Array<{name, isCaptain, joinedAt}>,
  challenges: Array<{id, barName, challengeType, pointsEarned, notes, completedAt}>,
  strategyNotes: string,
  stats: {
    challengesCompleted: number,
    totalPoints: number,
    barsVisited: number,
    morale: number (0-100),
    energy: number (0-100),
    strategy: number (0-100)
  }
}
```

### The 14 Temples (Bars)

- Caligo
- Casa Gotuzzo
- Cereria
- Circolo Sport
- Excalibur
- Le Fontane
- Loomi
- Mary Jo
- Storico
- Teleria 108
- Tirebouchon
- Vinoria
- Vinoteca
- Vitae

Each temple has one challenge, and teams earn points (0-100) for completing challenges.

## Google Sheets Integration

The scoreboard (`index.html`) integrates with Google Sheets using **JSONP technique** to avoid CORS issues:

1. User provides Google Sheet ID via prompt
2. App creates script tag: `https://docs.google.com/spreadsheets/d/{sheetId}/gviz/tq?tqx=out:json`
3. Google Sheets calls `window.google.visualization.Query.setResponse()`
4. Data is parsed and loaded into the scoreboard

**Required Google Sheets Structure:**
```
SQUADRA | CALIGO | CASA GOTUZZO | CERERIA | ... | VITAE | BONUS
```

Sheet must be publicly accessible: "Anyone with the link can view"

## Development Workflow

### Local Development

Since this is a static HTML/CSS/JS application with no build process:

```bash
# Serve with Python
python3 -m http.server 8000

# Or with Node.js
npx serve .

# Then open
http://localhost:8000/index.html         # Scoreboard
http://localhost:8000/hub-capitani.html  # Captain Hub
http://localhost:8000/homepage.html      # Homepage
```

### Testing Approach

- **Manual testing** in browser (Chrome/Firefox/Safari recommended)
- Test responsive breakpoint at **768px** (mobile vs desktop views)
- Test LocalStorage persistence across page reloads
- Test Google Sheets integration with public sheets

### Making Changes

1. **Read the code first** - Understand existing patterns before modifying
2. **Maintain the Greek aesthetic** - Gold/bronze colors (#daa520, #ffd700, #b8860b)
3. **Test responsive design** - Desktop and mobile views are significantly different
4. **Preserve LocalStorage structure** - Breaking changes will lose user data

## Code Style and Patterns

### JavaScript Patterns

- **ES6+ syntax**: Classes, arrow functions, template literals
- **Object-oriented**: `OlympicCaptainHub` class for captain hub logic
- **Functional**: Standalone functions for scoreboard logic
- **Event-driven**: DOM event listeners for user interactions

### Naming Conventions

- **camelCase**: Variables and functions (`teamData`, `calculateScores`)
- **kebab-case**: CSS classes and IDs (`team-card`, `score-table`)
- **UPPER_SNAKE_CASE**: Constants (`BAR_NAMES`)

### Responsive Design

Mobile view activates at **max-width: 768px** via media queries:
- Desktop: Full table layout
- Mobile: Card-based layouts with tabs/sections

### Data Persistence

- **Auto-save**: Changes are immediately saved to LocalStorage
- **No backend**: All data is client-side only
- **Export/Import**: JSON export available for backups

## Key Features and Implementation Details

### Captain Hub State Management

The hub tracks three key stats that change based on actions:
- **Morale** (0-100): Increases with victories, decreases with losses
- **Energy** (0-100): Decreases with challenges, restored with captain actions
- **Strategy** (0-100): Increases with experience and strategy meetings

### Score Calculation Algorithm

Scores are calculated by:
1. Summing all bar scores (0-100 each) + bonus
2. Sorting teams by total score descending
3. Assigning ranks (#1, #2, etc.)

### Mobile View Modes

**Classifica Finale** has two mobile tabs:
1. **🏆 Classifica**: Searchable card view of all teams
2. **🏛️ Per Bar**: Filter by specific bar to see all team scores for that location

### Animation and UX

- Number animations using `requestAnimationFrame`
- Status bar transitions (0.8s ease)
- Greek vase flame animation using CSS keyframes
- Collapsible sections with smooth transitions

## Developer Console Commands

Hidden developer commands available in browser console:

```javascript
// Captain Hub commands
olympicCommands.reset()           // Reset all data
olympicCommands.export()          // Export backup
olympicCommands.summary()         // Show team summary
olympicCommands.addPoints(n)      // Add points
olympicCommands.setMorale(n)      // Set morale (0-100)
olympicCommands.setEnergy(n)      // Set energy (0-100)
olympicCommands.help()            // Show help

// Keyboard shortcuts
Ctrl+Alt+R  // Reset data
Ctrl+Alt+E  // Export backup
Ctrl+Alt+S  // Show summary in console
Esc         // Close modals
```

## Common Issues and Solutions

### Google Sheets Not Loading

- Verify sheet is set to "Anyone with the link can view"
- Check that Sheet ID (from URL) is correct
- Ensure internet connection is active
- Check browser console for JSONP errors

### LocalStorage Data Loss

- Browser privacy mode may block LocalStorage
- Browser cache clearing will delete data
- Use export functionality to create backups

### Responsive Layout Issues

- Mobile view only activates at exactly ≤768px width
- Test on actual devices, not just browser resize
- Greek vase aesthetic requires specific font loading (Cinzel, JetBrains Mono from Google Fonts CDN)

### Score Sync Between Pages

- Scoreboard and Captain Hub use **separate LocalStorage keys**
- They are **independent systems** - scores in captain hub don't auto-sync to main scoreboard
- Main scoreboard is designed for manual entry or Google Sheets import

## Design System

### Color Palette (Greek/Olympic Theme)

- **Primary Gold**: `#daa520` (goldenrod)
- **Light Gold**: `#ffd700` (gold)
- **Dark Gold/Bronze**: `#b8860b` (darkgoldenrod)
- **Cream/Ivory**: `#fff8dc`, `#ffefd5` (backgrounds)
- **Dark Text**: `#2c3e50`

### Typography

- **Headings**: Cinzel (serif, classical Greek style)
- **Body/UI**: JetBrains Mono (monospace, modern)
- **Letter spacing**: Generous for Olympic feel

### Layout Principles

- **Card-based design** on mobile
- **Table-based** on desktop for scoreboard
- **Grid layouts** for stats and members
- **Linear gradients** for buttons and headers
- **Box shadows** for depth

## Important Notes

- **No server required** - Pure client-side application
- **No dependencies** - No npm packages, build tools, or frameworks
- **No authentication** - All data is local and public
- **Italian language** - UI text is in Italian
- **Event-specific** - Designed for Olimpiadi Goliardiche 2025 event in Chiavari, Italy

## Future Enhancement Considerations

If extending this application:

1. **Backend sync**: Could add server-side storage for multi-device sync
2. **Real-time updates**: WebSockets for live score updates
3. **Authentication**: Team login system for secure data
4. **Mobile app**: PWA conversion with service workers
5. **Analytics**: Track most visited bars, popular challenges
6. **Social features**: Team messaging, challenge photos

However, the current design intentionally keeps it simple and dependency-free for easy deployment and maintenance.
