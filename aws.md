# Working with Amazon Web Services (AWS)

## Adding AWS-CLI to Ubuntu (v. 20.04)

## Hosting a static website on AWS S3 buckets

Sequence:  
1. setting up *S3 bucket* with *bucketname*, permissions and inserting content
  - unblock **all public access** by unticking box and confirming decision
  - add access policy with customized *bucketname*
    ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
              "s3:GetObject"
            ],
            "Resource": [
              "arn:aws:s3:::bucketname/*"
            ]
          }
        ]
      }
    ```
  - initial upload of files, e.g. from your own repository (Add Files / Folders)

2. setting up *Route 53* by adding a hosted zone
  - screenshot  
    ![alt text][img01]
  - copy AWS Route 53 DNS server names for use in step 3

3. add server names in Freenom Domain administration
  - screenshot  
    ![alt text][img02]

4. setting up AWS CodePipeline (one pipeline covered by FreeTier)
  - screenshot  
    ![alt text][img03]

[img01]:  ./Pictures/2021-09-01_AWS_Route53_HostedZoneS3.png "Setting up Route53 hosted zone for website S3 bucket"
[img02]:  ./Pictures/2021-09-01_freenom_DNS.png "Entering AWS Route 53 DNS servers to Freenom Domain Settings"
[img03]:  ./Pictures/2021-09-01_AWS_CodePipeline_GitHub-S3.png "Setup for AWS CodePipeline"