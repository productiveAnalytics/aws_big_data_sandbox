# To allow user to assume role with restricted permissions

## Step 1: Create Role named "TrustPolicy_Demo"
## Step 2: setup Trust Policy
Let "cloud_user" assume the role
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "mySid1",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::105955217431:user/cloud_user"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```
## Step 3: attach permissions
   - AmazonEC2FullAccess
   - AmazonS3FullAccess
   - AWSLambda_FullAccess
   - AWSCloudShellFullAccess
## Step 4: Comfirm in Cloud Shell
### While using directly for the existing logged-in user
```aws sts get-caller-identity```
```json
{
    "UserId": "AIDARRK3MKALXNHOOM5BH",
    "Account": "105955217431",
    "Arn": "arn:aws:iam::105955217431:user/cloud_user"
}
```
### While login using switching the role
    Switch role URL: https://signin.aws.amazon.com/switchrole?roleName=TrustPolicy_Demo&account=105955217431
```aws sts get-caller-identity```
**Note the difference in ARN**
```json
{
    "UserId": "AROARRK3MKAL4SCEY44KV:cloud_user",
    "Account": "105955217431",
    "Arn": "arn:aws:sts::105955217431:assumed-role/Demo/cloud_user"
}
```
