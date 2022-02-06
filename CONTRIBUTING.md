## Do not use Organizations to Create PRs

Wyvern may modify your PR in the future, to focus on any adjustments, may be a reabase
or small adjustments. If you use an organization for the PR, it prevents us from editing
it, and requires us to manually merge the PR. 

We much prefer to have PRs show as merged, so please do not use repositories
on organizations for PRs.

See <https://github.com/isaacs/github/issues/1681> for more information on the
issue.

## Requirements

To get started with PRing changes, you'll need the following software, most of
which can be obtained in (most) package managers such as `apt` (Debian / Ubuntu;
you will most likely use this for WSL), `homebrew` (macOS / Linux), and more:

- `git` (package `git` everywhere);
- A Java 17 or later JDK (packages vary, use Google/DuckDuckGo/etc.).
  - [Adoptium](https://adoptium.net/) has builds for most operating systems.
  - Paper requires JDK 17 to build, however makes use of Gradle's
    [Toolchains](https://docs.gradle.org/current/userguide/toolchains.html)
    feature to allow building with only JRE 8 or later installed. (Gradle will
    automatically provision JDK 17 for compilation if it cannot find an existing
    install).

## Understanding Patches

Patches (or extensions) are split into different directories 
which target certain parts of the code. These directories are:

- `Wyvern-API` - The API we create for users to develop against. 
- `Wyvern-Server` - Modifications to the base Vanilla Server.

Because the entire structure is based on patches and git, a basic understanding
of how to use git is required. A basic tutorial can be found here:
<https://git-scm.com/docs/gittutorial>.


Add more details on patches

## Adding Patches

Adding patches to Wyvern is simple:

1. Modify `Wyvern-Server` and/or `Wyvern-API` with the appropriate changes;
1. Type `git add .` inside these directories to add your changes;
1. Run `git commit` with the desired patch message;
1. Run `./wyvern rebuildPatches` in the main directory to convert your commit into a new
patch;
1. PR the generated patch file(s) back to this repository.

Your commit will be converted into a patch that you can then PR into Wyvern.

## Modifying Patches

Modifying previous patches is a bit more complex:

### Method 1

This method works by temporarily resetting your `HEAD` to the desired commit to
edit it using `git rebase`.

> ❗ While in the middle of an edit, you will not be able to compile unless you
> *also* reset the opposing module(s) to a related commit. In the API's case,
> you must reset the Server, and reset the API if you're editing the Server.
> Note also that either module _may_ not compile when doing so. This is not
> ideal nor intentional, but it happens. Feel free to fix this in a PR to us!

1. If you have changes you are working on, type `git stash` to store them for
later;
   - You can type `git stash pop` to get them back at any point.
1. Type `git rebase -i base`;
   - It should show something like
   [this](https://gist.github.com/zachbr/21e92993cb99f62ffd7905d7b02f3159) in
   the text editor you get.
   - If your editor does not have a "menu" at the bottom, you're using `vim`.  
   If you don't know how to use `vim` and don't want to
   learn, enter `:q!` and press enter. Before redoing this step, do
   `export EDITOR=nano` for an easier editor to use.
1. Replace `pick` with `edit` for the commit/patch you want to modify, and
"save" the changes;
   - Only do this for **one** commit at a time.
1. Make the changes you want to make to the patch;
1. Type `git add .` to add your changes;
1. Type `git commit --amend` to commit;
   - **Make sure to add `--amend`** or else a new patch will be created.
   - You can also modify the commit message and author here.
1. Type `git rebase --continue` to finish rebasing;
1. Type `./wyvern rebuildPatches` in the root directory;
   - This will modify the appropriate patches based on your commits.
1. PR your modified patch file(s) back to this repository.

### Method 2 - Fixup commits

If you are simply editing a more recent commit or your change is small, simply
making the change at HEAD and then moving the commit after you have tested it
may be easier.

This method has the benefit of being able to compile to test your change without
messing with your HEADs.

#### Manual method

1. Make your change while at HEAD;
1. Make a temporary commit. You don't need to make a message for this;
1. Type `git rebase -i base`, move (cut) your temporary commit and
move it under the line of the patch you wish to modify;
1. Change the `pick` to the appropriate action:
   1. `f`/`fixup`: Merge your changes into the patch without touching the
  message.
   1. `s`/`squash`: Merge your changes into the patch and use your commit message
  and subject.
1. Type `./wyvern rebuildPatches` in the root directory;
   - This will modify the appropriate patches based on your commits.
1. PR your modified patch file(s) back to this repository.

#### Automatic method

1. Make your change while at HEAD;
1. Make a fixup commit. `git commit -a --fixup <hashOfPatchToFix>`;
   - You can also use `--squash` instead of `--fixup` if you want the commit
   message to also be changed.
   - You can get the hash by looking at `git log` or `git blame`; your IDE can
  assist you too.
   - Alternatively, if you only know the name of the patch, you can do
  `git commit -a --fixup "Subject of Patch name"`.
1. Rebase with autosquash: `git rebase --autosquash -i base`.
This will automatically move your fixup commit to the right place, and you just
need to "save" the changes.
1. Type `./wyvern rebuildPatches` in the root directory;
   - This will modify the appropriate patches based on your commits.
1. PR your modified patch file(s) back to this repository.

This CONTRIBUTING document is derived from PaperMC/Paper, and they have credit 
to the majority of the contents of it, minus a few changes we have added to fit Wyvern. This document 
inherits the licenses from Paper, which is Licensed under the GPL v3, same as Wyvern.
You can view more information about this files licensing, and PaperMC/Paper's at
https://github.com/PaperMC/Paper/blob/master/LICENSE.md
If at any point a member of the PaperMC Team would like this document removed,
we will happily comply.