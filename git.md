# GIT

## Table of Contents
- [Repo config](#repo_config)
- [Log viewing](#log_viewing)
- [Repo Cleanup](#repo_cleanup)
- [Tags](#tags)
- [Branches](#branches)
- [Remote tracking](#remote_tracking)
- [Stash](#stash)
- [Submodule](#submodule)
- [Setup SSH keys](#ssh_keys)
- [Repo cleanup](#cleanup)
- [Misc](#misc)

<a name="repo_config"></a>
## Repo config
- View user and email used for git commit
```
git config user.name
git config user.email
```

- To set user + email
```
git config user.name "<user-name>"
git config user.email "<user-email-address>"
```

<a name="log_viewing"></a>
## Log viewing
- Simple view
`git log --graph --decorate --abbrev-commit --pretty=oneline`
![git_simple_log](resources/git_simple_log.png)

- Log with date/time + author
`git log --graph --decorate --abbrev-commit --pretty=format:"%C(cyan)%h %C(yellow)%ad%Cred%d %Cgreen [%an:%ae] %Creset%s" --decorate --date=iso`
![git_date_log](resources/git_date_log.png)

<a name="repo_cleanup"></a>
## Repo Cleanup
```
git reflog expire --expire=now --all
git gc --prune=now
git stash clear
```

<a name="tags"></a>
## Tags
- `git tag <tag_name>` - Create a tag
- `git tag -d <tag_name>` - Delete a tag
- `git tag -l | sort -V` - See tags in the sort order
- `git push --tags` - Push tags to remote
- Rename an old tag
```
git tag <new_tag_name> <old_tag_name>
git tag -d <old_tag_name>
git push origin :refs/tags/<old_tag_name>
```

<a name="branches"></a>
## Branches

- List all local branches -- `git branch -avv`
- List all local + remote branches -- `git branch -arvv`
- List branch of a specific remote -- `git branch --remote --list origin*`
- Remove branch locally -- `git branch -d {the_local_branch}`
- Remove remote branch -- `git push origin --delete {the_remote_branch}`
- Create a branch in a old repo without history -- `git checkout --orphan newbranch`

<a name="remote_tracking"></a>
## Remote tracking

- To view remote tracking of the branches
  * simple command
    ```
    git branch -rvv
    ```
  * more detailed view
    ```
    git remote show origin
    ```

- List all currently configured remotes
```
git remote -v
```

- To set remote of a repository
```
git remote add <remote_name> <url>
```

- To remove a remote
```
git remote rm <remote_name>
```

- To set/change URL for a remote
```
git remote set-url origin <url>
```

- To set remote of a branch
```
git branch branch_name --set-upstream-to <remote_name/branch_name>
```

- Push and track a remote branch
```
git push --set-upstream <remote_name> <branch_name>
```

- To remove upstream tracking of a branch
```
git branch --unset-upstream <branch_name>
```

- Show information about a remote
```
git remote show <remote>
```

- Download all changes from remote but don't merged into HEAD
```
git fetch <remote>
```

- Re-sync git to remove dead remote tracking and tags
```
git fetch --all --prune --prune-tags
```

- Download changes and merge/integrated into HEAD
```
git pull <remote> <branch>
```

- Push local commits to remote
```
git push <remote> <branch>
```

- Push a specific commit id to remote
```
git push origin <commit_id_has>:master
```

- Push everything
```
git push --all
```
- chmod changes
```
git diff --summary | grep --color 'mode change 100755 => 100644' | cut -d' ' -f7- | xargs -d'\n' chmod +x
git diff --summary | grep --color 'mode change 100644 => 100755' | cut -d' ' -f7- | xargs -d'\n' chmod -x
```

<a name="stash"></a>
## Stash
Temporarily saves changes of half-done work.

- `git stash` - Temporarily stores all modified tracked files
- `git stash -u` - Temporarily stores all modified tracked files + includes new files
- `git stash -a` - Temporarily stores all modified tracked files + includes ignore files too
- `git stash list` - Show list of stashes
- `git stash pop` - Restore changes from the most recently stashed
- `git stash pop stash@{1}` - Restore a specific stash and remove it from the list
- `git stash drop` - Clear the most recently stashed
- `git stash drop stash@{1}` - Clear a specific stash
- `git stash show` - Summary of the changes
- `git stash show -p` - Summary of the changes in patch format
- `git stash branch new-branch-name stash@{1}` - Create branch from git stash
- `git stash apply` - Apply stash changes
- `git stash clear` - Clear all stashes
- `git stash save "add style to our site"` - Save stash with comments

<a name="submodule"></a>
## Submodule

- Initialize local repo for a new submodule
```
git submodule init
git submodule status
```

- Add a new repo as a submodule
```
git submodule add -f link-to-new-repo path/to/folder/to/checkout/repo
```

- Clone a repo having submodule(s)
```
git clone --recurse-submodules link-to-repo
```

- Fetch submodule in an already cloned repo
```
git submodule update --init --recursive
```

- Check out the master and update all submodules
```
git submodule foreach --recursive git checkout master
git submodule foreach --recursive git pull
```

- Pull/fetch changes for submodules
```
git submodule foreach --recursive git checkout master
git submodule foreach --recursive git pull
```

- Remove a submodule
```
git submodule deinit path/to/folder/having/repo/as/submodule
git rm path/to/folder/having/repo/as/submodule
```

<a name="ssh_keys"></a>
## Setup SSH keys

### For single user
https://help.github.com/articles/connecting-to-github-with-ssh/


### For multiple accounts
Let's manage two git accounts where first one is treated as a primary one.

- Create a folder to have ssh keys
```
mkdir -p ~/.ssh
chmod u+rwx ~/.ssh
```

- Create key for primary user
```
ssh-keygen -t rsa -b 4096 -C "primary_user@primary.com" -f ~/.ssh/id_rsa_primary
```
  * After entering yoru passphrase, two files will be generated:
   - `Your identification has been saved in ~/.ssh/id_rsa_primary.`
   - `Your public key has been saved in ~/.ssh/id_rsa_primary.pub.`

- Create key for secondary user
```
ssh-keygen -t rsa -b 4096 -C "secondary_user@secondary.com" -f ~/.ssh/id_rsa_secondary
```
  * After entering yoru passphrase, two files will be generated:
   - `Your identification has been saved in ~/.ssh/id_rsa_secondary.`
   - `Your public key has been saved in ~/.ssh/id_rsa_secondary.pub.`

- Start the ssh-agent in the background.
```
eval "$(ssh-agent -s)"
```

- View active keys
```
ssh-add -L
```

- Add above generated SSH private key(s) to the ssh-agent
```
ssh-add ~/.ssh/id_rsa_primary
ssh-add ~/.ssh/id_rsa_secondary
```

- To test ssh key with github.com: `ssh -T git@github.com`

- Copy above generated SSH private key(s) to the clipboard
```
xclip -sel clip < ~/.ssh/id_rsa_primary.pub
xclip -sel clip < ~/.ssh/id_rsa_secondar.pub
```

- Set configuration file to manage both accounts.
```
touch ~/.ssh/config
```

- Add following texts in the above created `~/.ssh/config` file:
```
Host github-primary
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_primary

Host github-secondary
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_secondary
```

- Now to checkout a repo against a specific user:
```
git clone github-secondary:git-hub-user-name/repo-name
```

- Config user name and email as per your github account:
```
git config user.name "git-hub-user-name"
git config user.email "git-hub-email-visible-to-others"
```

<a name="cleanup"></a>
## Repo cleanup

```
git reflog expire --expire=now --all
git gc --prune=now
```
  -or-
```
git fsck --unreachable
git reflog expire --expire=0 --all
git repack -a -d -l
git prune
git gc --aggressive --prune=now
```

<a name="misc"></a>
## Misc

- Count number of commits b/w two commits
```
git log starthash..endhash --pretty=oneline | wc -l
```

- List un-tracked files
```
git ls-files . --exclude-standard --others
```

- List ignored files
```
git ls-files --exclude-standard --others --ignored
```

- List ignored files in a specific directory
```
git ls-files --exclude-standard --others --ignored --directory </path/to/directory>
```

- Remove a file from history (Becareful: It will change git hash values.)

```
git filter-branch -f --index-filter "git rm -rf --cached --ignore-unmatch path/to/file-or-folder/to/be/removed" HEAD

# Remove backup
git update-ref -d refs/original/refs/heads/master

# Once happy with removal, repackage git repo
git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin
git reflog expire --expire=now --all
git gc --prune=now
```
- Revert to a commit in the history
`git reflog` - Find your commit
`git reset HEAD@{1}` - Revert to the commit assuming it is HEAD@{1}.

- List only deleted items for commit
  `git ls-files --deleted -z | xargs -0 git rm`

- Create empty commit
```
git commit --allow-empty -m "Do an empty commit"
```

- Amend author the last commit
```
git commit --amend --author="aakbar5 <16612387+aakbar5@users.noreply.github.com>"
```

- Generate patch with binary file - `git format-patch --full-index --binary <commit-id>`
    - Now use `git am *.patch` command to apply patches.

- Update your forked repo with the original repo
```
git checkout <local-branch>
git remote -v
git remote add upstream </path/to/original/repo>
git remote -v
git fetch upstream
git merge upstream/branch-name
```
