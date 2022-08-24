---
title: How to set up two Git repositories from the same directory
---

<img src="/assets/img/52156992129_9aaab4beeb_o.jpg" class="rounded mx-auto d-block img-fluid" >

---
### Introduction

We recently needed to setup two remote repositories to version control the content in one local repository. One of the remotes was to be used to push _everything_ from the local repository, and the second remote was to be used to push a _sub-set_ of files in the local repositories content into. Additionally, since we were working with a vendor technology we didn’t want to radically change anything with the local repository.

I had a couple of initial ideas that went nowhere before discovering the best solution already exits in Git.

---

### First Proposed Solution

Setup one remote to push everything into and copy the selected content into a different directory that's also under Git version control.

##### Problem...

Since the new directory would effectively be downstream from the initial directory it would require manual or automated intervention to keep the new directory up to date with changes as they happen. That wouldn't be the end of the world, but there's surely a better solution.

### Second Proposed Solution

Set one remote to push everything into and symlink the selected content from a different directory that is under git version control.

##### Problem...

I wasn’t sure how Git would handle seeing a symlink, my naive hope was that it would follow the links to the data but after some reading I found that, of course, it would [put the symlink itself under version control](https://stackoverflow.com/a/18791647){:target="_blank"}, not that data that it points to.

### Actual Solution

Have two Git repositories in the same directory and point each one to a different remote.

This is the solution that we used and the steps to recreate it are as follows.

1. Initialise a Git repository in a temporary directory first.

    >`mkdir temp && cd temp`
    >
    >`git init`

2. The newly initialised Git repository can then be moved into the same project directory already under version control and given a new directory name from the default `.git`. However, this step is optional as files in one directory can be put under version control using a Git repository that exists elsewhere, the parameter used for that is shown in the next step.

    >`mv .git ../project/.gittwo`

3. If your use case is the same as ours, you'll only want to add selected content from the project directory into the second Git repository, this is done using the `--git-dir` parameter. As mentioned in the previous step, when using the `--git-dir` parameter a path to a Git repository can be pointed to anywhere on the system, but having the repository in the same directory keeps the command nice and short.

    >`git --git-dir=.gittwo add .\chosen_content.json` 

4. Working with the second Git directory is exactly the same as it would be without using the `--git-dir` parameter. To complete our setup we need to commit the change, add the new remote and push the change, all while remembering to use the `--git-dir` parameter.

    >`git --git-dir=.gittwo commit -m "Initial commit"`
    >
    >`git --git-dir=.gittwo remote add chosen_content_repo https://[any_value]:[personal_access_token]@example.com/chosen_content.git`
    >
    > **_NOTE:_** I'm using an instance of GitLab Enterprise Edition for the remote and a [project access token for authentication](https://docs.gitlab.com/ee/user/project/settings/project_access_tokens.html){:target="_blank"}, so authentication in the URL is done using "any non-blank value as a username" and "the project access token as the password".
    >
    >`git --git-dir=.gittwo push --set-upstream chosen_content_repo main`
    >


5. Now you're all set to push changes as you would normally with the existing Git repository and `add`, `commit` and `push` changes to the second Git repository and second remote. 

    A simple script scheduled by cron to make occasional commits and pushes to the second remote would keep the files added to this repository up to date as they're worked on.

---

The featured image is [Penguin Nests](https://www.flickr.com/photos/davidstanleytravel/52156992129/in/photolist-2nsWdCZ-2nuChDW-2ntY5AP-2nCGeM1-2nxHbSt-2kKPLwa-2nCuh3M-nFYwha-2nw4JyR-2nD43em-2dhx1p4-XbYyaQ-Q2iYfA-MMMVdp-CJ7nfg-2hnTwih-2hzbuRp-5PA87j-2nu3TcR-2iSQTHm-2ntyRmt-2nuW4aP-2nx21Ta-2nx1oFT-2ny7jeP-2eUUV5e-2ntCsW8-2nvpnPP-2iTWHCx-2ny3TY9-2nwMMs1-89zbeu-2mbNbab-2nvjzBt-2nxg5SA-2ntaCz9-2nwsRzU-2nDmdgH-2kevj4w-21Xpc5g-2mG29yx-2kChA2L-2nvkU2y-VBAc3Q-2nxA7yb-24bJKrS-oQb7wc-GbMrTG-2nxeUfm-2nAQw6o){:target="_blank"}
