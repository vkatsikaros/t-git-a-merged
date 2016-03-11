# Test `git branch -a --merged`

`mergeA` and `mergeB` are 2 different local repos that synchronize with the remote repo. Test to see what each local repo thinks about the `git branch -a --merged` of a remote branch

Prepare the local dirs. An empty repo named `t-git-a-merged` is also created in github.
```
user@host:~/t $ rm -rf mergeA mergeB
user@host:~/t $ mkdir mergeA
user@host:~/t $ mkdir mergeB
```

Create the repo in mergeA and push to remote
```
user@host:~/t $ cd mergeA
user@host:~/t/mergeA $ echo "Hello" >> hello.txt
user@host:~/t/mergeA $ git init
Initialized empty Git repository in /home/vag/t/mergeA/.git/
user@host:~/t/mergeA $ git commit -m "first commit"
[master (root-commit) 15a7183] first commit
 1 file changed, 1 insertion(+)
 create mode 100644 hello.txt
user@host:~/t/mergeA (master)$ git remote add origin git@github.com:vkatsikaros/t-git-a-merged.git
user@host:~/t/mergeA (master)$ git push -u origin master
Counting objects: 3, done.
Writing objects: 100% (3/3), 223 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To git@github.com:vkatsikaros/t-git-a-merged.git
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
```

mergeA: create new branch, commit, push it to remote. Merge in master and push it too.
```
user@host:~/t/mergeA (master)$ git checkout -b branch1
Switched to a new branch 'branch1'
user@host:~/t/mergeA (branch1)$ echo "Καλημέρα" >> kalimera.txt
user@host:~/t/mergeA (branch1)$ git add kalimera.txt
user@host:~/t/mergeA (branch1)$ git commit -m"GR hello"
[branch1 8b55db8] GR hello
 1 file changed, 1 insertion(+)
 create mode 100644 kalimera.txt
user@host:~/t/mergeA (branch1)$ git push origin branch1
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 303 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To git@github.com:vkatsikaros/t-git-a-merged.git
 * [new branch]      branch1 -> branch1
user@host:~/t/mergeA (branch1)$ git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.
user@host:~/t/mergeA (master)$ git merge branch1
Updating 15a7183..8b55db8
Fast-forward
 kalimera.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 kalimera.txt
user@host:~/t/mergeA (master)$ git push origin master
Total 0 (delta 0), reused 0 (delta 0)
To git@github.com:vkatsikaros/t-git-a-merged.git
   15a7183..8b55db8  master -> master
```

Clone into mergeB. Check what mergeB thinks about the merged branches.
```
user@host:~/t/mergeA (master)$ cd ../mergeB
user@host:~/t/mergeB $ git clone git@github.com:vkatsikaros/test-merged.git .
Cloning into '.'...
ERROR: Repository not found.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
user@host:~/t/mergeB $ ls -al
total 8
drwxrwxr-x  2 vag vag 4096 Mar 11 11:38 .
drwxrwxr-x 13 vag vag 4096 Mar 11 11:31 ..
user@host:~/t/mergeB $ git clone git@github.com:vkatsikaros/t-git-a-merged .
Cloning into '.'...
remote: Counting objects: 6, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 6 (delta 0), reused 6 (delta 0), pack-reused 0
Receiving objects: 100% (6/6), done.
Checking connectivity... done.
user@host:~/t/mergeB (master)$ git branch -a --merged
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/branch1
  remotes/origin/master
```

Go back to mergeA and go to branch1, commit and push it to remote.
```
user@host:~/t/mergeB (master)$ cd ../mergeA/
user@host:~/t/mergeA (master)$ git branch
  branch1
* master
user@host:~/t/mergeA (master)$ git checkout branch1
Switched to branch 'branch1'
user@host:~/t/mergeA (branch1)$ vim kalimera.txt
user@host:~/t/mergeA (branch1)$ git add kalimera.txt
user@host:~/t/mergeA (branch1)$ git commit -m"Make a change"
[branch1 cc16c95] Make a change
 1 file changed, 1 insertion(+), 1 deletion(-)
user@host:~/t/mergeA (branch1)$ git push origin branch1
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 306 bytes | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
To git@github.com:vkatsikaros/t-git-a-merged.git
   8b55db8..cc16c95  branch1 -> branch1
```

Check what mergeB thinks about the merged branches. Then pull and check again.
```
user@host:~/t/mergeA (branch1)$ cd ../mergeB
user@host:~/t/mergeB (master)$ git branch -a --merged
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/branch1
  remotes/origin/master
user@host:~/t/mergeB (master)$ git pull
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:vkatsikaros/t-git-a-merged
   8b55db8..cc16c95  branch1    -> origin/branch1
Already up-to-date.
user@host:~/t/mergeB (master)$ git branch -a --merged
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
```
It learned about the new state of the `branch1` after the pull.

Switch to mergeA, merge branch1 in master and push to origin.
```

user@host:~/t/mergeB (master)$ cd ../mergeA
user@host:~/t/mergeA (branch1)$ git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.
user@host:~/t/mergeA (master)$ git merge branch1
Updating 8b55db8..cc16c95
Fast-forward
 kalimera.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
user@host:~/t/mergeA (master)$ git push origin master
Total 0 (delta 0), reused 0 (delta 0)
To git@github.com:vkatsikaros/t-git-a-merged.git
   8b55db8..cc16c95  master -> master
```

Check what mergeB thinks about branch1
```
user@host:~/t/mergeA (master)$ cd ../mergeB/
user@host:~/t/mergeB (master)$ git branch -a --merged
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
user@host:~/t/mergeB (master)$ git pull
From github.com:vkatsikaros/t-git-a-merged
   8b55db8..cc16c95  master     -> origin/master
Updating 8b55db8..cc16c95
Fast-forward
 kalimera.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
user@host:~/t/mergeB (master)$ git branch -a --merged
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/branch1
  remotes/origin/master
```
It learned about the new state of the `branch1` after the pull.

So, *after* the pull it knows the remote branch is merged (ie `git branch -a` doesn't communicate with the remote repo).
```
-a, --all
	List both remote-tracking branches and local branches.
```

TODO: does the same happen with fetch?
