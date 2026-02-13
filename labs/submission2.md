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
---
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
---
Analysis: tree hash stores a hash of a directory of a repo. Tree/Blob hashes are used to save storage on duplicate data across repository

