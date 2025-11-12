# ZenAI Quick Reference - Common Commands

## 🚀 Development Commands

### Start Services (Local Development)
```bash
# Start all services with Docker Compose
docker-compose up -d

# Start with logs visible
docker-compose up

# View logs for specific service
docker-compose logs -f backend
docker-compose logs -f ai-engine
docker-compose logs -f frontend
```

### Backend Development
```bash
# Install dependencies
cd zenai-backend
npm install

# Start development server
npm run dev

# Run tests
npm run test

# Run with nodemon (auto-restart)
npm run dev

# Lint code
npm run lint

# Format code
npm run format
```

### Frontend Development
```bash
# Install dependencies
cd zenai-frontend
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Lint & format
npm run lint
npm run lint:fix
```

### AI Engine Development
```bash
# Install dependencies
cd zenai-ai-engine
npm install

# Start AI engine
npm start

# Development mode
npm run dev
```

---

## 🏭 Production Commands

### Build Production Images
```bash
# Build all images
docker-compose -f docker-compose.prod.yml build

# Build specific image
docker-compose -f docker-compose.prod.yml build backend
```

### Deploy to Production
```bash
# Start all production services
docker-compose -f docker-compose.prod.yml up -d

# Scale backend to 3 instances
docker-compose -f docker-compose.prod.yml up -d --scale backend=3

# Stop all services
docker-compose -f docker-compose.prod.yml down

# Restart specific service
docker-compose -f docker-compose.prod.yml restart backend

# Update and restart
docker-compose -f docker-compose.prod.yml pull
docker-compose -f docker-compose.prod.yml up -d
```

---

## 📊 Monitoring Commands

### View Service Status
```bash
# List all running containers
docker-compose -f docker-compose.prod.yml ps

# Show resource usage
docker stats

# Show detailed info about container
docker inspect zenai-backend
```

### Access Monitoring Dashboards
```
Grafana:     http://localhost:3001
Prometheus:  http://localhost:9090
Health:      http://localhost/health
API Health:  http://localhost:5000/health
```

### Check Logs
```bash
# View last 50 lines
docker-compose -f docker-compose.prod.yml logs --tail 50

# Follow logs in real-time
docker-compose -f docker-compose.prod.yml logs -f

# View specific service logs
docker-compose -f docker-compose.prod.yml logs backend

# Export logs to file
docker-compose -f docker-compose.prod.yml logs > deployment.log
```

---

## 💾 Database Commands

### MongoDB Operations
```bash
# Connect to MongoDB
docker-compose -f docker-compose.prod.yml exec mongodb mongo

# Backup database
bash scripts/backup.sh

# Restore database
bash scripts/restore.sh backups/backup_latest.tar.gz

# Create database indexes
docker-compose -f docker-compose.prod.yml exec mongodb mongo \
  --username zenai \
  --password your-password \
  --authenticationDatabase admin \
  /dev/stdin << 'EOF'
use zenai
db.users.createIndex({email: 1}, {unique: true})
db.projects.createIndex({owner: 1})
EOF
```

### Redis Operations
```bash
# Connect to Redis
docker-compose -f docker-compose.prod.yml exec redis redis-cli

# Check Redis health
docker-compose -f docker-compose.prod.yml exec redis redis-cli ping
# Expected: PONG

# Clear cache (careful in production!)
docker-compose -f docker-compose.prod.yml exec redis redis-cli FLUSHALL
```

---

## 🔐 SSL/Certificate Commands

### Generate SSL Certificate
```bash
# Using Let's Encrypt
sudo certbot certonly --standalone \
  -d your-domain.com \
  -d api.your-domain.com \
  --email admin@your-domain.com \
  --agree-tos

# Copy certificates
sudo cp /etc/letsencrypt/live/your-domain.com/fullchain.pem nginx/ssl/cert.pem
sudo cp /etc/letsencrypt/live/your-domain.com/privkey.pem nginx/ssl/key.pem
```

### Check Certificate Expiration
```bash
# View certificate details
openssl x509 -in nginx/ssl/cert.pem -text -noout

# Check expiration date
openssl x509 -in nginx/ssl/cert.pem -noout -dates
```

---

## 🧪 Testing Commands

### Run Tests
```bash
# All tests
npm test

# Specific test suite
npm run test:integration

# Load testing
npm run test:load

# Watch mode
npm run test:watch

# Coverage report
npm run test -- --coverage
```

### Load Testing
```bash
# Run load test (100 connections, 30 seconds)
npm run test:load

# Custom load test
autocannon -c 50 -d 30 http://localhost:5000
```

---

## 📦 Dependency Management

### Update Dependencies
```bash
# Check for updates
npm outdated

# Update all packages
npm update

# Audit vulnerabilities
npm audit

# Fix vulnerabilities
npm audit fix

# Audit specific package
npm audit --package mongoose
```

### Clean Installation
```bash
# Remove all node_modules
rm -rf node_modules package-lock.json

# Fresh install
npm install

# Clean install (more strict)
npm ci
```

---

## 🔄 Git Commands

### Commit & Push
```bash
# Check status
git status

# Stage all changes
git add .

# Stage specific file
git add path/to/file

# Commit
git commit -m "feat: your message"

# Push to main
git push origin main

# Push with force (careful!)
git push -f origin main
```

### Branches
```bash
# Create feature branch
git checkout -b feature/feature-name

# List branches
git branch -a

# Switch branch
git checkout branch-name

# Delete branch
git branch -d branch-name

# Merge branch
git merge feature/feature-name
```

