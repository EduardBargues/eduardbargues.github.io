---
layout: default
title: Top 10 git commands I use at work
parent: Articles
nav_order: 1
has_children: false
---

`03-01-2021` `git`

## Introduction

As a software developer, I spend most of my time dealing with GIT. This magic tool that, if you know how to use it, can make your life so easy but, if you treat it badly, can easily backfire.
In this article, I'll try to show and explain my most used GIT commands at work. How I use them and why. I'll try to list them following a typical development flow but a little GIT knowledge from the reader might be required 😉.

[fetch](#fetch), [checkout](#checkout), [status](#status), [add](#add), [commit](#commit), [fixup](#fixup), [log](#log), [rebase](#rebase), [push](#push), [stash](#stash).

---

### fetch

> git fetch

Have you ever needed to press F5 on a website to refresh the content? git fetch is the same thing for GIT. Whenever you are going to do something that requires some origin state, remember to do first a git fetch.

### checkout

> git checkout -b \<branch-name>
> git checkout -b \<branch-name> origin/\<origin-branch-name>

The first thing to do before starting coding? Create a feature (or bugfix) branch 😄! Here we are doing this by checking out a new branch (note the -b) called `<branch-name>` that will be tracking the branch in origin called `<origin-branch-name>`. If you don't have an origin branch the last part can be omitted.

Example: `git checkout -b feature/branch`

### status

> git status

One of my favorites, not because of its impact but because it defines a common ground between me and the current state of my repository. Each time a colleague asks me anything about how to do something I always start with `Could you do a git-status, please?` Most of the time, the answer it's just one git status away 🙂.

### add

> git add -p

Well, I would say the (almost always) first git command I type into my terminal after finishing my development. It allows deciding which part of the work you want to stage.

### commit

> git commit -m "\<message>"

Do you want to save your work? Then commit it. There is no better way to ensure its persistence. Once committed, can not be deleted (unless you do it on purpose 😜).

Example: `git commit -m "this is a commit message"`

### fixup

> git commit --fixup \<commit-id>

I use that one when I'm just fixing a previous commit. Usually, my commits are topic-based, which means each one of them contains business value. When one of them requires to be improved (with something I forgot maybe), I use fixup. In ``

Example: `git commit --fixup 8573jf`

### log

> git log --pretty=oneline

That's a basic one. It shows you the list of commits in the past commit history. The format I use makes the commits appear in a single line ( commit-id + commit-message). Useful to find out the ids of certain commits (something I'm going to need to do a fixup for example).

### rebase

> git rebase -i --autosquash origin/\<branch-name>

That is my favorite one. What can I say? Rebase is my soft point 😄. Here I'm making sure my commit history stays as clean as possible. Rebase basically means make my branch start after the last commit of the origin branch I'm giving you. It's like fast-forwarding in time! `-i` means interactively, and `-autosquash` means all the commits previously tagged with the fixup word will be squashed automatically.

### push

> git push -f -u origin \<branch-name>

That's an unusual one. Here we are doing many things at the same time. Usually, in most companies, you start your development in your project management tool (GitHub, Jira, GitLab, …). There, you create a new branch from a ticket and you start coding.
Here I'm doing the opposite 😝. Once I finish my development and I committed my changes, I do a git push with `-f`; which enforces the push and creates the branch in the origin in case the origin branch doesn't exist, `-u`; which tells git to make my local branch track the origin branch, `origin <branch-name>`; which pushes the local branch content to the origin branch.

### stash

> git stash
> git stash pop

Not as used as the ones before, but has anybody asked you to check on something while you were working on your feature? If so, you encounter yourself in a situation where you are in the middle of your work and you want to check the codebase without your changes. `git stash` basically does that. It stores your current git state in a LIFO-queue (removing all your changes), allowing you to work on something else. Once you want to resume your task, just type `git stash pop` and the last entry of the LIFO-queue will be applied to your current state.

---

## Conclusions

We have seen ten git commands I use every day (several times per day) at work. This article was not intended to be a how-to-git but more a list of useful commands that anybody can review in case they forgot something.

Hopefully, this will help you in your day to day work, and feel free to provide feedback 😊!
Also, I'll leave here the complete list of commands for those of you that just read the Conclusions part 😁 (Don't worry, I do the same sometimes).

```bash
git fetch # F5 for git
git checkout -b "<branch-name>" # checkout new branch
git checkout -b "<branch-name>" "origin/<origin-branch-name>" # checkout new branch and track origin branch
git status # Know the state of your local git repository
git add -p # Stage only the changes you want
git commit -m "<message>" # commit your staged changes
git commit --fixup "<commit-id>" # Fixup a specific commit
git push -f -u origin "<branch-name>" # Push to origin branch + create(if doesn't exist) + track branch
git log --pretty=oneline # git log but more compact
git rebase -i --autosquash "origin/<branch-name>" # Clean your commit history
git stash # Save your current work to deal with something more urgent
git stash pop # Recover your previously saved work to resume it
```
