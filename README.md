# Leave Tracker - Frontend

React frontend for leave management system. Single-file HTML app with no build step.

## Setup

Simply open `index.html` in a browser. No installation required.

If using a local server:
```bash
cd frontend
python -m http.server 3000
```

Open: http://localhost:3000

## Project Structure
frontend/
├── index.html          # Entire React app (no build step)
│   ├── Styles          # Tailwind CSS + custom styles
│   ├── React Components (inline)
│   │   ├── LoginScreen
│   │   ├── EmployeeDashboard
│   │   ├── ManagerDashboard
│   └── API calls       # Fetch to FastAPI backend

## Features

### Employee Dashboard
- **Balance Tab**: View leave balance for all 4 types
- **Apply Leave Tab**: 
  - AI Natural Language Parser ("I need Monday off" → auto-fills)
  - Manual form (Leave type, dates, reason, manager)
  - Submit to manager
- **History Tab**: View past/pending/approved leaves with status

### Manager Dashboard
- **Pending Tab**: Review pending requests, approve/reject with comments
- **Team Calendar Tab**: See who's off this week/next week
- **AI Insights Tab**: AI-generated summary of team availability

## API Calls

All calls to: `http://localhost:8000/api`

**Login**
```javascript
fetch(`${API}/auth/login?username=X&password=Y`, { method: "POST" })
```

**Apply Leave**
```javascript
fetch(`${API}/leaves/apply`, {
    method: "POST",
    headers: { "Content-Type": "application/x-www-form-urlencoded" },
    body: `user_id=4&leave_type=Casual&start_date=2025-01-20&end_date=2025-01-21&reason=Family&manager_id=1`
})
```

**Parse with AI**
```javascript
fetch(`${API}/ai/parse-leave?user_input=I need Monday off&user_id=4`, { method: "POST" })
```

**Approve Request**
```javascript
fetch(`${API}/manager/approve/5?manager_id=1&comment=Approved`, { method: "POST" })
```

## Tech Stack

- **Framework**: React 18 (via CDN, no build)
- **Styling**: Tailwind CSS (CDN)
- **State**: JavaScript objects + function re-renders
- **API**: Fetch API (no axios)

## Design Decisions

**Why single file?**
- No build step needed
- Easy to demo locally
- Works instantly in any browser
- Minimal setup

**Why no state management (Redux, Context)?**
- 4-hour constraint
- Simple data flow
- App-level state in `appState` object

**Why Tailwind CDN?**
- Instant styling
- No CSS files to manage
- Responsive design built-in

## Styling

Custom classes:
- `.balance-card` - Gradient blue cards for balance display
- `.request-card` - Left-border cards for requests
- `.tab-active` / `.tab-inactive` - Tab styling
- `.input` - Form input styling

Tailwind utilities for responsive design (md:, lg: breakpoints).

## Error Handling

- **Login fails**: Show error message
- **AI parsing fails**: Show error, user fills manually
- **Submit fails**: Show error, user retries
- **API down**: Network error message

## Testing

**Test as Employee**:
1. Login: john_smith / password123
2. View balance
3. Apply leave using AI parser ("I need Monday off")
4. Submit
5. View in History

**Test as Manager**:
1. Login: alice_manager / password123
2. View pending requests
3. Add comment, approve/reject
4. View team calendar
5. View AI insights

## Responsive Design

Works on:
- ✅ Desktop (1920px+)
- ✅ Tablet (768px - 1024px)
- ✅ Mobile (320px - 767px)

Grid layout adjusts: `grid-cols-1 md:grid-cols-2 lg:grid-cols-4`

## Future Improvements

- Dark mode toggle
- Email notifications
- CSV export
- Streaming AI responses (show tokens as they arrive)
- Advanced filters (date range, leave type)
- Bulk approve/reject