---

## 🐛 Troubleshooting Commands

### Container Issues
```bash
# Restart service
docker-compose -f docker-compose.prod.yml restart backend

# Remove and recreate container
docker-compose -f docker-compose.prod.yml up -d --force-recreate backend

# Remove dangling images
docker image prune -f

# Clear Docker cache
docker system prune -af
```

### Port Issues
```bash
# Check which process uses port 5000
lsof -i :5000

# Kill process
kill -9 <PID>

# Or change port in .env
PORT=5001
```

### Network Issues
```bash
# Test connectivity to backend
curl http://localhost:5000/health

# Test connectivity to database
docker-compose -f docker-compose.prod.yml exec backend nc -zv mongodb 27017

# Test Redis connection
docker-compose -f docker-compose.prod.yml exec backend redis-cli -h redis ping
```

---

## 📈 Performance Commands

### Monitor System Resources
```bash
# Real-time stats
docker stats

# Memory usage
docker stats --format "table {{.Container}}\t{{.MemUsage}}\t{{.MemPerc}}"

# CPU usage
docker stats --format "table {{.Container}}\t{{.CPUPerc}}"
```

### Database Performance
```bash
# MongoDB profiling
db.setProfilingLevel(1)
db.system.profile.find().limit(10).pretty()

# Check slow queries
db.system.profile.find({millis: {$gt: 100}}).pretty()
```

---

## 🚨 Emergency Commands

### Restart All Services
```bash
# Hard restart
docker-compose -f docker-compose.prod.yml down
docker-compose -f docker-compose.prod.yml up -d

# Quick restart
docker-compose -f docker-compose.prod.yml restart
```

### Rollback to Previous State
```bash
# Backup current data
bash scripts/backup.sh

# Restore from backup
bash scripts/restore.sh backups/backup_<timestamp>.tar.gz

# Restart services
docker-compose -f docker-compose.prod.yml restart
```

### Emergency Database Access
```bash
# Direct MongoDB access
docker-compose -f docker-compose.prod.yml exec mongodb mongosh

# Direct Redis access
docker-compose -f docker-compose.prod.yml exec redis redis-cli

# Direct backend shell
docker-compose -f docker-compose.prod.yml exec backend /bin/sh
```

---

## 🔍 Debugging Commands

### Enable Debug Logging
```bash
# Set in .env
LOG_LEVEL=debug

# Restart service
docker-compose -f docker-compose.prod.yml restart backend
```

### Execute Commands in Container
```bash
# Run command in container
docker-compose -f docker-compose.prod.yml exec backend npm test

# Interactive shell
docker-compose -f docker-compose.prod.yml exec backend /bin/bash

# Run Node REPL
docker-compose -f docker-compose.prod.yml exec backend node
```

### Check Environment Variables
```bash
# View backend environment
docker-compose -f docker-compose.prod.yml exec backend env

# View specific variable
docker-compose -f docker-compose.prod.yml exec backend echo $MONGODB_URI
```

---

## 📋 Health Checks

### Verify All Services
```bash
# Backend health
curl -s http://localhost:5000/health | jq

# Frontend health
curl -s http://localhost:3000/health

# MongoDB
docker-compose -f docker-compose.prod.yml exec mongodb mongo ping

# Redis
docker-compose -f docker-compose.prod.yml exec redis redis-cli ping

# Prometheus
curl -s http://localhost:9090/-/healthy

# Grafana
curl -s http://localhost:3001/api/health
```

### Test API Endpoints
```bash
# Login
curl -X POST http://localhost:5000/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password"}'

# Chat with AI
curl -X POST http://localhost:5000/api/v1/ai/chat \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"message":"Hello AI","context":{"type":"project-management"}}'

# Get projects
curl http://localhost:5000/api/v1/projects \
  -H "Authorization: Bearer YOUR_TOKEN"
```

---

## 📝 Common Workflows

### Deploy New Feature
```bash
# 1. Create feature branch
git checkout -b feature/new-feature

# 2. Make changes and commit
git add .
git commit -m "feat: add new feature"

# 3. Test locally
npm test
npm run dev

# 4. Build for production
docker-compose -f docker-compose.prod.yml build

# 5. Push and create PR
git push origin feature/new-feature

# 6. After PR approval, merge and deploy
git checkout main
git merge feature/new-feature
docker-compose -f docker-compose.prod.yml up -d
```

### Emergency Hotfix
```bash
# 1. Create hotfix branch
git checkout -b hotfix/critical-bug

# 2. Fix and commit
git add .
git commit -m "fix: critical bug"

# 3. Test thoroughly
npm test
npm run test:integration

# 4. Deploy immediately
docker-compose -f docker-compose.prod.yml up -d

# 5. Merge back to main and dev
git checkout main
git merge hotfix/critical-bug
```

### Upgrade Dependencies
```bash
# 1. Check outdated packages
npm outdated

# 2. Update specific package
npm install package-name@latest

# 3. Test thoroughly
npm test

# 4. Rebuild and deploy
docker-compose -f docker-compose.prod.yml build
docker-compose -f docker-compose.prod.yml up -d

# 5. Monitor for issues
docker-compose -f docker-compose.prod.yml logs -f
```

---

## 📚 Documentation

- Full deployment guide: `docs/DEPLOYMENT.md`
- API documentation: `docs/api.md`
- Architecture guide: `docs/architecture.md`
- Agent documentation: `docs/agents.md`
- Project README: `README.md`

---

**Last Updated**: November 12, 2025  
**Version**: 1.0.0
