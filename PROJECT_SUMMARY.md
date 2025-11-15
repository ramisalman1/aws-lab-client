# Todo Application - Complete Project Structure

## 📦 What You Have

### Backend (todo-app)
- Express.js server
- PostgreSQL integration
- REST API with CRUD operations
- Configurable via `.env`

### Frontend (todo-frontend-simple)
- Express.js static server
- Single HTML file (no build tools, no webpack)
- Vanilla JavaScript
- Configurable backend URL via `.env`

## 🚀 Quick Start

### 1. Terminal 1 - Start Backend

```bash
cd todo-app
npm install
npm start
```

Expected: Backend runs on `http://localhost:5000`

### 2. Terminal 2 - Start Frontend

```bash
cd todo-frontend-simple
npm install
npm start
```

Expected: Frontend runs on `http://localhost:3000`

### 3. Open Browser

Visit: `http://localhost:3000`

## 📁 File Structure

```
your-project/
│
├── todo-app/                           # Backend
│   ├── config/
│   │   └── db.js                       # PostgreSQL connection
│   ├── db/
│   │   └── queries.js                  # Database queries
│   ├── controllers/
│   │   └── todoController.js           # Request handlers
│   ├── routes/
│   │   └── todoRoutes.js               # API routes
│   ├── database/
│   │   └── schema.sql                  # Database schema
│   ├── server.js                       # Main server
│   ├── package.json
│   ├── .env                            # Configuration
│   ├── .env.example
│   └── README.md
│
└── todo-frontend-simple/               # Frontend
    ├── public/
    │   └── index.html                  # Single page app
    ├── server.js                       # Express server
    ├── package.json
    ├── .env                            # Configuration
    ├── .env.example
    ├── README.md
    ├── QUICK_START.md
    └── CONFIGURATION.md
```

## ⚙️ Configuration

### Backend .env

```env
NODE_ENV=development
PORT=5000
HOST=localhost

DB_USER=postgres
DB_PASSWORD=your_password
DB_HOST=localhost
DB_PORT=5432
DB_DATABASE=crud
```

### Frontend .env

```env
FRONTEND_PORT=3000
NODE_ENV=development
BACKEND_URL=http://localhost:5000/api
```

## 🔑 Key Features

✅ **No Build Tools** - Frontend is a single HTML file
✅ **No Webpack Errors** - Uses vanilla JavaScript
✅ **Configurable Backend** - Change `BACKEND_URL` via `.env`
✅ **Production Ready** - Can be deployed to AWS EC2
✅ **CRUD Operations** - Add, read, update, delete todos
✅ **Beautiful UI** - Responsive and modern design
✅ **Error Handling** - Comprehensive error messages
✅ **Loading States** - Visual feedback for operations

## 🌐 API Endpoints

All relative to `BACKEND_URL` (e.g., `http://localhost:5000/api`)

```
GET    /todos          # Get all todos
POST   /todos          # Create todo
PUT    /todos/:id      # Update todo
DELETE /todos/:id      # Delete single todo
DELETE /todos          # Delete all todos
```

## 🗄️ Database Setup

### PostgreSQL Table

```sql
CREATE TABLE todo (
  id SERIAL PRIMARY KEY,
  newitem VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## 🔍 How Backend URL Configuration Works

1. **Frontend .env** contains `BACKEND_URL`
2. **Frontend server** reads `.env` file
3. **Frontend exposes** `/api/config` endpoint with backend URL
4. **Frontend HTML** fetches `/api/config` on page load
5. **JavaScript** uses this URL for all API calls

### Example Flow

```
User opens http://localhost:3000
    ↓
Frontend loads index.html
    ↓
JavaScript calls /api/config
    ↓
Server responds with: { backendUrl: "http://localhost:5000/api" }
    ↓
JavaScript uses this URL for todos API calls
```

## 🚢 Deployment Scenarios

### Local Development
```env
# Frontend
BACKEND_URL=http://localhost:5000/api

# Backend
DB_HOST=localhost
```

### AWS EC2 (Single Instance)
```env
# Frontend
BACKEND_URL=http://localhost:5000/api
# Or with public IP:
# BACKEND_URL=http://your-ec2-public-ip:5000/api

# Backend
DB_HOST=your-rds-endpoint.rds.amazonaws.com
HOST=0.0.0.0
```

### AWS EC2 with Domain
```env
# Frontend
BACKEND_URL=https://api.your-domain.com/api

