# Test Application with Local Database Connection

## Objective
Test the application's database connectivity using the pre-configured RDS instance, ensuring full functionality before container deployment.

## Overview
Now that you've built the application locally, you'll connect it to the AWS RDS database that's been pre-seeded with test data. This validates that all application features work correctly with persistent storage.

## Prerequisites
- Application built successfully from previous step
- Docker installed and running
- RDS database endpoint from lab environment
- AWS credentials configured

## Instructions

### Step 1: Locate RDS Connection Details
1. Open AWS Console
2. Navigate to **RDS** service
3. Click on **Databases**
4. Find database: `cisotopia-lab-db`
5. Note the **Endpoint** and **Port**

Or retrieve via CLI:
```bash
aws rds describe-db-instances \
  --db-instance-identifier cisotopia-lab-db \
  --query 'DBInstances[0].Endpoint.Address' \
  --output text
```

### Step 2: Configure Database Environment Variables
Create a `.env` file in your application directory:

```bash
# Database Configuration
DB_HOST=<YOUR_RDS_ENDPOINT>
DB_PORT=5432
DB_NAME=appdb
DB_USER=appuser
DB_PASSWORD=<inject key="DBPassword" />

# Application Settings
NODE_ENV=development
APP_PORT=3000
```

<aside class="warning">
<strong>Security Note</strong>: The `.env` file contains sensitive credentials. Verify it's in your `.gitignore` to prevent accidentally committing secrets to GitHub.
</aside>

### Step 3: Review Seeded Database Data
The RDS database has been pre-populated with test data including:
- User accounts (for authentication testing)
- Product catalog
- Shopping cart data
- **Fake PII** (names, emails, addresses)
- **Test affiliate codes**

```bash
# Connect to database to explore
psql -h <RDS_ENDPOINT> -U appuser -d appdb

# List tables
\dt

# View sample data
SELECT * FROM users LIMIT 5;
SELECT * FROM products LIMIT 10;
```

### Step 4: Run Application with Database Connection

```bash
# Ensure .env file is present
cat .env

# Start application
npm run dev

# Or with Docker
docker run -p 3000:3000 --env-file .env vulnerable-app:local
```

### Step 5: Test Application Features

Open browser to `http://localhost:3000` and test:

1. **User Authentication**
   - Try logging in with test credentials
   - Register a new user account
   - Verify data persists in RDS

2. **Product Browsing**
   - Browse product catalog
   - Search for products
   - View product details

3. **Shopping Cart**
   - Add items to cart
   - Update quantities
   - Checkout process

4. **Database Queries**
   - Monitor application logs for SQL queries
   - Note any slow queries or errors

### Step 6: Check for Security Issues (Initial Reconnaissance)

While testing, observe potential security issues:

```bash
# View application logs
docker logs <container_id>

# Check for exposed secrets
grep -r "password" src/
grep -r "api_key" src/

# Look for SQL injection opportunities
# Try adding ' OR '1'='1 in login fields
```

<aside class="tip">
<strong>Learning Opportunity</strong>: This application intentionally contains vulnerabilities. Take notes on what you find - you'll address these issues in Phase 2 using Wiz security tools.
</aside>

### Step 7: Verify Seed Data Isolation

The application's `data/` folder contains a local seed file used for development:

```bash
# Check local seed data
type data\seed-data.json
```

<aside class="note">
<strong>Important Discovery</strong>: This seed file contains the same sensitive PII that's in the production RDS database. It should NOT be committed to version control, but you'll find it's already in your GitHub repository - creating a data exposure risk!
</aside>

## Expected Outcomes
- Application successfully connects to RDS
- All features function correctly
- Data persists across restarts
- Understanding of application architecture
- Initial security concerns identified

## Common Issues

### Connection Timeout
- Verify security group allows inbound from your IP
- Check VPC and subnet configuration
- Confirm RDS endpoint is correct

### Authentication Failures
```bash
# Reset database password if needed
aws rds modify-db-instance \
  --db-instance-identifier cisotopia-lab-db \
  --master-user-password <NEW_PASSWORD> \
  --apply-immediately
```

### Application Errors
- Check `.env` file formatting
- Verify all required environment variables are set
- Review application logs for specific errors

## Security Findings to Note

At this stage, you should observe:

1. ✅ **Seed Data Exposure**: Local `data/seed-data.json` with PII in GitHub
2. ✅ **Hardcoded Credentials**: Database passwords in code comments
3. ✅ **Affiliate Codes**: Discount codes visible in source
4. ✅ **SQL Injection Potential**: Unvalidated user inputs
5. ✅ **Dependency Vulnerabilities**: Old package versions

<aside class="warning">
These security issues are intentional for training purposes. In Phase 2, you'll use Wiz CLI and IDE plugins to detect and remediate these vulnerabilities.
</aside>

## Next Steps
With the application fully functional and database connectivity confirmed, you'll next create an Amazon ECR repository and push your container image for deployment to EKS.

<aside class="note">
<strong>Estimated completion time</strong>: 15-20 minutes
</aside>
