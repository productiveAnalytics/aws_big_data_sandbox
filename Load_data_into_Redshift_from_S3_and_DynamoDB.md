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

## Step 1: Prepare data on S3
### Create S3 bucket
```
export S3_BUCKET_NAME="redshift-import-20230418-2212"
aws s3 mb s3://${S3_BUCKET_NAME}
```
Confirm the bucket
```
aws s3 ls
```
### Load sample data into S3 bucket
```
aws s3api put-object --bucket ${S3_BUCKET_NAME} --key redshift-data.csv --body redshift-data.csv
```
Confirm data in S3 bucket
```
aws s3 ls s3://${S3_BUCKET_NAME}
```

## Step 2: Create DynamoDB table and load JSON data
### Create DDB table
```
aws dynamodb create-table --table-name redshift-import --attribute-definitions AttributeName=ID,AttributeType=N --key-schema AttributeName=ID,KeyType=HASH --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=100
```
Confirm DDB table
```
aws dynamodb list-tables
```
### Load sample data into DDB table
```
aws dynamodb batch-write-item --request-items file://redshift-data.json
```
Confirm data in DDB table
```
aws dynamodb scan --table-name redshift-import
```

## Step 3: Create IAM Role
- In the IAM side menu, click Roles.
- Click Create role.
- Scroll down and select EC2 as the service.
  Click the first EC2 use case.
  Click Next: Permissions.
- On the Attach permissions policies page, in the Filter policies box, search for and select each of the following managed policies:
  1. AmazonS3ReadOnlyAccess
  2. AmazonDynamoDBReadOnlyAccess
- Click Next: Tags.
  Add the following tag:
  Key: name
  Value: redshift-import
- Click Next: Review.
  For Role name, enter "redshift-import".
- Click Create role.
- On the IAM > Roles page, in the search box, search for and select redshift-import.
- On the redshift-import Summary page, click the Trust relationships tab.
  Click Edit trust relationship.
    Change the "Service" line to:
    "Service": "redshift.amazonaws.com"
  Click Update Trust Policy.
  
#### Confirm the Role
Note the ARN of the role: "redshift-import"
```
aws iam get-role --role-name redshift-import
```
`
{
    "Role": {
        "Path": "/",
        "RoleName": "redshift-import",
        "RoleId": "AROA2MKN3YXJQ2LDWT2ZI",
        "Arn": "arn:aws:iam::713664742867:role/redshift-import",
        "CreateDate": "2023-04-19T03:46:49Z",
        "AssumeRolePolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Principal": {
                        "Service": "redshift.amazonaws.com"
                    },
                    "Action": "sts:AssumeRole"
                }
            ]
        },
        "Description": "Allows Redshift to call AWS services on your behalf.",
        "MaxSessionDuration": 14400,
        "Tags": [
            {
                "Key": "name",
                "Value": "redshift-import"
            }
        ],
        "RoleLastUsed": {}
    }
}
`
## Step 4: Load data to Redshift

### Get Redshift cluster into
```
aws redshift describe-clusters | head -25
```
e.g.
`
`
Note the Edpoint address and port and export as PGHOST and PGPORT
```
export PGHOST="redshiftcluster-mwtzbkky9dld.cd30wmfwyvzc.us-east-1.redshift.amazonaws.com"
export PGPORT=5439
```
Connect to Redshift clusster using known username and password
```
psql -U masteruser -p $PGPORT import-test
```

### Create table and import data from S3
#### On psql console, issue DDL
```
create table users_import_s3 (ID int, Name varchar, Department varchar, ExpenseCode int);
```
Confirm table 
```
\dt
```
#### Load data from S3
```
COPY users_import_s3  FROM 's3://redshift-import-20230418-2212/redshift-data.csv' IAM_ROLE 'arn:aws:iam::713664742867:role/redshift-import' DELIMITER ',' ;
```
#### Config data loaded in Redshift
```
SELECT * FROM users_import_s3 LIMIT 10;
```