# Backend
DB_HOST=your-rds-endpoint.rds.amazonaws.com
HOST=0.0.0.0
```

## 📊 Environment Variables

### Backend
| Variable | Purpose |
|----------|---------|
| `NODE_ENV` | Environment (development/production) |
| `PORT` | Server port |
| `HOST` | Bind address |
| `DB_USER` | Database username |
| `DB_PASSWORD` | Database password |
| `DB_HOST` | Database server |
| `DB_PORT` | Database port |
| `DB_DATABASE` | Database name |

### Frontend
| Variable | Purpose |
|----------|---------|
| `FRONTEND_PORT` | Server port |
| `NODE_ENV` | Environment |
| `BACKEND_URL` | Backend API URL |

## ✅ Verification Checklist

Before starting:
- [ ] Node.js installed
- [ ] PostgreSQL accessible (or RDS)
- [ ] Backend `.env` has database credentials
- [ ] Frontend `.env` has backend URL
- [ ] Ports 5000 and 3000 are available (or change in `.env`)

## 🐛 Troubleshooting

### Backend won't connect to database
```bash
# Check .env
cat todo-app/.env

# Verify database
psql -h your-db-host -U postgres -d crud -c "SELECT 1"
```

### Frontend can't connect to backend
```bash
# Check frontend .env
cat todo-frontend-simple/.env

# Test backend
curl http://localhost:5000/api/todos

# Check browser console (F12)
```

### Port already in use
```bash
# Change in .env or kill process
# Backend: Change PORT in todo-app/.env
# Frontend: Change FRONTEND_PORT in todo-frontend-simple/.env
```

## 📚 Documentation Files

- **README.md** (both) - Complete documentation
- **QUICK_START.md** - Step-by-step setup
- **CONFIGURATION.md** - Detailed configuration guide
- **CORS_SETUP.js** - CORS configuration reference

## 🎯 Next Steps

1. Start backend: `cd todo-app && npm install && npm start`
2. Start frontend: `cd todo-frontend-simple && npm install && npm start`
3. Open: `http://localhost:3000`
4. Test the app
5. Deploy to AWS (optional)

## 💾 Database Schema

```sql
CREATE TABLE todo (
  id SERIAL PRIMARY KEY,
  newitem VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_todo_created_at ON todo(created_at DESC);
```

## 🔐 Security Notes

⚠️ **For Production:**
- Use strong database passwords
- Enable HTTPS/SSL
- Configure security groups properly
- Use environment variables for secrets
- Never commit `.env` files to git
- Consider API rate limiting
- Add authentication if needed

## 📦 Dependencies

### Backend
- `express` - Web framework
- `pg-promise` - PostgreSQL driver
- `dotenv` - Environment variables
- `cors` - CORS support

### Frontend
- `express` - Web framework
- `cors` - CORS support
- `dotenv` - Environment variables

## 🎓 Learning Resources

### How It Works
1. Frontend static server serves HTML
2. HTML loads and makes config request
3. Server returns backend URL from `.env`
4. JavaScript uses this URL for API calls
5. Backend handles CRUD operations
6. Database stores data

### Key Concepts
- **Express.js** - Web framework
- **PostgreSQL** - Database
- **REST API** - Communication protocol
- **Environment Variables** - Configuration management
- **CORS** - Cross-origin requests
- **Static Files** - Serving HTML/CSS/JS

## 🚀 Performance

- Frontend: Single HTML file (no build overhead)
- Backend: Optimized queries
- Database: Indexed for fast lookups
- No webpack/build delays
- Fast startup times

## 📞 Support

### Check Logs
```bash
# Backend logs
cd todo-app && npm start

# Frontend logs
cd todo-frontend-simple && npm start
```

### Test Endpoints
```bash
# Backend
curl http://localhost:5000/health
curl http://localhost:5000/api/todos

# Frontend
curl http://localhost:3000/health
curl http://localhost:3000/api/config
```

### Debug Browser
Open DevTools (F12):
- **Console** - JavaScript errors
- **Network** - API requests
- **Application** - Local storage/cookies

---

**Ready to go!** 🚀

Start with:
```bash
# Terminal 1
cd todo-app && npm install && npm start

# Terminal 2
cd todo-frontend-simple && npm install && npm start

# Browser
http://localhost:3000
```

Enjoy your Todo App! 📝
