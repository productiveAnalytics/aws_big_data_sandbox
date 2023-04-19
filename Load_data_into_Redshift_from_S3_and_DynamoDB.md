# Load data into Redshift from S3 and DynamoDB

## Download sample data

Using Coud Shell, login to bastion host using provided username (e.g. cloud_user) and password
```
ssh cloud_user@bastion-public-IP
```
Interacively configure AWS CLI using Access Key and Secret Access Key
```
aws configure
```
Confirm the configuration
```
aws configure list
```
- **S3** data (CSV)
Download the CSV data file
```
curl -O -l https://raw.githubusercontent.com/linuxacademy/content-aws-database-specialty/master/S06_Additional%20Database%20Services/redshift-data.csv
```
- **DynamoDB** data (JSON)
```
curl -O -l https://raw.githubusercontent.com/linuxacademy/content-aws-database-specialty/master/S06_Additional%20Database%20Services/redshift-data.json
```

## Step 1: Create S3 bucket
```
export S3_BUCKET_NAME="redshift-import-20230418-2212"
aws s3 mb s3://${S3_BUCKET_NAME}
```
Confirm the bucket
```
aws s3 ls
```
Load data into S3 bucket
```
aws s3api put-object --bucket ${S3_BUCKET_NAME} --key redshift-data.csv --body redshift-data.csv
```
Confirm data in S3 bucket
```
aws s3 ls s3://${S3_BUCKET_NAME}
```
