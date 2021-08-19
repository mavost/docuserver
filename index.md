# Welcome to my the Docuserver pages

## Requirements
1. in a folder I started with a bunch of ".md" files and subfolders containing pictures
2. in those files I edited the Markdown format to match the syntax needed by MDwiki
  - no *xml*, *console*, *shell*, *property* tags in code blocks
  - eliminated manual arrows "-->"
  - indented code blocks may not contain manual horizontal rulers "--------"
3. copied the **mdwiki.html** file to folder and renamed it to **index.html**
4. created a **navigation.md** containing the links to all Markdown files
  - also an **about.md** and **index.md** for further info
  - added symbolic links:  
  **home.md** &rightarrow; **index.md**
  **Readme.md** &rightarrow; **index.md**
5. results and edit are pushed to [Github](https://github.com/mavost/docuserver)
6. given a webserver is installed and running the folder is copied to */var/www/landing_page*
or checked out via  `git clone https://github.com/mavost/docuserver`
