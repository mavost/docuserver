# Welcome to the MavoSt Docuserver Wiki pages
This is a little webspace where I maintain my collected knowledge on Linux and cloud technologies.  
As information is coming in from different directions I try to add sources as much as possible.
Feel free to browse...  
Live instance currently accessible as:  
  - hosted on [AWS S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html),  
  - using a free [Freenom](https://www.freenom.com/en/index.html?lang=en) domain,  
  - and changes to the code base in [GitHub](https://github.com/mavost/docuserver) are automatically deployed using [AWS Codepipeline](https://aws.amazon.com/codepipeline/?nc=bc&pg=pr)

see [cloud-webhosting](https://rajesh-r6r.medium.com/hosting-a-free-website-on-aws-s3-with-freenom-domains-for-dummies-a363aac39b1e) 
and [code pipeline](https://james-turner.medium.com/connecting-github-to-aws-codepipeline-ce19a4a2f213) examples to replicate this setup.

---
To my AWS playground - under heavy construction - 
- follow this [link](uploader/index.html) to the S3 uploader.
- follow this [link](https://api.mavost.ml/greetings) to an API-endpoint.

---
## Requirements for MDwiki
1. in a folder I started with a bunch of ".md" files and subfolders containing pictures
2. in those files I edited the Markdown format to match the syntax needed by MDwiki
  - no *xml*, *console*, *shell*, *property* tags in code blocks
  - eliminated manual arrows "-->"
  - indented code blocks may not contain manual horizontal rulers "--------"
3. copied the **mdwiki.html** file to folder and renamed it to **index.html**
4. some basic configuration for the wiki
  - created a **navigation.md** containing the links to all Markdown files
  - also an **about.md** and **index.md** for further info
  - added symbolic link **home.md** &rightarrow; **index.md**
  - **Readme.md** is a copy of **index.md**
  - setting the theme 
    - either manually by adding a specific theme to **navigation.md**
    ```
    [gimmick:theme](slate)
    ```
  or
    - by adding a theme chooser option
    ```
    [gimmick:themechooser](Choose theme)
    ```
  - added a **config.json** file containing some basic settings
5. results and edits are pushed to [Github](https://github.com/mavost/docuserver)
6. given a webserver is installed and running the folder is copied to */var/www/landing_page*
or checked out via  `git clone https://github.com/mavost/docuserver`
