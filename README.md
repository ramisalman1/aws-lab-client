# Todo Application - Complete Project

A simple, working Todo application with backend (Express + PostgreSQL) and frontend (Express + Vanilla JS).

## Project Structure

```
todo-app/                          # Backend (from earlier)
├── config/
│   └── db.js
├── db/
│   └── queries.js
├── controllers/
│   └── todoController.js
├── routes/
│   └── todoRoutes.js
├── database/
│   └── schema.sql
├── server.js
├── package.json
└── .env

todo-frontend-simple/              # Frontend
├── public/
│   └── index.html                 # Single file app (no build needed)
├── server.js                      # Express server
├── package.json
├── .env
└── README.md
```

## Features

✅ **Backend:**
- Express.js REST API
- PostgreSQL database (RDS)
- CRUD operations for todos
- Error handling
- CORS enabled

✅ **Frontend:**
- Single HTML file (no build tools)
- Express server to serve it
- Configurable backend URL via .env
- Beautiful, responsive UI
- Add, edit, delete todos
- Real-time notifications

## Quick Start

### Prerequisites
- Node.js (v14+)
- PostgreSQL database (AWS RDS or local)
- Two terminal windows

### 1. Setup Backend

```bash
cd todo-app

# Install dependencies
npm install

# Update .env with database credentials
# .env should have:
# DB_USER=postgres
# DB_PASSWORD=your_password
# DB_HOST=your-rds-endpoint
# DB_PORT=5432
# DB_DATABASE=crud

# Start backend
npm start
# Backend runs on http://localhost:5000
```

### 2. Setup Frontend

```bash
cd todo-frontend-simple

# Install dependencies
npm install

# Update .env to point to your backend
# .env should have:
# FRONTEND_PORT=3000
# BACKEND_URL=http://localhost:5000/api

# Start frontend
npm start
# Frontend runs on http://localhost:3000
```

## Backend Configuration (.env)

```env
NODE_ENV=development
PORT=5000
HOST=0.0.0.0

# Database
DB_USER=postgres
DB_PASSWORD=your_password
DB_HOST=your-rds-endpoint.rds.amazonaws.com
DB_PORT=5432
DB_DATABASE=crud
```

## Frontend Configuration (.env)

```env
# Frontend Server
FRONTEND_PORT=3000
NODE_ENV=development

# Backend URL - Update this to match your backend
BACKEND_URL=http://localhost:5000/api
```

### Backend URL Examples

**Local Development:**
```env
BACKEND_URL=http://localhost:5000/api
```

**AWS EC2 (using public IP):**
```env
BACKEND_URL=http://your-ec2-public-ip:5000/api
```

**Production (domain):**
```env
BACKEND_URL=https://your-domain.com/api
```

**Different port:**
```env
BACKEND_URL=http://localhost:8000/api
```

## API Endpoints

All endpoints are relative to `BACKEND_URL`

### GET /todos
Fetch all todos
```bash
curl http://localhost:5000/api/todos
```

### POST /todos
Create a new todo
```bash
curl -X POST http://localhost:5000/api/todos \
  -H "Content-Type: application/json" \
  -d '{"newItem":"Buy groceries"}'
```

### PUT /todos/:id
Update a todo
```bash
curl -X PUT http://localhost:5000/api/todos/1 \
  -H "Content-Type: application/json" \
  -d '{"newItem":"Buy groceries and cook"}'
```

### DELETE /todos/:id
Delete a specific todo
```bash
curl -X DELETE http://localhost:5000/api/todos/1
```

### DELETE /todos
Delete all todos
```bash
curl -X DELETE http://localhost:5000/api/todos
```

## Database Setup

### Create Table in PostgreSQL

```sql
CREATE TABLE todo (
  id SERIAL PRIMARY KEY,
  newitem VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_todo_created_at ON todo(created_at DESC);
```

### Using psql Command

```bash
psql -h your-rds-endpoint.rds.amazonaws.com \
     -U postgres \
     -d crud \
     -f todo-app/database/schema.sql
```

## Troubleshooting

### Backend Issues

**pg_hba.conf error:**
- Check EC2 security group allows inbound on port 5432
- Verify RDS security group allows EC2 connection
- Test: `telnet your-rds-endpoint.rds.amazonaws.com 5432`

