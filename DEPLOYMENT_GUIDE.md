# Production Deployment Guide

This guide covers deploying the Cyber Crime Monitoring System to production environments.

## 🏗️ Architecture Overview

The system consists of:
- **Frontend**: Next.js application with React and TypeScript
- **Backend**: Next.js API routes with authentication
- **Database**: PostgreSQL with Prisma ORM
- **File Storage**: Local file system (upgradeable to cloud storage)
- **Email**: SMTP email service for notifications
- **Real-time**: WebSocket connections for live updates

## 🚀 Deployment Options

### Option 1: Netlify (Recommended for Quick Deploy)

Netlify is perfect for getting the system up and running quickly with their database addon.

1. **Prerequisites**:
   - GitHub repository with your code
   - Netlify account

2. **Deploy Steps**:
   ```bash
   # Version your project first
   bun run versioning

   # Deploy as dynamic site (required for API routes)
   # Use the deploy tool in Same.new or manually:
   ```

3. **Environment Variables** (set in Netlify dashboard):
   ```env
   DATABASE_URL="your-postgresql-connection-string"
   NEXTAUTH_SECRET="your-secure-secret-key"
   NEXTAUTH_URL="https://your-domain.netlify.app"
   EMAIL_HOST="smtp.gmail.com"
   EMAIL_PORT="587"
   EMAIL_USER="your-email@gmail.com"
   EMAIL_PASS="your-app-password"
   EMAIL_FROM="PNG Police Cyber Crime Unit <noreply@pngpolice.gov.pg>"
   ```

4. **Database Setup**:
   ```bash
   # After deployment, run migrations
   bunx prisma migrate deploy
   bunx tsx src/lib/seed.ts
   ```

### Option 2: Vercel (Next.js Optimized)

1. **Connect Repository**: Link your GitHub repo to Vercel
2. **Environment Variables**: Set in Vercel dashboard
3. **Database**: Use Vercel Postgres or external provider
4. **Deploy**: Automatic deployment on git push

### Option 3: Traditional Server Deployment

