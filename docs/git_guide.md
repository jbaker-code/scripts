+++ Process for each issue/branch

# create branch (for example for issue #11): git checkout -b hc-11
# to switch back to main "git checkout main" (note -b means create!, don't use that here)
# list branches 'git branch -a'
# delete a branch 'git branch --delete HC-11'
# check status 'git status'
# to auto add all files to commit 'git commit -a'
# if not auto-adding all files, do 'git commit' to commit locally
# then do 'git push origin <branch>'
# git fetch --all
# git checkout main 

+++ Process for squash (branch only)

# open interactive rebase with something like 'git rebase -i origin <branch>' where the '5' here is how many commits to reconsider
# work through the interactive for squashing commits and the new commit message
# push to origin with 'git push origin <branch> -f'

+++ Process for merging into clean local branch

git checkout <branch>
git pull origin main
git push origin <branch>

+++ Merge notes

1. log command:

	git log --date-order --reverse --no-merges --abbrev-commit --date=short --format="# %h - %s [%cn :: %cI]%n%n%b" -n 100

2. merge command:

	git cherry-pick -x 35a840f83a48c05f0827d8d0bd994b0f146423d5

3. ammend commit msg:

	git commit --amend

	example commit msg: (note #73 here was the merge ticket, #126 was the ticket being merged in this merge commit)

		[#73] Merge [#126] Azure AD Auth provider login support - web-parent updates

		Merge from 5.4.x to main. Original commit:

		4c680e68e2 - [#126] Azure AD Auth provider login support - web-parent updates [Jason Baker :: 2023-03-21T21:29:38-07:00]

4. force push

	git push origin hc-146.i -f

+++ Fix commit signing

$ git rebase -i <origin branch>

Now mark every commit as 'e' for edit, save and quit

Run this to sign each commit until rebase is done:

$ git commit -S --amend --no-edit && git rebase --continue

force push.

$ git push origin <working branch> -f


+++ Diff Notes

See file names diffed between two branches (example here is branch v5.12.17 and branch v5.14.5):

$ git diff --name-only v5.12.17 v5.14.5

Diff a file between two branches:

$ git diff v5.12.17 v5.14.5 -- karaf-parent/karaf-model/src/main/resources/io/hyte/karaf/v5/hyte-karaf-v5.xsd


++++ Basic checkout process notes

console # go to console dir
git status # check status of console dir git
git checkout 5.16.x-maint # check out branch
git fetch --all --prune # fetch everything from origin server, doesn't change any files, just fetches metadata for changes
git pull # update current branch to latest, might want to git add */git stash/git rebase -i before this
bt # build console
runconsolekaraf-nomq  # run console in karaf


echo "Commit process"
	echo "  1. git-backup - backup current work folder, runs 'mvn clean', then zips"
	echo "  2. prep_commit - recreate clean commit staging folder, updating to latest, auto staging commits from current work"
	echo "     2a. stage_commit_files - stage all changes from current work to exiting commit staging folder"
	echo "     2b. stage_git_file - stage one file from current work to existing commit staging folder"
	echo "  3. Rebase if needed: git rebase -i <target branch>"
	echo "  4. Review changes in sourcetree, commit and push to origin"
	echo "  3. Check status, back in working dir: run 'git_update' OR:"
	echo "     3a. git add * && git stash - save current work"
	echo "     3b. git fetch --all prune - fetch from remote"
	echo "     3c. git_pull_force_overwrite - force update current branch from remote"
	echo "     3d. git stash pop - check that desired work was pushed to remote branch"
	echo "     3e. repeat process from beginning if work needs to be added to remote"
	echo
	echo "Rebasing process"
	echo "  1. prep_commit - create clean commit folder"
	echo "  2. in commit folder: git_pull_force_overwrite - discard any changes"
	echo "  3. in commit folder: git_list_remote_branches list remote branches "
	echo "  4. checkout desired branch: git checkout <branch>"
	echo "  5. rebase branch to some other branch: git rebase -i origin/5.16.maint"
	echo "  6. force push rebased stuff: git push origin <current branch> -f"	



+++ Commit process notes

prep_commit console # create copy of console's in tmp commit directory, copy changed files in console dir over tmp commit copy
git checkout -b ui-work-20260303 # if needed, now in tmp commit directory, create branch based on current branch such as 5.16.x-maint
stage_commit_files console # copy changed files from console dir to tmp commit directory again 
git_create_pull_request # create PR based on current branch in tmp commit dir
console # go back to console
cd .. # go to folder above console
git_backup console-5x mbt-fixes-20260302 # backup console-5x dir with "branch" mbt-fixes-20260302
console # go back to console
git add * # add all files to stash them
git stash # stash files
git fetch --all --prune # get latest from origin
git checkout mbt-fixes-20260302 # checkout branch with changes we've sent to origin
git stash pop # attempt to pop stashed files on branch, this should leave nothing new in console folder
stree . # open current dir in source tree to check on any changes we didn't commit earlier
karafkill && bt && runconsolekaraf-nomq # kill existing karafs, build all, run console

++++ Rebase Process notes

prep_commit console # prep commit, this will automatically checkout the branch the source folder is working on
git status # make sure we're rebasing the branch we think
git add * # add all files to commit directory, so we can stash them
git stash # stash files, now our commit dir has no changes in it and ready for rebase
git rebase -i origin/5.16.x-maint # do the rebase
# if rebase complains about some files with merge issues, edit them: 
stext messaging-parent/messaging-web/src/main/java/io/hyte/messaging/web/browse/HyteAPIBrowsePanel.java
# in sourcetree UI visually check the hand-merges and add the files hand-merged back to ongoing commit
git rebase --continue # tell rebase to continue
git push origin ui-work-20260303 -f # force-push the branch we're working in to origin