# Git source code mgmt process

## Overview:

* all development is done on feature or bug fix branch
    * when branch is code complete and tested it is merged back to master using the squash process outlined bellow
      + this process insures the branch appears a single atomic commit on the master
	* insures master is stable
	* insures as features become available we can release them to production. I.E. incomplete features should never block a release or bug fix
    * ensures all feature and bug fix functionaly can be removed as a single atomic action from master
    * ensures all feature and bug fix have a single comment
      + this make source code control and management easier while still allowing developers to commit early and often durring development
* release are made from master by creating a tag
* production bug fixes are made by creating a branch from the release tag
	* code change must also be merge back to master
	
There are three main use cases outline in this document
* UC1: Working with feature branch
* UC2: Merging feature branches back to master
* UC3: Sharing a feature branch

You may find it easier to use a Git GUI instead of the CLI. I personally like [sourceTree](https://www.sourcetreeapp.com/) it is free

## gitHub.com Code Commit details
- clone using https
- when you clone you will be prompted for you usename and password

## UC1: Working with feature branch

Assume you have already cloned the master repo and are in the repo root directory on your local machine


1 switch to master branch and update

```
$ git branch
* master
$ git branch -vv
* master 6ddb8e4 [origin/master] Renamed DataProcessing to fantasySports
 
```

2 update master

```
$ git pull
```

3 Create feature branch

```
$ git checkout -b myFeatureBranch --track origin/master
```

4 Implement code changes. Feel free to use 'git commit' early and often. You changes will not effect anyone else

5 make sure you keep you branch in sync with master. 

Always use 'git merge'. Never ever use rebase. rebase rewrites history. never ever change history. it makes it very difficult to recover from problem. Strictly speaking you do not need the origin/master argument how ever its a good habit particularly if more than one person is working on the same branch as you

```
$ git merge origin/master

```

## UC2: Merging feature branches back to master

<span style="color:red">Merging back to the master branch should only be done by the merge master. I.E. Andy</span>

Once your feature set is complete and tested merge it back to master. Lets assume you have run git commit 15 times on your local branch. The following process will cause all of those changes to be merged back to master such that your changes appear as a single commit at the head

1. make sure master is unto date

   ```
   $ git checkout master
   $ git pull
   ```

2. create a merge candidate branch. Notice our mergeCandidate branch does not track anything

   ```
   $ git checkout -b mergeCandidate
   ```

3. merge your feature branch changes into the mergeCandidate branch such that they appear as a single commit

   ```
   $ git merge myFeatureBranch --squash
   ```

4. Testing the mergeCandidate branch. 

   At this point the mergeCandidate files will have all the code changes you made in your feature branch. Make sure the code builds and all unit tests pass.

5. Use 'git status' to make sure all the feature branch changes are staged.



6. use 'git commit' to create the feature completed msg. 

   This will be the only commit msg we will
see on the master. Master will not have any history of your original 15 commits you made on your branch. All commit comments associated with the 15 changes will be lost.

    ```
    $ git commit -m 'Feature set code complete. This will be the only commit msg on master' .
    ```
 
8 merge the mergeCandidate back to master and push

  ```
  $ git checkout master
  $ git merge mergeCandidate
  $ git push origin master

  ```

9 clean up dead branches. 

  NEVER USE your feature branch again once it has been merged back to master. Create a new branch from master if needed.


```
$ git branch -D mergeCanddiate myFeatureBranch
```

"-D" is short hand for --delete --force

9.1  If you have pushed your branch to origin make sure you remove it from origin

```
$ git push origin --delete myFeatureBranch
```


## UC3: Sharing a feature branch

Here is how to set up a feature branch so that several people can work on it together.

1 Andy creates a feature branch following directions above. In our example the feature branch is named 'twitterFilter' and is tracking orgin/master

```
$ pwd
/Users/andrewdavidson/workSpace/BigPWS
$ git branch -vv
  master        a30c251 [origin/master] Added description, and support for removing the Spark stuff from the uber jar. Updated run.sh script with correct path to uber jar.
* twitterFilter 0e0e94b [origin/master: ahead 1] grade uber jar problem?
$ 
```

2 Andy push the feature branch back to the root repo

```
$ git push origin twitterFilter
```

3 Henrik needs to checkout the branch and set up tracking so that Andy and Henrik can see each other changes to the shared branch

```
$ git fetch origin
$ git checkout -b twitterFilter --track origin/twitterFilter
```

4 Andy also needs to change his branch so that it tracks origin/branch instead of origin master

```
$ git branch twitterFilter --set-upstream-to origin/twitterFilter
```

5 Periodically either Andy or Henrik should merge changes in from master

```
$ git merge origin/master
```

