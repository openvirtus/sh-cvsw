# CVSW - CVS WRAPPER

An easier interface for managing CVS repositories `cvsw` and scripts
for managing a personal CVS repository.

## Help

cvsw

    Usage: cvsw ...
    
    A wrapper around cvs(1) for easier management.
    
    ... update                     :      Pull changes.
    ... rtag-update TAG            :      Create new branch tag.
    ...  tag-update TAG            :      Create new tag.
    ... add [FILES...]             : [a]  Add files to CVS.
    ... commit MESSAGE             : [c]  Commit changes. (git commit/push)
    ... status-tags                : [st] List tags. (cvs status)
    ... status-branches            : [sb] List sticky tags. (cvs status)
    ... diff [TAG_START] [TAG_END] :      Create a diff.
    ... checkout [TAG]             :      Checkout a tag.
    ... update-merge [TAG]         : [m]  Merge tag in current (add/commit later)
    ... apply PATCHES...           :      Apply patches.
    ... tkrev [install]            :      Install/execute "tkrev" GUI.
    
    All subcommands supports "--help" describing exactly what and how
    they perform the operation.
    
    Environment variables: CVSROOT

rb-clone-cvs-ssh

    Usage: rb-clone-cvs-ssh [-R CVS_SSH_SRCROOT] { -V | ... }
    
    Program for managing a CVS repository in a SSH server.
    
    ... new [MODULE]      : Create new module with "README.md".
    ... ls                : List modules in CVSROOT.
    ... clone [MODULE]    : Clone remote module to CVS_SSH_SRCROOT.
    ... rm [MODULES...]   : Remove modules.
    
    Environment variables: CVS_SSH_{CVSROOT,SRCROOT}

## Usage examples

Create your own CVS repository:

    $ ssh USER@SERVER mkdir -p cvsroot
    $ tee -a ~/.profile <<EOF
    export CVS_SSH_CVSROOT=:ext:USER@SERVER:/home/USER/cvsroot
    export CVS_SSH_SRCROOT=/src/cvs
    EOF
    $ sudo mkdir -p /src/cvs
    $ sudo chown $(whoami):$(whoami) /src/cvs

Create a module and check it out.
    
    $ rb-clone-cvs-ssh new c/hello
    cvs server: Updating .
    ? c/hello
    Directory /home/USER/cvsroot/c added to the repository
    Directory /home/USER/cvsroot/c/hello added to the repository
    cvs server: scheduling file `c/hello/README.md' for addition
    cvs server: use 'cvs commit' to add this file permanently
    cvs commit: Examining .
    cvs commit: Examining c
    cvs commit: Examining c/hello
    RCS file: /home/USER/cvsroot/c/hello/README.md,v
    done
    Checking in c/hello/README.md;
    /home/USER/cvsroot/c/hello/README.md,v  <--  README.md
    initial revision: 1.1
    done
    
    $ rb-clone-cvs-ssh clone c/hello
    Entering /src/cvs ...
    cvs server: Updating c/hello
    U c/hello/README.md
    cvs server: Updating .
    Leaving /src/cvs/c/hello ...
    
    $ cd /src/cvs/c/hello
    
Create a branch, add a file and commit.

    $ vi hello.c
    
    $ cvsw rtag-update MY_BRANCH
    cvs rtag: Tagging c/hello
    ? hello.c
    cvs server: Updating .
    
    $ cvsw add hello.c
    cvs server: scheduling file `hello.c' for addition on branch `MY_BRANCH'
    cvs server: use 'cvs commit' to add this file permanently
    
    $ cvsw commit 'Add hello.c'
    cvs commit: Examining .
    RCS file: /home/USER/cvsroot/c/hello/Attic/hello.c,v
    done
    Checking in hello.c;
    /home/USER/cvsroot/c/hello/Attic/hello.c,v  <--  hello.c
    new revision: 1.1.2.1; previous revision: 1.1
    done
    
Merge branch.

    $ cvsw checkout HEAD
    cvs server: Updating c/hello
    cvs server: c/hello/hello.c is no longer in the repository
    
    $ cvsw update-merge MY_BRANCH
    cvs server: Updating .
    U hello.c
    
    $ cvsw commit 'Merge MY_BRANCH'
    cvs commit: Examining .
    Checking in hello.c;
    /home/USER/cvsroot/c/hello/hello.c,v  <--  hello.c
    new revision: 1.2; previous revision: 1.1
    done

## Collaborating

Feel free to open bug reports and feature/pull requests.

More software like this here:

1. [https://harkadev.com/prj/](https://harkadev.com/prj/)
2. [https://devreal.org](https://devreal.org)
