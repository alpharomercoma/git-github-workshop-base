# Git Tutorial

A hands-on git tutorial. Follow each section in order, typing out every command yourself.

---

## 1. Setup

### Initialize a repository

```bash
mkdir git-practice
cd git-practice
git init
```

### Configure your identity

```bash
git config user.name "<Your Name>"
git config user.email "<your.email@example.com>"
```

> These are stored per-repo. Add `--global` to set them for all repos on your machine.

---

## 2. Add and Commit

Create a file called `main.js`:

```js
const name = "Juan"
```

Stage and commit it:

```bash
git add main.js
git commit -m "Add name"
```

Now add a second line to `main.js`:

```js
const name = "Juan"
const age = 21
```

```bash
git add main.js
git commit -m "Add age"
```

Add a third line:

```js
const name = "Juan"
const age = 21
const hobbies = ["Coding", "Reading", "Gaming"]
```

```bash
git add main.js
git commit -m "Add hobbies"
```

Add a fourth line:

```js
const name = "Juan"
const age = 21
const hobbies = ["Coding", "Reading", "Gaming"]
const university = "Jose Rizal University"
```

```bash
git add main.js
git commit -m "Add university"
```

---

## 3. Log

View your commit history:

```bash
git log
git log --oneline
```

You should see four commits listed from newest to oldest.

---

## 4. Unstaging Changes

### Unstage a file

Add a new file and stage it, then unstage it before committing:

```bash
echo "temp" > notes.txt
git add notes.txt
git status
git reset HEAD -- notes.txt
git status
```

`notes.txt` is now untracked again.

### Unstage an entire directory

```bash
mkdir docs
echo "draft" > docs/plan.txt
echo "draft" > docs/outline.txt
git add docs/
git status
git reset HEAD -- docs
git status
```

All files inside `docs/` are now unstaged.

> Clean up: `rm -rf notes.txt docs`

---

## 5. Undo the Last Commit (soft reset)

Undo the most recent commit but keep the changes staged:

```bash
git log --oneline
git reset --soft HEAD~1
git status
git log --oneline
```

The "Add university" commit is gone, but the change is still staged. Re-commit it:

```bash
git commit -m "Add university"
```

---

## 6. Gitignore

Create a `.gitignore` file to tell git which files to ignore:

```bash
echo "node_modules/" > .gitignore
echo ".env" >> .gitignore
echo "dist/" >> .gitignore
```

```bash
git add .gitignore
git commit -m "Add gitignore"
```

Now test it — create an ignored file and check the status:

```bash
echo "SECRET=abc123" > .env
git status
```

`.env` won't show up as untracked because `.gitignore` is filtering it out.

> Common patterns: `node_modules/`, `.env`, `dist/`, `*.log`, `.DS_Store`

---

## 7. Branches

### Create and switch branches

```bash
git branch feature-greeting
git checkout feature-greeting
```

Or in one step:

```bash
git checkout -b feature-greeting
```

Add a line to `main.js`:

```js
const greeting = `Hello, my name is ${name}`
```

```bash
git add main.js
git commit -m "Add greeting"
```

### List and delete branches

```bash
git branch            # list all branches
git checkout main     # switch back to main
git branch -d feature-greeting   # delete (safe — only if merged)
git branch -D feature-greeting   # delete (force — even if not merged)
```

---

## 8. Merge

Create a branch, make a commit, then merge it into `main`:

```bash
git checkout -b feature-greeting
```

Add to `main.js`:

```js
const greeting = `Hello, my name is ${name}`
```

```bash
git add main.js
git commit -m "Add greeting"
git checkout main
git merge feature-greeting
```

The commit from `feature-greeting` is now part of `main`. Clean up:

```bash
git branch -d feature-greeting
```

---

## 9. Merge Conflicts

Conflicts happen when two branches change the same line. Let's create one on purpose.

From `main`, create two branches:

```bash
git checkout -b feature-age-update
```

Change the age line in `main.js`:

```js
const age = 22
```

```bash
git add main.js
git commit -m "Update age to 22"
git checkout main
```

Now create a second branch from `main` and change the same line differently:

```bash
git checkout -b feature-age-fix
```

Change the age line in `main.js`:

```js
const age = 25
```

