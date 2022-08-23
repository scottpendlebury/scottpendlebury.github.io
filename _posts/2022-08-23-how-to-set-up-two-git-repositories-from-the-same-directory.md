---
title: How to set up two Git repositories from the same directory
description: Check the third solution
---

We had a need recently to setup two remote repositories to version control the content in one local repository. One of the remotes was to be used to push everything from the local repository, and the second remote was to be used to push a sub-set of files in the local repositories content into. Additionally, since we were working with a vendor technology we didn’t want to radically change anything with the local repository.

---

### Proposed Solution 1

Setup one remote to push everything into and copy the selected content into a different directory that is under git version control.

##### Problem

Since the new directory would effectively be downstream from the initial directory it would require manual or automated intervention to keep the new directory up to date with changes as they happen.

### Proposed Solution 2

Set one remote to push everything into and symlink the selected content from a different directory that is under git version control.

##### Problem

I wasn’t sure how Git would handle seeing a symlink, my naive hope was that it would follow the links to the data but after some reading I found that, of course, it would [put the symlink itself under version control](https://stackoverflow.com/a/18791647){:target="_blank"}, not that data that it points to.

### Proposed Solution 3

Create two Git repositories in the same directory and point each one to a different remote.

Since we were working in a directory that had an existing .git directory and didn’t want to interfere with it, we created a ... to be continued

