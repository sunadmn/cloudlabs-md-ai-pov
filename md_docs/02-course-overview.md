# Welcome to the CISOtopia Security Bootcamp

## Course Overview

Welcome to the **CISOtopia Security Bootcamp** - a comprehensive, hands-on training program designed to teach modern cloud security practices using real-world scenarios. This bootcamp combines application development, infrastructure deployment, and security tooling to give you practical experience with cloud-native security workflows.

## What You'll Build

Throughout this bootcamp, you'll work with a **vulnerable e-commerce application** deployed on AWS. Your mission: transform it from a security liability into a production-ready, secure application using industry-leading security tools and best practices.

### The Application Stack

- **Frontend**: HTLM/CSS/JS web application
- **Backend**: Node.js API server
- **Database**: Amazon RDS PostgreSQL with customer data
- **Infrastructure**: Amazon EKS (Kubernetes) cluster
- **Container Registry**: Amazon ECR
- **Security Platform**: Wiz Cloud Security

## Bootcamp Structure

This bootcamp is organized into **6 progressive phases**, each building on the previous one:

### Phase 1: Developer Setup
Set up your development environment and deploy your first containerized application to AWS.
- Configure Wiz Cloud Connector for security monitoring
- Create GitHub repository and upload application code
- Build and test applications locally with Docker
- Push container images to Amazon ECR
- Deploy to Amazon EKS using CloudFormation

### Phase 2: Deployment & Security Scanning
Discover how Wiz protects your infrastructure and implement "shift-left" security practices.
- Experience Kubernetes admission controller blocking vulnerable deployments
- Install and use Wiz CLI for code, container, and IaC scanning
- Implement pre-commit hooks and GitHub Actions for automated security
- Remediate critical vulnerabilities and PII exposure
- Successfully deploy a secured application

### Phase 3: Security Policies & Governance
Learn to manage security at scale with custom policies and exception handling.
- Configure Wiz ignore rules for false positives
- Understand security policy governance
- Balance security enforcement with development velocity

### Phase 4: Advanced Security Controls
Implement sophisticated security controls for sensitive data protection.
- Create custom matchers for proprietary data (affiliate codes)
- Perform minor security remediations
- Fine-tune security detection capabilities

### Phase 5: Integration & Automation
Connect Wiz with external systems for automated incident response.
- Configure webhooks with AWS SNS
- Integrate security alerts with communication platforms
- Explore the Wiz Python SDK for custom automation
- Build programmatic security workflows

### Phase 6: AI-Powered Security (Future)
Leverage AI to accelerate security operations.
- Explore AI-assisted security analysis
- Install and configure MCP servers
- Use AI tools to enhance security decision-making

## Learning Objectives

By completing this bootcamp, you will:

- ✅ Understand modern cloud security architecture
- ✅ Implement defense-in-depth security controls
- ✅ Master shift-left security practices
- ✅ Configure automated security scanning in CI/CD pipelines
- ✅ Manage security policies and exceptions at scale
- ✅ Integrate security tools with business workflows
- ✅ Remediate real-world vulnerabilities
- ✅ Build secure containerized applications on AWS

## Lab Environment

Your fully-provisioned lab environment includes:

### AWS Resources
- **Amazon EKS Cluster**: Kubernetes environment with Wiz sensor
- **Amazon RDS Database**: PostgreSQL with seeded customer data
- **Amazon ECR Registry**: Private container image repository
- **IAM Roles & Policies**: Pre-configured security boundaries
- **CloudFormation Templates**: Infrastructure as Code for deployment

### Developer Tools
- **Windows VM Workstation**: Pre-installed development environment
  - Visual Studio Code
  - Docker Desktop
  - Git & GitHub CLI
  - Node.js & npm/gulp
  - Chrome browser
- **Wiz Platform Access**: Full security scanning and monitoring
- **GitHub Account**: For source code management

## The Security Challenge

Your application contains intentional security vulnerabilities representing real-world issues:

- 🔴 **Critical vulnerabilities** in npm dependencies
- 🔴 **PII exposure** in database seed files
- 🔴 **Hardcoded credentials** in source code
- 🔴 **Exposed API keys** and secrets
- 🔴 **Affiliate discount codes** in git history
- 🔴 **Malware test signatures**
- 🔴 **SQL injection** vulnerabilities
- 🔴 **Misconfigured infrastructure**

You'll use Wiz to detect, prioritize, and remediate these issues while learning how to prevent them in the future.

## Key Technologies

- **Cloud Platform**: Amazon Web Services (AWS)
- **Container Orchestration**: Kubernetes (Amazon EKS)
- **Container Runtime**: Docker
- **CI/CD**: GitHub Actions
- **Infrastructure as Code**: AWS CloudFormation
- **Security Platform**: Wiz Cloud Security
- **Programming**: Node.js, React, Python
- **Build Tools**: Gulp, npm

## Prerequisites

This bootcamp assumes basic familiarity with:
- Command line interfaces (bash/PowerShell)
- Git version control
- Cloud computing concepts
- Containers and Docker basics
- Basic programming concepts

**No prior security experience required!** This bootcamp is designed to teach security concepts from the ground up.

## Time Commitment

- **Total Duration**: 6-8 hours
- **Recommended Pace**: Complete 1-2 phases per session
- **Lab Access**: Available for the duration of your enrollment

## How to Use This Bootcamp

1. **Follow the Steps**: Each phase contains numbered steps - complete them in order
2. **Read Carefully**: Instructions include important context and security concepts
3. **Experiment**: The lab environment is yours to explore safely
4. **Ask Questions**: Use provided resources and documentation
5. **Take Notes**: Document what you learn for future reference

<aside class="tip">
<strong>Success Tip</strong>: Don't rush! Understanding <em>why</em> security controls work is more valuable than just following steps. Take time to explore the Wiz platform and understand what each security finding means.
</aside>

## Ready to Begin?

You're about to embark on a comprehensive journey through modern cloud security practices. By the end of this bootcamp, you'll have hands-on experience with real-world security tools and techniques used by leading organizations.

Let's start with **Phase 1: Developer Setup** and build your foundation!

---

**Need Help?**
- Review the lab environment documentation
- Check the Wiz platform documentation
- Consult AWS service guides
- Refer back to earlier phases as needed
