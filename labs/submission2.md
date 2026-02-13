## Task 1

### Commit hash
Command:
```bash
git log --oneline -1
# >>> ef48d66 (HEAD -> feature/lab2) Add test file
git cat-file -p ef48d66
```
Outputs:
```bash
tree 203f28a407bb91786601e7f2c30c77a1e5da1344
parent 74929ddf5675b6a13db49fb45095b99cf86f35c7
author Nikita <nikitazag3@gmail.com> 1771004381 +0300
committer Nikita <nikitazag3@gmail.com> 1771004381 +0300
gpgsig -----BEGIN SSH SIGNATURE-----
 U1NIU0lHAAAAAQAAADMAAAALc3NoLWVkMjU1MTkAAAAg7WlE0jujqNUeCEQ7Lpo/AH7Eh3
 65Uj+bGGLCoGprLsAAAAADZ2l0AAAAAAAAAAZzaGE1MTIAAABTAAAAC3NzaC1lZDI1NTE5
 AAAAQEP+1vdv19EDrwk0xIA4GHCeQDeVcToAPy/4cQQHXBECTs9QUvBTKtfXtv6A7SALTv
 ZbV/+0EAJJw45/Hq9meQA=
 -----END SSH SIGNATURE-----

Add test file
```
Analysis: commit hash stores a point in time representing specific commit

### Tree hash
Command:
```bash
git cat-file -p 203f28a407bb91786601e7f2c30c77a1e5da1344 # taken from previous section
```
Output:
```bash
040000 tree fe4054b3019331dbc6e40f33aaa9b795d415240d    .github
100644 blob 6e60bebec0724892a7c82c52183d0a7b467cb6bb    README.md
040000 tree a1061247fd38ef2a568735939f86af7b1000f83c    app
040000 tree eb79e5a468ab89b024bd4f3ed867c6a3954fe1f0    labs
040000 tree d3fb3722b7a867a83efde73c57c49b5ab3e62c63    lectures
100644 blob 2eec599a1130d2ff231309bb776d1989b97c6ab2    test.txt
```
Analysis: tree hash stores a hash of a directory of a repo. Tree/Blob hashes are used to save storage on duplicate data across repository


## Task 2

### Commits
Commands (taken from instruction):
```bash
echo "First commit" > file.txt && git add file.txt && git commit -m "First commit"
echo "Second commit" >> file.txt && git add file.txt && git commit -m "Second commit"
echo "Third commit"  >> file.txt && git add file.txt && git commit -m "Third commit"
git reflog -5
```
Outputs:
```bash
ae7b394 (HEAD -> git-reset-practice) HEAD@{0}: commit: Third commit
1402e35 HEAD@{1}: commit: Second commit
54bc58e HEAD@{2}: commit: First commit
9b3b85f (origin/feature/lab2, feature/lab2) HEAD@{3}: checkout: moving from feature/lab2 to git-reset-practice
9b3b85f (origin/feature/lab2, feature/lab2) HEAD@{4}: commit: Add submission2
```
Analysis: reflog shows movements of head: switching to new branch (`@{3} -> @{2}`) and piling up commits (`@{1}`, `@{2}`, `@{3}`)

### Resets
Commands (taken from instruction):
```bash
git reset --soft HEAD~1
git reset --hard HEAD~1
git reflog -5
```
Outputs:
```bash
54bc58e (HEAD -> git-reset-practice) HEAD@{0}: reset: moving to HEAD~1
1402e35 HEAD@{1}: reset: moving to HEAD~1
ae7b394 HEAD@{2}: commit: Third commit
1402e35 HEAD@{3}: commit: Second commit
54bc58e (HEAD -> git-reset-practice) HEAD@{4}: commit: First commit
```
Analysis: Resets discarded two latest commits, movement can be seen in reflog (`@{0}`, `@{1}`)
---
Commands:
```bash
git reset --hard 9b3b85f
git log --oneline -2
```
Outputs:
```
9b3b85f (HEAD -> git-reset-practice, origin/feature/lab2, feature/lab2) Add submission2
ef48d66 Add test file
```
Analysis: last reset moved us to source branch

## Task 3
Graph:
```bash
* 307e4d9 (side-branch) Side branch commit
* 816acab (HEAD -> feature/lab2, origin/feature/lab2) Update submission2.md
* 9b3b85f (git-reset-practice) Add submission2
* ef48d66 Add test file
| * be21017 (origin/feature/lab1, feature/lab1) Add PR template proof
| * ddab4df Add evidences
| * 7bd19c0 Add submission1.md
|/  
* 74929dd (origin/main, origin/HEAD, main) Add template
* d6b6a03 Update lab2
* 87810a0 feat: remove old Exam Exemption Policy
| * 0a09c16 (origin/release/f25) feat: remove old Exam Exemption Policy
|/  
```
Analysis: relevant commits start with `ef48d66`. The tree contains two commits for previous tasks, as well as commit from `side-branch`. 
Head points to previous commit `816acab`, first `side-branch` commit `307e4d9` builds on top of HEAD, so it is not in `feature/lab2`

## Task 4
Commands:
```bash
git tag v1.0.0
git push origin v1.0.0
git log --oneline -3
```
Outputs:
```bash
b80dd13 (HEAD -> feature/lab2, tag: v1.0.0, origin/feature/lab2) Add task3
816acab Update submission2.md
9b3b85f (git-reset-practice) Add submission2
```
---
Commands:
```bash
echo "new contents" > v110.txt; git add v110.txt; git commit -m "Add commit with new tag"
git tag v1.1.0
git push origin v1.1.0
git log --oneline -4
```
Outputs:
```bash
cd4b097 (HEAD -> feature/lab2, tag: v1.1.0) Add commit with new tag
b80dd13 (tag: v1.0.0, origin/feature/lab2) Add task3
816acab Update submission2.md
9b3b85f (git-reset-practice) Add submission2
```
---
Analysis: as we can see, commits in history are annotated with tags. Tags are a powerful tool to add versioning to consecutive commits

## Task 5
Commands:
```bash
git switch -c cmd-compare # Created and switched to new branch
git switch - # back to original branch
git checkout -b cmd-compare-2 # cmd-compare-2 branch is created and switched to it
echo "scratch" >> demo.txt; git add demo.txt
git restore --staged demo.txt 
git status
git branch
```

`git status` Outputs:
```bash
On branch cmd-compare-2
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        demo.txt

nothing added to commit but untracked files present (use "git add" to track)
```

`git branch` Outputs:
```bash
  cmd-compare
* cmd-compare-2
  feature/lab1
  feature/lab2
  git-reset-practice
  main
  side-branch
```

Analysis: There are multiple ways to navigate between branches/commits or file versions.
- `git checkout` is legacy way that supports both files and branches
- `git switch` is used to navigate between branches
- `git restore` is used to manage file versions across commits
