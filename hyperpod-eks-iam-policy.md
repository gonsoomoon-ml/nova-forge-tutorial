# SageMaker HyperPod EKS IAM Policy

**Policy ARN**: `arn:aws:iam::081416279078:policy/hyperpod-eks-policy` (v2)

**Attached to**: IAM user `gonsoomoon` (account `081416279078`)

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
