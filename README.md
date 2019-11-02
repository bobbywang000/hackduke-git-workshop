# hackduke-git-workshop

Notes:
- Unless a partner is specifically mentioned, you should do all steps.
- If you see something like `<var>`, that means that you should replace `#{var}` with the actual value of that variable. For example, if the instructions say to type in `echo '<your_name>'`, I would type in `echo 'Bobby'`.

## Part 1: Basic Setup (10 minutes)

- Follow [this guide](https://kbroman.org/github_tutorial/pages/first_time.html) to set up Git and Github.
- Find a partner. Decide who is partner A and who is partner B.
- Partner A creates a [fork](https://help.github.com/en/github/getting-started-with-github/fork-a-repo) of this repo and adds partner B as a [collaborator](https://help.github.com/en/github/setting-up-and-managing-your-github-user-account/inviting-collaborators-to-a-personal-repository).
- Clone the repo by typing `git clone <git_url>` in your terminal. Move your terminal into the repo by typing `cd <name_of_newly_created_folder>`.

## Part 2: Basic Git (15 minutes)

- Look around the repo! You can do this either by typing in `ls` in your terminal, or typing `open .` to open a finder window. You should see the following files: `test.py`, `data.json`, `output.log`.
- Type in `git branch`. This shows which branches exist. Currently, this is `master`. You can tell which branch you're on by which one has a star to the left.
- Type in `git log`. This shows the history of your current branch.
- Create a new branch and switch to it using `git checkout -b '<your_name>'`. If you and your partner have the same name, add your last name with a hyphen. Note that branch names cannot contain spaces.
- Type in `git status`. You should see something like
```
On branch <your_name>
nothing to commit, working tree clean
```
- Partner A should add their name to the top of `output.log`. Partner B should add their name to the bottom of `output.log`.
- Type in `git status`. You should see something like
```
On branch <your_name>
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   output.log
```
- Type in `git diff`. You should a diff containing the line where you added your name.
- This means that your changes are in the **working tree**, or the group of files that Git is tracking. You can add them to your **staging area** by typing in `git add output.log`. The **staging area** is what gets added to your commit when you type `git commit`. Once you type `git add output.log`, you should see the following when you type `git status`:
```
On branch <your_name>
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	modified:   output.log
```
- Type in `git diff` again. You should see nothing. This is because `git diff` (with no additional arguments) only tracks changes in the working tree - once it's moved to the staging area, those changes are no longer visible.
- Let's actually commit our changes by typing in `git commit -m '<a_helpful_message>'`. Actualy write a helpful message - it requires some extra time now but saves a LOT of time debugging down the road. At this point, you should see something like 
```
[<your_name> f9ba68e] <a_helpful_message>
 1 file changed, 1 insertions(+), 0 deletion(-)
```
- `f9ba68e` is what's called a hash - you can think of it as a unique fingerprint for your commit that the machine uses, while the commit message is a human-readable message.
- Let's update Github with our updates. Type in `git push --set-upstream origin <your_name>`. This creates a branch in Github that is identical to the branch you have on your computer. Your goal should usually be the state of your branches on Github to be as close as possible to the state of your branches on your local computer. We'll go over strategies to accomplish this in the next section. Note: once you create the branch in Github using `git push --set-upstream origin <your_name>`, you can simply `git push` on subsequent pushes. You should see something like 
```
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 4 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 1.19 KiB | 608.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0)
remote:
remote: Create a pull request for '<your_name>' on GitHub by visiting:
remote:      https://github.com/<your_github_user>/hackduke-git-workshop/pull/new/<your_name>
remote:
To https://github.com/<your_github_user>/hackduke-git-workshop.git
 * [new branch]      <your_name> -> <your_name>
Branch '<your_name>' set up to track remote branch '<your_name>' from 'origin'.
```
- Let's follow that link. Fill out the title and description sections of the pull request, then create the pull request.
- Admire your work, then click on the link for merging your pull request.
- Click into the `code` tab of your repo on Github, and click on `output.log`. You should see that it has both of your names now!

## Part 3: Git Usage Patterns (30 minutes)

We may not get through all of these!

### Merging and rebasing

- In the Github UI, check out branch `conflict-example-<A_or_B>`. Partners A and B should both open pull requests from their respective branch.
- Partner A should merge their PR. Wait 1 minute.
- Partner B should try to merge their PR. But they can't!
- This is an example of a **merge conflict**. Partner B's changes make sense relative to the old `master`, but not the new `master` that includes partner A's work!
- There are two ways of solving this: merging, and rebasing.
- Merging is the (usually) faster option. You can even do this in the Github UI. Just click the "resolve merge conflicts" button at the bottom. Alternatively, you can `git merge origin master` while your checked-out branch is `conflict-example`. This will add a merge commit to `conflict-example`.
- Rebasing is the (usually) cleaner option. You can do this by typing in `git pull -rebase origin master` while your checked-out branch is `conflict-example`. The benefit of rebasing is that you won't have an added merge commit to the commit history of `conflict-example`.
- Try using either one or the other to fix the merge conflict. For each file that says `both modified`, you'll need to fix the lines between `<<<<<<<` and `>>>>>>>`. Once you're done, `git add` the conflicting files and `git merge --continue` (or `git rebase --continue`).

### Force pushing
- Try to `git push`. You should see a failure message. Now try `git push -f`.
- Force pushing like this can be very dangerous if multiple people are working on the same branch. You can easily overwrite someone else's commits, and/or really mess others up if they attempt to rebase on top of a branch you force-pushed to. This can be avoided by following one simple rule: **do not work on the same branch as another person.** A correlary of this is **do not rebase off another person's personal branch unless you know what you're doing.** This saves *so* many headaches in the long run.
- As an aside, this is also why you should *never* force push to master. To enforce this, Github actually lets you disable force-pushes to master (or any given branch).
- After force pushing, you should see that `conflict-example-B` no longer has a merge conflict. Merge it with master.

### Interactive rebasing

- Run `git checkout rebase-example-<A_or_B>`. Type in `git log` and notice how there's a couple of commits. 
- Type in `git rebase -i HEAD~5`. You should see something like the following pop up in vim:
```
pick afbc457 please work lol
pick 7d63553 fixup: bugfix
pick 79329e3 add a comment

# Rebase 4b42e7d..79329e3 onto 4b42e7d (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified). Use -c <commit> to reword the commit message.
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```
- I personally find `pick`, `reword`, `squash`, and `fixup` to be the most useful. Using those 4 commands, collapse the messages into the following. Choose a better commit message name for "please work lol."
```
pick 79329e3 add a comment
reword afbc457 please work lol
fixup 7d63553 fixup: bugfix
```
- If you're not familiar with vim, the above might be very hard. One workaround is to copy the text into your favorite text editor (`cmd + c` works in a terminal), edit the commits, delete the existing lines (`dd` in editor mode), paste the edited lines back into vim (`cmd + v` works in a terminal too), and save and quit (`:wq` in editor mode).
- If you rebase your local after already pushing, you'll need to force push after rebasing. Because interactive rebasing is so useful, this is another reason to always **work in separate branches** so that you can safely use interactive rebase.
- Interactive rebasing also allows you to quickly make some not-great commit messages at first and then edit your Git history into something more easily understandable.
- After rebasing, you should see the following after typing in git log:

```
commit c0335d03589f09e707d51c87b14db173bdc86264 (HEAD -> rebase-example-A)
Author: Bobby Wang <bcw22@duke.edu>
Date:   Sat Nov 2 12:30:48 2019 -0700

    <your_better_message>

commit 3a726e83a72a83426d7f16d36103420b1e5bcba7
Author: Bobby Wang <bcw22@duke.edu>
Date:   Sat Nov 2 12:31:51 2019 -0700

    add a comment

commit 4b42e7db57120171ba4a31417b165d4fff2e8fcb (origin/master, origin/HEAD, master)
Author: Bobby Wang <bcw22@duke.edu>
Date:   Sat Nov 2 12:28:30 2019 -0700

    add test.py
```

### Resetting

- The `git reset <commit>` command can be super powerful. There's three flavors of `git reset`:
	- `git reset --soft <commit>`: remove the last commit, but keep all your changes in the staging area. You could type in `git commit` immediately after running `git reset --soft` and end up with the same commit.
	- `git reset <commit>`: remove the last commit, and remove the changes from your staging area (but keep them in the working tree). This is effectively the same as running `git rebase -i <commit>`, and putting in `reword` for the first commit, and `fixup` for every subsequent commit.
	- `git reset --hard <commit>`: remove the last commit, and remove the changes from your working tree entirely. This effectively means that your code is 100% in the same state as the commit. It's extremely useful if you've messed up your branch and want to completely restart from an earlier commit.

### Reflog

- If at any point you've messsed up your branch after a botched rebase, or you've accidentally deleted a commit, you can "undo" your actions using `git reflog`. This shows a list of the most recently created commits, including commits that are no longer referenced by a branch. You can simply look up the commit in the reflog, then `git reset --hard <commit>` to restore the head of your branch to that commit.
- If you pushed your branch to Github before rebasing, you can also look up the commit in the Github UI to `git reset --hard` to.
- Try looking in the reflog for the commit that represented the old head of `conflict-example-B` (before you merged/rebased and force pushed). `git reset --hard` the head of `conflict-example-B` to that branch. If you solved the conflict using a merge the first time around, try solving it using a rebase (and vice versa).

### Random smaller tips

#### Checking out a commit

- We previously used `git checkout <branch>` to check out a branch. You can also check out a commit itself. Try running `git checkout HEAD^`: your code will be at the state of the second-most-recent commit. You can create a new branch off this commit using `git checkout -b <branch name>`. 

#### Reverting

- `git revert <commit>` adds a new commit that undoes every change in the specified commit.

#### Amending commits

- If you have a big commit, and are about to make a second commit to address issues in the first commit, instead of making a `fixup` commit and running `git rebase -i`, you can just add the files to your staging area, then `git commit --amend`.


## Extras


### Using Git as a deploy system


### Gitignore


### Bash Aliasing



