# Quick Start Guide

## System Architecture

```
┌─────────────────────────────────────────────────┐
│                                                 │
│         Frontend (Express Server)               │
│         http://localhost:3000                   │
│                                                 │
│  ┌──────────────────────────────────────────┐  │
│  │  HTML + Vanilla JavaScript               │  │
│  │  - Add/Edit/Delete Todos                 │  │
│  │  - Beautiful UI                          │  │
│  │  - Responsive Design                     │  │
│  └──────────────────────────────────────────┘  │
│                      ↓                          │
│              .env configured                   │
│          BACKEND_URL variable                  │
│                                                 │
└──────────────────┬───────────────────────────────┘
                   │ HTTP Requests
                   ↓
┌─────────────────────────────────────────────────┐
│                                                 │
│         Backend (Express Server)                │
│         http://localhost:5000                   │
│                                                 │
│  ┌──────────────────────────────────────────┐  │
│  │  Express REST API                        │  │
│  │  - GET /api/todos                        │  │
│  │  - POST /api/todos                       │  │
│  │  - PUT /api/todos/:id                    │  │
│  │  - DELETE /api/todos/:id                 │  │
│  └──────────────────────────────────────────┘  │
│                      ↓                          │
│         PostgreSQL Connection                  │
│                                                 │
└──────────────────┬───────────────────────────────┘
                   │
                   ↓
        ┌──────────────────────┐
        │   PostgreSQL (RDS)   │
        │                      │
        │  - todo table        │
        │  - Data persistence  │
        └──────────────────────┘
```

## Installation Steps

### Step 1: Backend Setup (Terminal 1)

```bash
# Navigate to backend directory
cd todo-app

# Install dependencies
npm install

# Create .env file with your database credentials
cat > .env << EOF
NODE_ENV=development
PORT=5000
HOST=0.0.0.0

DB_USER=postgres
DB_PASSWORD=your_password
DB_HOST=your-rds-endpoint.rds.amazonaws.com
DB_PORT=5432
DB_DATABASE=crud
EOF

# Start backend
npm start
```

Expected output:
```
Server running on http://0.0.0.0:5000
Environment: development
```

### Step 2: Frontend Setup (Terminal 2)

```bash
# Navigate to frontend directory
cd todo-frontend-simple

# Install dependencies
npm install

# Create .env file
cat > .env << EOF
FRONTEND_PORT=3000
NODE_ENV=development
BACKEND_URL=http://localhost:5000/api
EOF

# Start frontend
npm start
```

Expected output:
```
✅ Frontend server running on http://localhost:3000
🔗 Backend API: http://localhost:5000/api
📝 Environment: development
```

### Step 3: Test the Application

1. Open browser: `http://localhost:3000`
2. Add a todo
3. Edit a todo
4. Delete a todo
5. Clear all todos

## Backend Configuration Examples

### Local Development
```env
DB_HOST=localhost
DB_PORT=5432
```

### AWS RDS
```env
DB_HOST=todo-db.c9akciq32.us-east-1.rds.amazonaws.com
DB_PORT=5432
```

### EC2 to RDS (Production)
```env
NODE_ENV=production
PORT=5000
HOST=0.0.0.0
DB_HOST=todo-db.c9akciq32.us-east-1.rds.amazonaws.com
DB_USER=postgres
DB_PASSWORD=your_secure_password
DB_DATABASE=crud
```

## Frontend Configuration Examples

### Local Development
```env
FRONTEND_PORT=3000
BACKEND_URL=http://localhost:5000/api
```

### EC2 with Public IP
```env
FRONTEND_PORT=3000
BACKEND_URL=http://your-ec2-public-ip:5000/api
```

### Production with Domain
```env
FRONTEND_PORT=3000
BACKEND_URL=https://api.your-domain.com/api
```

### Custom Ports
```env
FRONTEND_PORT=8080
BACKEND_URL=http://localhost:9000/api
```

## Troubleshooting

### Backend won't start

**Error: "Cannot find module"**
```bash
cd todo-app
rm -rf node_modules package-lock.json
npm install
npm start
```

**Error: Database connection failed**
```bash
# Check your .env credentials
cat .env

# Test connection with psql
psql -h your-rds-endpoint.rds.amazonaws.com -U postgres -d crud -c "SELECT 1"
```

