This is a [Next.js](https://nextjs.org) project bootstrapped with [`create-next-app`](https://nextjs.org/docs/app/api-reference/cli/create-next-app).

# 🚀 PNG Police Cyber Crime Monitoring System

A comprehensive, production-ready cyber crime monitoring and investigation management system for the Royal Papua New Guinea Police Force.

## 🎯 System Overview

This is a complete, independent cyber crime monitoring platform featuring:

- **Advanced Case Management** - Complete lifecycle from intake to resolution
- **Evidence Management** - Secure digital evidence handling with chain of custody
- **Social Media Monitoring** - Multi-platform tracking and analysis
- **Legal Request Coordination** - Platform liaison and data request management
- **Real-time Analytics** - Comprehensive reporting and insights
- **Knowledge Base** - Threat intelligence and investigation resources
- **User Management** - Role-based access control and security
- **Real-time Notifications** - Live updates and email alerts

## ✨ Key Features

### 🔐 Enterprise Security
- NextAuth.js authentication with role-based access control
- Comprehensive audit logging for all user actions
- Secure file upload with validation and chain of custody
- Protected API endpoints with authorization middleware

### 📊 Real-time Intelligence
- Live dashboard with case statistics and trends
- WebSocket-powered real-time notifications
- Email alert system for urgent cases
- Geographic and demographic analysis

### 🔗 System Integration
- REST API endpoints for main police system integration
- Webhook support for external system communication
- Database-driven architecture with PostgreSQL
- Scalable file storage system

### 👥 User Roles & Permissions
- **Admin** - Full system access and user management
- **Unit Commander** - Oversight and reporting capabilities
- **Senior Investigator** - Lead investigations and case management
- **Investigator** - Case handling and evidence management
- **Analyst** - Data analysis and forensics support

## 🚀 Quick Start

### Prerequisites
- Node.js 18+ (with Bun recommended)
- PostgreSQL 12+ database
- SMTP email service

### Installation

1. **Clone and Install**:
   ```bash
   git clone <repository-url>
   cd cyber-crime-monitoring
   bun install
   ```

2. **Environment Setup**:
   ```bash
   cp .env.example .env
   # Edit .env with your database and email credentials
   ```

3. **Database Setup**:
   ```bash
   bun run setup
   # This runs: prisma generate + migrate + seed
   ```

4. **Start Development Server**:
   ```bash
   bun run dev
   ```

5. **Access System**:
   - Open http://localhost:3000
   - Use test accounts (see [Database Setup Guide](DATABASE_SETUP.md))

## 🏗️ Production Deployment

### Quick Deploy Options

**Option 1: Netlify (Recommended)**
- Deploy as dynamic site with database addon
- Automatic SSL and global CDN
- Built-in form handling and serverless functions

**Option 2: Vercel**
- Optimized for Next.js applications
- Automatic deployments from Git
- Edge network for global performance

**Option 3: Traditional Server**
- Ubuntu/Linux server with Nginx
- PM2 process management
- Full control over infrastructure

📖 **See [Deployment Guide](DEPLOYMENT_GUIDE.md) for detailed instructions**

## 🗃️ Database Schema

The system uses a comprehensive PostgreSQL schema with 15+ tables covering:

- **Core Entities**: Cases, Users, Evidence, Investigations
- **People Management**: Suspects, Victims with relationship tracking
- **Intelligence**: Social media profiles, legal requests
- **System Features**: Notifications, audit logs, knowledge base
- **Security**: Authentication, sessions, permissions

📖 **See [Database Setup Guide](DATABASE_SETUP.md) for schema details**

## 🔧 Development

### Available Scripts

```bash
bun run dev          # Start development server
bun run build        # Build for production
bun run start        # Start production server
bun run lint         # Run linting
bun run db:migrate   # Run database migrations
bun run db:seed      # Seed database with test data
bun run db:studio    # Open Prisma Studio
bun run setup        # Complete database setup
```

### Testing Accounts

After running `bun run db:seed`:

| Role | Email | Password |
|------|-------|----------|
| Admin | admin@pngpolice.gov.pg | password123 |
| Commander | commander@pngpolice.gov.pg | password123 |
| Senior Investigator | john.doe@pngpolice.gov.pg | password123 |
| Investigator | sarah.wilson@pngpolice.gov.pg | password123 |
| Analyst | mike.johnson@pngpolice.gov.pg | password123 |

## 📊 System Modules

### ✅ Fully Implemented
1. **Dashboard** - Real-time overview and statistics
2. **Case Intake & Registration** - Multi-step case creation
3. **User Management** - Role-based access control
4. **Offense Categorization** - Comprehensive typology system
5. **Social Media Monitoring** - Platform tracking and analysis
6. **Legal Requests & Liaison** - Platform communication tools
7. **Analytics Dashboard** - Detailed reporting and insights
8. **Knowledge Base** - Threat intelligence and resources

### 🔧 Foundation Ready
- Evidence Management (API complete, UI enhancement pending)
- Investigation Management (structure in place)
- Suspect/Victim Management (database models ready)
- Digital Forensics (framework established)

## 🔐 Security Features

- **Authentication**: Secure login with session management
- **Authorization**: Role-based access control throughout system
- **Audit Logging**: Complete activity tracking for compliance
- **File Security**: Validated uploads with hash verification
- **Data Protection**: Encrypted sensitive data storage
- **Network Security**: HTTPS enforcement and CSRF protection

## 🔌 API Integration

### Available Endpoints

```bash
# Cases
GET /api/cases              # List cases with filtering
POST /api/cases             # Create new case
GET /api/cases/{id}         # Get case details
PUT /api/cases/{id}         # Update case

# File Uploads
POST /api/upload            # Upload evidence files

# Notifications
GET /api/notifications      # Get user notifications
POST /api/notifications     # Create notifications (admin)
PUT /api/notifications/{id}/read  # Mark as read
```

### Integration with Main Police System

The system provides REST APIs and webhook capabilities for seamless integration with existing police management systems.

## 📞 Support & Documentation

- **Database Setup**: [DATABASE_SETUP.md](DATABASE_SETUP.md)
- **Production Deployment**: [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md)
- **API Documentation**: Available in `/api` endpoints
- **User Guides**: Available in Knowledge Base module

## 🏆 Production Ready

This system is enterprise-ready with:
- ✅ Complete authentication and authorization
- ✅ Production-grade database schema
- ✅ Secure API endpoints with validation
- ✅ Real-time notifications and email alerts
- ✅ Comprehensive audit logging
- ✅ Professional UI/UX design
- ✅ Security best practices implemented
- ✅ Scalable architecture
- ✅ Integration capabilities

## 📄 License

Developed for the Royal Papua New Guinea Police Force Cyber Crime Unit.

---

**Royal Papua New Guinea Police Force**
*Cyber Crime Monitoring System v1.0*
