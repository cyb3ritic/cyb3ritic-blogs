---
title: Git command line for beginners
published: 2024-08-31
description: Article on Git magic through command line interface.
image: ./medias/git_logo.png
tags: [articles, git, github, cli]
category: Article
draft: false
---


# <center>🧙‍♂️ Git Magic: Command Line Edition</center>

## 🔧 Configure Your Git Persona

Before you dive into the Git world, you need to tell Git who you are. It’s like introducing yourself at a party!

Run these spells:
- ```git config --global user.email "You@example.com"```
- ```git config --global user.name "Your Name"```

This sets your identity across all your Git adventures. Want to keep it secret, just for this project? Drop the `--global`!

## 🛠️ Crafting a New Repository

### Step 1: Summon a Project

1. Create a new project directory (your magical workspace).
2. Enter your lair (open the directory).
3. Cast the ancient spell:
   - ```git init```

Boom! You’ve just conjured a new Git repository. ✨

## 📁 Adding Files to Your Spellbook

Adding files isn’t just a flick of the wrist; it requires a command or two.

### Incantations to Add Files:
- ```git add <filename>```
- ```git add *``` (This adds everything, like a magical vacuum cleaner!)

Now that your files are staged, lock them into your spellbook with:
- ```git commit -m "Your magical commit message"```

Congrats, your changes are committed! But hold on, they’re still local.

*Tip: Use ```git status``` to keep tabs on your files, like checking a magical mirror.*

## 🌐 Connecting to a Remote Repository

### ☁️ Choose Your Magical Cloud
Ready to take your project online so fellow wizards can join in? Time to set up a **remote repository**.

Pick your favorite magic cloud service:
- [Github](https://github.com) 🐙
- [Gitlab](https://gitlab.com) 🦊
- [Bitbucket](https://bitbucket.org) 🦈

Or create your own private sanctuary with [Gogs](https://gogs.io) 🏰.

### 🧩 Assemble the Connection
First, make sure your changes are in your local repository with ```git commit```.

Now, link your local work to the magical cloud with:
- ```git remote add origin <server>```

Send your magical artifacts (code) to the cloud:
- ```git push origin master```

✨ Your code is now soaring through the clouds! ✨

## 🕵️‍♂️ Checkout a Remote Repository

### Download the Magic

Want to work on an existing project? Time to clone it to your local spellbook.

### Incantation to Clone:
- ```git clone username@host:/path/to/repository```

You’ll get the magic link from your chosen Git service. If it’s on your local network, the spell is:
- ```git clone /path/to/repository```

### 🔍 Investigate Project History
To see the entire history of your magical project:
- ```git log```

It’s like reading the ancient scrolls of your project’s past!

## 🚨 Undoing Spells Gone Wrong

Made a mistake? Don’t panic! If you haven’t pushed it online yet, you can undo it with:
1) ```git commit --amend```

It’s like saying, “Wait, I meant to add this!” before finalizing the spell.

### Example:
1) ```git commit -m "Initial commit"```
2) ```git add forgotten_file```
3) ```git commit --amend```

If your mistake has already escaped to the cloud, use the time-travel spell:
- ```git reset --hard <commitID>```

But beware! You’ll lose all changes after that commit. 🕰️

## 🌿 Branching: Multiverse of Code

Branches allow you to work on different features simultaneously, like exploring parallel universes. The **master** branch is the main timeline, but you can create as many branches as you like!

### Commands to Navigate the Multiverse:

To see existing branches:
- ```git branch```

To create a new branch called feature_x:
- ```git checkout -b feature_x```

To return to the master timeline:
- ```git checkout master```

To erase a parallel universe (delete a branch):
- ```git branch -d feature_x```

To publish your branch to the cloud:
- ```git push origin <branch>```

To merge your branch with the main timeline:
- ```git merge <branch>```

## 🔄 Syncing and Merging

Working with others? Keep your project up to date with the latest magical spells from your team:
- ```git pull```

If you’re in a branch, mix your spells with:
- ```git merge <branch>```

## 🏷️ Tagging Your Spells

Mark your important commits with a tag for easy identification:
- ```git tag <tag> <commitID>```

It’s like bookmarking the most powerful spells in your spellbook!

## 🌟 Git Stash: A Quick Hideaway

Imagine you're in the middle of casting a spell (coding), but something urgent comes up. You don't want to commit your half-finished spell. No worries! Just stash it away for safekeeping.

### Hideaway Incantation:
- ```git stash```

Your work is safely tucked away, and your workspace is clean again. When you're ready to pick up where you left off:

### Recall the Stash:
- ```git stash pop```

Voila! Your stashed changes are back, just as you left them.

## 🧙‍♂️ Revert a Commit: Undoing with Precision

Sometimes you need to undo a commit but keep your changes safe. Here’s how to roll back without losing anything:

### Revert Spell:
- ```git revert <commitID>```

This creates a new commit that undoes the changes from the specified commit. It’s like rewriting history with a twist!

## 🛡️ Protect Your Master Branch: Create a Backup

Before you make any big changes to the master branch, create a backup branch just in case.

### Backup Branch Spell:
- ```git checkout -b master-backup```

Now, if anything goes wrong, you can always return to this safe point.

## 🧩 Interactive Rebase: Rewriting History

If you’re a perfectionist and want to tidy up your commits, you can rewrite your history with an interactive rebase.

### Rebase Spell:
- ```git rebase -i <commitID>```

This allows you to edit commit messages, squash commits together, or even remove them entirely. Be careful—this is advanced magic!

## 🕹️ Cherry-Picking Commits: Selective Magic

Found a great change in another branch and want to apply it to your current branch without merging everything? Use the cherry-pick spell.

### Cherry-Pick Spell:
- ```git cherry-pick <commitID>```

It’s like plucking just the ripe cherries you want from a different timeline.

## 🚀 Git Bisect: Hunting Down Bugs

Got a bug, but not sure where it came from? Git Bisect is like a detective, helping you find the exact commit that introduced the bug.

### Start the Investigation:
- ```git bisect start```

### Mark the Good and Bad Commits:
- ```git bisect good <commitID>```
- ```git bisect bad <commitID>```

Git will guide you through the history until the culprit is found!

## 🗝️ SSH Keys: A Secure Passage

Tired of typing your password every time you push to a remote repository? Set up SSH keys for a secure and seamless connection.

### Generate the Key:
- ```ssh-keygen -t rsa -b 4096 -C "you@example.com"```

### Add the Key to Your Magical Keychain:
- ```ssh-add ~/.ssh/id_rsa```

### Copy the Key to Your Git Service:
Find your SSH public key and add it to your Git service’s settings. Now you can push without passwords!

---

And there you have it! You’re now equipped with all the essential spells to navigate the vast world of Git. Go forth, and may your coding adventures be ever successful! 🧙‍♀️✨
