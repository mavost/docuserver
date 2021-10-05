# Working with Amazon Web Services (AWS)

## Adding AWS-CLI to Ubuntu (v. 20.04)
### Installation
- `curl -sS "https://awscli.amazonaws.com/awscli-exe-linux-x86_64-2.0.30.zip" -o "awscliv2.zip"`  
  update above path by looking up [latest-version](https://github.com/aws/aws-cli/blob/v2/CHANGELOG.rst)
- unzip content and install `unzip awscliv2.zip && sudo ./aws/install`
- verfiy installation and cleanup `aws --version && rm -rf aws && rm awscliv2.zip`

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
- add a profile to the CLI by running `aws configure --profile awscliuser`  
  &rightarrow; type in keys  
  &rightarrow; type in default region "eu-central-1"  
  &rightarrow; type in default output "json"


- credential files (config, credentials) are located in `$HOME\.aws`
- testing access to an existing resource in your AWS account, e.g. by `aws s3 ls --profile awscliuser` replies
  ```
  2021-08-05 11:58:32 inventory-12345
  2021-09-01 15:04:55 example.com
  2021-09-01 18:47:09 www.example.com
  ```
- by adding the parameter `export AWS_PROFILE=awscliuser` in either .profile or .bashrc you can omit the "--profile awscliuser" 
identification when submitting commands interactively

### Adding bash shell syntax completion
Edit *.bashrc* file by adding the following line and executing the file:  
`[ -x /usr/local/bin/aws_completer ] && complete -C '/usr/local/bin/aws_completer' aws`  
which triggers given that the **aws_completer** command is present

### (optional) Installing AWS serverless application model (SAM) CLI
[source](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install-linux.html)

- `curl -sSLO "https://github.com/aws/aws-sam-cli/releases/download/v1.27.0/aws-sam-cli-linux-x86_64.zip"`  
  Note: had problems with the master version post(1.27) not deploying the bootstrap system
- unzip content and install `unzip aws-sam-cli-linux-x86_64.zip -d sam-cli && sudo sam-cli/install`
- verfiy installation and cleanup `/usr/local/bin/sam --version && rm -rf sam-cli && rm aws-sam-cli-linux-x86_64.zip`  
  Note: `/usr/local/bin` should actually be included in `PATH` variable already

#### Working with AWS SAM CLI
1. `sam init` initializes a serverless application with an AWS SAM template in the [language of choice](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-init.html)
2. to build your serverless application, use the `sam build` [command](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-using-build.html)
3. deploy your application using the `sam deploy --guided --debug` [command](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-deploying.html)  
  <img src="./images/2021-09-04_AWS-SAM-deploy.png" alt="AWS SAM output" width="720"/>
4. Testing serverless Lambda function  
  `curl https://----API-----.execute-api.eu-central-1.amazonaws.com/Prod/hello/ -w "\n"`  
  with `----API-----` refering to the endpoint-URL generated in the previous step replies in JSON format:  
  `{"message":"hello world"}`


---
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
    <img src="./images/2021-09-01_AWS_S3_subdomain_redirect.png" alt="Adjusting redirect of one subdomain to another" width="728"/>



2. setting up *Route 53* by adding a hosted zone
    - add new hosted zone (not included in AWS free tier!) for both bucket destinations  
      domain *bucketname* &rightarrow; s3-website.eu-central-1.amazonaws.com S3 bucket  
      subdomain *www.bucketname* &rightarrow; s3-website.eu-central-1.amazonaws.com S3 bucket
    - screenshot  
      <img src="./images/2021-09-01_AWS_Route53_HostedZoneS3.png" alt="Setting up Route53 hosted zone for website S3 bucket" width="728"/>
    - copy AWS Route 53 DNS server names for use in step 3

3. add server names in Freenom Domain administration
    - screenshot  
      <img src="./images/2021-09-01_freenom_DNS.png" alt="Entering AWS Route 53 DNS servers to Freenom Domain Settings" height="410"/>

4. setting up AWS CodePipeline (one pipeline covered by FreeTier)
    - screenshot  
      <img src="./images/2021-09-01_AWS_CodePipeline_GitHub-S3.png" alt="Setup for AWS CodePipeline" width="728"/>

---

### Advanced webhosting of the S3 content using Cloudfront, Route 53 and a Freenom domain

- Fine-tuning: In the Cloudfront distribution, for the origin-URL, instead of using the S3 bucket endpoint, use the S3 static website url which is slightly differnt. Distributions then are all working with subdirectory "/" endpoints going directly to the *index.html* files within the subdirectory. 

- Note: the Cloudfront cache does only update once a day so your hosted site will refresh much slower than what you expect, [source](https://aws.amazon.com/premiumsupport/knowledge-center/cloudfront-serving-outdated-content-s3/)  
&rightarrow; use the s3 website endpoint to verify the result  
&rightarrow; add versioning or flush the cache (*expect added costs*)