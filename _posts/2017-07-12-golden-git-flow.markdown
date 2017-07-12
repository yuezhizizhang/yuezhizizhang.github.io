---
layout: post
title:  "Golden Git Flow"
categories: "Git"
---

When I work on Git, usually, I start with creating a private branch:

```
git checkout -b private/fiona
```

Afterwards, I'm able to work on the branch in whatever way I like. I can commit whenever I want and I don't have to mind the messy history. It's because in the end I'm able to squash the commits and rewrite a beautiful message using command:

```
git rebase -i HEAD~3
```

See, I merge the latest 3 commits and squash them into 1. In this way, I'm able to keep the history nice and clean.

After I have finished my work, I'm ready to merge my work into the master branch.

```
git fetch origin
git rebase origin/master
git checkout master
git rebase private/fiona
git push origin master
```

Done.