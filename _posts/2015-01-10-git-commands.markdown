---
layout: post
title:  "Git - All the commands you need to work on Git"
categories: git
---

Git has become the most widely used version control system in the world. Following are the premier commands one needs to master to work on Git.

* **Setup Git**

```
git config --global user.name <your-name>
git config --global user.email <youremail@xxx.com>
```
The <i>git config</i> command configures the user name and email for all commits by the current user.

* **Clone a repository**

```
git clone <repo>
```

The <i>git clone</i> command copies an existing central Git repository to your local machine. Take an example

```
git clone https://github.com/openssl/openssl.git
cd openssl
```

A copy of openssl Git repository is stored on your local machine within openssl directory.

* **Check out branch**

```
git checkout <branch>
```

The <i>git checkout</i> command checks out a branch. It's also easy to create a new branch for your work.

```
git checkout -b <your-new-branch>
```

You have created a new branch and switched to it. To verify that you are working on that branch, use:

```
git branch
```

* **Add changes**

```
git add <files>
```

The <i>git add</i> command adds a change in the working directory to the staging area. To add all the changes in the current directory, use:

```
git add .
```

* **Save changes**

```
git commit
```

The <i>git commit</i> command commits the staged changes to the project histroy. 

* **Inspect**

```
git status
```

The <i>git status</i> displays the state of the working directory and the staging area.

```
git log
```

The <i>git log</i> command displays the committed history.

* **Rewrite history**

```
git rebase -i HEAD~i
```

The <i>git rebase</i> with -i flag begins an interactive rebasing session. It opens an editor where you can enter commands for each commit, whether to merge it into other commits or rewrite its commit message. 

* **Push to remote**

```
git push origin master
```

The <i>git push</i> command pushes a branch to the remote centralized server.
