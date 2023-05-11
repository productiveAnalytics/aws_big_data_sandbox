# S3 bucket policy for providing public accesss to selective objects and/or allow CloudFront to access S3

## Step 1: Create bucket _cdn-bucket-20230510_ that allows all public access
## Step 2: Create two folders: contents/ and secrets/
## Step 3: Upload few images to contents/ and secrets/
e.g. 
- contents /
  - **my_public_profile.jpg**
  - dog_and_cat.png
- secrets /
  - my_private_photo.jpg
## Step 4: Define tag only on _contents/my_public_profile.jpg_
- is_public=yes
## Step 5: Define the S3 bucket policy
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "allow_public_access_only_to_objects_with_tag",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::cdn-bucket-20230510/*",
            "Condition": {
                "StringEquals": {
                    "s3:ExistingObjectTag/is_public": [
                        "yes",
                        "true"
                    ]
                }
            }
        }
    ]
}
```
## Step 6: Confirm the access
   | object path |expected to be publicly accessable |
   | --- | --- |
   | https://cdn-bucket-20230510.s3.amazonaws.com/contents/my_public_profile.jpg | Yes |
   | https://cdn-bucket-20230510.s3.amazonaws.com/contents/dog_and_cat.png | Should Not be |
   | https://cdn-bucket-20230510.s3.amazonaws.com/secretss/my_public_profile.jpg | Shoud NOT be |
## [Optional] Step 7: Create CloudFront distribution and point "Default root object" to _contents/my_public_profile.jpg_
