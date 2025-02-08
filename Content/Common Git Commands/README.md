### **Git Commands: A Detailed Guide with Examples**

Git is an essential tool for version control, especially for developers working on projects collaboratively or individually. Below is a breakdown of the **most important Git commands**, categorized for easier understanding.

---

## **0. Install Git**

First of all download and install the Git command tools via [this link](https://git-scm.com/downloads)

- **Description**: Git is a distributed version control system that allows multiple developers to work on a project simultaneously without interfering with each other's work. Installing Git is the first step to start using it.

## **1. Git Configuration Commands**
Before using Git, you should configure it with your name and email.

### **1.1 Set up your username and email**

Open up a terminal (Git Bash is recommended) :

```sh
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```
- These settings are used for commits to associate changes with your identity.

- **Description**: Configuring your username and email is crucial as it helps in identifying the author of the commits. This information is stored in the commit history.

### **1.2 Check your Git configuration**
```sh
git config --list
```
- Displays the current Git configuration.

- **Description**: This command lists all the Git configurations that are currently set, including user information, editor preferences, and more.

---

## **2. Git Repository Initialization & Cloning**
### **2.1 Initialize a new Git repository**
```sh
git init
```

- **Description**: This command initializes a new Git repository in the current directory. It creates a `.git` directory that tracks all changes to the files.

### **2.2 Clone an existing repository**
```sh
git clone <repository_url>
```
Example:
```sh
git clone https://github.com/user/repo.git
```
- Downloads a repository and all its history.
- Cloning into a specific directory:
  ```sh
  git clone https://github.com/user/repo.git my-directory
  ```

- **Description**: Cloning a repository means creating a copy of an existing repository. This is useful for getting a local copy of a remote repository to work on.

---

## **3. Git Staging and Committing**
### **3.1 Check the current status of your repository**
```sh
git status
```
- Shows the status of tracked and untracked files.

- **Description**: This command displays the state of the working directory and the staging area. It shows which changes have been staged, which haven't, and which files aren't being tracked by Git.

### **3.2 Add files to the staging area**
```sh
git add <filename>
```
Example:
```sh
git add index.html
```
- Stages `index.html` for commit.

To add all files:
```sh
git add .
```
- Stages all modified and new files.

- **Description**: Adding files to the staging area means preparing them for a commit. This step allows you to selectively choose which changes to include in the next commit.

### **3.3 Commit changes**
```sh
git commit -m "Commit message"
```
Example:
```sh
git commit -m "Fixed login issue"
```
- Saves changes to the local repository.

- **Description**: Committing changes means saving the staged changes to the local repository. Each commit should have a meaningful message describing the changes.

### **3.4 Commit with all staged changes**
```sh
git commit -a -m "Updated styles"
```
- Stages and commits all tracked files in one step.

- **Description**: This command stages all modified tracked files and commits them in one step, bypassing the need to use `git add`.

---

## **4. Git Branching and Merging**
### **4.1 Create a new branch**
```sh
git branch <branch_name>
```
Example:
```sh
git branch feature-branch
```
- Creates a new branch named `feature-branch`.

- **Description**: Branching allows you to create a separate line of development. This is useful for working on new features or bug fixes without affecting the main codebase.

### **4.2 List all branches**
```sh
git branch
```
- Shows all branches, with `*` marking the current branch.

- **Description**: This command lists all the branches in the repository and shows which branch you are currently on.

### **4.3 Switch to a different branch**
```sh
git checkout <branch_name>
```
Example:
```sh
git checkout feature-branch
```
- Switches to `feature-branch`.

Or, using the modern approach:
```sh
git switch feature-branch
```

- **Description**: Switching branches means changing the current working branch. This updates the working directory to match the specified branch.

### **4.4 Create and switch to a new branch**
```sh
git checkout -b <branch_name>
```
Example:
```sh
git checkout -b new-feature
```
- Creates and switches to `new-feature` in one step.

Or using:
```sh
git switch -c new-feature
```

- **Description**: This command creates a new branch and switches to it immediately, combining two steps into one.

### **4.5 Merge a branch into the current branch**
```sh
git merge <branch_name>
```
Example:
```sh
git merge feature-branch
```
- Merges `feature-branch` into the current branch.

- **Description**: Merging combines the changes from one branch into another. This is typically done to integrate new features or bug fixes into the main branch.

### **4.6 Delete a branch**
```sh
git branch -d <branch_name>
```
Example:
```sh
git branch -d feature-branch
```
- Deletes `feature-branch` if it's already merged.

To force delete an unmerged branch:
```sh
git branch -D feature-branch
```

- **Description**: Deleting branches that are no longer needed helps keep the repository clean and manageable. The `-d` flag deletes a branch that has been merged, while `-D` force deletes an unmerged branch.

---

## **5. Git Remote Repositories**
### **5.1 View remote repositories**
```sh
git remote -v
```
- Lists the remotes (e.g., GitHub, GitLab).

- **Description**: This command shows the remote repositories that are linked to your local repository. It displays the URLs for fetching and pushing data.

### **5.2 Add a remote repository**
```sh
git remote add origin <repository_url>
```
Example:
```sh
git remote add origin https://github.com/user/project.git
```
- Links the local repository to a remote one.

- **Description**: Adding a remote repository means linking your local repository to a remote server. This allows you to push and pull changes to and from the remote repository.

### **5.3 Push changes to a remote repository**
```sh
git push origin <branch_name>
```
Example:
```sh
git push origin main
```
- Pushes local changes to the `main` branch on the remote.

To push all branches:
```sh
git push --all origin
```

- **Description**: Pushing changes means sending your local commits to the remote repository. This updates the remote repository with your latest changes.

### **5.4 Pull changes from a remote repository**
```sh
git pull origin <branch_name>
```
Example:
```sh
git pull origin main
```
- Fetches and merges updates from the remote `main` branch.

- **Description**: Pulling changes means fetching the latest changes from the remote repository and merging them into your local branch. This keeps your local repository up-to-date with the remote.

---

## **6. Git Undoing Changes**
### **6.1 Undo changes before staging**
```sh
git checkout -- <filename>
```
Example:
```sh
git checkout -- index.html
```
- Discards local changes in `index.html`.

- **Description**: This command reverts changes in the working directory to match the last commit. It is useful for discarding unwanted changes before they are staged.

### **6.2 Remove a file from the staging area**
```sh
git reset <filename>
```
Example:
```sh
git reset index.html
```
- Unstages `index.html` without deleting the changes.

- **Description**: Resetting a file removes it from the staging area but keeps the changes in the working directory. This is useful for re-evaluating which changes to include in the next commit.

### **6.3 Reset a commit (soft, mixed, hard)**
#### Soft reset (keeps changes staged)
```sh
git reset --soft HEAD~1
```
- Moves the commit back but keeps the changes in the staging area.

#### Mixed reset (keeps changes but unstages them)
```sh
git reset --mixed HEAD~1
```
- Moves the commit back and unstages the files.

#### Hard reset (removes all changes)
```sh
git reset --hard HEAD~1
```
- Deletes the last commit and changes.

- **Description**: Resetting a commit changes the state of the repository to a previous commit. The `--soft` option keeps changes staged, `--mixed` keeps changes but unstages them, and `--hard` removes all changes.

### **6.4 Revert a commit**
```sh
git revert <commit_hash>
```
Example:
```sh
git revert a1b2c3d
```
- Creates a new commit that undoes the changes from commit `a1b2c3d`.

- **Description**: Reverting a commit creates a new commit that undoes the changes from a previous commit. This is useful for undoing changes without altering the commit history.

---

## **7. Git Logs and History**
### **7.1 View commit history**
```sh
git log
```
- Shows commit history.

To see one-line commits:
```sh
git log --oneline
```

To see commits by a specific author:
```sh
git log --author="Your Name"
```

- **Description**: Viewing the commit history shows all the commits made in the repository. The `--oneline` option shows a simplified view, and the `--author` option filters commits by a specific author.

### **7.2 Show the difference between commits**
```sh
git diff
```
- Shows unstaged changes.

To compare staged changes:
```sh
git diff --staged
```

To compare two commits:
```sh
git diff <commit1> <commit2>
```

- **Description**: Showing the difference between commits helps in understanding what changes were made. The `git diff` command compares the working directory with the staging area, while `git diff --staged` compares the staging area with the last commit.

---

## **8. Git Stashing**
### **8.1 Save changes without committing**
```sh
git stash
```
- Temporarily stores uncommitted changes.

- **Description**: Stashing saves your local modifications away and reverts the working directory to match the HEAD commit. This is useful for switching branches without committing changes.

### **8.2 List stashed changes**
```sh
git stash list
```

- **Description**: Listing stashed changes shows all the stashes that have been saved. Each stash is identified by an index.

### **8.3 Apply the last stashed changes**
```sh
git stash apply
```

- **Description**: Applying the last stashed changes restores the most recent stash to the working directory without removing it from the stash list.

### **8.4 Apply and remove a stash**
```sh
git stash pop
```

- **Description**: Popping a stash restores the most recent stash to the working directory and removes it from the stash list.

### **8.5 Drop a specific stash**
```sh
git stash drop stash@{0}
```

- **Description**: Dropping a specific stash removes it from the stash list without applying it. This is useful for cleaning up stashes that are no longer needed.

---

## **9. Git Tags**
### **9.1 Create a tag**
```sh
git tag <tag_name>
```
Example:
```sh
git tag v1.0
```
- Tags a specific commit with `v1.0`.

- **Description**: Creating a tag marks a specific point in the commit history as important. Tags are often used for marking release points (e.g., v1.0, v2.0).

### **9.2 List all tags**
```sh
git tag
```

- **Description**: Listing all tags shows all the tags that have been created in the repository.

### **9.3 Push tags to a remote repository**
```sh
git push origin --tags
```

- **Description**: Pushing tags sends all local tags to the remote repository. This is useful for sharing release points with others.

### **9.4 Delete a tag**
```sh
git tag -d <tag_name>
```

- **Description**: Deleting a tag removes it from the local repository. To delete a tag from the remote repository, you need to push the deletion.

---

## Use Github Desktop

Alternatively, you can download and install the Github Desktop and use the GUI instead. You can download and install via [this link](https://desktop.github.com/download/).

- **Description**: GitHub Desktop is a graphical user interface for Git. It simplifies the Git workflow and is useful for users who prefer a visual interface over the command line.

## **Conclusion**
These are the essential Git commands you’ll use frequently. If you're working with Unity, **always be careful with `git reset --hard` and merging branches**, as it can overwrite files permanently.

- **Description**: Understanding and using these Git commands will help you manage your codebase effectively. Always be cautious with commands that can overwrite changes, especially when working in a collaborative environment.