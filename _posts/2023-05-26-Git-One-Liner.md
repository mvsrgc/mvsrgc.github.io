---
title: My favorite Git one-liner
date: 2023-05-26 00:00:00
categories: [Tools, Git]
tags: [git, bash]
---

# Behold...

This is a great snippet to add to your toolbox when you're working on solo branches.
It's useful when you make a typo or mistake and you want to fix it without having to modify the git message, or create a new commit, or cleanup your git history.

This one-liner stages your changes, amends the last commit while preserving the commit message, and then performs a force push (make sure you are the sole developer on the branch since force pushing overwrites history). Voilà! You're now able to correct any typos effortlessly and quickly fix small mistakes.

I find this particularly valuable when working on CI/CD improvements, as it enables quick iteration and testing. When making changes and repeatedly checking the pipeline for expected outcomes, this command eliminates the need for multiple commits.

I also used it multiple times while configuring this blog, since I wanted to keep a single "First commit" or "First post" commit while configuring the blog and writing the post. As you can see, there's many use cases where this command can be useful.

```sh
git add . && git commit --amend --no-edit && git push -f
```

Add it to your .bashrc:
```sh
echo 'alias gfix="git add . && git commit --amend --no-edit && git push -f"' >> ~/.bashrc
source ~/.bashrc
```
