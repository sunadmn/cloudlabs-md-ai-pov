# Deploy EKS Cluster from VS Code Terminal

## Objective
Deploy an Amazon EKS (Elastic Kubernetes Service) cluster using AWS CLI from your developer workstation. The cluster will be empty (no security tooling yet) - you'll add Wiz monitoring in Phase 2.

## Overview
EKS is AWS's managed Kubernetes service. As a developer, you'll use Infrastructure as Code (CloudFormation) to deploy your Kubernetes cluster via command line - just like real DevOps teams do.

**Important**: We're deploying just the infrastructure now. Security tooling (Wiz) comes later!

## Prerequisites
- Windows EC2 developer workstation with VS Code
- AWS CLI configured
- ECR repository with container image (from previous step)
- IAM permissions for EKS and CloudFormation

## Architecture Overview

Your EKS cluster will include:
- **Control Plane**: Managed by AWS (public + private endpoints)
- **Worker Nodes**: 2x t3.medium EC2 instances in private subnets
- **VPC Configuration**: Uses existing VPC with NAT Gateway
- **IAM Roles**: For cluster management and node operations
- **No Wiz yet**: Clean cluster ready for security tooling in Phase 2

## Instructions

### Step 1: Connect to Your Developer Workstation

#### Option A: Fleet Manager (Browser-Based RDP) ✅ RECOMMENDED

1. Go to **AWS Systems Manager Console**
2. Navigate to **Fleet Manager** in the left sidebar
3. Find your EC2 instance: `<stack-name>-WindowsVM`
4. Click on the instance
5. Click **Node actions** → **Connect** → **Connect with Remote Desktop**
6. Your browser opens a native RDP session - no password needed!

<aside class="tip">
<strong>Why Fleet Manager?</strong>
- No RDP client installation needed
- No password management
- Uses IAM for authentication
- Works from any browser
</aside>

#### Option B: Traditional RDP (Backup Method)

If you prefer a native RDP client:

```bash
# Get RDP password from Secrets Manager
aws secretsmanager get-secret-value \
  --secret-id <inject key="RDPPasswordSecretName" /> \
  --query SecretString \
  --output text | jq -r '.password'

# Copy the password, then connect via RDP client:
# Server: <inject key="EC2PublicIp" />
# Username: Administrator
# Password: <paste from above>
```

### Step 2: Open VS Code Terminal

Once connected to your Windows developer workstation:

1. Open **Visual Studio Code**
2. Click **Terminal** → **New Terminal**
3. You should see a PowerShell prompt

<aside class="note">
<strong>Developer Workstation</strong>: This EC2 instance has VS Code, Docker, AWS CLI, kubectl, and Helm pre-installed. Think of it as your cloud-based development environment!
</aside>

### Step 3: Verify AWS CLI Configuration

Test your AWS access:

```powershell
# Verify AWS CLI works
aws sts get-caller-identity

# Should show your AWS account ID and IAM role
```

### Step 4: Get VPC and Subnet Information

Your EKS cluster will deploy into the EXISTING VPC (same VPC as your Lambda, RDS, and EC2):

```powershell
# Get VPC ID
$VpcId = aws cloudformation describe-stacks `
  --stack-name <inject key="MainStackName" /> `
  --query "Stacks[0].Outputs[?OutputKey=='VpcId'].OutputValue" `
  --output text

Write-Host "VPC ID: $VpcId"

# Get Private Subnet IDs
$SubnetIds = aws cloudformation describe-stacks `
  --stack-name <inject key="MainStackName" /> `
  --query "Stacks[0].Outputs[?OutputKey=='PrivateSubnetIds'].OutputValue" `
  --output text

Write-Host "Subnet IDs: $SubnetIds"
```

<aside class="tip">
<strong>Why existing VPC?</strong> Your EKS pods will communicate with RDS database and share the same network infrastructure. One VPC, all services!
</aside>

### Step 5: Deploy EKS Cluster

Now deploy the cluster using CloudFormation:

```powershell
# Deploy EKS cluster
aws cloudformation create-stack `
  --stack-name cisotopia-eks `
  --template-url https://lab-scafold.s3.amazonaws.com/cisotopia-bootcamp/iac/cf/eks/eks-root.yaml `
  --parameters `
      ParameterKey=CloudLabsDeploymentID,ParameterValue=<inject key="CloudLabsDeploymentID" /> `
      ParameterKey=ClusterName,ParameterValue=cisotopia-eks-cluster `
      ParameterKey=VpcId,ParameterValue=$VpcId `
      ParameterKey=PrivateSubnetIds,ParameterValue=`"$SubnetIds`" `
  --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM `
  --region us-east-1

Write-Host "EKS cluster deployment started!"
```

<aside class="warning">
<strong>Patient Required</strong>: EKS cluster creation takes 15-20 minutes. The control plane needs to be fully provisioned, then worker nodes launch and join the cluster.
</aside>

### Step 6: Monitor Stack Creation

Watch the deployment progress:

```powershell
# Monitor stack status (refreshes every 30 seconds)
while ($true) {
    $status = aws cloudformation describe-stacks `
        --stack-name cisotopia-eks `
        --query "Stacks[0].StackStatus" `
        --output text

    $timestamp = Get-Date -Format "HH:mm:ss"
    Write-Host "[$timestamp] Stack Status: $status"

    if ($status -like "*COMPLETE*" -or $status -like "*FAILED*") {
        break
    }

    Start-Sleep -Seconds 30
}

Write-Host "Final Status: $status"
```

Or watch in the AWS Console:
1. Navigate to **CloudFormation** service
2. Find stack `cisotopia-eks`
3. Click **Events** tab to see detailed progress

Status progression:
1. `CREATE_IN_PROGRESS` - Stack being created
2. IAM roles created (~2 min)
3. EKS control plane created (~10 min)
4. Worker nodes launching (~5 min)
5. `CREATE_COMPLETE` - Stack successfully created ✅

<aside class="coffee">
☕ <strong>Coffee Break Time!</strong> This is a perfect 15-20 minute break. Grab coffee, stretch your legs, or review the Wiz console to see what vulnerabilities were found in your container image.
</aside>

### Step 7: Configure kubectl Access

Once the stack shows `CREATE_COMPLETE`, configure kubectl:

```powershell
# Update kubeconfig to connect to your new cluster
aws eks update-kubeconfig `
  --name cisotopia-eks-cluster `
  --region us-east-1

Write-Host "kubectl configured for cisotopia-eks-cluster"

# Verify connection
kubectl get nodes
```

Expected output (after ~2 minutes for nodes to reach Ready):
```
NAME                          STATUS   ROLES    AGE   VERSION
ip-10-0-2-123.ec2.internal    Ready    <none>   3m    v1.28.0
ip-10-0-3-234.ec2.internal    Ready    <none>   3m    v1.28.0
```

<aside class="tip">
<strong>Understanding kubectl</strong>: The `kubectl` command is your control panel for Kubernetes. It talks to the EKS API server to manage pods, deployments, services, and more.
</aside>

### Step 8: Explore Your Empty Cluster

Check what's running in your brand new cluster:

```powershell
# View all namespaces
kubectl get namespaces

# You should see:
# - default (your apps go here)
# - kube-system (Kubernetes core components)
# - kube-public (public cluster info)

# View what's running
kubectl get pods --all-namespaces

# You'll see only Kubernetes system pods:
# - coredns (DNS)
# - aws-node (VPC CNI for networking)
# - kube-proxy (service networking)

# Check cluster info
kubectl cluster-info

# View worker nodes in detail
kubectl describe nodes
```

### Step 9: Verify Network Configuration

Confirm your cluster is properly networked:

```powershell
# Check nodes are in private subnets
kubectl get nodes -o wide

# Verify nodes can pull images from ECR
kubectl run test-pod --image=nginx --restart=Never --rm -it -- echo "Network works!"