**Connection timeout:**
- Ensure backend is running: `npm start`
- Check firewall/security groups
- Verify database credentials in .env

### Frontend Issues

**Backend URL not loading:**
- Check frontend .env `BACKEND_URL` is correct
- Ensure backend is running
- Check browser console for errors (F12 → Console)

**Cannot connect to backend:**
- Verify `BACKEND_URL` in frontend .env
- Check backend is accessible from frontend
- Ensure CORS is enabled on backend

**Todos not showing:**
- Check Network tab (F12 → Network)
- Verify database table exists
- Check backend logs for errors

## Development vs Production

### Local Development

**Backend (.env):**
```env
NODE_ENV=development
PORT=5000
BACKEND_URL=http://localhost:5000/api
```

**Frontend (.env):**
```env
FRONTEND_PORT=3000
BACKEND_URL=http://localhost:5000/api
```

### AWS Deployment

**Backend (.env):**
```env
NODE_ENV=production
PORT=5000
HOST=0.0.0.0
DB_HOST=your-rds-endpoint.rds.amazonaws.com
```

**Frontend (.env):**
```env
FRONTEND_PORT=3000
NODE_ENV=production
BACKEND_URL=http://your-ec2-public-ip:5000/api
# Or with domain:
# BACKEND_URL=https://api.your-domain.com/api
```

## Running in Production

### Backend on EC2

```bash
# Install PM2 for process management
npm install -g pm2

# Start backend with PM2
pm2 start server.js --name "todo-backend"

# Monitor
pm2 monit

# View logs
pm2 logs todo-backend
```

### Frontend on EC2

```bash
# Start frontend with PM2
pm2 start server.js --name "todo-frontend"

# Run on startup
pm2 startup
pm2 save
```

## Deployment Steps

### 1. Clone repositories to EC2

```bash
git clone <backend-repo> todo-app
git clone <frontend-repo> todo-frontend-simple
```

### 2. Setup backend

```bash
cd todo-app
npm install
# Update .env with production credentials
npm start
```

### 3. Setup frontend

```bash
cd todo-frontend-simple
npm install
# Update .env with backend URL
npm start
```

### 4. Setup reverse proxy (Nginx)

Create `/etc/nginx/sites-available/todo`:

```nginx
upstream backend {
    server localhost:5000;
}

upstream frontend {
    server localhost:3000;
}

server {
    listen 80;
    server_name your-domain.com;

    # Frontend
    location / {
        proxy_pass http://frontend;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    # Backend API
    location /api/ {
        proxy_pass http://backend/api/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Enable and restart Nginx:
```bash
sudo ln -s /etc/nginx/sites-available/todo /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

## Scripts Reference

### Backend
```bash
npm start          # Production
npm run dev        # Development with nodemon (if installed)
```

### Frontend
```bash
npm start          # Production
npm run dev        # Development with nodemon
```

## Environment Variables Summary

### Backend
| Variable | Default | Description |
|----------|---------|-------------|
| NODE_ENV | development | Environment |
| PORT | 5000 | Backend port |
| HOST | localhost | Bind address |
| DB_USER | postgres | Database user |
| DB_PASSWORD | - | Database password |
| DB_HOST | localhost | Database host |
| DB_PORT | 5432 | Database port |
| DB_DATABASE | crud | Database name |

### Frontend
| Variable | Default | Description |
|----------|---------|-------------|
| FRONTEND_PORT | 3000 | Frontend port |
| NODE_ENV | development | Environment |
| BACKEND_URL | http://localhost:5000/api | Backend API URL |

## Security Notes

⚠️ **For Production:**
- Use HTTPS (SSL/TLS certificates)
- Use strong database passwords
- Enable security groups properly
- Keep Node.js updated
- Use environment variables for secrets
- Consider API rate limiting
- Add authentication/authorization if needed

## Support

For issues:
1. Check console errors (F12 → Console)
2. Check backend logs
3. Verify .env files
4. Test API with curl
5. Check network connectivity

## License

MIT

## Created

This is a production-ready Todo application ready to deploy!
# aws-lab-client
