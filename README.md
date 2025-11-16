# Deployment and DevOps Essentials - Week 7 Assignment

This repository contains a fully deployed MERN stack real-time chat application with CI/CD pipelines, production-ready configurations, and comprehensive monitoring setup.

## Live Application URLs

- **Frontend (Vercel)**: https://deployment-and-devops-essentials-mu.vercel.app
- **Backend (Render)**: https://chatapp1212.onrender.com
- **Database**: MongoDB Atlas (Configured and Connected)

## Table of Contents

1. [Application Overview](#application-overview)
2. [Architecture](#architecture)
3. [Prerequisites](#prerequisites)
4. [Local Development Setup](#local-development-setup)
5. [MongoDB Atlas Setup](#mongodb-atlas-setup)
6. [Backend Deployment (Render)](#backend-deployment-render)
7. [Frontend Deployment (Vercel)](#frontend-deployment-vercel)
8. [CI/CD Pipeline](#cicd-pipeline)
9. [Environment Variables](#environment-variables)
10. [Monitoring and Maintenance](#monitoring-and-maintenance)
11. [Troubleshooting](#troubleshooting)

## Application Overview

A real-time chat application with the following features:
- User authentication (register/login)
- Real-time messaging using Socket.io
- Multiple chat rooms (public and private)
- Private messaging between users
- Message reactions
- Typing indicators
- Online/offline status
- Message persistence with MongoDB

### Tech Stack

**Backend:**
- Node.js & Express.js
- Socket.io for real-time communication
- MongoDB with Mongoose ODM
- Helmet for security headers
- Express rate limiting
- CORS configuration

**Frontend:**
- React 18
- Vite for fast development and optimized builds
- Socket.io client
- Modern CSS with responsive design

## Architecture

```
┌─────────────┐         ┌─────────────┐         ┌─────────────┐
│   Vercel    │────────▶│   Render    │────────▶│   MongoDB   │
│  (Frontend) │         │  (Backend)  │         │    Atlas    │
│   React +   │         │  Express +  │         │             │
│   Vite      │         │  Socket.io  │         │             │
└─────────────┘         └─────────────┘         └─────────────┘
       │                       │
       │                       │
       └───────────────────────┘
         WebSocket Connection
```

## Prerequisites

Before deploying, ensure you have:

- [x] Node.js 18+ installed
- [x] Git installed and configured
- [x] GitHub account
- [x] MongoDB Atlas account (free tier available)
- [x] Render account (linked to GitHub)
- [x] Vercel account (linked to GitHub)

## Local Development Setup

1. **Clone the repository**
   ```bash
   git clone <your-repository-url>
   cd deployment-and-devops-essentials-MUNENE1212
   ```

2. **Install server dependencies**
   ```bash
   cd server
   npm install
   ```

3. **Install client dependencies**
   ```bash
   cd ../client
   npm install
   ```

4. **Configure environment variables**

   Create `server/.env`:
   ```env
   PORT=5000
   CLIENT_URL=http://localhost:5173
   MONGODB_URI=mongodb://localhost:27017/realtime-chat
   NODE_ENV=development
   ```

   Create `client/.env`:
   ```env
   VITE_SOCKET_URL=http://localhost:5000
   ```

5. **Start the development servers**

   Terminal 1 (Backend):
   ```bash
   cd server
   npm run dev
   ```

   Terminal 2 (Frontend):
   ```bash
   cd client
   npm run dev
   ```

6. **Access the application**
   - Frontend: http://localhost:5173
   - Backend: http://localhost:5000

## MongoDB Atlas Setup

### Step 1: Create a MongoDB Atlas Account

1. Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Sign up for a free account
3. Create a new organization (if needed)

### Step 2: Create a Cluster

1. Click "Build a Database"
2. Choose the **FREE** shared tier (M0)
3. Select a cloud provider and region closest to your Render deployment
4. Name your cluster (e.g., "chat-app-cluster")
5. Click "Create Cluster"

### Step 3: Configure Database Access

1. In the left sidebar, click "Database Access"
2. Click "Add New Database User"
3. Choose "Password" authentication
4. Create a username and strong password (save these!)
5. Set "Database User Privileges" to "Read and write to any database"
6. Click "Add User"

### Step 4: Configure Network Access

1. In the left sidebar, click "Network Access"
2. Click "Add IP Address"
3. Click "Allow Access from Anywhere" (0.0.0.0/0)
   - This is necessary for Render and Vercel to connect
4. Click "Confirm"

### Step 5: Get Connection String

1. Click "Database" in the left sidebar
2. Click "Connect" on your cluster
3. Choose "Connect your application"
4. Select "Node.js" and version "4.1 or later"
5. Copy the connection string
6. Replace `<password>` with your database user password
7. Replace `myFirstDatabase` with your desired database name (e.g., `realtime-chat`)

Example:
```
mongodb+srv://username:password@cluster0.xxxxx.mongodb.net/realtime-chat?retryWrites=true&w=majority
```

## Backend Deployment (Render)

### Step 1: Prepare Your Repository

Ensure your code is pushed to GitHub:
```bash
git add .
git commit -m "Prepare for deployment"
git push origin main
```

### Step 2: Deploy to Render

1. Go to [Render](https://render.com)
2. Sign in with your GitHub account
3. Click "New +" and select "Web Service"
4. Connect your GitHub repository
5. Render will detect it's a Node.js application

### Step 3: Configure the Service

Fill in the following settings:

- **Name**: `chat-app-backend` (or your preferred name)
- **Region**: Choose the closest region to you
- **Branch**: `main`
- **Root Directory**: `server`
- **Runtime**: `Node`
- **Build Command**: `npm install`
- **Start Command**: `node server.js`
- **Instance Type**: Select **Free** tier

### Step 4: Add Environment Variables

Scroll down to the "Environment Variables" section and add:

| Key | Value |
|-----|-------|
| `NODE_ENV` | `production` |
| `PORT` | `5000` |
| `MONGODB_URI` | `<your-mongodb-atlas-connection-string>` |
| `CLIENT_URL` | `https://your-vercel-app.vercel.app` |

**Important:** Leave `CLIENT_URL` as a placeholder for now. You'll update it after deploying the frontend.

### Step 5: Deploy

1. Click "Create Web Service"
2. Render will automatically build and deploy your backend
3. Wait for the deployment to complete (5-10 minutes for first deploy)
4. Once deployed, copy your Render URL (e.g., `https://chat-app-backend.onrender.com`)
5. Save this URL - you'll need it for the frontend

### Step 6: Test the Backend

Visit your Render URL in a browser. You should see:
```
Socket.io Chat Server with MongoDB is running
```

Test the health endpoint:
```
https://chat-app-backend.onrender.com/health
```

**Note:** Render's free tier may spin down with inactivity. The first request after inactivity may take 30-60 seconds to respond.

## Frontend Deployment (Vercel)

### Step 1: Deploy to Vercel

1. Go to [Vercel](https://vercel.com)
2. Sign in with your GitHub account
3. Click "Add New Project"
4. Import your GitHub repository
5. Configure the project:

   - **Framework Preset**: Vite
   - **Root Directory**: `client`
   - **Build Command**: `npm run build`
   - **Output Directory**: `dist`

### Step 2: Add Environment Variables

1. In the "Environment Variables" section, add:

```env
VITE_SOCKET_URL=https://your-render-app.onrender.com
```

Replace with your actual Render URL from the backend deployment.

### Step 3: Deploy

1. Click "Deploy"
2. Wait for the deployment to complete
3. Vercel will provide you with a URL (e.g., `https://your-app.vercel.app`)

### Step 4: Update Backend Environment

1. Go back to Render
2. Go to your web service dashboard
3. Click on "Environment" in the left sidebar
4. Update the `CLIENT_URL` environment variable with your Vercel URL:
   ```env
   CLIENT_URL=https://your-app.vercel.app
   ```
5. Render will automatically redeploy

### Step 5: Test the Application

1. Visit your Vercel URL
2. Register a new account
3. Start chatting!
4. Open another browser/incognito window to test real-time features

## CI/CD Pipeline

This project includes three GitHub Actions workflows:

### 1. Backend CI/CD (`backend-ci-cd.yml`)

- **Triggers**: Push or PR to `main`/`develop` affecting `server/` files
- **Jobs**:
  - Installs dependencies
  - Checks for syntax errors
  - Deploys to Render (on main branch)

### 2. Frontend CI/CD (`frontend-ci-cd.yml`)

- **Triggers**: Push or PR to `main`/`develop` affecting `client/` files
- **Jobs**:
  - Installs dependencies
  - Builds the application
  - Verifies build output
  - Deploys to Vercel (on main branch)

### 3. Full Stack CI (`full-stack-ci.yml`)

- **Triggers**: Push or PR to `main`/`develop`
- **Jobs**:
  - Code quality checks
  - Security audits
  - Integration tests

### Viewing CI/CD Status

1. Go to your GitHub repository
2. Click the "Actions" tab
3. View workflow runs and their status
4. Green checkmarks indicate successful builds

### CI/CD Pipeline Screenshots

After deployment, you can view your CI/CD pipeline status:

![GitHub Actions Workflows](./docs/screenshots/github-actions.png)

## Environment Variables

### Server Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `PORT` | Server port | `5000` |
| `NODE_ENV` | Environment | `production` |
| `MONGODB_URI` | MongoDB connection string | `mongodb+srv://...` |
| `CLIENT_URL` | Frontend URL for CORS | `https://app.vercel.app` |

### Client Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `VITE_SOCKET_URL` | Backend WebSocket URL | `https://app.onrender.com` |

## Monitoring and Maintenance

### Health Check Endpoint

The backend includes a health check endpoint at `/health`:

```bash
curl https://your-render-app.onrender.com/health
```

Response:
```json
{
  "status": "healthy",
  "timestamp": "2025-11-16T...",
  "uptime": 12345.67,
  "environment": "production"
}
```

### Monitoring Setup

#### Render Monitoring

1. Go to your Render dashboard
2. Click on your web service
3. View the "Metrics" and "Logs" tabs to monitor:
   - CPU usage
   - Memory usage
   - Network traffic
   - Request logs
   - Build logs

#### Vercel Analytics

1. Go to your Vercel project
2. Click "Analytics" tab
3. View:
   - Page views
   - User sessions
   - Performance metrics

### Error Tracking (Optional)

For production error tracking, consider integrating:
- [Sentry](https://sentry.io) for error monitoring
- [LogRocket](https://logrocket.com) for session replay
- [Datadog](https://www.datadoghq.com) for comprehensive monitoring

### Database Backups

MongoDB Atlas provides automatic backups:
1. Go to your cluster in Atlas
2. Click "Backup" tab
3. Backups are taken automatically in the free tier
4. Configure backup schedule for paid tiers

### Maintenance Checklist

- [ ] Monitor application logs regularly
- [ ] Check health endpoint weekly
- [ ] Review MongoDB Atlas metrics
- [ ] Update dependencies monthly (`npm audit`)
- [ ] Review and rotate secrets quarterly
- [ ] Test backup restoration procedures

## Troubleshooting

### Common Issues

#### 1. Socket.io Connection Failed

**Symptom**: Frontend can't connect to backend

**Solutions**:
- Verify `VITE_SOCKET_URL` in Vercel matches your Render URL
- Check Render logs for errors
- Ensure Render service is running (free tier may spin down after inactivity)
- Verify CORS configuration in server

#### 2. MongoDB Connection Error

**Symptom**: Backend fails to start, database errors in logs

**Solutions**:
- Verify MongoDB Atlas connection string is correct
- Check MongoDB Atlas network access (allow 0.0.0.0/0)
- Ensure database user has proper permissions
- Check if your IP is whitelisted in Atlas

#### 3. Build Failures

**Symptom**: GitHub Actions or deployment fails

**Solutions**:
- Check Node.js version compatibility (use 18+)
- Verify all dependencies are in `package.json`
- Review build logs for specific errors
- Ensure environment variables are set correctly

#### 4. CORS Errors

**Symptom**: Browser console shows CORS policy errors

**Solutions**:
- Update `CLIENT_URL` in Render to match Vercel URL exactly
- Ensure no trailing slashes in URLs
- Check if CORS middleware is properly configured

#### 5. WebSocket Upgrade Failed

**Symptom**: WebSocket connection fails, falls back to polling

**Solutions**:
- Ensure Render supports WebSocket connections (it does)
- Check if any proxy/CDN is blocking WebSocket
- Verify Socket.io client and server versions match
- Note: Free tier services may take time to wake up from sleep

### Viewing Logs

**Render Logs**:
1. Go to your Render dashboard
2. Click on your web service
3. Click "Logs" tab to view real-time logs
4. Use the search feature to filter logs

**Vercel Logs**:
```bash
# Install Vercel CLI
npm install -g vercel

# Login
vercel login

# View logs
vercel logs
```

### Getting Help

- Check the [original chat app README](./CHAT_APP_README.md) for feature details
- Review Render documentation: https://render.com/docs
- Review Vercel documentation: https://vercel.com/docs
- Check Socket.io documentation: https://socket.io/docs/v4/

## Rollback Procedures

### Rolling Back Backend (Render)

1. Go to your Render dashboard
2. Click on your web service
3. Go to "Events" tab
4. Find the previous successful deployment
5. Click "Rollback to this version"

### Rolling Back Frontend (Vercel)

1. Go to your Vercel project
2. Click "Deployments" tab
3. Find the previous working deployment
4. Click "..." menu and select "Promote to Production"

## Security Considerations

This application implements several security measures:

- **Helmet.js**: Secure HTTP headers
- **Rate Limiting**: 100 requests per 15 minutes per IP
- **CORS**: Restricted to specific origins
- **Input Validation**: Username and password requirements
- **Password Hashing**: bcryptjs for secure password storage
- **Environment Variables**: Sensitive data not in code
- **HTTPS**: Enforced by Vercel and Render

## Performance Optimizations

- **Code Splitting**: Automatic with Vite
- **Asset Caching**: Long-term caching for static assets
- **Connection Pooling**: MongoDB connection pooling
- **Gzip Compression**: Automatic on Vercel and Render
- **CDN**: Vercel Edge Network for global distribution
- **Auto-Sleep**: Render free tier sleeps after 15 minutes of inactivity (first request may be slow)

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Assignment Completion Checklist

- [x] Application copied to Week 7 directory
- [x] Production-ready backend with security headers
- [x] Environment variable templates created
- [x] Render configuration files
- [x] Vercel configuration files
- [x] GitHub Actions CI/CD workflows
- [x] Comprehensive README with deployment instructions
- [x] MongoDB Atlas cluster created
- [x] Backend deployed to Render
- [x] Frontend deployed to Vercel
- [x] Environment variables configured
- [x] CI/CD pipeline tested
- [x] Application functioning in production
- [x] Live URLs updated in README

**Note**: Add screenshots of your deployments to complete documentation (optional)

## License

MIT License - Created for Week 7 MERN Stack Assignment

## Author

Created as part of the PLP MERN Stack course - Week 7: Deployment and DevOps Essentials
