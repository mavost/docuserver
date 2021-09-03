# Working with Amazon Web Services (AWS)

## Adding AWS-CLI to Ubuntu (v. 20.04)
### Installation
- `curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64-2.0.30.zip" -o "awscliv2.zip"`  
  update above path by looking up [latest-version](https://github.com/aws/aws-cli/blob/v2/CHANGELOG.rst)
- `unzip awscliv2.zip && sudo ./aws/install && rm -rf aws && rm awscliv2.zip`

### Linking the CLI to your AWS account
- check "programatic access" in IAM user detail
- add new user
- add permissions - policies(left) - "AdministratorAccess"
- open User summary &rightarrow; "Security credentials" &downarrow; "Access keys" &rightarrow; "Create access key" download CSV or copy/paste key to a safe place
  ```
  Example:
  AKIA3RN7------RVROB5
  gzpYwF9M-------------GhsODuvpXaa1JtRuhVY
  ```
- add key to CLI  
  ```
  aws configure --profile awscliuser
  &rightarrow; type in keys
  &rightarrow; type in default region "eu-central-1"
  &rightarrow; type in default output "json"
  ```
- credential files (config, credentials) are located in `$HOME\.aws`
- testing access to an existing resource in your AWS account, e.g. by `aws s3 ls --profile awscliuser` replies
  ```
  2021-08-05 11:58:32 inventory-12345
  2021-09-01 15:04:55 example.com
  2021-09-01 18:47:09 www.example.com
  ```
- by exporting the parameter `export AWS_PROFILE=awscliuser` to .profile/.bashrc you can omit the "--profile awscliuser"

## Hosting a static website on AWS S3 buckets

Sequence:

1. setting up *S3 bucket* with *bucketname* (e.g. *mavost.ml*), access permissions and inserting content
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
    - added a second bucket called *www.bucketname* and repeated above first two steps but instead 
    of uploading the same hosted content again I added a redirect  
      ![alt text][img01]

2. setting up *Route 53* by adding a hosted zone
    - add new hosted zone (not included in AWS free tier!) for both bucket destinations  
      domain *bucketname* &rightarrow; s3-website.eu-central-1.amazonaws.com S3 bucket  
      subdomain *www.bucketname* &rightarrow; s3-website.eu-central-1.amazonaws.com S3 bucket
    - screenshot  
      ![alt text][img02]
    - copy AWS Route 53 DNS server names for use in step 3

3. add server names in Freenom Domain administration
    - screenshot  
      ![alt text][img03]

4. setting up AWS CodePipeline (one pipeline covered by FreeTier)
    - screenshot  
      ![alt text][img04]

[img01]:  ./Pictures/2021-09-01_AWS_S3_subdomain_redirect.png "Adjusting redirect of one subdomain to another"
[img02]:  ./Pictures/2021-09-01_AWS_Route53_HostedZoneS3.png "Setting up Route53 hosted zone for website S3 bucket"
[img03]:  ./Pictures/2021-09-01_freenom_DNS.png "Entering AWS Route 53 DNS servers to Freenom Domain Settings"
[img04]:  ./Pictures/2021-09-01_AWS_CodePipeline_GitHub-S3.png "Setup for AWS CodePipeline"