```bash
git add main.js
git commit -m "Update age to 25"
git checkout main
```

Merge the first branch (this will succeed):

```bash
git merge feature-age-update
```

Now merge the second branch (this will **conflict**):

```bash
git merge feature-age-fix
```

Git will tell you there's a conflict. Open `main.js` and you'll see:

```
<<<<<<< HEAD
const age = 22
=======
const age = 25
>>>>>>> feature-age-fix
```

### Resolving the conflict

1. Edit `main.js` — pick the value you want and remove the conflict markers:

```js
const age = 25
```

2. Stage and commit the resolution:

```bash
git add main.js
git commit -m "Resolve merge conflict: keep age as 25"
```

Clean up:

```bash
git branch -d feature-age-update
git branch -d feature-age-fix
```

---

## 10. Push and Remote

### Connect to a remote repository

```bash
git remote add origin <your-repo-url>
```

### Push main for the first time

```bash
git push -u origin main
```

`-u` sets the upstream so future pushes only need `git push`.

### Regular push

After making commits on `main`:

```bash
git push
```

---

## 11. Pull Requests (GitHub workflow)

In practice, you don't merge locally — you push a branch and open a **Pull Request** on GitHub. Here's the full workflow.

### Create a feature branch and push it

```bash
git checkout -b feature-bio
```

Add to `main.js`:

```js
const bio = `${name}, ${age}, studies at ${university}`
```

```bash
git add main.js
git commit -m "Add bio"
git push -u origin feature-bio
```

### Open a Pull Request

On GitHub, go to your repo and you'll see a prompt to open a PR for `feature-bio`.

> At this point, your team reviews the code on GitHub before merging.

### Merge the Pull Request

On GitHub, click **"Merge pull request"** and then **"Confirm merge"**

```

### Update your local main

After merging on GitHub, pull the changes locally:

```bash
git checkout main
git pull
```

Clean up the branch:

```bash
git branch -d feature-bio
```

---

## 12. Rebase (updating your branch)

Sometimes `main` moves forward while you're working on a branch. Rebase keeps your branch up to date with a clean history.

### Setup the scenario

```bash
git checkout -b feature-header
```

Add a comment to the top of `main.js`:

```js
// Student Info
```

```bash
git add main.js
git commit -m "Add header comment"
```

Meanwhile, suppose someone merged a different PR into `main` on GitHub. Pull those changes:

```bash
git checkout main
git pull
```

### Rebase your branch onto the updated main

```bash
git checkout feature-header
git rebase main
```

This replays your `feature-header` commits on top of the latest `main`. If there are conflicts, resolve them, then:

```bash
git add main.js
git rebase --continue
```

### Force push after rebase

Rebase rewrites commit history, so a regular `git push` will be rejected. You need to force push:

```bash
git push --force
```

This overwrites the remote branch entirely. **Use with caution — this rewrites remote history.**

A safer alternative that only force-pushes if no one else has pushed since your last fetch:

```bash
git push --force-with-lease
```

> **Best practice:** Always prefer `--force-with-lease` over `--force`. It protects you from accidentally overwriting a teammate's work.

Now open a PR, get it reviewed, and merge as shown in the previous section.

---

## Quick Reference

| Command | What it does |
|---|---|
| `git init` | Initialize a new repo |
| `git config user.name "<name>"` | Set your name |
| `git config user.email "<email>"` | Set your email |
| `git add <file>` | Stage a file |
| `git commit -m "msg"` | Commit staged changes |
| `git log --oneline` | View commit history |
| `git reset HEAD -- <file>` | Unstage a file |
| `git reset HEAD -- <dir>` | Unstage a directory |
| `git reset --soft HEAD~1` | Undo last commit, keep changes staged |
| `.gitignore` | File that tells git what to ignore |
| `git checkout <branch>` | Switch branches |
| `git branch <name>` | Create a branch |
| `git branch -d <name>` | Delete a merged branch |
| `git branch -D <name>` | Force delete a branch |
| `git merge <branch>` | Merge a branch into current |
| `git rebase <branch>` | Rebase current branch onto another |
| `git push` | Push to remote |
| `git push --force` | Force push (overwrites remote) |
| `git push --force-with-lease` | Safe force push |
| `git pull` | Fetch and merge remote changes |
