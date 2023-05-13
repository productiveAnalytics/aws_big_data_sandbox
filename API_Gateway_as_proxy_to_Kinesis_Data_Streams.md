# Deploying API Gateway as a Proxy for Kinesis Data Streams (KDS)

## Step 1: Create Kinesis Data Streams
1. Use AWS Portal to create a couple of KDS either On-Demand or Provisioned
2. Confirm from CloudShell
```
aws kinesis list-streams
```

## Step 2: Create a Custom Trust Policy
1. Visit IAM.
2. In the left-hand navigation menu, under Access management, select Roles.
3. In the upper right corner, click the Create role button.
4. Under Trusted entity type, select **Custom trust policy**.
5. Under Custom trust policy, paste the trust policy below:
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Allow_AssumeRole_to_API_GW",
      "Effect": "Allow",
      "Principal": {
        "Service": "apigateway.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```
6. In the bottom right corner, click the Next button.
7. In the search bar under Permissions policies, enter Kinesis.
8. In the search results, check the box next to AmazonKinesisFullAccess.
8. Click the Next button.
10. Under Role name, enter "APIG_as_Proxy_to_KDS".
11. Click the Create role button.
12. In the search bar under Roles, enter name "APIG_as_Proxy_to_KDS" and select the role
13. Copy the Role's ARN.

## Step 3: Deploy REST API in API GW
### Create API
1. In the search bar at the very top of the console, enter api.
2. From the search results, select API Gateway.
3. Click the Get Started button.
4. In the **REST API** area, click the Build button.
5. Click OK.
6. Under Create new API, click the radio button next to New API.
7. In API name, enter "Kinesis proxy"
8. Click the Create API button.
### Create Resource
1. At the top, select Actions.
2. From the dropdown menu, select **Create Resource**.
3. In Resource Name, enter Streams.
4. Click the "Create Resource" button
### Create Method
1. At the top, select Actions again.
2. From the dropdown menu, select **Create Method**.
3. Under /streams, select GET from the dropdown menu.
4. Click the check icon next to GET.
5. In Integration type, select **AWS Service**.
6. Set the following parameters for the method:
   - AWS Region: us-east-1 (from the menu)
   - AWS Service: Kinesis
   - HTTP method: POST
   - Action: Enter "ListStreams"
   - Execution role: Paste the ARN that you copied previously for the role: APIG_as_Proxy_to_KDS
7. Click the Save button.
8. Click Integration Request.
9. Click HTTP Headers to expand the section.
10. Select Add header.
   - Content-Type: 'application/x-amz-json-1.1' (Note: use quotes)
11. Click the plus icon to the right.
   - Click Mapping Templates to expand this section.
   - Select Add mapping template
   - Under Content-Type, enter application/json.
   - Click the checkmark icon to the right.
14. Click the Yes, secure this integration button.
15. In the code box under Generate template, enter emply JSON i.e. {}.
16. Click the Save button.

## Step 4: Test API Gateway to List Kinesis Streams
1. At the top, select Method Execution.
2. In the vertical Client pane, select TEST.
3. Click the Test button.
4. Make sure that you receive a Status: 200 result. 
   - In the logs, you should be able to see names of KDS like myStream and yourStream listed next to Streams.
5. Check the response
```json
{"HasMoreStreams":false,"StreamNames":["myDataStream","yourDataStream"],"StreamSummaries":[{"StreamARN":"arn:aws:kinesis:us-east-1:218137335374:stream/myDataStream","StreamCreationTimestamp":1.684013726E9,"StreamModeDetails":{"StreamMode":"ON_DEMAND"},"StreamName":"myDataStream","StreamStatus":"ACTIVE"},{"StreamARN":"arn:aws:kinesis:us-east-1:218137335374:stream/yourDataStream","StreamCreationTimestamp":1.684013748E9,"StreamModeDetails":{"StreamMode":"PROVISIONED"},"StreamName":"yourDataStream","StreamStatus":"ACTIVE"}]}
```
