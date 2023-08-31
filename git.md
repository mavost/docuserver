# Working with Git/Github/Gitlab

## Git command line

- updating local repo  
`git fetch`
- applying changes to local clone  
`git pull`

### Cleaning ignored files from a branch

1. Remove the files from the index (not the actual files in the working copy)

    ```bash
    git rm -r --cached .
    ```

2. Add these removals to the staging area...

    ```bash
    git add .
    ```

3. And commit/push them to the remote repo

    ```bash
    git commit -m "Clean up ignored files"
    git push
    ```

4. In order to prune the excess files from the branch run `git clean -xdn` for a dry run followed by `git clean -xdf` if satisfied with the effect. Note: to stash configuration files like `.env`s before cleaning.
