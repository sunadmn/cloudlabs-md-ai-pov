# Create ECR Repository and Push Container Image

## Objective
Create an Amazon Elastic Container Registry (ECR) repository and push your Docker container image to AWS for deployment to EKS.

## Overview
ECR is AWS's managed container registry service, providing secure storage for your Docker images. In this step, you'll create a repository, authenticate Docker to ECR, tag your image, and push it to AWS.

## Prerequisites
- Docker image built locally (`vulnerable-app:local`)
- AWS CLI configured with credentials
- ECR permissions granted to your IAM user
- Docker running on developer workstation

## Instructions

### Step 1: Create ECR Repository

Using AWS Console:
1. Navigate to **Amazon ECR** service
2. Click **Create repository**
3. Configure repository:
   - **Visibility**: Private
   - **Repository name**: `cisotopia-vulnerable-app`
   - **Tag immutability**: Disabled (for learning flexibility)
   - **Scan on push**: Disabled (we'll enable Wiz scanning instead)
4. Click **Create repository**

Or using AWS CLI:
```bash
aws ecr create-repository \
  --repository-name cisotopia-vulnerable-app \
  --region us-east-1
```

### Step 2: Retrieve ECR Login Credentials

Authenticate Docker to your ECR registry:

```bash
# Get login password and authenticate
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <YOUR_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com
```

Replace `<YOUR_ACCOUNT_ID>` with: `<inject key="AWSAccountId" />`

You should see: `Login Succeeded`

<aside class="tip">
<strong>Note</strong>: This authentication is temporary and expires after 12 hours. You'll need to re-authenticate if you return to this lab later.
</aside>

### Step 3: Tag Your Local Image for ECR

Tag your image with the ECR repository URI:

```bash
docker tag vulnerable-app:local <YOUR_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/cisotopia-vulnerable-app:v1.0
```

Verify the tag:
```bash
docker images | grep cisotopia-vulnerable-app
```

You should see both tags:
- `vulnerable-app:local`
- `<account-id>.dkr.ecr.us-east-1.amazonaws.com/cisotopia-vulnerable-app:v1.0`

### Step 4: Push Image to ECR

Push the tagged image:

```bash
docker push <YOUR_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/cisotopia-vulnerable-app:v1.0
```

This may take several minutes depending on image size and network speed.

Monitor progress:
```
The push refers to repository [xxxxx.dkr.ecr.us-east-1.amazonaws.com/cisotopia-vulnerable-app]
5f70bf18a086: Pushing [==>        ]  5.123MB/10.24MB
...
```

### Step 5: Verify Image in ECR

Check AWS Console:
1. Go to **ECR** > **Repositories**
2. Click on **cisotopia-vulnerable-app**
3. Verify image with tag `v1.0` appears
4. Note the **Image URI** - you'll need this for EKS deployment

Or using CLI:
```bash
aws ecr describe-images \
  --repository-name cisotopia-vulnerable-app \
  --region us-east-1
```

### Step 6: Review Image Details

In the ECR console, examine:
- **Image size**: Note how large the unoptimized image is
- **Push timestamp**: When the image was uploaded
- **Image digest**: SHA256 hash for verification
- **Scan results**: Currently "Not scanned" (Wiz will handle this)

<aside class="note">
<strong>Security Consideration</strong>: This image contains all the vulnerabilities from your local build. In Phase 2, you'll use Wiz to scan and identify these security issues before deployment.
</aside>

### Step 7: Test Pulling Image (Optional)

Verify you can pull the image back:

```bash
# Remove local copy
docker rmi <YOUR_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/cisotopia-vulnerable-app:v1.0

# Pull from ECR
docker pull <YOUR_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/cisotopia-vulnerable-app:v1.0

# Run from ECR image
docker run -p 3000:3000 <YOUR_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/cisotopia-vulnerable-app:v1.0
```

## Expected Outcomes
- ECR repository created
- Container image successfully pushed
- Image accessible for EKS deployment
- Understanding of container registry workflow

## Best Practices (For Discussion)

### What's Wrong with This Approach?
This step demonstrates a common but problematic workflow:
1. ❌ No security scanning before push
2. ❌ No vulnerability assessment
3. ❌ No secrets detection
4. ❌ No policy enforcement
5. ❌ Manual process prone to human error

### What You'll Improve in Phase 2:
- ✅ Automated Wiz CLI scanning before push
- ✅ Custom policies for vulnerability thresholds
- ✅ Secrets detection in images
- ✅ GitHub Actions automation
- ✅ Admission controller to block vulnerable images

## Common Issues

### Authentication Errors
```bash
# Error: no basic auth credentials
# Solution: Re-run get-login-password command

aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <YOUR_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com
```

### Push Denied
- Verify IAM permissions include `ecr:PutImage`
- Check repository exists and name matches exactly
- Confirm you're in the correct AWS region

### Large Image Size
Your image may be large due to:
- Development dependencies included
- No multi-stage build optimization
- Unnecessary files in image layers

<aside class="tip">
These inefficiencies are intentional for learning. You'll optimize the image in later phases while also implementing security controls.
</aside>

## Image Tagging Strategy

For this lab, we use simple versioning (`v1.0`), but production systems should consider:

- **Semantic versioning**: `v1.2.3`
- **Git commit SHA**: `abc123f`
- **Build number**: `build-456`
- **Environment**: `prod`, `staging`, `dev`
- **Date stamps**: `2024-11-05`

Example:
```bash
docker tag app:latest ecr-uri/app:v1.2.3-abc123f-prod-20241105
```

## Next Steps
With your container image securely stored in ECR, you'll prepare the EKS cluster for deployment using CloudFormation templates.

<aside class="note">
<strong>Estimated completion time</strong>: 10-15 minutes
</aside>