### Frontend won't start

**Error: Port 3000 in use**
```bash
# Change port in .env
FRONTEND_PORT=3001

# Or kill process
# On Mac/Linux
lsof -i :3000
kill -9 <PID>

# On Windows
netstat -ano | findstr :3000
taskkill /PID <PID> /F
```

### Cannot connect to backend from frontend

**Check browser console (F12):**
- Look for CORS errors
- Check network requests
- Verify `BACKEND_URL` in console

**Solution:**
```bash
# Update frontend .env with correct backend URL
BACKEND_URL=http://localhost:5000/api

# Restart frontend
npm start
```

### Todos not displaying

**Step 1:** Check backend is running
```bash
curl http://localhost:5000/api/todos
```

**Step 2:** Check frontend .env
```bash
cat todo-frontend-simple/.env
```

**Step 3:** Check browser console for errors (F12)

**Step 4:** Check database table exists
```bash
psql -h your-rds-endpoint -U postgres -d crud -c "SELECT * FROM todo LIMIT 1"
```

## Useful Commands

### Backend

```bash
# Start backend
npm start

# Development with auto-reload
npm run dev

# Test API endpoint
curl http://localhost:5000/api/todos

# Check health
curl http://localhost:5000/health
```

### Frontend

```bash
# Start frontend
npm start

# Check frontend health
curl http://localhost:3000/health

# Get backend config from frontend
curl http://localhost:3000/api/config
```

### Database

```bash
# Connect to database
psql -h your-rds-endpoint -U postgres -d crud

# View all todos
SELECT * FROM todo;

# Delete all todos
DELETE FROM todo;

# Count todos
SELECT COUNT(*) FROM todo;
```

## Environment Variables Explained

### Backend (.env)

| Variable | Purpose | Example |
|----------|---------|---------|
| `NODE_ENV` | Environment mode | `development` or `production` |
| `PORT` | Backend server port | `5000` |
| `HOST` | Server bind address | `localhost` or `0.0.0.0` |
| `DB_USER` | Database username | `postgres` |
| `DB_PASSWORD` | Database password | `your_password` |
| `DB_HOST` | Database server address | `localhost` or RDS endpoint |
| `DB_PORT` | Database port | `5432` |
| `DB_DATABASE` | Database name | `crud` |

### Frontend (.env)

| Variable | Purpose | Example |
|----------|---------|---------|
| `FRONTEND_PORT` | Frontend server port | `3000` |
| `NODE_ENV` | Environment mode | `development` or `production` |
| `BACKEND_URL` | Backend API URL | `http://localhost:5000/api` |

## File Locations

### Backend
```
todo-app/
├── .env                    # Configuration
├── server.js              # Main server
├── config/db.js           # Database connection
├── db/queries.js          # SQL queries
├── controllers/           # Request handlers
├── routes/                # API routes
└── database/schema.sql    # Database schema
```

### Frontend
```
todo-frontend-simple/
├── .env                   # Configuration
├── server.js             # Express server
├── public/
│   └── index.html        # Single page app
└── package.json          # Dependencies
```

## Production Deployment

### On AWS EC2

1. **Backend:**
```bash
cd todo-app
npm install
# Update .env with production values
npm start &  # Run in background
# Or use PM2: pm2 start server.js
```

2. **Frontend:**
```bash
cd todo-frontend-simple
npm install
# Update .env with EC2 public IP
npm start &  # Run in background
# Or use PM2: pm2 start server.js
```

3. **Access:**
- Frontend: `http://your-ec2-public-ip:3000`
- Backend: `http://your-ec2-public-ip:5000`

## Security Checklist

- [ ] Database password is strong
- [ ] EC2 security groups configured correctly
- [ ] RDS security groups allow EC2 connection
- [ ] Backend CORS is configured
- [ ] HTTPS enabled (production)
- [ ] Environment variables not committed to git
- [ ] .gitignore includes .env files

## Next Steps

1. ✅ Start backend
2. ✅ Start frontend
3. ✅ Test application
4. ✅ Deploy to AWS EC2 (optional)
5. ✅ Configure domain (optional)

Enjoy your Todo App! 🚀
