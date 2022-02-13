

# `resources`

This repo contains various scripts that are used by a group of projects. These include such things
as ‘start’, ’push’, ‘pull’, and a generic make file.

Built into the scripts are assumptions on the structure of the directory used for
holding a project.


## How To Install the ‘resources’ repo
--------

1. find a or make a place to put all of the projects you are working on.  These projects might be in
  different languages, etc. 

  ```
    > mkdir ~/repos
  ```

2. install the system scripts used by the scripts in this repo


  ```
    > cd ~/repos
    > git clone git@github.com:Thomas-Walker-Lynch/usr-local-bin
  ```

  Follow the directions from the usr-local-bin repo for installing ‘home’ and ‘Z’.  There is not really much to it.

  ‘Z’ is used for timestamps.  ‘home’ returns the home directory from /etc/passwd.

3. clone this repo to get the shared resources

  ```
    > cd ~/repos
    > git clone git@github.com:Thomas-Walker-Lynch/resources.git
  ```

4. set the executables PATH

I added the following to my `.bashrc`:

```
 export PATH=~/bin:~/repos/resources:"$PATH"

``

<!-- end of list -->

That is it for installing this repo.

## How to install a project that has sub-modules.

1. install the project

  Suppose the project with submodules is called <project>_ensemble

  ```
    > cd ~/repos
    > git clone git@github.com/<user>/<project>_ensemble.git
    > mv <project>_ensemble <project>_master 
    > cd <project>_master
    > 
  ```

  Note in the line that moves the cloned repo directory to `<project>_master`, you might
  use a different suffix than `master`.  Conventionally the suffix is the branch to be
  checked out and worked on, but the scripts do not care what it is set to. Inside the
  scripts this suffix is called the project ‘version’.

2. for submodules that have not yet been added:

  ```
    > git submodule add https://github.com/../<resource_project>
    > git submodule add https://github.com/../<resource_project>
    ...

  ```
   Etc. for the other modules


3. if a submodule is empty, then do the following:

  ```
    > git submodule init
    > git submodule update
  ```

   Submodules directories will be empty when the `--recursive` switch is not provided with
   the clone. Actually, I prefer not to use `--recursive` and then to follow up with an
   `init` and `update` so that it is easier to tell what caused errors.

<!-- end of list -->

## Generally About Project Security

1. audit source files and legible scripts

  Watch what git installs.  For new things that are source code or scripts, audit those.

2. PATH

   Do not have the repo in your execution PATH.

2. binary executbles

  It is best to not have binary executables in the repo.  Instead, build anything executable
  from local audited source code.

  It is a common error for co-developers to compile and get an executable, and then not
  add that to the .gitignore, so it poisons the repo.  Watch your pulls and clones,
  and remove those from the repo, and complain about them.

  Edit your .gitignore so that you will not be the bad guy.

  If you must run an executable from a repo, then you surely trust the source.  Make
  sure there is a cryptographic signature provided for checking it.  Make sure the
  permissions and user and group membership are correct. Consider running it in a
  container.
  
3. .gitignore does not help for pulls

  ‘.gitignore’ does not apply to pulled content.  If another developer failed to
  ignore executables on a push, you will get them on a pull even if they are
  listed in ‘.gitignore’

4. check for the .gitignore and audit it, add to it

 Conversely, we do not want to add clutter to the repo ourselves, so you will
 want to have a `.gitignore`.  This might be part of the project, check for it
 after cloning a new repo.

 Typical .gitignore files:

  ```
      env/
      tmp/
      .*
      !.gitignore
      *~
  ```

  For a C or C++ project home directory we will also ignore various intermediate files,

  ```
      tmp/
      .*
      !.gitignore
      *~
      *.o
      *.i
      *.s
      a.out
  ```


  For a python project:
  ```
      tmp/
      .*
      !.gitignore
      __pycache__/
      **/*.pyc
  ```
 
    And for a django project:

  ```
      tmp/
      .*
      !.gitignore
      __pycache__/
      **/*.pyc
      manage.py
      **/migrations
      .vscode
  ```

5. no hidden files

 Git already messes this up with their ‘.git’ directory and ‘.gitignore’.  Don't
 add any more.

 By convention files ending in a ‘~’ character are to be ignored.

 Consider adding a tmp directory where its contents are ignored.

<!-- end of list -->

## Some of the scripts found in this repo

### start

  This replaces any other script you might be accustom to for entering a ‘virtual environment’.
  (Note ‘virtual’ is a misnomer.  An environment is just what it is.  What we are doing is
  changing the environment.  There is nothing ‘virtual’ about it.)

  This following command will open a new shell with the environment setup for the project.  The new shell will source
  your user directory `.bashrc` file, and will source the project environment's `env/bin/init.sh` file.


  ```
     start <project> <version>
  ```

  As noted above `<version>` is typically the name of the branch that will be expanded out
  in the project home directory.

  So for my `ws4` project:

  ```
     start ws4
  ```

  The `<version>` defaults to ‘master’.  If you are making use of my .bashrc prompt,
  after `start` runs you will see this prompt:
  
  ```
      2020-12-01T14:56:31Z [ws4_master]
      thomas@localhost§~/projects/ws4_master§
      > 
  ```
   
  On the first line, the time shown is UTC in standard iso8601 format. This comes from the ‘/usr/bin/Z’ script.  We use
  time stamps of this form for transcripts and logs.

  On the same line following the time, in square brackets you will see the name of the project.

  On the second line we have the user name, machine name, and current working directory.

  Then on the third line we have the prompt, `>`. Anything you type after the prompt is
  taken as the command for the shell.

### pull and push

  These are short cuts for the git repo commands.  It is best to use these as it allows us to wrap up
  other work to be done along with the git repo command.  

  When you have made changes in the project home directory and want to push them back to 
  the repo, first pull on the work from other team members:

  ```
     > pull
  ```

  You will then have to work out any conflicts if any, as for any git pull.


  Then push your work back to the repo:

  ```
     > push
  ```

  Those scripts do the intermediate staging, commit, and push/pull, both for the project
  and the project environment.  If things go wrong, you will have to read through the
  transcripts.  Sometimes the scripts may be played again, sometime you have to drop back
  and use `git` directly.
  

## General info and concepts

### What is a ‘project’

The term ‘project’ occurs repeatedly in this document and generally when we talk about code, so it is best to start by
nailing that term down.  Generally speaking, a ‘project’ has a well defined final product, along with a list resource
needs, tasks, task dependencies, and a tasks schedule.  It is the job of the team members to execute said tasks.

In project management parlance, a group of related projects is called a ‘program’. Well that sure is an unfortunate
choice of terminology for us CS people, so instead we will call a group of related projects a ‘project ensemble’.  A
project ensemble itself is also a kind of project, where the tasks are the component projects.  Hence we have a
recursive structure. CS people like recursive structures ;-)

Projects are held in git repositories, as project ensembles are also projects, they are also held in git
repositories. When we clone a project ensemble we will find other git repositories have been expanded inside the
directory tree.  Those are for the component projects.  In git speak we call these component project directory trees
‘submodules’.

When a project ensemble is expanded out, we end up with a directory tree structure where project resources, tools, and
work products are stored.


### Where project code goes
--------

On a typical project we will have three distinct types of code:

1. the application source code
2. the libraries and other resources the application makes use of
3. the tools used for building the source code

<!-- end of list -->

Let's give these code types short names:

1. source
2. resources
3. tools

<!-- end of list -->

We have various places where we might put code that we need in a program:

1. in the system
2. in the developer's user directory
3. in a project ensemble
4. in a project's home directory.

<!-- end of list -->

We shorten this list of places to:

1. system
2. user
3. ensemble
4. project

<!-- end of list -->

Now combining our code and locations into one list:

1. source
   1. project
2. resources
   1. system
   2. user
   3. ensemble
3. tools
   1. system
   2. user
   3. ensemble

<!-- end of list -->
   
So there is only one place we will find the application source code that we are
developing, and that is under the project directory. Resources that we may need in
order to compile our code will be found either in the system, the user directory, or in
the ensemble. The same can be said for the tools we will use.

This is what my home directory looks like:

```
/home/thomas/
  bin/
  Desktop/
  Documents/
  Downloads/
  repos/
    chessgame/
    resources/  <--- resources for all projects - expand this repo here
        LICENSE
        README.md
        makefile-cc
        pull
        push
        rm_tilda_files_tree
        start
    subu/ 
    tm/
    ws4_master/   <--- an ensemble directory
      LICENSE
      README.md
      env/  <--- resources specific to the ensemble (wish this was called ‘resources’
          bin/
          lib/
          include/
      tmp/
      uWebSockets/ <--- resource project directory
      ws4/   <--- target project directory
```

Now looking under my `projects` directory, and expanding out `ws4_master`:


```
   ws4_master/
     LICENSE
     README.md
     env/
        bin/
        include/
        lib/
     tmp/
     uWebSockets/
     ws4/
```

`ws4_master` is a project ensemble. The component projects include `uWebSockets` and
`ws4`. This project ensemble also comes from a git repo.

`ws4_master/ws4` is the home directory for the project this team is actively working on.
We know it is the active project because it has the same name as the ensemble prefix.
Whereas `ws4_master/uWebSockets` is someone else's github project. There are other people
working that, but it is not us, and it is being developed in a different
environment. Rather we are just making use of the work product of that project.

Also inside of `ws4_master` we have a directory called `env`. Frankly, I don't like the
name.  I have toyed with calling it `project-share`, but `env` is the name that Python and others
expect.  Perhaps make it a symbol link?  This directory is used to hold project specific
resources and tools.  Note that the contents of `env` are *not* pushed to the repo. This
means that custom edits you make to scripts will not be backed up to the repo.  I also do
not like `env` because it is not pushed to the repo, but it might be *pulled* from it.
`.gitignore` does not affect pulls.  This is a security hazard.

## Modifying your resources

If you need to modify a script in the `resources` project and do not want to distribute the
changes to the team, make a copy of the script in your own bin directory and modify it
there.  note `~/bin` appears before `~/projects/share`, so the changed script will get
picked up instead of the `project-share` version. 

For project specific scripts put them in the project environment `env/bin`.  Be sure to modify the default
`env/bin/init.sh` script to add `env/bin` to the executables search path.  Also note, that the contents of the `env`
directory do not get pushed back to the repo, so you will need to make copies of your `env` stuff if you want to keep
them.


### Repo and Directory Naming

All my repos for project ensembles have a `_ensemble` suffix.  Hence they will clone
into directories named `<project>_ensemble`.  After cloning I rename the directory to
`<project>_<version>`, where `<version>` is typically the same name as the branch
that is most commonly checked out in the directory.

Here is another example listing of a projects directory:

```
> cd ~/repos
> ls
    customer_gateway_master
    customer_gateway_v1.0
    customer_gateway_v2.0
```

This is how the `customer_gateway_master` project environment directory was made:

```
> cd ~/repos
> git clone --recursive --jobs 8 git@github.com:Reasoning-Technology/customer_gateway_ensemble.git
> mv customer_gateway_ensemble customer_gateway_master

```
When downloading a `<project>_ensemble` repo, we can expand the submodules at the same time
by including the --recursive switch, as was shown above.  If the `--recursive` switch
is not given, the submodules will have to be initialized and updated.

The second directory was then created with the commands:

```
> git clone --recursive --jobs 8 git@github.com:Reasoning-Technology/customer_gateway_ensemble.git
> mv customer_gateway_ensemble customer_gateway_v1.0
> cd customer_gateway_v1.0
> git checkout v1.0

```

So you ask why do we need more than one directory for the same repo? Well in this case I
am running a web server against the v2.0 branch, and it needs to see the files the v2.0.
The v1.0 directory was the one that was formally being served. There are no servers
pointed at it now, so I should probably delete it.  If I ever need v1.0 again I can always
check it out. 


### Git Modules and Submodules

Cloning a `git` repository produces a directory tree, which in git speak is
apparently called a `module`.

We may `cd` into a module and clone another module, this will be called a ‘submodule’.  A
submodule clone operation requires a special command so that the module will know it is
there:

```
git submodule add <repo>
```

We might do this because our project depends on other projects, and those other projects
have their own git repositories, or because we are developing more than one project
together with one shared environment.

We then use the submodule just like we would use any other git module.  I.e. after we make
changes we must add the changes, then commit them, and then push the submodule.

The parent module only sees the submodule changes when there is a commit in the
submodule.  Hence, after there is a commit in a submodule, we must go up to the module, and
then add the submodule, commit, then push the module.

We truly have two layers, and we have to maintain them individually. Luckily we have some
scripts so that we don't to type stuff twice. In the `project-share` project there are two
scripts, one called `push` the other called `pull`.  When we run the `push` script it goes
into the project home and does an add, commit, and push.  It then goes up to the ensemble
directory and `git add`s the project submodule, commits the change, and then pushes. Finally
it pops directory back to the project.  The current `pull` script pulls down all the
submodules and the ensemble.


