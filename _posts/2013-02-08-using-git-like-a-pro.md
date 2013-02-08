---
layout: post
title: "Using git like a PRO"
category: posts
---

Git has a lot of features, but, well, 90% of who use it doesn't know half of
them, and, well, maybe, someday, one of those features can "save your life".

So, I decided to compile a small list of useful git commands, tips and tricks
that use or used sometime in my work.

Of course, if you have yours tricks/tips (and you probably have), comment above
the post or ping me at twitter/mail/anything, I'll be glad to add your tip and
put your name in it.

Let's do this.

----------


#### See who and in which commit each line of a file was changed last time

    git blame path/to/file

#### See the reference log

This will show the log of your local operations in the git tree, useful to get
revision code of a specific commit (see next item).

    git revlog

#### Get revision code for anything

    git rev-parse HEAD # last commit
    git rev-parse HEAD~5 # 5 commits back from last commit
    git rev-parse develop # last commit from develop branch

#### Revert a specific file to a specific commit

    git log path/to/file # to check the revision code
    git checkout revision_code path/to/file

#### Revert a specific file to last commit

    git checkout HEAD path/to/file

#### Revert a specific file to 3 commits back from last

    git checkout HEAD~3 path/to/file

#### Get an old version of some file

    git show HEAD~3:path/to/file > path/to/file_3_commits_ago

#### Shallow clone

According to the docs:

> Create a shallow clone with a history truncated to the specified number of
revisions. A shallow repository has a number of limitations (you cannot clone
or fetch from it, nor push from nor into it), but is adequate if you are only
interested in the recent history of a large project with a long history, and
would want to send in fixes as patches.

So, if you want only the last revision:

    git clone https://github.com/twitter/bootstrap --depth 1


This can drastically reduce the clone. The bad thing is that you will have NO
HISTORY BEFORE THE HEAD WHEN YOU DO THE CLONE.

#### Move a recent commit to another branch

This is pretty useful when you're working on something, commit, and them
realize that it would fit better in another branch (or that you're in the
wrong branch)... this is how to move it to another branch.

    git branch new
    git reset --hard HEAD~1 #go back 1 commit, YOU WILL LOST UNCOMMITED CHANGES
    git checkout new


#### Revert your entire tree to the last commit state
(by [@luizkowalski](https://github.com/luizkowalski))

Useful when you do some crap and want to throw it all away.

    git reset --hard HEAD

You can also specify something like `HEAD~3` to get back 3 commits from HEAD.


#### Remove files that have not been added to staging area
(by [@luizkowalski](https://github.com/luizkowalski))

    git clean -df

---

Let's make this list bigger! Have your own tip/trick? So share it with us!