# Git Basic

Git은 버전 관리 시스템 중 하나 소스코드를 효과적으로 관리할 수 있게 해주는 소프트웨어이다. 이러한 Git은 버전 관리 외에도 버전 관리를 통해서 소스코드를 백업하거나 복구할 수 있는 기능을 제공해주고 다른 사람들과 협업할 수 있습니다.

<br>

## Commands

### Config

```bash
$ git config --global user.name "name"
$ git config --global user.email "email"
```

<br>

### init

```bash
$ mkdir git_practice
$ git init
Initialized empty Git repository in .../git_practice/.git/
```

<br>

### Status

```bash
$ git status
On branch master
No commits yet
n
```

<br>

### Staging

```bash
$ git add file
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   readme.md
```

<br>

### Commit

```bash
$ git commit -m "v0.1"
[master (root-commit) 61a80f4] v0.1
 1 file changed, 1 insertion(+)
 create mode 100644 readme.md
```

<br>

### Log & Diff

```bash
$ git log
Author: Ubuntu <ubuntu@ip-172-31-36-238.ap-northeast-2.compute.internal>
Date:   Sat Aug 1 08:50:45 2020 +0000

    v0.1
```

```bash
$ git diff
diff --git a/readme.md b/readme.md
index 0db7fca..f551d92 100644
--- a/readme.md
+++ b/readme.md
@@ -1 +1 @@
-# this is git practice
+# this is git practice 2
```

<br>

### Push

```bash
$ git push
Counting objects: 3, done.
Writing objects: 100% (3/3), 252 bytes | 252.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/mustnot/git_practice.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

