# Leave Tracker - Backend

FastAPI backend for leave management system with AI-powered natural language parsing.

## Setup

```bash
cd backend
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
python seed.py
python -m uvicorn main:app --reload
```

API runs at: http://localhost:8000
Docs: http://localhost:8000/docs

## Project Structure
backend/
├── main.py                 # FastAPI app, routes
├── database.py             # SQLAlchemy setup
├── seed.py                 # Seed 20 users + 22 requests
├── utils.py                # Helper functions (working days calc)
├── models/
│   ├── models.py           # User, LeaveBalance, LeaveRequest
├── routes/
│   ├── auth.py             # Login, register
│   ├── leaves.py           # Apply, balance, history, calendar
│   ├── manager.py          # Approve, reject
│   ├── ai_assistant.py     # AI endpoints
├── services/
│   ├── ai_service.py       # Groq integration
└── requirements.txt        # Dependencies

## Database Schema

### Users Table
```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY,
    username VARCHAR UNIQUE,
    email VARCHAR UNIQUE,
    hashed_password VARCHAR,
    full_name VARCHAR,
    is_manager BOOLEAN DEFAULT FALSE,
    created_at DATETIME
);
```

### LeaveBalance Table
```sql
CREATE TABLE leave_balances (
    id INTEGER PRIMARY KEY,
    user_id INTEGER FOREIGN KEY,
    leave_type ENUM(Sick, Casual, WFH, Comp-off),
    yearly_quota INTEGER DEFAULT 10,
    used INTEGER DEFAULT 0
);
```

### LeaveRequest Table
```sql
CREATE TABLE leave_requests (
    id INTEGER PRIMARY KEY,
    applicant_id INTEGER FOREIGN KEY,
    leave_type ENUM(Sick, Casual, WFH, Comp-off),
    start_date DATE,
    end_date DATE,
    reason TEXT,
    working_days INTEGER,
    status ENUM(Pending, Approved, Rejected),
    manager_id INTEGER FOREIGN KEY,
    approved_by_id INTEGER FOREIGN KEY (nullable),
    manager_comment TEXT (nullable),
    created_at DATETIME,
    decided_at DATETIME (nullable)
);
```

## API Endpoints

### Auth
- `POST /api/auth/login?username=X&password=Y` - Login user
- `POST /api/auth/register` - Register new user
- `GET /api/auth/managers` - List all managers

### Leaves
- `POST /api/leaves/apply` - Apply for leave
- `GET /api/leaves/balance/{user_id}` - Get leave balance
- `GET /api/leaves/history/{user_id}` - Get history (filterable by status)
- `GET /api/leaves/team-calendar` - Get team leaves (this week + next)

### Manager
- `GET /api/manager/pending/{manager_id}` - Pending requests
- `GET /api/manager/all/{manager_id}` - All requests
- `POST /api/manager/approve/{request_id}?comment=X` - Approve
- `POST /api/manager/reject/{request_id}?comment=X` - Reject

### AI
- `POST /api/ai/parse-leave?user_input=X&user_id=Y` - Parse natural language
- `GET /api/ai/team-insights/{manager_id}` - AI summary of team leaves

## Example Requests

**Login**
```bash
curl -X POST "http://localhost:8000/api/auth/login?username=john_smith&password=password123"
```

**Apply Leave**
```bash
curl -X POST "http://localhost:8000/api/leaves/apply" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "user_id=4&leave_type=Casual&start_date=2025-01-20&end_date=2025-01-21&reason=Family function&manager_id=1"
```

**Parse with AI**
```bash
curl -X POST "http://localhost:8000/api/ai/parse-leave?user_input=I%20need%20Monday%20off&user_id=4"
```

## Key Features

- ✅ Working days calculation (Mon-Fri only, excludes holidays)
- ✅ AI natural language parsing (Groq Llama 3.3 70B)
- ✅ Server-side validation (never trust client)
- ✅ Manager approval workflow with comments
- ✅ Team calendar view
- ✅ Leave balance tracking per type
- ✅ Holiday calendar integration

## Tech Stack

- **Framework**: FastAPI
- **Database**: SQLite (SQLAlchemy ORM)
- **Auth**: Simple hash (demo only, use bcrypt in production)
- **AI**: Groq API (Llama 3.3 70B)
- **Validation**: Pydantic

## Environment Variables
GROQ_API_KEY=your_key_here
SECRET_KEY=your_secret
DATABASE_URL=sqlite:///./leave_tracker.db

## Testing

**Login as employee**:
- Username: john_smith
- Password: password123

**Login as manager**:
- Username: alice_manager
- Password: password123

## Seed Data

- 20 users (3 managers + 17 employees)
- 22 realistic leave requests (Pending/Approved/Rejected)
- Leave balances for all 4 types

Run: `python seed.py`

## Future Improvements

- Email notifications
- PostgreSQL for production
- Unit tests
- Rate limiting
- Request pagination
- Audit logs