# If you see "Network works!", your cluster can reach the internet via NAT Gateway ✅
```

## Expected Outcomes
- ✅ EKS cluster deployed successfully
- ✅ 2 worker nodes in Ready state
- ✅ kubectl configured and working
- ✅ Cluster is empty (no application workloads)
- ✅ **NO Wiz sensor yet** (that's intentional!)

## Understanding What You Built

### EKS Control Plane (AWS Managed)
- Kubernetes API server
- etcd database (configuration storage)
- Scheduler (decides which node runs which pod)
- Controller manager (maintains desired state)

**You don't manage this** - AWS handles updates, backups, and high availability!

### Worker Nodes (Your EC2 Instances)
- 2x t3.medium instances in private subnets
- Run your application pods
- Managed by EKS node group (auto-scaling ready)
- Can reach internet via NAT Gateway
- Can access RDS database (same VPC)

### Why No Wiz Sensor Yet?

**Pedagogical decision**: We want you to:
1. See an empty cluster first
2. Try to deploy your vulnerable app
3. Realize there's no security monitoring
4. Manually install Wiz in Phase 2
5. Appreciate the value Wiz provides!

This is more realistic - many teams add security tooling to existing clusters.

## Troubleshooting

### Stack Creation Fails

Common issues:
```powershell
# Check stack events for error details
aws cloudformation describe-stack-events `
  --stack-name cisotopia-eks `
  --max-items 20

# Look for events with Status = "CREATE_FAILED"
```

Possible causes:
- Insufficient IAM permissions
- VPC/subnet configuration errors
- Resource limits exceeded (EKS/EC2 quotas)
- CloudFormation template URL incorrect

### Nodes Not Ready

```powershell
# Check node status details
kubectl describe nodes

# Look for errors in "Conditions" section

# Check if nodes registered with cluster
aws eks describe-nodegroup `
  --cluster-name cisotopia-eks-cluster `
  --nodegroup-name <nodegroup-name>
```

### kubectl Connection Fails

```powershell
# Re-update kubeconfig
aws eks update-kubeconfig `
  --name cisotopia-eks-cluster `
  --region us-east-1 `
  --debug

# Verify IAM permissions
aws eks describe-cluster --name cisotopia-eks-cluster
```

## Cost Considerations

<aside class="warning">
<strong>Important</strong>: This EKS cluster costs approximately <strong>$0.30/hour</strong> (~$7/day):
- EKS control plane: $0.10/hour
- 2x t3.medium nodes: ~$0.0416/hour each
- Data transfer: varies
- EBS volumes: ~$0.10/GB/month

<strong>Remember to delete the stack</strong> when not actively using the lab to avoid unnecessary charges!
</aside>

To delete the cluster when done:
```powershell
aws cloudformation delete-stack --stack-name cisotopia-eks
```

## Security Note

Your cluster currently has:
- ✅ Private worker nodes (not publicly accessible)
- ✅ Network policies enabled
- ✅ IAM roles for service accounts (IRSA) ready
- ✅ Encrypted etcd storage
- ✅ CloudWatch logging enabled
- ❌ **NO runtime security monitoring** (Wiz not installed yet)
- ❌ **NO admission controller** (can deploy any image!)

**This is intentional** - you'll add Wiz security in Phase 2!

## What's Next?

**Phase 1 Complete!** 🎉

You've successfully:
- ✅ Configured Wiz cloud connector
- ✅ Created GitHub repository with application code
- ✅ Built and tested application locally
- ✅ Pushed container image to ECR
- ✅ **Deployed EKS cluster** (empty, no security tooling)

**In Phase 2, you'll:**
1. **Install Wiz sensor** manually using Helm
2. Try to deploy your vulnerable app → **BLOCKED!**
3. Install Wiz CLI to scan code locally
4. Fix vulnerabilities step by step
5. Finally deploy a secure application

<aside class="note">
<strong>Estimated completion time</strong>: 25-30 minutes (20 min waiting, 5 min commands)
</aside>

---

## Discussion Questions

Before moving to Phase 2, think about:

<aside class="tip">
<strong>Reflect on This</strong>:
- What could go wrong deploying apps to this cluster right now?
- How would you know if a vulnerable image was deployed?
- What happens if someone deploys a cryptominer?
- Why might companies add security tooling after cluster creation?
</aside>

**Ready for Phase 2**: Install Wiz sensor and test the admission controller!
