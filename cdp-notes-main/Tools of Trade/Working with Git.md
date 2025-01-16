# Working with Git Locally 

## What is Git?
- **Definition**: Version control system used for storing application code, configurations, and documentation.
- **Usage**: Essential for DevOps/DevSecOps for managing code, infrastructure, and CI/CD pipelines.

## Initial Git Setup
- **Set Username and Email**:
    ```bash
    git config --global user.email "student@pdevsecops.com"
    git config --global user.name "student"
    ```

## Cloning a Repository
- **Wait for GitLab Machine**: 2 minutes for setup.
- **Clone Command**:
    ```bash
    git clone git@gitlab-ce-oiahxczk:root/django-nv.git
    ```

## Authentication Prompt
- **Response**: Type `yes` and press Enter if prompted to confirm authenticity.

#### Navigating the Repository
- **Change Directory**:
    ```bash
    cd django-nv
    ```
- **List Contents**:
    ```bash
    ls -l
    ```

## Adding and Modifying Files
- **Create File**:
    ```bash
    cat > myfile <<EOL
    This is my file
    EOL
    ```
- **Modify README**:
    ```bash
    echo "Practical DevSecOps" >> README.md
    ```

#### Checking Repository Status
- **Status Command**:
    ```bash
    git status
    ```
- **Output**: Shows modified and untracked files.

## Staging Files
- **Add Files**:
    ```bash
    git add myfile README.md
    ```
- **Check Status Again**:
    ```bash
    git status
    ```

## Committing Changes
- **Commit Command**:
    ```bash
    git commit -m "Add myfile and update README.md"
    ```

## Pushing to Remote Repository
- **Push Command**:
    ```bash
    git push
    ```

## Checking the pushed files 

In this lab, they have provided with a Gitlab credentials and checking the repo recent commit shows that `nithissh` pushed two files which is `myfile` and updated `README.md`

![](Files/gitlab.png)

### Key Points
- **Setup Git**: Configure username and email.
- **Clone Repositories**: Download code to local machine.
- **Manage Files**: Add, modify, stage, commit, and push changes.
- **Version Control**: Keep track of changes and collaborate with team members.

# Git Branches 

## Creating and Managing Branches

### Creating a New Branch
- **Create Branch**:
    ```bash
    git branch new-branch
    ```
- **List Branches**:
    ```bash
    git branch --list
    ```
    - Output:
        ```
        * main
          new-branch
        ```
- **Show Current Branch**:
    ```bash
    git branch --show-current
    ```

### Switching to a Branch
- **Switch to Branch**:
    ```bash
    git checkout new-branch
    ```
    - Output:
        ```
        Switched to branch 'new-branch'
        ```
- **Check Current Branch**:
    ```bash
    git branch --list
    ```
    - Output:
        ```
          main
        * new-branch
        ```

### Creating and Switching to a Branch Simultaneously
- **Create and Switch to Branch**:
    ```bash
    git checkout -b checkout-branch
    ```
    - Output:
        ```
        Switched to a new branch 'checkout-branch'
        ```
- **Using `git switch`**:
    ```bash
    git switch -c switch-branch
    ```
    - Output:
        ```
        Switched to branch 'switch-branch'
        ```

### Check Branch List
- **List Branches**:
    ```bash
    git branch --list
    ```
    - Output:
        ```
          checkout-branch
          main
          new-branch
        * switch-branch
        ```

### Differences Among `git branch`, `git checkout`, and `git switch`
- **`git branch`**: Creates a new branch.
- **`git checkout`**: Switches to an existing branch or creates a new branch and switches to it when used with `-b`.
- **`git switch`**: Newer command to switch branches; can also create and switch to a new branch with `-c`.

## Updating Commits to the Repository

### Switching to a Branch
- **Switch to Branch**:
    ```bash
    git checkout new-branch
    ```
    - or
    ```bash
    git switch new-branch
    ```
    - Output:
        ```
        Switched to branch 'new-branch'
        ```

