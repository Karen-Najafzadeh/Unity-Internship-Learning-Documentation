### **Git Commands: A Detailed Guide with Examples**

Git is an essential tool for version control, especially for developers working on projects collaboratively or individually. Below is a breakdown of the **most important Git commands**, categorized for easier understanding.

---

## **1. Git Configuration Commands**
Before using Git, you should configure it with your name and email.

### **1.1 Set up your username and email**
```sh
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```
- These settings are used for commits to associate changes with your identity.

### **1.2 Check your Git configuration**
```sh
git config --list
```
- Displays the current Git configuration.

---

## **2. Git Repository Initialization & Cloning**
### **2.1 Initialize a new Git repository**
```sh
git init
```
- Creates a new Git repository in the current directory.

### **2.2 Clone an existing repository**
```sh
git clone <repository_url>
```
Example:
```sh
git clone https://github.com/user/repo.git
```
- Downloads a repository and all its history.

---

## **3. Git Staging and Committing**
### **3.1 Check the current status of your repository**
```sh
git status
```
- Shows the status of tracked and untracked files.

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

### **3.3 Commit changes**
```sh
git commit -m "Commit message"
```
Example:
```sh
git commit -m "Fixed login issue"
```
- Saves changes to the local repository.

### **3.4 Commit with all staged changes**
```sh
git commit -a -m "Updated styles"
```
- Stages and commits all tracked files in one step.

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

### **4.2 List all branches**
```sh
git branch
```
- Shows all branches, with `*` marking the current branch.

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

### **4.5 Merge a branch into the current branch**
```sh
git merge <branch_name>
```
Example:
```sh
git merge feature-branch
```
- Merges `feature-branch` into the current branch.

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

---

## **5. Git Remote Repositories**
### **5.1 View remote repositories**
```sh
git remote -v
```
- Lists the remotes (e.g., GitHub, GitLab).

### **5.2 Add a remote repository**
```sh
git remote add origin <repository_url>
```
Example:
```sh
git remote add origin https://github.com/user/project.git
```
- Links the local repository to a remote one.

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

### **5.4 Pull changes from a remote repository**
```sh
git pull origin <branch_name>
```
Example:
```sh
git pull origin main
```
- Fetches and merges updates from the remote `main` branch.

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

### **6.2 Remove a file from the staging area**
```sh
git reset <filename>
```
Example:
```sh
git reset index.html
```
- Unstages `index.html` without deleting the changes.

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

### **6.4 Revert a commit**
```sh
git revert <commit_hash>
```
Example:
```sh
git revert a1b2c3d
```
- Creates a new commit that undoes the changes from commit `a1b2c3d`.

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

---

## **8. Git Stashing**
### **8.1 Save changes without committing**
```sh
git stash
```
- Temporarily stores uncommitted changes.

### **8.2 List stashed changes**
```sh
git stash list
```

### **8.3 Apply the last stashed changes**
```sh
git stash apply
```

### **8.4 Apply and remove a stash**
```sh
git stash pop
```

### **8.5 Drop a specific stash**
```sh
git stash drop stash@{0}
```

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

### **9.2 List all tags**
```sh
git tag
```

### **9.3 Push tags to a remote repository**
```sh
git push origin --tags
```

### **9.4 Delete a tag**
```sh
git tag -d <tag_name>
```

---

## **Conclusion**
These are the essential Git commands you’ll use frequently. If you're working with Unity, **always be careful with `git reset --hard` and merging branches**, as it can overwrite files permanently.