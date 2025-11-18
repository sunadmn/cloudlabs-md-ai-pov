# Configure Wiz Cloud Connector

## Objective
Configure the Wiz Cloud Connector to enable security scanning and monitoring of your AWS environment, learning about IAM role delegation and CloudFormation execution roles.

## Overview
The Wiz Cloud Connector integrates with your AWS account to provide continuous security posture management, vulnerability scanning, and compliance monitoring. In this step, you'll manually configure the connector using a CloudFormation execution role, a common pattern in enterprise environments where direct IAM permissions are restricted.

## Prerequisites
- Access to Wiz console
- AWS Account ID from your lab environment
- Understanding of IAM roles and trust relationships

## Instructions

### Step 1: Access Wiz Console
1. Open Chrome browser on your developer workstation
2. Navigate to your Wiz tenant URL (provided in lab credentials)
3. Log in with your lab credentials

### Step 2: Navigate to Cloud Connectors
1. In Wiz console, click **Settings** (gear icon)
2. Select Deploymnets
3. Choose **Cloud** Then **AWS**
4. Click **Add Cloud Account**

### Step 3: Configure AWS Connection
1. Select **Account**
2. Choose **CloudFormation**
3. Select **Add DSPM Permissions**
4. Select **Add EKS Scanning** 🤔 Perhaps hold back on this and use this as a learning experince later
5. Select **Add Cloud To Code Permissions**
6. Choose **Launch CloudFormation**
7. Keep this page open you will come back to it in a sec

### Step 4: Understanding IAM Role Delegation

<aside class="note">
<strong>Important</strong>: In enterprise environments, developers often don't have direct permissions to create all AWS resources. Instead, they use <strong>CloudFormation execution roles</strong> to deploy infrastructure.
</aside>

**Why use execution roles?**
- Your student IAM user has restricted permissions
- The Wiz connector needs to create IAM roles and policies
- CloudFormation can assume a more privileged role to perform these actions
- This is a security best practice called "role delegation"

### Step 5: Find the CloudFormation Execution Role

1. Open AWS Cloud Shell

Your lab environment includes a pre-created CloudFormation execution role. Find it using AWS CLI:

```bash
aws iam list-roles --query 'Roles[?contains(RoleName, `CFRoleStack-CloudFormationExecutionRo`)].{Name:RoleName,ARN:Arn}' --output table
```

Copy the full ARN (looks like: `arn:aws:iam::ACCOUNT:role/CisoBoot-CFRoleStack-CloudFormationExecutionRo-1`)

### Step 6: Deploy Wiz Connector Stack

Deploy the Wiz connector using the execution role:

```bash
aws cloudformation create-stack \
  --stack-name WizConnector-Lab \
  --template-url <WIZ_TEMPLATE_URL> \
  --parameters ParameterKey=ExternalId,ParameterValue=<EXTERNAL_ID> \
  --role-arn <EXECUTION_ROLE_ARN> \
  --capabilities CAPABILITY_NAMED_IAM
```

**Replace:**
- `<WIZ_TEMPLATE_URL>` - The CloudFormation template URL from Wiz console
- `<EXTERNAL_ID>` - The External ID from Step 3
- `<EXECUTION_ROLE_ARN>` - The role ARN from Step 5

**What's happening here?**
1. You (student user) initiate the stack creation
2. The `--role-arn` parameter tells CloudFormation to assume that role
3. CloudFormation uses the execution role's permissions to create resources
4. The Wiz connector IAM roles and policies are created successfully
5. You get credit for creating the stack, but don't need elevated permissions

### Step 7: Monitor Stack Creation

Watch the deployment progress:

```bash
# Check overall stack status
aws cloudformation describe-stacks \
  --stack-name WizConnector-Lab \
  --query 'Stacks[0].StackStatus' \
  --output text

# View recent events
aws cloudformation describe-stack-events \
  --stack-name WizConnector-Lab \
  --max-items 10 \
  --query 'StackEvents[*].[Timestamp,ResourceType,ResourceStatus,ResourceStatusReason]' \
  --output table
```

Wait for the status to show `CREATE_COMPLETE` (typically 2-3 minutes).

### Step 8: Verify Connector in Wiz Console

1. Return to Wiz console
2. Navigate to **Settings** > **Cloud Accounts**
3. Your AWS account should appear with status **Connected**
4. Wait for initial scan to complete (5-10 minutes)
5. Click on the account to view discovered resources

### Step 9: Explore Initial Scan Results

Once the scan completes, review what Wiz discovered:

1. Navigate to **Inventory** to see all AWS resources
2. Check **Security Graph** for resource relationships
3. View **Issues** for any security findings
4. Note the types of resources Wiz can monitor (EC2, RDS, S3, IAM, etc.)

## Expected Outcomes

- Understanding of CloudFormation execution roles and IAM delegation
- Successfully deployed Wiz connector using role assumption
- Wiz actively scanning your AWS environment
- Initial security posture visible in Wiz console

## Key Takeaways

<aside class="tip">
<strong>CloudFormation Execution Roles</strong> are a security best practice that allows:
- Separation of duties between who initiates deployments and what permissions are used
- Developers to deploy infrastructure without having elevated IAM permissions
- Organizations to enforce security controls on infrastructure-as-code deployments
- Audit trails showing both the user and the role used for deployments
</aside>

**Why this matters:**
- In production environments, you rarely have direct admin access
- Role delegation is how enterprises manage infrastructure safely
- Understanding this pattern prepares you for real-world DevOps workflows
- This same pattern is used by CI/CD pipelines (GitHub Actions, Jenkins, etc.)

## Troubleshooting

### Connector Not Showing as Connected
- Verify External ID matches exactly between CLI and Wiz console
- Check IAM role trust relationship allows Wiz to assume it
- Review CloudFormation stack outputs for the connector role ARN
- Check CloudTrail logs for API errors

### Stack Creation Failed
- Ensure you used the `--role-arn` parameter
- Verify the execution role ARN is correct
- Check that you have `cloudformation:CreateStack` and `iam:PassRole` permissions
- Review stack events for specific error messages

### "Access Denied" When Creating Stack
- Confirm your student policy allows `cloudformation:CreateStack`
- Verify you have `iam:PassRole` for the execution role
- Check that the execution role exists and is accessible

## Next Steps

With the Wiz connector configured and actively scanning, you'll next upload your vulnerable application code to GitHub for version control and collaboration.

<aside class="note">
<strong>Estimated completion time</strong>: 15-20 minutes
</aside>
