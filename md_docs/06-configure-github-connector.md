# Configure Wiz GitHub Connector

## Objective
Connect Wiz to your GitHub repository to enable code scanning, secret detection, and Infrastructure-as-Code (IaC) security analysis directly in your source control.

## Overview
The Wiz GitHub Connector integrates with your GitHub repositories to scan for security issues before code reaches production. This "shift-left" approach catches vulnerabilities early in the development lifecycle when they're cheaper and easier to fix.

## Prerequisites
- GitHub repository created (from previous step)
- Wiz console access
- GitHub account with repository admin permissions

## Instructions

### Step 1: Access Wiz Console
1. Open Chrome browser
2. Navigate to your Wiz tenant URL
3. Log in with your lab credentials

### Step 2: Navigate to Code Security
1. In Wiz console, click **Code** in the left navigation
2. Select **Repositories**
3. Click **Add Repository** or **Connect Git Provider**

### Step 3: Choose GitHub Integration
1. Select **GitHub** as the provider
2. Choose **OAuth App** integration (recommended for ease)
3. Click **Connect to GitHub**

### Step 4: Authorize Wiz
You'll be redirected to GitHub:

1. Review the permissions Wiz is requesting:
   - Read access to code
   - Read access to metadata
   - Read access to pull requests
2. Click **Authorize Wiz**
3. You may need to confirm your GitHub password

<aside class="note">
<strong>What Wiz Can Access</strong>: The OAuth integration allows Wiz to read your code and scan for security issues, but it cannot modify code, create commits, or change repository settings.
</aside>

### Step 5: Select Repository
Back in Wiz console:

1. You'll see a list of your GitHub repositories
2. Find and select **cisotopia-vulnerable-app** (or your repository name)
3. Click **Add Selected Repositories**

### Step 6: Configure Scan Settings
1. **Scan frequency**: Set to **On every commit** (default)
2. **Scan scope**: Select all scan types:
   - ✅ Secrets scanning
   - ✅ Vulnerability scanning (SCA - Software Composition Analysis)
   - ✅ IaC scanning (Dockerfile, Kubernetes manifests)
   - ✅ Code scanning (SAST - Static Application Security Testing)
3. **Notifications**: Leave default settings
4. Click **Save**

### Step 7: Trigger Initial Scan
The first scan happens automatically, but you can also trigger manually:

1. Navigate to **Code** > **Repositories**
2. Find your repository
3. Click the **⋯** menu
4. Select **Scan Now**

### Step 8: Wait for Scan Results
The initial scan takes 2-5 minutes depending on repository size:

1. Watch the status indicator change from "Scanning..." to "Completed"
2. The scan analyzes:
   - All source code files
   - Dependencies in `package.json`
   - Dockerfile configuration
   - Any IaC templates
   - Git history for exposed secrets

### Step 9: Review Initial Findings

<aside class="warning">
<strong>Expected Findings</strong>: Remember, this is an intentionally vulnerable application. You should see numerous security issues!
</aside>

Navigate through the findings:

1. **Secrets**: Click to see hardcoded credentials, API keys, tokens
2. **Vulnerabilities**: Check dependency vulnerabilities (outdated packages)
3. **IaC Issues**: Review Dockerfile misconfigurations
4. **Code Issues**: Examine SQL injection, XSS vulnerabilities

Example findings you might see:
- 🔴 **CRITICAL**: Hardcoded database password in source code
- 🔴 **HIGH**: SQL injection vulnerability in user input
- 🟠 **MEDIUM**: Dockerfile running as root user
- 🟠 **MEDIUM**: Vulnerable npm packages (outdated dependencies)
- 🟡 **LOW**: Missing security headers

### Step 10: Explore a Finding
Click on one of the critical findings:

1. **Issue description**: What the vulnerability is
2. **File location**: Exact line of code with the problem
3. **Risk**: Why this matters
4. **Remediation**: How to fix it
5. **References**: Links to CVE, CWE, or security documentation

<aside class="tip">
<strong>Learning Opportunity</strong>: Don't fix these issues yet! In later phases, you'll learn how to use Wiz's automated remediation and policy enforcement to address these systematically.
</aside>

## Expected Outcomes
- GitHub repository connected to Wiz
- Initial security scan completed
- Comprehensive view of code vulnerabilities
- Understanding of "shift-left" security approach
- Baseline of security posture before remediation

## Key Takeaways

<aside class="tip">
<strong>Shift-Left Security</strong> means catching security issues as early as possible in the development lifecycle:
- <strong>Code scanning</strong>: Find issues before commit
- <strong>Repository scanning</strong>: Detect problems in existing code
- <strong>Pull request checks</strong>: Block vulnerable code from merging
- <strong>Automated remediation</strong>: Fix issues with suggested patches

<strong>Benefits:</strong>
- Cheaper to fix (earlier = less rework)
- Faster resolution (developers have context)
- Prevents vulnerabilities from reaching production
- Builds security awareness in development teams
</aside>

## Understanding the Findings

### Secrets Detection
Wiz scans for:
- API keys and tokens
- Database credentials
- Private keys and certificates
- Cloud provider credentials (AWS, Azure, GCP)
- Third-party service credentials

### Vulnerability Scanning (SCA)
Analyzes dependencies for:
- Known CVEs in npm packages
- Outdated libraries with security patches
- License compliance issues
- Transitive dependency vulnerabilities

### IaC Scanning
Checks infrastructure-as-code for:
- Dockerfile best practices
- Container security misconfigurations
- Kubernetes manifest issues
- Terraform/CloudFormation problems

### Code Scanning (SAST)
Static analysis for:
- SQL injection
- Cross-site scripting (XSS)
- Path traversal
- Command injection
- Insecure deserialization

## Troubleshooting

### Repository Not Showing in List
- Ensure repository is public or Wiz has access to private repos
- Verify OAuth authorization completed successfully
- Check repository isn't archived or empty
- Refresh the repository list

### Scan Stuck or Failed
- Check repository size isn't too large
- Verify Wiz has read access to all files
- Review scan logs in Wiz console
- Try triggering manual scan again

### No Findings Detected
- Verify `.gitignore` didn't exclude all code files
- Ensure code was actually pushed to GitHub
- Check scan settings include all vulnerability types
- Wait a few more minutes (large repos take longer)

## Next Steps
With both AWS and GitHub connected to Wiz, and your code in source control, you'll next test the application locally using Docker to understand its functionality before deploying to the cloud.

<aside class="note">
<strong>Estimated completion time</strong>: 10-15 minutes
</aside>
