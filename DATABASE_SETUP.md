# Database Setup Guide

This guide will help you set up the PostgreSQL database for the Cyber Crime Monitoring System.

## Prerequisites

1. **PostgreSQL Database** - You need access to a PostgreSQL database (version 12 or higher)
2. **Database Credentials** - Username, password, host, port, and database name

## Setup Options

### Option 1: Local PostgreSQL Installation

1. Install PostgreSQL on your local machine
2. Create a new database:
   ```sql
   CREATE DATABASE cyber_crime_monitoring;
   ```
3. Update the `.env` file with your local database credentials

### Option 2: Cloud Database (Recommended for Production)

Popular cloud PostgreSQL providers:
- **Neon** (Free tier available): https://neon.tech
- **Supabase** (Free tier available): https://supabase.com
- **Railway** (Free tier available): https://railway.app
- **PlanetScale**
- **Heroku Postgres**

### Option 3: Docker PostgreSQL (Development)

1. Create a `docker-compose.yml` file:
   ```yaml
   version: '3.8'
   services:
     postgres:
       image: postgres:15
       environment:
         POSTGRES_DB: cyber_crime_monitoring
         POSTGRES_USER: postgres
         POSTGRES_PASSWORD: password123
       ports:
         - "5432:5432"
       volumes:
         - postgres_data:/var/lib/postgresql/data

   volumes:
     postgres_data:
   ```

2. Start the database:
   ```bash
   docker-compose up -d
   ```

## Environment Configuration

1. Copy the `.env` file and update the database URL:
   ```env
   DATABASE_URL="postgresql://username:password@localhost:5432/cyber_crime_monitoring?schema=public"
   ```

2. Replace the placeholders:
   - `username`: Your PostgreSQL username
   - `password`: Your PostgreSQL password
   - `localhost:5432`: Your database host and port
   - `cyber_crime_monitoring`: Your database name

## Database Setup Commands

Once your database is running and configured:

### 1. Generate Prisma Client
```bash
bun run db:generate
```

### 2. Run Database Migrations
```bash
bun run db:migrate
```

### 3. Seed the Database with Initial Data
```bash
bun run db:seed
```

### 4. One-Command Setup (All Steps)
```bash
bun run setup
```

## Test User Accounts

After seeding, you can log in with these accounts:

| Role | Email | Password |
|------|-------|----------|
| Admin | admin@pngpolice.gov.pg | password123 |
| Unit Commander | commander@pngpolice.gov.pg | password123 |
| Senior Investigator | john.doe@pngpolice.gov.pg | password123 |
| Investigator | sarah.wilson@pngpolice.gov.pg | password123 |
| Analyst | mike.johnson@pngpolice.gov.pg | password123 |

## Database Management

### View Database (Prisma Studio)
```bash
bun run db:studio
```
This opens a web interface at http://localhost:5555 to view and edit your data.

### Reset Database (Development Only)
```bash
bun run db:reset
```
⚠️ **Warning**: This deletes all data and recreates the database.

## Database Schema Overview

The database includes the following main tables:

### Core Tables
- **users** - System users with role-based access
- **cyber_cases** - Cyber crime cases
- **victims** - Case victims information
- **suspects** - Suspect profiles and information
- **evidence** - Digital evidence with chain of custody
- **investigations** - Investigation tracking and management

### Supporting Tables
- **social_media_profiles** - Monitored social media accounts
- **legal_requests** - Legal data requests to platforms
- **notifications** - System notifications
- **audit_logs** - Complete audit trail
- **knowledge_articles** - Knowledge base articles
- **offense_categories** - Cybercrime categorization

### Authentication Tables (NextAuth.js)
- **accounts** - OAuth provider accounts
- **sessions** - User sessions
- **verification_tokens** - Email verification tokens

## Production Considerations

### Security
1. Use strong, unique passwords for database users
2. Enable SSL/TLS for database connections
3. Restrict database access to specific IP addresses
4. Regular database backups
5. Monitor database access logs

### Performance
1. Set up proper database indexing
2. Configure connection pooling
3. Monitor database performance
4. Set up read replicas for reporting queries

### Backup Strategy
1. Automated daily backups
2. Point-in-time recovery capability
3. Test backup restoration procedures
4. Store backups in multiple locations

## Troubleshooting

### Connection Issues
- Verify database is running
- Check connection string format
- Confirm firewall settings
- Test database connectivity

### Migration Errors
- Check database permissions
- Verify schema compatibility
- Review migration logs
- Ensure database is accessible

### Performance Issues
- Monitor connection pool usage
- Check for long-running queries
- Analyze database logs
- Consider database optimization

## Support

For additional database setup assistance:
1. Check the Prisma documentation: https://prisma.io/docs
2. Review PostgreSQL documentation
3. Contact your database provider support
4. Consult the development team
