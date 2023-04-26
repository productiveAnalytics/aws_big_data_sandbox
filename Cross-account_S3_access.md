# Setup S3 access across the AWS account

Note: Bucket Policy (resource policy) always takes precedence over the IAM policy (Idenity policy)

## Situation 1
If the S3 in Owner AWS account has "bucket policy" that explicitly ALLOW the user from other account (using ARN), then the user can access the S3

## Situation 2
If the S3 in Owner AWS account has "bucket policy" that neither ALLOW nor DENY, then
1. Setup IAM Role in Owner AWS Account
2. Attach the Permission Policy to the role to grant access to S3
3. Establish Trust Policy to allow "entity outside the current AWS account" to assume that role
4. Finally in the other AWS account, create IAM Role with Permission to assume the role in Owner AWS account
