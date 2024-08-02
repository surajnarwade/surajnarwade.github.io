+++
title = "Gerrit Error not Signed-off-by author/........ Resolved"
date = "2016-04-15"
category = "Troubleshoot"
author = "Suraj Narwade"

+++

Yesterday, I was sending patch for review on gerrit, I commmited the code as follows,
Everything works fine ;) ,
```
 [user@localhost project]$ git commit --amend
 [project d9593ab] <commit message here>
 Date: Thu Apr 14 17:34:53 2016 +0530
 1. file changed, 75 insertions(+)
   create mode 100644 project/file.py
```   

While I was pushing code to gerrit for review, Suddenly Heavy errorfall occurs with :o
following error:
```
 [user@localhost project]$ git review develop
 remote: Processing changes: refs: 1, done.
 To ssh://user@xxxxxxxxxxxxxxxxxxxxxxxx.com:22/project
 ! [remote rejected] HEAD -> refs/publish/develop/project (not Signed-off-by author/committer/uploader in  commit message footer)
 error: failed to push some refs to 'ssh://user@xxxxxxxxxxxxxxxxxxxxxxxxx.com:22/project'
```
After trying different methods for half an hour, we have checked commit which was as below:

```
 <commit messgae here>
 Change-Id: yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy
 # Please enter the commit message for your changes. Lines starting
 # with '#' will be ignored, and an empty message aborts the commit.
 #
 # Date:      Thu Apr 14 17:34:53 2016 +0530
 #
 # On branch project
 # Your branch is ahead of 'origin/develop' by 1 commit.
 #   (use "git push" to publish your local commits)
 #
 # Changes to be committed:
 #       new file:   project/file.py
```
After that we had removed Change-Id for patch set and added Signed-off-by line along with username and email id as shown below:
```
 <commit message here>
 Signed-off-by: username <userid@xyz.com>
 # Please enter the commit message for your changes. Lines starting
 # with '#' will be ignored, and an empty message aborts the commit.
 #
 # Date:      Thu Apr 14 17:34:53 2016 +0530
 #
 # On branch project
 # Your branch is ahead of 'origin/develop' by 1 commit.
 #   (use "git push" to publish your local commits)
 #
 # Changes to be committed:
 #       new file:   project/file.py
```
**And yippee, it works !!! :-) :-)**

```
 [user@localhost project]$ git review develop
 remote: Processing changes: new: 1, refs: 1, done
 remote:
 remote: New Changes:
 remote:   https://xxxxxxxxxxxxxxxxxxxx.com/gerrit/00000 <commit message here>
 remote:
 To ssh://user@xxxxxxxxxxxxxxxxxxxxxx.com:22/project
- [new branch]      HEAD -> refs/publish/develop/project
```  

Thank you, [ChandanKumar](https://twitter.com/ciypro) ++

