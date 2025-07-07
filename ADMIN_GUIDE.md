# 🔧 PNG Police Cyber Crime Monitoring System - Administrator Guide

## 🎯 System Administration Overview

This guide provides comprehensive instructions for system administrators managing the PNG Police Cyber Crime Monitoring System. It covers user management, system configuration, maintenance, and troubleshooting.

## 📚 Table of Contents

1. [System Architecture](#system-architecture)
2. [User Management](#user-management)
3. [Database Administration](#database-administration)
4. [Security Management](#security-management)
5. [System Monitoring](#system-monitoring)
6. [Backup and Recovery](#backup-and-recovery)
7. [Integration Management](#integration-management)
8. [Performance Optimization](#performance-optimization)
9. [Troubleshooting](#troubleshooting)
10. [Maintenance Procedures](#maintenance-procedures)

---

## 🏗️ System Architecture

### Technology Stack
- **Frontend**: Next.js 15 with React and TypeScript
- **Backend**: Next.js API routes with NextAuth.js
- **Database**: PostgreSQL with Prisma ORM
- **Authentication**: NextAuth.js with role-based access
- **File Storage**: Local filesystem with cloud backup options
- **Email**: SMTP with multiple provider support
- **Real-time**: WebSocket for live notifications

### Infrastructure Components
- **Web Application**: Next.js application server
- **Database Server**: PostgreSQL instance
- **File Storage**: Evidence file system
- **Email Service**: SMTP relay for notifications
- **Monitoring**: Application and system monitoring
- **Backup System**: Automated backup procedures

### Security Architecture
- **Authentication**: Multi-factor authentication support
- **Authorization**: Role-based access control (RBAC)
- **Data Encryption**: At-rest and in-transit encryption
- **Audit Logging**: Complete activity tracking
- **Network Security**: HTTPS, firewall protection
- **File Security**: Validated uploads, virus scanning

---

## 👥 User Management

### User Roles and Permissions

#### Administrator
- **System Management**: Full administrative access
- **User Management**: Create, modify, delete users
- **Configuration**: System settings and parameters
- **Monitoring**: Access to all logs and analytics
- **Integration**: Manage external system connections

#### Unit Commander
- **Unit Oversight**: Manage unit personnel and cases
- **Reporting**: Generate unit performance reports
- **Case Assignment**: Assign cases to investigators
- **Resource Management**: Allocate system resources
- **Training**: Oversee user training and certification

#### Senior Investigator
- **Investigation Leadership**: Lead complex investigations
- **Case Management**: Access to all assigned cases
- **Mentoring**: Guide junior investigators
- **Evidence Review**: Approve evidence submissions
- **Legal Coordination**: Interface with legal departments

#### Investigator
- **Case Handling**: Manage assigned investigations
- **Evidence Management**: Upload and analyze evidence
- **Victim Support**: Interface with crime victims
- **Reporting**: Generate case status reports
- **Platform Liaison**: Submit legal data requests

#### Analyst
- **Data Analysis**: Generate intelligence reports
- **Forensic Analysis**: Digital evidence examination
- **Pattern Recognition**: Identify crime trends
- **Support Services**: Assist investigations
- **Research**: Gather threat intelligence

### Creating New Users

#### Via Web Interface
1. **Navigate to User Management**
2. **Click "Add User"**
3. **Complete User Form**:
   - Full name and contact information
   - Email address (will be username)
   - Department and role assignment
   - Initial password (user must change on first login)
   - Phone number and emergency contact

4. **Set Permissions**:
   - Select appropriate role
   - Configure specific permissions if needed
   - Set case access levels
   - Enable/disable features

5. **Account Activation**:
   - Send welcome email with login instructions
   - Provide training materials
   - Schedule orientation session
   - Monitor initial system usage

#### Via Database (Emergency)
```sql
INSERT INTO users (
  email, name, password, role, department,
  phone_number, is_active, created_at, updated_at
) VALUES (
  'new.user@pngpolice.gov.pg',
  'Officer Name',
  '$2a$12$hashed_password_here',
  'INVESTIGATOR',
  'Cyber Crime Unit',
  '+675 XXX XXXX',
  true,
  NOW(),
  NOW()
);
```

### User Account Management

#### Password Policies
- **Minimum Length**: 12 characters
- **Complexity**: Upper, lower, numbers, symbols
- **Expiration**: 90 days (configurable)
- **History**: Cannot reuse last 5 passwords
- **Failed Attempts**: Account lockout after 5 failures

#### Account Lifecycle
1. **Creation**: Administrator creates account
2. **Activation**: User receives welcome email
3. **First Login**: Forced password change
4. **Training**: Complete system training
5. **Certification**: Pass competency assessment
6. **Active Use**: Regular system access
7. **Monitoring**: Ongoing activity review
8. **Deactivation**: Account suspension/termination

### Bulk User Operations

#### CSV Import
```csv
email,name,role,department,phone
officer1@pngpolice.gov.pg,Officer One,INVESTIGATOR,Cyber Crime Unit,+675 XXX XXXX
officer2@pngpolice.gov.pg,Officer Two,ANALYST,Digital Forensics,+675 XXX XXXY
```

#### Bulk Password Reset
```sql
-- Reset passwords for all users in a department
UPDATE users
SET password = '$2a$12$new_temp_password_hash'
WHERE department = 'Cyber Crime Unit'
AND is_active = true;
```

---

## 🗃️ Database Administration

### Database Schema Management

#### Core Tables
- **users**: System users and authentication
- **cyber_cases**: Cyber crime cases and investigations
- **evidence**: Digital evidence with metadata
- **suspects**: Suspect profiles and information
- **victims**: Victim information and support tracking
- **social_media_profiles**: Monitored social accounts
- **legal_requests**: Platform data requests
- **notifications**: System notifications and alerts
- **audit_logs**: Complete activity audit trail

#### Relationship Integrity
```sql
-- Check for orphaned records
SELECT 'evidence' as table_name, COUNT(*) as orphaned_count
FROM evidence e
LEFT JOIN cyber_cases c ON e.case_id = c.id
WHERE c.id IS NULL

UNION ALL

SELECT 'legal_requests', COUNT(*)
FROM legal_requests lr
LEFT JOIN cyber_cases c ON lr.case_id = c.id
WHERE c.id IS NULL;
```

### Performance Monitoring

#### Database Statistics
```sql
-- Check database size and growth
SELECT
  schemaname,
  tablename,
  attname,
  n_distinct,
  correlation
FROM pg_stats
WHERE schemaname = 'public'
ORDER BY tablename;

-- Monitor query performance
SELECT
  query,
  calls,
  total_time,
  mean_time,
  rows
FROM pg_stat_statements
ORDER BY total_time DESC
LIMIT 10;
```

#### Index Management
```sql
-- Check index usage
SELECT
  schemaname,
  tablename,
  attname,
  n_distinct,
  correlation
FROM pg_stats
WHERE schemaname = 'public'
AND n_distinct > 100;

-- Create recommended indexes
CREATE INDEX CONCURRENTLY idx_cases_status_priority
ON cyber_cases(status, priority);

CREATE INDEX CONCURRENTLY idx_evidence_case_type
ON evidence(case_id, evidence_type);

CREATE INDEX CONCURRENTLY idx_notifications_user_unread
ON notifications(user_id, is_read) WHERE is_read = false;
```

### Data Maintenance

#### Regular Cleanup
```sql
-- Archive old notifications (older than 6 months)
DELETE FROM notifications
WHERE created_at < NOW() - INTERVAL '6 months'
AND is_read = true;

-- Clean up expired sessions
DELETE FROM sessions
WHERE expires < NOW();

-- Archive completed audit logs (older than 2 years)
INSERT INTO audit_logs_archive
SELECT * FROM audit_logs
WHERE timestamp < NOW() - INTERVAL '2 years';

DELETE FROM audit_logs
WHERE timestamp < NOW() - INTERVAL '2 years';
```

#### Data Validation
```sql
-- Check for data consistency issues
SELECT
  'Cases without assigned investigator' as issue,
  COUNT(*) as count
FROM cyber_cases
WHERE assigned_to_id IS NULL
AND status IN ('IN_PROGRESS', 'UNDER_INVESTIGATION')

UNION ALL

SELECT
  'Evidence without case',
  COUNT(*)
FROM evidence e
LEFT JOIN cyber_cases c ON e.case_id = c.id
WHERE c.id IS NULL;
```

---

## 🔒 Security Management

### Authentication Configuration

#### NextAuth.js Settings
```javascript
// src/lib/auth.ts configuration
export const authOptions: NextAuthOptions = {
  session: {
    strategy: "jwt",
    maxAge: 8 * 60 * 60, // 8 hours
  },
  pages: {
    signIn: "/auth/signin",
    error: "/auth/error",
  },
  callbacks: {
    jwt: async ({ token, user }) => {
      // Custom JWT logic
    },
    session: async ({ session, token }) => {
      // Session customization
    }
  }
};
```

#### Password Security
```javascript
// Password hashing configuration
const SALT_ROUNDS = 12;
const hashedPassword = await bcrypt.hash(password, SALT_ROUNDS);

// Password validation rules
const passwordRegex = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]{12,}$/;
```

### Access Control Management

#### Role-Based Permissions
```typescript
// Permission matrix
const PERMISSIONS = {
  ADMIN: ['*'], // All permissions
  UNIT_COMMANDER: [
    'cases.read', 'cases.write', 'cases.assign',
    'users.read', 'users.manage_unit',
    'reports.read', 'reports.generate'
  ],
  SENIOR_INVESTIGATOR: [
    'cases.read', 'cases.write', 'cases.assign',
    'evidence.read', 'evidence.write',
    'legal_requests.read', 'legal_requests.write'
  ],
  INVESTIGATOR: [
    'cases.read', 'cases.write_assigned',
    'evidence.read', 'evidence.write',
    'social_monitoring.read', 'social_monitoring.write'
  ],
  ANALYST: [
    'cases.read', 'evidence.read',
    'analytics.read', 'reports.read',
    'knowledge_base.read', 'knowledge_base.write'
  ]
};
```

#### API Security
```typescript
// API route protection
export async function middleware(request: NextRequest) {
  const session = await getServerSession(authOptions);

  if (!session) {
    return NextResponse.redirect('/auth/signin');
  }

  // Check role-based access
  const requiredRole = getRequiredRole(request.nextUrl.pathname);
  if (!hasPermission(session.user.role, requiredRole)) {
    return NextResponse.redirect('/unauthorized');
  }

  return NextResponse.next();
}
```

### Audit and Compliance

#### Audit Log Configuration
```sql
-- Enable audit logging for all tables
CREATE OR REPLACE FUNCTION audit_trigger_function()
RETURNS trigger AS $$
BEGIN
  INSERT INTO audit_logs (
    user_id, action, resource, resource_id,
    old_values, new_values, timestamp
  ) VALUES (
    current_setting('app.user_id')::uuid,
    TG_OP,
    TG_TABLE_NAME,
    COALESCE(NEW.id, OLD.id),
    row_to_json(OLD),
    row_to_json(NEW),
    NOW()
  );
  RETURN COALESCE(NEW, OLD);
END;
$$ LANGUAGE plpgsql;
```

#### Compliance Reporting
```sql
-- Generate compliance report
SELECT
  DATE_TRUNC('month', timestamp) as month,
  action,
  resource,
  COUNT(*) as activity_count
FROM audit_logs
WHERE timestamp >= NOW() - INTERVAL '12 months'
GROUP BY DATE_TRUNC('month', timestamp), action, resource
ORDER BY month DESC, activity_count DESC;
```

---

## 📊 System Monitoring

### Application Monitoring

#### Health Check Endpoints
```typescript
// API health check
export async function GET() {
  try {
    // Database connectivity
    await db.$queryRaw`SELECT 1`;

    // External services
    const emailService = await checkEmailService();
    const fileSystem = await checkFileSystem();

    return NextResponse.json({
      status: 'healthy',
      timestamp: new Date().toISOString(),
      services: {
        database: 'up',
        email: emailService ? 'up' : 'down',
        storage: fileSystem ? 'up' : 'down'
      }
    });
  } catch (error) {
    return NextResponse.json({
      status: 'unhealthy',
      error: error.message
    }, { status: 500 });
  }
}
```

#### Performance Metrics
```typescript
// Monitor API response times
const performanceMetrics = {
  requestDuration: new Map(),
  errorRate: new Map(),
  activeUsers: new Set(),

  recordRequest(endpoint: string, duration: number) {
    this.requestDuration.set(endpoint, duration);
  },

  getMetrics() {
    return {
      avgResponseTime: Array.from(this.requestDuration.values())
        .reduce((a, b) => a + b, 0) / this.requestDuration.size,
      errorRate: this.errorRate.size / this.requestDuration.size,
      activeUsers: this.activeUsers.size
    };
  }
};
```

### Database Monitoring

#### Connection Monitoring
```sql
-- Monitor database connections
SELECT
  state,
  COUNT(*) as connection_count
FROM pg_stat_activity
WHERE datname = 'cyber_crime_monitoring'
GROUP BY state;

-- Check long-running queries
SELECT
  pid,
  now() - pg_stat_activity.query_start AS duration,
  query
FROM pg_stat_activity
WHERE state = 'active'
AND now() - pg_stat_activity.query_start > interval '5 minutes';
```

#### Resource Usage
```sql
-- Database size monitoring
SELECT
  pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) as size,
  schemaname,
  tablename
FROM pg_tables
WHERE schemaname = 'public'
ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC;

-- Buffer cache hit ratio
SELECT
  round(
    (sum(heap_blks_hit) / (sum(heap_blks_hit) + sum(heap_blks_read))) * 100, 2
  ) as cache_hit_ratio
FROM pg_statio_user_tables;
```

### System Alerts

#### Alert Configuration
```typescript
// Alert thresholds
const ALERT_THRESHOLDS = {
  responseTime: 5000, // 5 seconds
  errorRate: 0.05, // 5%
  diskUsage: 0.85, // 85%
  memoryUsage: 0.90, // 90%
  failedLogins: 10, // per hour
  suspiciousActivity: 5 // per user per hour
};

// Alert notification
async function sendAlert(type: string, message: string, severity: 'low' | 'medium' | 'high') {
  await sendEmail({
    to: process.env.ADMIN_EMAIL,
    subject: `[${severity.toUpperCase()}] System Alert: ${type}`,
    html: generateAlertEmail(type, message, severity)
  });

  // Log alert
  await db.auditLog.create({
    data: {
      userId: 'system',
      action: 'ALERT',
      resource: 'system',
      newValues: { type, message, severity }
    }
  });
}
```

---

## 💾 Backup and Recovery

### Backup Strategy

#### Database Backups
```bash
#!/bin/bash
# Daily database backup script

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups/database"
DB_NAME="cyber_crime_monitoring"

# Create backup directory
mkdir -p $BACKUP_DIR

# Full database backup
pg_dump -h localhost -U postgres -d $DB_NAME \
  --verbose --clean --no-owner --no-privileges \
  --format=custom \
  > $BACKUP_DIR/full_backup_$DATE.dump

# Schema-only backup
pg_dump -h localhost -U postgres -d $DB_NAME \
  --schema-only --verbose \
  > $BACKUP_DIR/schema_backup_$DATE.sql

# Compress backups
gzip $BACKUP_DIR/schema_backup_$DATE.sql

# Upload to cloud storage (optional)
aws s3 cp $BACKUP_DIR/full_backup_$DATE.dump \
  s3://png-police-backups/database/

# Cleanup old backups (keep 30 days)
find $BACKUP_DIR -type f -mtime +30 -delete

echo "Backup completed: $DATE"
```

#### File System Backups
```bash
#!/bin/bash
# Evidence file backup script

DATE=$(date +%Y%m%d_%H%M%S)
SOURCE_DIR="/app/uploads"
BACKUP_DIR="/backups/files"
CLOUD_BUCKET="s3://png-police-backups/files"

# Create incremental backup
rsync -av --delete --backup --backup-dir=$BACKUP_DIR/incremental_$DATE \
  $SOURCE_DIR/ $BACKUP_DIR/current/

# Create compressed archive
tar -czf $BACKUP_DIR/evidence_backup_$DATE.tar.gz -C $SOURCE_DIR .

# Upload to cloud
aws s3 cp $BACKUP_DIR/evidence_backup_$DATE.tar.gz $CLOUD_BUCKET/

# Verify backup integrity
tar -tzf $BACKUP_DIR/evidence_backup_$DATE.tar.gz > /dev/null
if [ $? -eq 0 ]; then
  echo "Backup verified: $DATE"
else
  echo "Backup verification failed: $DATE"
  exit 1
fi
```

### Recovery Procedures

#### Database Recovery
```bash
# Full database restore
pg_restore -h localhost -U postgres -d cyber_crime_monitoring_new \
  --verbose --clean --no-owner --no-privileges \
  /backups/database/full_backup_20240107_120000.dump

# Point-in-time recovery (if WAL archiving enabled)
pg_basebackup -h localhost -U postgres -D /tmp/base_backup -P -W

# Selective table restore
pg_restore -h localhost -U postgres -d cyber_crime_monitoring \
  --table=cyber_cases --data-only \
  /backups/database/full_backup_20240107_120000.dump
```

#### File System Recovery
```bash
# Extract evidence files
tar -xzf /backups/files/evidence_backup_20240107_120000.tar.gz \
  -C /app/uploads/

# Restore from incremental backup
rsync -av /backups/files/incremental_20240107_120000/ /app/uploads/

# Verify file integrity
find /app/uploads -type f -exec sha256sum {} \; > /tmp/current_hashes.txt
diff /backups/files/evidence_hashes_20240107_120000.txt /tmp/current_hashes.txt
```

### Disaster Recovery Plan

#### Recovery Time Objectives (RTO)
- **Critical Systems**: 4 hours
- **Database Recovery**: 2 hours
- **File System Recovery**: 6 hours
- **Full System Recovery**: 8 hours

#### Recovery Point Objectives (RPO)
- **Database**: 1 hour (continuous WAL archiving)
- **Evidence Files**: 24 hours (daily backups)
- **Configuration**: 24 hours (daily backups)

#### Emergency Procedures
1. **Assess Damage**: Determine scope of data loss
2. **Activate DR Team**: Notify key personnel
3. **Isolate Systems**: Prevent further damage
4. **Begin Recovery**: Follow documented procedures
5. **Verify Integrity**: Test all recovered systems
6. **Resume Operations**: Gradual service restoration
7. **Post-Incident Review**: Document lessons learned

---

## 🔗 Integration Management

### External System APIs

#### Main PNG Police System Integration
```typescript
// Integration configuration
const MAIN_SYSTEM_CONFIG = {
  baseUrl: process.env.MAIN_SYSTEM_API_URL,
  apiKey: process.env.MAIN_SYSTEM_API_KEY,
  timeout: 30000,
  retryAttempts: 3
};

// Sync case data
export async function syncCaseToMainSystem(caseData: CyberCase) {
  try {
    const response = await fetch(`${MAIN_SYSTEM_CONFIG.baseUrl}/cases`, {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${MAIN_SYSTEM_CONFIG.apiKey}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        externalId: caseData.id,
        caseId: caseData.caseId,
        title: caseData.title,
        status: caseData.status,
        priority: caseData.priority,
        assignedOfficer: caseData.assignedTo?.name,
        lastUpdated: caseData.updatedAt
      })
    });

    if (!response.ok) {
      throw new Error(`Sync failed: ${response.statusText}`);
    }

    return await response.json();
  } catch (error) {
    console.error('Main system sync error:', error);
    // Queue for retry
    await queueFailedSync(caseData.id, 'main_system', error.message);
  }
}
```

#### Webhook Management
```typescript
// Webhook endpoints for external systems
export async function POST(request: NextRequest) {
  const signature = request.headers.get('x-webhook-signature');
  const body = await request.text();

  // Verify webhook signature
  const expectedSignature = createHmac('sha256', process.env.WEBHOOK_SECRET)
    .update(body)
    .digest('hex');

  if (signature !== `sha256=${expectedSignature}`) {
    return NextResponse.json({ error: 'Invalid signature' }, { status: 401 });
  }

  const payload = JSON.parse(body);

  // Process webhook event
  switch (payload.event) {
    case 'case.updated':
      await handleMainSystemCaseUpdate(payload.data);
      break;
    case 'officer.transferred':
      await handleOfficerTransfer(payload.data);
      break;
    default:
      console.warn('Unknown webhook event:', payload.event);
  }

  return NextResponse.json({ status: 'processed' });
}
```

### Third-Party Service Integration

#### Email Service Configuration
```typescript
// Multiple email provider support
const emailProviders = {
  sendgrid: {
    host: 'smtp.sendgrid.net',
    port: 587,
    auth: {
      user: 'apikey',
      pass: process.env.SENDGRID_API_KEY
    }
  },
  gmail: {
    host: 'smtp.gmail.com',
    port: 587,
    auth: {
      user: process.env.GMAIL_USER,
      pass: process.env.GMAIL_APP_PASSWORD
    }
  },
  custom: {
    host: process.env.SMTP_HOST,
    port: parseInt(process.env.SMTP_PORT || '587'),
    auth: {
      user: process.env.SMTP_USER,
      pass: process.env.SMTP_PASS
    }
  }
};
```

#### Cloud Storage Integration
```typescript
// Multi-cloud storage support
import AWS from 'aws-sdk';
import { Storage } from '@google-cloud/storage';

const storageProviders = {
  aws: new AWS.S3({
    accessKeyId: process.env.AWS_ACCESS_KEY_ID,
    secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY,
    region: process.env.AWS_REGION
  }),

  gcp: new Storage({
    projectId: process.env.GCP_PROJECT_ID,
    keyFilename: process.env.GCP_KEY_FILE
  }),

  local: {
    basePath: process.env.UPLOAD_DIR || './uploads',
    maxSize: parseInt(process.env.MAX_FILE_SIZE || '52428800')
  }
};
```

---

## ⚡ Performance Optimization

### Database Optimization

#### Query Optimization
```sql
-- Analyze slow queries
SELECT
  query,
  calls,
  total_time,
  mean_time,
  rows,
  100.0 * shared_blks_hit / nullif(shared_blks_hit + shared_blks_read, 0) AS hit_percent
FROM pg_stat_statements
ORDER BY total_time DESC
LIMIT 20;

-- Update table statistics
ANALYZE cyber_cases;
ANALYZE evidence;
ANALYZE users;

-- Vacuum maintenance
VACUUM ANALYZE cyber_cases;
VACUUM ANALYZE evidence;
```

#### Index Optimization
```sql
-- Find missing indexes
SELECT
  schemaname,
  tablename,
  attname,
  n_distinct,
  correlation
FROM pg_stats
WHERE schemaname = 'public'
AND n_distinct > 100
AND correlation < 0.1;

-- Create composite indexes for common queries
CREATE INDEX CONCURRENTLY idx_cases_status_priority_assigned
ON cyber_cases(status, priority, assigned_to_id);

CREATE INDEX CONCURRENTLY idx_evidence_case_created
ON evidence(case_id, created_at DESC);
```

### Application Optimization

#### Caching Strategy
```typescript
// Redis caching implementation
import Redis from 'ioredis';

const redis = new Redis(process.env.REDIS_URL);

// Cache frequently accessed data
export async function getCachedCaseStats() {
  const cacheKey = 'dashboard:case_stats';
  const cached = await redis.get(cacheKey);

  if (cached) {
    return JSON.parse(cached);
  }

  const stats = await calculateCaseStats();
  await redis.setex(cacheKey, 300, JSON.stringify(stats)); // 5 minutes

  return stats;
}

// Cache invalidation
export async function invalidateCaseCache(caseId: string) {
  await redis.del(`case:${caseId}`);
  await redis.del('dashboard:case_stats');
  await redis.del('analytics:offense_types');
}
```

#### Image Optimization
```typescript
// Optimize image processing
import sharp from 'sharp';

export async function processImageEvidence(buffer: Buffer) {
  // Create multiple sizes efficiently
  const [thumbnail, medium, large] = await Promise.all([
    sharp(buffer)
      .resize(150, 150, { fit: 'cover' })
      .jpeg({ quality: 80 })
      .toBuffer(),

    sharp(buffer)
      .resize(300, 300, { fit: 'inside', withoutEnlargement: true })
      .jpeg({ quality: 85 })
      .toBuffer(),

    sharp(buffer)
      .resize(1200, 1200, { fit: 'inside', withoutEnlargement: true })
      .jpeg({ quality: 90 })
      .toBuffer()
  ]);

  return { thumbnail, medium, large };
}
```

### Infrastructure Optimization

#### CDN Configuration
```javascript
// Next.js optimization
module.exports = {
  images: {
    domains: ['cdn.pngpolice.gov.pg'],
    formats: ['image/webp', 'image/avif'],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384]
  },

  compress: true,

  headers: async () => [
    {
      source: '/api/:path*',
      headers: [
        { key: 'Cache-Control', value: 'no-store, max-age=0' }
      ]
    },
    {
      source: '/uploads/:path*',
      headers: [
        { key: 'Cache-Control', value: 'public, max-age=31536000, immutable' }
      ]
    }
  ]
};
```

---

## 🔧 Troubleshooting

### Common Issues

#### Authentication Problems
```typescript
// Debug authentication issues
export async function debugAuth(email: string) {
  const user = await db.user.findUnique({
    where: { email },
    include: {
      sessions: { orderBy: { expires: 'desc' }, take: 5 },
      auditLogs: {
        where: { action: 'LOGIN' },
        orderBy: { timestamp: 'desc' },
        take: 10
      }
    }
  });

  return {
    userExists: !!user,
    isActive: user?.isActive,
    lastLogin: user?.lastLogin,
    recentSessions: user?.sessions.length || 0,
    recentLogins: user?.auditLogs.length || 0
  };
}
```

#### Database Connection Issues
```sql
-- Check connection limits
SELECT
  setting as max_connections,
  (SELECT count(*) FROM pg_stat_activity) as current_connections,
  (SELECT count(*) FROM pg_stat_activity WHERE state = 'active') as active_connections
FROM pg_settings
WHERE name = 'max_connections';

-- Check for connection leaks
SELECT
  datname,
  usename,
  state,
  count(*)
FROM pg_stat_activity
GROUP BY datname, usename, state
ORDER BY count(*) DESC;
```

#### Performance Issues
```typescript
// Monitor API performance
const performanceDebug = {
  slowQueries: [],

  logSlowQuery(query: string, duration: number) {
    if (duration > 1000) { // > 1 second
      this.slowQueries.push({
        query: query.substring(0, 100),
        duration,
        timestamp: new Date()
      });
    }
  },

  getSlowQueries() {
    return this.slowQueries
      .sort((a, b) => b.duration - a.duration)
      .slice(0, 10);
  }
};
```

### Error Handling

#### Global Error Handler
```typescript
// Global error boundary
export default function GlobalError({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    // Log error to monitoring service
    logError(error);
  }, [error]);

  return (
    <div className="error-boundary">
      <h2>Something went wrong!</h2>
      <details style={{ whiteSpace: 'pre-wrap' }}>
        {error && error.toString()}
      </details>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}
```

#### API Error Handling
```typescript
// Standardized API error responses
export function handleApiError(error: unknown) {
  console.error('API Error:', error);

  if (error instanceof z.ZodError) {
    return NextResponse.json({
      error: 'Validation failed',
      details: error.errors
    }, { status: 400 });
  }

  if (error instanceof PrismaClientKnownRequestError) {
    return NextResponse.json({
      error: 'Database error',
      code: error.code
    }, { status: 500 });
  }

  return NextResponse.json({
    error: 'Internal server error'
  }, { status: 500 });
}
```

---

## 🛠️ Maintenance Procedures

### Regular Maintenance Tasks

#### Daily Tasks
- Monitor system health and performance
- Review error logs and alerts
- Check backup completion status
- Monitor disk space and resource usage
- Review active user sessions
- Check for failed email notifications

#### Weekly Tasks
- Analyze database performance statistics
- Review and clean up old log files
- Update system documentation
- Test backup and recovery procedures
- Review user access and permissions
- Monitor integration health

#### Monthly Tasks
- Security patch updates
- Database maintenance and optimization
- Performance trend analysis
- User training and certification review
- System capacity planning
- Disaster recovery testing

#### Quarterly Tasks
- Security audit and vulnerability assessment
- Full system backup testing
- Documentation updates
- User feedback collection and analysis
- Infrastructure scaling review
- Third-party service evaluation

### Update Procedures

#### Application Updates
```bash
#!/bin/bash
# Application update script

# Stop application
pm2 stop cyber-crime-app

# Backup current version
cp -r /app/current /app/backup-$(date +%Y%m%d)

# Download new version
git pull origin main

# Install dependencies
bun install

# Run database migrations
bunx prisma migrate deploy

# Build application
bun run build

# Start application
pm2 start cyber-crime-app

# Verify deployment
curl -f http://localhost:3000/api/health || exit 1

echo "Update completed successfully"
```

#### Database Updates
```sql
-- Pre-update checks
SELECT version();
SELECT pg_size_pretty(pg_database_size('cyber_crime_monitoring'));

-- Backup before update
\! pg_dump cyber_crime_monitoring > pre_update_backup.sql

-- Apply migrations
\i migrations/001_add_new_features.sql

-- Post-update verification
SELECT COUNT(*) FROM cyber_cases;
SELECT COUNT(*) FROM evidence;
```

### System Health Checks

#### Automated Health Check Script
```bash
#!/bin/bash
# System health check script

echo "=== PNG Police Cyber Crime System Health Check ==="
echo "Date: $(date)"
echo

# Check application status
echo "1. Application Status:"
pm2 status cyber-crime-app
echo

# Check database connectivity
echo "2. Database Connectivity:"
psql -h localhost -U postgres -d cyber_crime_monitoring -c "SELECT 'Database OK' as status;"
echo

# Check disk space
echo "3. Disk Space:"
df -h | grep -E '/$|/app|/backups'
echo

# Check memory usage
echo "4. Memory Usage:"
free -h
echo

# Check recent errors
echo "5. Recent Errors (last 24 hours):"
journalctl --since="24 hours ago" --priority=3 | tail -10
echo

# Check backup status
echo "6. Backup Status:"
ls -la /backups/database/ | tail -5
echo

echo "=== Health Check Complete ==="
```

---

## 📞 Emergency Procedures

### Incident Response Plan

#### Severity Levels
- **P1 (Critical)**: System completely down, data loss risk
- **P2 (High)**: Major functionality affected, security breach
- **P3 (Medium)**: Some features not working, performance issues
- **P4 (Low)**: Minor issues, cosmetic problems

#### Contact Information
- **System Administrator**: [Primary Admin Contact]
- **Database Administrator**: [DB Admin Contact]
- **Security Officer**: [Security Contact]
- **Unit Commander**: [Commander Contact]
- **Vendor Support**: [Technical Support]

#### Escalation Matrix
1. **L1 Support**: System Administrator (Response: 1 hour)
2. **L2 Support**: Senior Technical Team (Response: 2 hours)
3. **L3 Support**: Vendor/External Support (Response: 4 hours)
4. **Management**: Unit Commander (Notification: Immediate)

### Recovery Procedures

#### System Restore Checklist
- [ ] Assess scope of failure
- [ ] Notify stakeholders
- [ ] Activate backup systems
- [ ] Begin data recovery
- [ ] Test recovered systems
- [ ] Gradually restore services
- [ ] Monitor for issues
- [ ] Document incident
- [ ] Conduct post-mortem

---

**This administration guide ensures proper management and maintenance of the PNG Police Cyber Crime Monitoring System. Regular review and updates of procedures are essential for optimal system performance and security.**
