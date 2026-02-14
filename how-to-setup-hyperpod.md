# How to Setup SageMaker HyperPod EKS

**AWS Account**: `081416279078` | **Region**: `us-east-1` | **IAM User**: `gonsoomoon`

**Workshop**: https://catalog.us-east-1.prod.workshops.aws/workshops/dcac6f7a-3c61-4978-8344-7535526bf743/en-US/02-smhp-rig

---

## Step 1: IAM Policy Setup

Created IAM policy `hyperpod-eks-policy` (v2) and attached to IAM user `gonsoomoon`.

**Policy ARN**: `arn:aws:iam::081416279078:policy/hyperpod-eks-policy`

<details>
<summary>IAM Policy JSON (click to expand)</summary>

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "SageMakerClusterAdmin",
            "Effect": "Allow",
            "Action": [
                "sagemaker:CreateCluster",
                "sagemaker:DeleteCluster",
                "sagemaker:UpdateCluster",
                "sagemaker:AddTags",
                "sagemaker:ListTags",
                "sagemaker:ListClusters",
                "sagemaker:ListClusterNodes",
                "sagemaker:ListComputeQuotas",
                "sagemaker:DescribeCluster",
                "sagemaker:DescribeClusterNode",
                "sagemaker:UpdateClusterSoftware",
                "sagemaker:BatchAddClusterNodes",
                "sagemaker:BatchDeleteClusterNodes",
                "sagemaker:ListClusterSchedulerConfigs",
                "sagemaker:DeleteClusterSchedulerConfig",
                "sagemaker:DeleteComputeQuota"
            ],
            "Resource": "*"
        },
        {
            "Sid": "EKSAccess",
            "Effect": "Allow",
            "Action": ["eks:*"],
            "Resource": "*"
        },
        {
            "Sid": "EKSAuthPodIdentity",
            "Effect": "Allow",
            "Action": ["eks-auth:AssumeRoleForPodIdentity"],
            "Resource": "*"
        },
        {
            "Sid": "EC2Networking",
            "Effect": "Allow",
            "Action": [
                "ec2:Describe*",
                "ec2:CreateSecurityGroup",
                "ec2:CreateTags",
                "ec2:DeleteSecurityGroup",
                "ec2:AuthorizeSecurityGroup*",
                "ec2:RevokeSecurityGroup*",
                "ec2:CreateNetworkInterface",
                "ec2:DeleteNetworkInterface",
                "ec2:DetachNetworkInterface",
                "ec2:AttachNetworkInterface",
                "ec2:AssignPrivateIpAddresses",
                "ec2:ModifyNetworkInterfaceAttribute",
                "ec2:UnassignPrivateIpAddresses"
            ],
            "Resource": "*"
        },
        {
            "Sid": "ECRImagePull",
            "Effect": "Allow",
            "Action": [
                "ecr:BatchCheckLayerAvailability",
                "ecr:BatchGetImage",
                "ecr:GetAuthorizationToken",
                "ecr:GetDownloadUrlForLayer"
            ],
            "Resource": "*"
        },
        {
            "Sid": "CloudFormation",
            "Effect": "Allow",
            "Action": ["cloudformation:*"],
            "Resource": "*"
        },
        {
            "Sid": "IAMPassRole",
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::*:role/*",
            "Condition": {
                "StringEquals": {
                    "iam:PassedToService": [
                        "sagemaker.amazonaws.com",
                        "eks.amazonaws.com",
                        "lambda.amazonaws.com",
                        "cloudformation.amazonaws.com"
                    ]
                }
            }
        },
        {
            "Sid": "IAMRoleManagement",
            "Effect": "Allow",
            "Action": [
                "iam:CreateRole",
                "iam:DeleteRole",
                "iam:GetRole",
                "iam:TagRole",
                "iam:AttachRolePolicy",
                "iam:DetachRolePolicy",
                "iam:PutRolePolicy",
                "iam:DeleteRolePolicy",
                "iam:CreateServiceLinkedRole"
            ],
            "Resource": [
                "arn:aws:iam::*:role/*sagemaker*",
                "arn:aws:iam::*:role/*eks*",
                "arn:aws:iam::*:role/*hyperpod*"
            ]
        },
        {
            "Sid": "S3Access",
            "Effect": "Allow",
            "Action": ["s3:*"],
            "Resource": "*"
        },
        {
            "Sid": "CloudWatchLogs",
            "Effect": "Allow",
            "Action": ["logs:*", "cloudwatch:*"],
            "Resource": "*"
        },
        {
            "Sid": "SSMAccess",
            "Effect": "Allow",
            "Action": ["ssm:*"],
            "Resource": "*"
        },
        {
            "Sid": "FSxAccess",
            "Effect": "Allow",
            "Action": ["fsx:*"],
            "Resource": "*"
        },
        {
            "Sid": "LambdaAccess",
            "Effect": "Allow",
            "Action": ["lambda:*"],
            "Resource": "*"
        },
        {
            "Sid": "KMSAccess",
            "Effect": "Allow",
            "Action": ["kms:Decrypt", "kms:GenerateDataKey"],
            "Resource": "*",
            "Condition": {
                "StringLike": {
                    "kms:ViaService": [
                        "sagemaker.*.amazonaws.com",
                        "ec2.*.amazonaws.com",
                        "s3.*.amazonaws.com",
                        "eks.*.amazonaws.com"
                    ]
                }
            }
        }
    ]
}
```

</details>

---

## Step 2: Environment Setup

Installed the following tools on the development environment:

| Tool | Version | Purpose |
|------|---------|---------|
| AWS CLI | v2.31.10 | AWS service interaction (min v2.17.47 required) |
| kubectl | v1.31.3 | Kubernetes API interaction with EKS cluster |
| eksctl | v0.222.0 | EKS cluster management, IAM OIDC provider, CSI drivers |
| Helm | v3.20.0 | Kubernetes package manager for installing dependencies |

---

## Step 3: Create S3 Buckets & HyperPod Cluster

### S3 Buckets

Created input/output S3 buckets for Nova model customization:

| Bucket | Name | Purpose |
|--------|------|---------|
| Input | `nova-input-20260214-4ff3` | Input data for Nova model customization |
| Output | `nova-output-20260214-c6fe` | Output artifacts from model customization |

### HyperPod Cluster Creation (Pending)

Using the SageMaker AI Console **Quick Setup** option:

1. Navigate to SageMaker AI Console > HyperPod > Create Cluster
2. Select Quick Setup
3. Add a Restricted Instance Group (RIG) with:
   - Instance type: `ml.p5.48xlarge`
   - Input S3: `s3://nova-input-20260214-4ff3`
   - Output S3: `s3://nova-output-20260214-c6fe`
   - FSx for Lustre: service-managed filesystem
4. Submit and wait 10-15 minutes for InService status

**Status**: Waiting for service quota increase (`ml.p5.48xlarge for cluster usage`)
