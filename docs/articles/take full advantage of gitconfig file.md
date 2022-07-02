---
layout: default
title: Take full advantage of gitconfig file
parent: Articles
nav_order: 2
has_children: false
---

![main.png](images/main.png)

## TLDR

For those of you that are always in a hurry 🙂, checkout this piece of .gitconfig file I use to speed up my Git workflow.

```bash
# omitted
[alias]
    new-branch = "!f(){ \
        git stash; \
        git checkout main; \
        git fetch; \
        git reset --hard origin/main; \
        git checkout -b $1; \
        git stash pop; \
        }; f"
    committ = "!f(){ \
        git add .; \
        git commit -m \"$1: $(git branch --show-current) $2\"; \
        }; f"
    committ-ticket = "!f(){ \
        git add .; \
        git commit -m \"$1: $2 $3\"; \
        }; f"
    fixupp = "!f(){ \
        git add .; \
        git commit --fixup \"$(git rev-parse --short HEAD)\"; \
        git fetch; \
        export GIT_EDITOR=true; \
        git rebase -i --autosquash origin/main; \
        }; f"
    fixupp-sha = "!f(){ \
        git add .; \
        git commit --fixup \"$1\"; \
        git fetch; \
        export GIT_EDITOR=true; \
        git rebase -i --autosquash origin/main; \
        }; f"
    rb = "!f(){ \
        git fetch; \
        git rebase -i --autosquash origin/main; \
        }; f"
    push-up = "!f(){ \
        git push -f -u origin \"$(git branch --show-current)\"; \
        }; f"
    resett = "!f(){ \
        git fetch; \
        git reset --hard origin/main; \
        }; f"
    search = "!f(){ \
        git log --graph --abbrev-commit --decorate --oneline --all -S \"$1\"; \
        }; f"
    l = log origin/main.. --graph --abbrev-commit --decorate --oneline
# omitted
```

Do you have 5 minutes? If so, let's do this together 🦾!

## Introduction

As a developer, I find myself using Git all day (dozens of times a day in fact) to manage the code my team and I have in several repositories we own.
If you read my previous articles, you'll realize I'm quite a fan of Git but, most importantly, I'm obsessed with automation. Here, I'll present you with a way to smooth your Git workflow. I'll present a series of scenarios and how I solve them with Git. Then, I'll show a way to encapsulate the commands in Git and create aliases (like terminal commands but for Git).

### Checkout a new branch

This is the most common thing I do. I'm about to start coding in a ticket with code XYZ-1234 and I want to create a new branch from main branch. The Git commands I would do are:

```bash
git stash; # in case I already started coding
git checkout main; # I move to main branch
git fetch; # A refresh is always helpful
git reset --hard origin/main; # A strong way to sync your local develop
git checkout -b XYZ-1234; # create the branch from the ticket in JIRA
git stash pop; # put back your code in place to keep coding
```

So, six commands that I need to type every time I'm starting a new feature? No way! Let's put this in a bash function inside our .gitconfig file under the [alias] section. Like this:

```bash
[alias]
    new-branch = "!f(){ \
        git stash; \
        git checkout main; \
        git fetch; \
        git reset --hard origin/main; \
        git checkout -b $1; \
        git stash pop; \
        }; f"
```

As you can see, I made the bash function receive the name of the branch as argument.

That way, next time I want to create a branch from main called `XYZ-1234`, I just type in my terminal:

```bash
git new-branch XYZ-1234
```

### Commit

I really like [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/). They are a standard that allows automating many things and it is so simple to follow. The only thing you need to add is a prefix to your commit messages. Also, I'd like to bring extra flavour to my commits by automatically providing custom prefixes and the JIRA ticket I'm working on with this couple of git aliases:

```bash
committ = "!f(){ \
    git add .; \
    git commit -m \"$1: $(git branch --show-current) $2\"; \
    }; f"
committ-ticket = "!f(){ \
    git add .; \
    git commit -m \"$1: $2 $3\"; \
    }; f"
```

you can see both aliases in action in this example that produced the commits you can see in the [log section](#nice-git-log).

```bash
git committ feat "created important file"
git committ-ticket fix(tfm) ABC-4321 "modify throttling configuration"
git committ break(src) "deprecate endpoint v1"
```

### Fixing up previous commits

Sometimes, after I created a bunch of commits, I realized I forgot to do some changes in one of them. When that happens, I used to do the following commands in my terminal:

```bash
git add .; # stage the change
git commit --fixup 549hgr; # fixup the commit by sha
git fetch; # refresh
git rebase --autosquash origin/main; # rebase with respect the origin/main branch with autosquash enabled
```

The autosquashed simply includes all the 'fixups' in the commits that belong to. Then, I created this couple of aliases:

```bash
fixupp = "!f(){ \
        git add .; \
        git commit --fixup \"$(git rev-parse --short HEAD)\"; \
        git fetch; \
        git rebase --autosquash origin/main; \
        }; f"
fixupp-sha = "!f(){ \
        git add .; \
        git commit --fixup \"$1\"; \
        git fetch; \
        git rebase --autosquash origin/main; \
        }; f"
```

where the first one fixups the previous commit and the second one receives the commit's sha to fixup.

### Nice git log

Sometimes, I need to track my git history to find inconsistencies. Undesired merges, bad format on a commit message, ... you name it. The classic git log sometimes falls short to properly see what is going on. That is why I have this alias in my .gitconfig.

```bash
l = log origin/main.. --graph --abbrev-commit --decorate --oneline
```

Which provides:

- the commits that I did in my branch with respect the main branch,
- in a graph form,
- with abbreviated commit id (sha),
- decorated in a single line.

In the next screenshot, you'll see the result of `git l`. Obviously, this command brings much more to the table 😍.

![git-l](images/git%20l.png)

### Working in a team

Either when I'm working with a colleague in the same commit, one of my QA teammates is testing my code or my PO asked me to do the code in the commit, I like to make it clear in the same commit message. This is done using a great feature of Git that is Trailers.

The best way to understand it is with an example. Imaging I did, together with a colleague developer, a commit with the message `feat: add important config file` that fixes a bug requested by my product owner and has been tested by a QA. Using those simple git commands in my terminal

```bash
git co-authored-by "my-colleague"
git requested-by "my-po"
git tested-by "my-friend-qa"
```

I can transform the commit from this

```bash
feat: add important config file
```

to this

```bash
feat: add important config file

Co-authored-by: my-colleague
Requested-by: my-po
Tested-by: my-friend-qa
```

## Conclusions

We've seen several scenarios in which automation brings a whole new level to our Git experience and how the .gitconfig file helps us achieve it by storing any bash function we want. Feedback is welcomed, and enjoy your coding! 👏😃
