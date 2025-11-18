# Upload Application to GitHub

## Objective
Create a public GitHub repository and upload your vulnerable application code for version control, enabling future CI/CD integration and security scanning.

## Overview
Version control is essential for modern software development. In this step, you'll create a GitHub repository and push your local application code. This establishes the foundation for implementing security controls in later phases.

## Prerequisites
- GitHub account (create at github.com if needed)
- Git installed on developer workstation
- Application code on local developer VM
- Basic understanding of Git commands

## Instructions

### Step 1: Create GitHub Repository
1. Open Chrome and navigate to https://github.com
2. Log in to your GitHub account
3. Click the **+** icon in top right, select **New repository**
4. Configure repository:
   - **Repository name**: `cisotopia-vulnerable-app`
   - **Description**: `CISOtopia Bootcamp - Vulnerable Demo Application`
   - **Visibility**: **Public** (required for lab)
   - **Initialize**: Do NOT check any boxes
5. Click **Create repository**

<aside class="warning">
<strong>Important</strong>: The repository must be public for this lab. In production environments, sensitive code should always be in private repositories with proper access controls.
</aside>

### Step 2: Locate Application Code
On your developer workstation, the vulnerable application was cloned from the lab repository during VM setup:

```bash
cd C:\workspace\app
```

Verify the contents:
```bash
dir
```

You should see:
- `package.json` - Node.js dependencies
- `gulpfile.js` - Build configuration
- `Dockerfile` - Container configuration
- `src/` - Application source code
- `data/` - Sample seed data (contains PII - should be excluded from repo)
- `README.md` - Application documentation

### Step 3: Remove Existing Git Configuration

The cloned app has the lab's git configuration. Remove it and start fresh:

```bash
# Remove existing git folder
Remove-Item -Recurse -Force .git

# Initialize new repository
git init
git config user.name "Your Name"
git config user.email "your.email@example.com"
```

### Step 4: Review Application Files

Take a moment to explore the application structure:

```bash
# View package dependencies
type package.json

# Check for any existing .git folder
dir /a

# List all files including hidden ones
dir /a /s
```

<aside class="note">
<strong>Security Note</strong>: You'll notice the application contains intentional vulnerabilities including hardcoded credentials, SQL injection flaws, and exposed secrets. These will be addressed in later phases using Wiz security tools.
</aside>

### Step 5: Create .gitignore
Create a `.gitignore` file to exclude unnecessary files:

```bash
echo node_modules/ > .gitignore
echo dist/ >> .gitignore
echo .env >> .gitignore
echo *.log >> .gitignore
echo data/ >> .gitignore
```

<aside class="tip">
<strong>Best Practice</strong>: Always use `.gitignore` to prevent committing:
- Dependencies (`node_modules/`)
- Build artifacts (`dist/`)
- Environment files (`.env`)
- Log files
- <strong>Seed data with PII</strong> (`data/` - only needed for local testing)
</aside>

### Step 6: Stage and Commit Code

```bash
# Add all files
git add .

# Create initial commit
git commit -m "Initial commit: Vulnerable application for security training"

# Verify commit
git log
```

### Step 7: Connect to Remote Repository
Link your local repository to GitHub:

```bash
git remote add origin https://github.com/YOUR_USERNAME/cisotopia-vulnerable-app.git

# Verify remote
git remote -v
```

Replace `YOUR_USERNAME` with your actual GitHub username.

### Step 8: Push Code to GitHub

```bash
# Push to main branch
git branch -M main
git push -u origin main
```

You may be prompted for GitHub credentials:
- **Username**: Your GitHub username
- **Password**: Use a Personal Access Token (not your password)
  - Generate at: Settings > Developer settings > Personal access tokens
  - Required scope: `repo`

### Step 9: Verify Upload
1. Refresh your GitHub repository page
2. Confirm all files are visible
3. Check that commit history shows your initial commit
4. Verify `.gitignore` is working (node_modules not uploaded)

## Expected Outcomes
- Public GitHub repository created
- Application code successfully pushed
- Git history established
- Ready for CI/CD integration in later phases

## Security Considerations

### What's Exposed in This Upload?
This repository now contains:
- ✅ Application source code
- ✅ Configuration files
- ✅ Dockerfile
- ❌ **Hardcoded credentials** (intentional for learning)
- ❌ **API keys** in comments
- ❌ **Affiliate discount codes**
- ❌ **Seed data with PII**

<aside class="warning">
<strong>Real-World Impact</strong>: Exposing secrets in public repositories is a common source of security breaches. In later phases, you'll use Wiz to detect and prevent these exposures.
</aside>

## Common Issues

### Authentication Errors
If push fails with authentication error:
1. Generate GitHub Personal Access Token
2. Use token as password
3. Or use SSH keys instead of HTTPS

### Large File Warnings
If you see warnings about large files:
- Check that `.gitignore` is working
- Remove `node_modules/` if accidentally added:
  ```bash
  git rm -r --cached node_modules
  git commit -m "Remove node_modules"
  ```

### Branch Name Confusion
If your default branch is `master` instead of `main`:
```bash
git branch -M main
git push -u origin main
```

## Next Steps
With your code now in GitHub, you'll build and test the application locally using Docker and Gulp to understand its functionality before deploying to AWS.

<aside class="note">
<strong>Estimated completion time</strong>: 10-15 minutes
</aside>
