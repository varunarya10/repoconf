repoconf
========

Repo configuration repository for autobuild

Contains a configuration file that is used
by [repo](https://code.google.com/p/git-repo/) to install things.

## Installing

Installing repo on Ubuntu 14.04

    sudo apt-get install openjdk-7-jdk
    sudo apt-get install bison g++-multilib git gperf libxml2-utils make zlib1g-dev:i386 zip
    mkdir ~/bin
    PATH=~/bin:$PATH
    curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
    chmod a+x ~/bin/repo

You will also need some dev dev tools to create packages and packge repos:

    apt-get install dpkg-dev devscripts sbuild

NOTE: I have no idea what the actual install steps are, or if there are
other deps. I am really just guessing and trying to provice docs b/c there
are no docs.

### Using Repo

Make a directory to install your repo in:

   mkdir repodir

Change to working directory and use repo to install repoconf repo:

   cd repodir
   repo init -u git@github.com:JioCloud/repoconf

The `-b` option can be used to select a branch to use from the repo:

   cd repodir
  repo init -u git@github.com:bodepd/repoconf -b mytestbranch

Next sync the repo

   repo sync

Now run the script that will generate the package:

    pushd repodir
    bash debian/sync-repo.sh build
    popd

Now run sbuild (you may need to escalate to sudo privs)

    sbuild -d trusty -A rjil-cicd_2014.2.179ubuntu1.dsc

When sbuild finsihes, it will generate a tons of debs into the top level dir.

create a new directory to serve as your package repo:

    mkdir new_repo

copy all of the debs there:

    cp *.deb new_repo

Now create the repo:

    pushd new_repo
    apt-ftparchive packages . > Packages
    popd

And just for run, tar it up:

    tar -cvzf new_repo.tgz new_repo

Now you are ready to do something with it!

## Configuring packages to install

The entire purpose of this package is to build packages and populate
them into a repo.

This is accomplished by configuration split across two repos:

### repoconf

Repoconf simply holds the file: default.xml which is used to specify upstream
remotes that should be pulled in.

### autobuild

One of the repos that repp pulls in from default.xml is called autobuild. This
repo contains the build script *debian/sync-repo.sh*. It also seems to contain
some other files that have some purpose that needs to be documented as well.

More information on autobuild can be found at: (https://github.com/JioCloud/jiocloud-docs/blob/master/build_system.md)
