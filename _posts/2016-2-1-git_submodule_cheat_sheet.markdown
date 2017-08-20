---
title:  "Git submodules cheat sheet"
description: Git submodules cheat sheet
---

출처 : [Git submodules cheat sheet]

프로젝트안에서 git submodule 를 다룰때 사용하는 명령어들

Note: run these from the top level of your repo.

Clone a repo with submodules:

	$ git clone git@bitbucket.org:akrabat/dotvim.git .vim
	$ git submodule update --init

View status of all submodules:

	$ git submodule status
	
Update submodules after switching branches:

	$ git submodule update
	
Add a submodule:

	$ git submodule add git://github.com/tpope/vim-sensible.git bundle/vim-sensible
    
Update all submodules to latest remote version

    $ git submodule update --remote --merge
    $ git commit -m "Update submodules"
    
Update a specific submodule to the latest version (explicit method):

    cd bundle/vim-sensible
    git pull origin master
    cd ../..
    git add bundle/vim-sensible
    git commit -m "update vim-sensible"
    
Remove a submodule:

    edit .gitmodules and remove this submodule's section
    $ git rm --cached bundle/vim-sensible
    $ rm -rf bundle/vim-sensible
    $ git commit -m "Remove vim-sensible"
    
or

    git submodule deinit bundle/vim-sensible    
    git rm bundle/vim-sensible
    git commit -m "Remove vim-sensible"

[Git submodules cheat sheet]: https://akrabat.com/git-submodules-cheat-sheet/