#### Prerequisites
- Ubuntu 20.04+ or similar Linux server
- Node.js 18+ and npm/bun
- PostgreSQL 12+
- Nginx (reverse proxy)
- SSL certificate (Let's Encrypt)

#### Server Setup

1. **Install Dependencies**:
   ```bash
   # Update system
   sudo apt update && sudo apt upgrade -y

   # Install Node.js
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt-get install -y nodejs

   # Install Bun (optional but recommended)
   curl -fsSL https://bun.sh/install | bash

   # Install PostgreSQL
   sudo apt install postgresql postgresql-contrib

   # Install Nginx
   sudo apt install nginx

   # Install PM2 (process manager)
   npm install -g pm2
   ```

2. **Database Setup**:
   ```bash
   # Create database user
   sudo -u postgres createuser --interactive

   # Create database
   sudo -u postgres createdb cyber_crime_monitoring
   ```

3. **Application Deployment**:
   ```bash
   # Clone repository
   git clone <your-repo-url>
   cd cyber-crime-monitoring

   # Install dependencies
   bun install

   # Set up environment
   cp .env.example .env
   # Edit .env with production values

   # Run database setup
   bun run setup

   # Build application
   bun run build

   # Start with PM2
   pm2 start ecosystem.config.js
   pm2 save
   pm2 startup
   ```

4. **Nginx Configuration**:
   ```nginx
   server {
       listen 80;
       server_name your-domain.com;

       location / {
           proxy_pass http://localhost:3000;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
           proxy_cache_bypass $http_upgrade;
       }
   }
   ```

5. **SSL Setup with Let's Encrypt**:
   ```bash
   sudo apt install certbot python3-certbot-nginx
   sudo certbot --nginx -d your-domain.com
   ```

## 🔒 Security Configuration

### Environment Variables

**Required for Production**:
```env
# Database
DATABASE_URL="postgresql://user:password@host:5432/dbname"

# Authentication (Generate strong secrets!)
NEXTAUTH_SECRET="32-character-random-string"
NEXTAUTH_URL="https://your-domain.com"

# Email Service
EMAIL_HOST="smtp.gmail.com"
EMAIL_PORT="587"
EMAIL_USER="notifications@your-domain.com"
EMAIL_PASS="app-specific-password"
EMAIL_FROM="PNG Police Cyber Crime Unit <noreply@your-domain.com>"

# File Uploads
UPLOAD_DIR="/secure/path/uploads"
MAX_FILE_SIZE="52428800"  # 50MB

# Security
JWT_SECRET="another-32-character-random-string"

# App Configuration
APP_NAME="PNG Police Cyber Crime Monitoring"
APP_URL="https://your-domain.com"
NODE_ENV="production"
```

### Security Checklist

- [ ] Use HTTPS (SSL/TLS) for all connections
- [ ] Strong, unique passwords for all accounts
- [ ] Enable database SSL connections
- [ ] Configure firewall to restrict access
- [ ] Regular security updates
- [ ] Secure file upload directory permissions
- [ ] Enable audit logging
- [ ] Regular database backups
- [ ] Monitor access logs
- [ ] Use environment variables for secrets
- [ ] Enable rate limiting
- [ ] Configure CORS properly

## 📊 Database Production Setup

### 1. Cloud Database (Recommended)

**Neon (Recommended)**:
- Free tier available
- Automatic backups
- SSL by default
- Easy scaling

**Supabase**:
- PostgreSQL with additional features
- Built-in auth (can integrate with existing system)
- Real-time subscriptions

**Railway**:
- Simple PostgreSQL hosting
- Git-based deployments
- Automatic scaling

### 2. Self-hosted PostgreSQL

```bash
# PostgreSQL configuration for production
sudo nano /etc/postgresql/14/main/postgresql.conf

# Key settings:
max_connections = 100
shared_buffers = 256MB
effective_cache_size = 1GB
maintenance_work_mem = 64MB
checkpoint_completion_target = 0.9
wal_buffers = 16MB
default_statistics_target = 100
```

### 3. Database Security

```sql
-- Create dedicated user for application
CREATE USER cyber_app WITH PASSWORD 'strong_password';

-- Grant minimal required permissions
GRANT CONNECT ON DATABASE cyber_crime_monitoring TO cyber_app;
GRANT USAGE ON SCHEMA public TO cyber_app;
GRANT CREATE ON SCHEMA public TO cyber_app;

-- Enable SSL (in postgresql.conf)
ssl = on
ssl_cert_file = 'server.crt'
ssl_key_file = 'server.key'
```

## 📧 Email Configuration

### Gmail Setup (Development/Small Scale)

1. Enable 2-factor authentication
2. Generate app-specific password
3. Use app password in EMAIL_PASS

### SendGrid (Production Recommended)

```env
EMAIL_HOST="smtp.sendgrid.net"
EMAIL_PORT="587"
EMAIL_USER="apikey"
EMAIL_PASS="your-sendgrid-api-key"
```

### Custom SMTP Server

```env
EMAIL_HOST="mail.your-domain.com"
EMAIL_PORT="587"
EMAIL_USER="notifications@your-domain.com"
EMAIL_PASS="your-email-password"
```

## 🔧 Performance Optimization

### 1. Database Optimization

```sql
-- Add indexes for frequently queried fields
CREATE INDEX idx_cases_status ON cyber_cases(status);
CREATE INDEX idx_cases_created_date ON cyber_cases(created_at);
CREATE INDEX idx_cases_assigned_to ON cyber_cases(assigned_to_id);
CREATE INDEX idx_evidence_case ON evidence(case_id);
CREATE INDEX idx_notifications_user ON notifications(user_id, is_read);
```

### 2. Application Optimization

```javascript
// Enable compression
// Add to next.config.js
module.exports = {
  compress: true,
  experimental: {
    gzipSize: true,
  },
}
```

### 3. Caching Strategy

- Use Redis for session storage
- Enable browser caching for static assets
- Implement API response caching
- Use CDN for file delivery

## 📈 Monitoring & Maintenance

### 1. Application Monitoring

```bash
# PM2 monitoring
pm2 monit

# Check logs
pm2 logs

# Restart application
pm2 restart cyber-crime-app
```

### 2. Database Monitoring

```sql
-- Monitor active connections
SELECT count(*) FROM pg_stat_activity;

-- Check database size
SELECT pg_size_pretty(pg_database_size('cyber_crime_monitoring'));

-- Monitor slow queries
SELECT query, mean_time, calls
FROM pg_stat_statements
ORDER BY mean_time DESC
LIMIT 10;
```

### 3. Backup Strategy

```bash
# Automated daily backups
#!/bin/bash
pg_dump cyber_crime_monitoring > backup_$(date +%Y%m%d).sql
aws s3 cp backup_$(date +%Y%m%d).sql s3://your-backup-bucket/

# Cron job (daily at 2 AM)
0 2 * * * /path/to/backup-script.sh
```

## 🔄 Integration with Main Police System

### API Integration

The system provides REST API endpoints for integration:

```bash
# Get all cases
GET /api/cases

# Create new case
POST /api/cases

# Get case details
GET /api/cases/{id}

# Update case
PUT /api/cases/{id}
```

### Authentication for External Systems

```javascript
// API key authentication for system integration
// Add to your API routes
const API_KEY = process.env.EXTERNAL_API_KEY;

if (request.headers.get('x-api-key') !== API_KEY) {
  return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
}
```

### Webhook Integration

```javascript
// Send updates to main police system
export async function notifyMainSystem(caseData) {
  await fetch(process.env.MAIN_SYSTEM_WEBHOOK_URL, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${process.env.MAIN_SYSTEM_API_KEY}`
    },
    body: JSON.stringify(caseData)
  });
}
```

## 📋 Pre-deployment Checklist

### Code Quality
- [ ] All TypeScript errors resolved
- [ ] Linting passes without errors
- [ ] Security vulnerabilities addressed
- [ ] Performance optimizations applied
- [ ] Error handling implemented

### Configuration
- [ ] Environment variables configured
- [ ] Database migrations tested
- [ ] Email service configured and tested
- [ ] File upload directories created
- [ ] SSL certificate installed

### Security
- [ ] Authentication tested with all roles
- [ ] Authorization rules verified
- [ ] File upload security validated
- [ ] Database access restricted
- [ ] Secrets management implemented

### Testing
- [ ] User registration/login flows tested
- [ ] Case creation/management tested
- [ ] File upload/download tested
- [ ] Email notifications working
- [ ] Real-time updates functioning

### Monitoring
- [ ] Error tracking configured
- [ ] Performance monitoring set up
- [ ] Database backup automated
- [ ] Log rotation configured
- [ ] Health check endpoints created

## 📞 Support & Maintenance

### Regular Maintenance Tasks

1. **Weekly**:
   - Review system logs
   - Check database performance
   - Monitor storage usage
   - Review security logs

2. **Monthly**:
   - Update dependencies
   - Review user accounts
   - Analyze system performance
   - Test backup restoration

3. **Quarterly**:
   - Security audit
   - Performance optimization
   - User training updates
   - System documentation review

### Emergency Procedures

1. **System Down**:
   - Check server status
   - Review error logs
   - Restart application if needed
   - Contact hosting provider

2. **Database Issues**:
   - Check database connectivity
   - Review slow query logs
   - Consider read replica failover
   - Restore from backup if needed

3. **Security Incident**:
   - Isolate affected systems
   - Review audit logs
   - Change compromised credentials
   - Notify relevant authorities

The system is now ready for production deployment with enterprise-grade security, performance, and reliability features.
