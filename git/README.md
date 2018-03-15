# Git Learnings

## Misc git commands

To pull source to local from any repository you have access:
```
git clone <clone url of repository>
```

## Get Set Up With GitHub

* Create a GitHub account if you don't already have one.
* Get invited to the specific group: you will need to ask the team lead for this.
* Make sure you have permissions to view and fork their repository.

You'll need to generate an SSH keypair in order to push commits to GitHub. See
the [GitHub SSH key instructions][1] for details on how to set this up. When you
generate the keypair, you'll also need to come up with a password. Don't forget
it, as you'll have to type it in whenever you want interact with GitHub.

If you are using your existing GitHub account, make sure that you create a new
keypair for use with work. Do not reuse any existing keypair.

```
ssh-keygen -t rsa -C "email.you.told.github.about@mail.com"
cat id_rsa.pub
```

Additionally, you can configure git to remember your name, email, etc.

```
git config --global user.name "John Doe"
git config --global user.email "john.doe@gmail.com"
git config --global github.user johndoe
git config --global github.token GUID-LIKE-TOKEN
```

If you already have a personal email address associated with your Github
account, you may also want to go to your [email settings][2] on Github to add
your work email. Once you do this, you can choose for certain repos to
send emails to your work email rather than your personal email (under
[Notification Center][3]).


## To work on XXX Repository

To work a repository first clone it and then work on your copy.
To do this, visit the repository in github and click the Fork button. Next,
copy the git URL for your fork; it should look something like
`git@github.com:yourname/xxx.git`. Now, `cd` to the directory where you want
to put the repo and then clone it and add a suitable remote:

```
cd ~/code
git clone git@github.com:yourname/xxx.git

cd xxx

# Your fork is now "origin"; add a "work" remote as well.
git remote add work git@github.com:work-organization/xxx.git

# Pull the latest from the work remote's master branch.
git pull work master
```

Next, modify your `.git/config` file in each repo to automatically fetch pull
request branches, making sure that it has fetch entries for your work remote.

```
[remote "work"]
  url = git@github.com:work-organization/xxx.git
  fetch = +refs/heads/*:refs/remotes/work/*
  fetch = +refs/pull/*/head:refs/remotes/work/pr/*
```

With this change, when you run git fetch work you will automatically fetch
references to pull request branches. If you want to check out pull request
`#1234`, you can run `git checkout work/pr/1234`.

## Creating a development branch

*You should never do work in the work master branch,* and you should keep your fork of the master branch updated from the main repo by pulling often. 
It is the responsibility of each contributor to make sure that each pull request he or she opens is mergeable by staying current with work/master.

```sh
    git checkout master
    git pull work master                  # assumes your work-organization remote is named "work"
    git push origin master    # assumes your remote is named "origin"
```

When you are ready to start working on a new feature, first create a branch locally and in your own fork.

```sh
    git checkout master                 # make sure you're on master first
    git pull work master                  # assumes your work-organization remote is named "work"
    git checkout -b jira-1234            # create a new branch based on master. Name it after the JIRA issue you are working.
    git push -u origin jira-1234
```

To update your branch with the latest master:

```sh
    git checkout jira-1234               # check out the branch you're working on if needed
    git fetch work                        # fetch the latest from the work repo
    git rebase -i work/master             # do an interactive rebase on your branch using work/master
```

[Learn more about `git rebase -i`.](http://git-scm.com/book/en/Git-Tools-Rewriting-History)

Note that once you rebase, you will need to force push your branch (`git push -f`) to your remote if you have already pushed it in the past.

Others who followed the [To work on XXX Repository] instructions can then fetch your pull request:
```sh
    git fetch work
    git checkout work/pr/4567             # Note that the last piece is the pull request number, *not* the branch name.
```

[1]: https://help.github.com/articles/generating-ssh-keys/
[2]: https://github.com/settings/emails
[3]: https://github.com/settings/notifications
