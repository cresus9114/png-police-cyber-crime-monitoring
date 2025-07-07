# Contributing to PNG Police Cyber Crime Monitoring System

Thank you for your interest in contributing to the Royal Papua New Guinea Police Force Cyber Crime Monitoring System. This document provides guidelines for contributing to this project.

## 🚨 Security Notice

This is a **law enforcement system** handling sensitive criminal investigation data. All contributions must comply with:
- Government security standards
- Data protection regulations
- Law enforcement protocols
- Chain of custody requirements

**Never commit sensitive data, credentials, or real case information.**

## 📋 Code of Conduct

### Expected Behavior
- Maintain professionalism appropriate for law enforcement technology
- Respect the sensitive nature of cyber crime investigations
- Follow all applicable laws and regulations
- Ensure code meets enterprise security standards

### Prohibited Behavior
- Committing real case data or personal information
- Introducing security vulnerabilities
- Adding dependencies without security review
- Bypassing authentication or authorization controls

## 🛡️ Security Requirements

### Before Contributing
1. **Security Review**: All contributions undergo security review
2. **Background Clearance**: Contributors may need security clearance verification
3. **NDA Agreement**: Signed non-disclosure agreement may be required
4. **Compliance Check**: Code must meet government security standards

### Security Guidelines
- Never commit secrets, API keys, or credentials
- Use secure coding practices (OWASP guidelines)
- Implement proper input validation
- Follow principle of least privilege
- Document security considerations

## 🚀 Getting Started

### Prerequisites
- Node.js 18+ (Bun recommended)
- PostgreSQL 12+
- Git knowledge
- Understanding of TypeScript/React

### Development Setup
1. **Fork and Clone**
   ```bash
   git clone https://github.com/YOUR_USERNAME/png-police-cyber-crime-monitoring.git
   cd png-police-cyber-crime-monitoring
   ```

2. **Install Dependencies**
   ```bash
   bun install
   ```

3. **Environment Setup**
   ```bash
   cp .env.example .env
   # Configure with development database
   ```

4. **Database Setup**
   ```bash
   bun run setup
   ```

5. **Start Development**
   ```bash
   bun run dev
   ```

## 📝 Contribution Process

### 1. Issue Creation
- **Bug Reports**: Include steps to reproduce, expected behavior, system info
- **Feature Requests**: Provide use case, requirements, security considerations
- **Security Issues**: Use private security reporting (see SECURITY.md)

### 2. Development Workflow
1. Create feature branch: `git checkout -b feature/description`
2. Implement changes following code standards
3. Add/update tests for new functionality
4. Update documentation if needed
5. Run quality checks: `bun run lint`
6. Commit with descriptive messages

### 3. Pull Request Guidelines
- **Clear Description**: Explain what changes and why
- **Security Impact**: Document any security implications
- **Testing**: Include test cases and manual testing steps
- **Documentation**: Update relevant docs
- **Small Changes**: Keep PRs focused and reviewable

### Pull Request Template
```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update
- [ ] Security enhancement

## Security Considerations
- [ ] No sensitive data committed
- [ ] Security review completed
- [ ] No new vulnerabilities introduced

## Testing
- [ ] Unit tests added/updated
- [ ] Manual testing completed
- [ ] Database migration tested

## Documentation
- [ ] README updated
- [ ] API docs updated
- [ ] User guides updated
```

## 🏗️ Code Standards

### TypeScript/React Guidelines
- Use TypeScript for all new code
- Follow existing code formatting (Biome)
- Implement proper error handling
- Use React best practices
- Document complex logic

### Database Guidelines
- Use Prisma for database operations
- Include proper migrations
- Maintain referential integrity
- Follow naming conventions
- Document schema changes

### API Guidelines
- RESTful API design
- Proper HTTP status codes
- Comprehensive input validation
- Rate limiting considerations
- Authentication/authorization

### Testing Requirements
- Unit tests for business logic
- Integration tests for APIs
- Security testing for auth flows
- Performance testing for critical paths

## 🔍 Review Process

### Code Review Criteria
1. **Functionality**: Does it work as intended?
2. **Security**: Are there any vulnerabilities?
3. **Performance**: Is it optimized?
4. **Maintainability**: Is it clean and documented?
5. **Compliance**: Does it meet standards?

### Review Timeline
- **Initial Review**: 2-3 business days
- **Security Review**: 3-5 business days
- **Final Approval**: 1-2 business days

## 📚 Resources

### Documentation
- [Database Setup Guide](DATABASE_SETUP.md)
- [Deployment Guide](DEPLOYMENT_GUIDE.md)
- [User Training Guide](USER_TRAINING_GUIDE.md)
- [Admin Guide](ADMIN_GUIDE.md)

### Technologies
- [Next.js Documentation](https://nextjs.org/docs)
- [Prisma Documentation](https://www.prisma.io/docs)
- [NextAuth.js Documentation](https://next-auth.js.org)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)

### Security Resources
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [Government Security Standards](https://www.nist.gov/)

## 🏛️ Government Compliance

### Legal Requirements
- Comply with Papua New Guinea data protection laws
- Follow international law enforcement cooperation standards
- Maintain audit trails for all changes
- Ensure data sovereignty requirements

### Documentation Requirements
- All changes must be documented
- Security impact assessments required
- Performance impact documentation
- User training material updates

## 📞 Contact

### Development Team
- **Technical Lead**: cybercrime-dev@pngpolice.gov.pg
- **Security Officer**: security@pngpolice.gov.pg
- **System Administrator**: admin@pngpolice.gov.pg

### Emergency Security Contact
For critical security issues: security-emergency@pngpolice.gov.pg

---

**Royal Papua New Guinea Police Force**
*Cyber Crime Unit - Development Team*

By contributing to this project, you acknowledge understanding of its sensitive nature and commit to maintaining the highest standards of security and professionalism.
