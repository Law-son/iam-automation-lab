# IAM Automation Lab

Automates the creation of IAM users, groups, and policies using AWS CloudFormation. A shared one-time password is generated and stored securely in AWS Secrets Manager.

## Resources Created

| Resource | Type | Description |
|---|---|---|
| `IAMUserPassword` | Secrets Manager Secret | Auto-generated 16-char one-time password |
| `EC2UserGroup` | IAM Group | Grants EC2 read + run permissions |
| `S3UserGroup` | IAM Group | Grants S3 list buckets permission |
| `ec2-user1` | IAM User | Member of EC2UserGroup |
| `ec2-user2` | IAM User | Member of EC2UserGroup |
| `s3-user` | IAM User | Member of S3UserGroup |

All users are created with console access and are required to change their password on first login.

## Permissions Summary

**EC2UserGroup**
- `ec2:DescribeInstances`, `ec2:DescribeImages`, `ec2:DescribeKeyPairs`, and other read actions
- `ec2:RunInstances`, `ec2:CreateTags`

**S3UserGroup**
- `s3:ListAllMyBuckets`
- `s3:GetBucketLocation`

## Prerequisites

- AWS CLI configured with sufficient permissions to create IAM and Secrets Manager resources
- AWS CloudFormation access

## Deployment

```bash
aws cloudformation deploy \
  --template-file templates/iam-resources.yaml \
  --stack-name iam-automation-lab \
  --capabilities CAPABILITY_NAMED_IAM
```

## Retrieve the One-Time Password

After deployment, retrieve the generated password from Secrets Manager:

```bash
aws secretsmanager get-secret-value \
  --secret-id iam-lab/one-time-password \
  --query SecretString \
  --output text
```

## Outputs

| Output | Description |
|---|---|
| `PasswordSecretARN` | ARN of the Secrets Manager secret |
| `EC2UserGroupName` | Name of the EC2 IAM group |
| `S3UserGroupName` | Name of the S3 IAM group |
| `EC2User1Name` | IAM username for ec2-user1 |
| `EC2User2Name` | IAM username for ec2-user2 |
| `S3UserName` | IAM username for s3-user |

## Cleanup

```bash
aws cloudformation delete-stack --stack-name iam-automation-lab
```