### Updating a File
- **Modify README**:
    ```bash
    echo "I am ready to be awesome with Practical DevSecOps" >> README.md
    ```

### Staging, Committing, and Pushing Changes
- **Stage Changes**:
    ```bash
    git add README.md
    ```
- **Commit Changes**:
    ```bash
    git commit -m "update the new text to README.md"
    ```
    - Output:
        ```
        [new-branch cbf207e] update the new text to README.md
         1 file changed, 2 insertions(+)
        ```
- **Push Changes**:
    ```bash
    git push origin new-branch
    ```
    - Output:
        ```
        Enumerating objects: 5, done.
        Counting objects: 100% (5/5), done.
        Delta compression using up to 40 threads
        Compressing objects: 100% (3/3), done.
        Writing objects: 100% (3/3), 353 bytes | 353.00 KiB/s, done.
        Total 3 (delta 2), reused 0 (delta 0)
        remote: 
        remote: To create a merge request for new-branch, visit:
        remote:   http://gitlab-ce-oiahxczk.lab.practical-devsecops.training/root/django-nv/-/merge_requests/new?merge_request%5Bsource_branch%5D=new-branch
        remote: 
        To gitlab-ce-4v3alqoo:root/django-nv.git
         * [new branch]      new-branch -> new-branch
        ```

### Explanation of Commands
| Command                              | Description                                                                                              |
|--------------------------------------|----------------------------------------------------------------------------------------------------------|
| `git add README.md`                  | Stages the changes made to `README.md` for commit.                                                       |
| `git commit -m "update the new text to README.md"` | Commits the staged changes with a descriptive message.                                |
| `git push origin new-branch`         | Pushes the committed changes to the remote repository in the `new-branch` branch.                        |

#### Post-Push Actions
- **Merge Request**: After pushing changes, create a merge request using the provided link:
    ```
    http://gitlab-ce-oiahxczk.lab.practical-devsecops.training/root/django-nv/-/merge_requests/new?merge_request%5Bsource_branch%5D=new-branch
    ```
- Then Click on merge where the commit will be merged into the main branch 

# Pull the branch 

## Check Current Branch
- **List Branches**:
    ```bash
    git branch --list
    ```
    - Output:
        ```
          checkout-branch
        * main
          new-branch
          switch-branch
        ```

## Switch to Main Branch
- **Switch to Main Branch**:
    ```bash
    git checkout main
    ```
    - or
    ```bash
    git switch main
    ```
    - Output:
        ```
        Switched to branch 'main'
        Your branch is up to date with 'origin/main'.
        ```

## Pull Changes from Remote Main Branch
- **Pull Changes**:
    ```bash
    git pull origin main
    ```
    - Output:
        ```bash
        remote: Enumerating objects: 1, done.
        remote: Counting objects: 100% (1/1), done.
        remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
        Unpacking objects: 100% (1/1), 270 bytes | 270.00 KiB/s, done.
        From gitlab-ce-4v3alqoo:root/django-nv
         * branch            main       -> FETCH_HEAD
           007a205..55ec95d  main       -> origin/main
        Updating 007a205..55ec95d
        Fast-forward
         README.md | 1 +
         1 file changed, 1 insertion(+)
        ```

## Removing Local Branches

### Delete Unused Branches
- **Delete Branch**:
    ```bash
    git branch -D new-branch
    ```
    - Output:
        ```
        Deleted branch new-branch (was 55ec95d).
        ```

### Check Branch List
- **List Branches**:
    ```bash
    git branch --list
    ```
    - Output:
        ```
        * main
          checkout-branch
          switch-branch
        ```

### Note
- **Branches Explanation**:
    - **Remote Branch**: Located in the GitLab repository.
    - **Local Branch**: Located on our local system, used to communicate with the remote branch.

### Summary

- **Pulling Updates**: Pull changes from remote main branch to local main branch using `git pull origin main`.
- **Removing Branches**: Delete unused local branches using `git branch -D branch-name`. Always ensure you are in the main branch before removing other branches.