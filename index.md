# Welcome to my the Docuserver pages

## Requirements
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
