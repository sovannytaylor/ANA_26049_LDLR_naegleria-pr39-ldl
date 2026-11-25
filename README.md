# ANA-XX_brief-description

*Advice for use*: This repository uses core scripts managed in a separate repository ([punctalyze](https://github.com/ocarmo/punctalyze)) to promote modularity and code reuse across projects in the Boeynaems lab. Each experiment uses a dedicated analysis repository containing a Git submodule that points to a personal fork of the shared punctalyze pipeline. This structure allows users to develop and track bespoke analysis workflows while contributing reusable improvements back to the shared pipeline.

```
boey-lab/punctalyze                  # Shared core pipeline (upstream)
        |
        └── YOUR_USERNAME/punctalyze # Personal fork (origin)
                    |
                    ├── main         # Shared/core development
                    |
                    └── dev_branch   # Experiment-specific development


boey-lab/boey-analysis-template      # Template ('use this template' on GitHub) 
        |      
        └── ANA-XX_analysis_repo     # Personal analysis repo cloned
                    |
                    └── punctalyze (submodule)        # Follow checklist for submodule install 
```
Important: A submodule points to a specific commit of punctalyze. Updating the upstream repository or your fork does not automatically update the submodule in existing experiment repositories.

The checklist below outlines the steps to initiate an experiment repository in a tidy manner and add the analysis submodule. 

Please **check off** the tick boxes with an 'x' as you go. This repository assumes elementary knowledge of python and git.

### If you have not already forked the punctalyze repository:
- [ ] fork the core pipeline if you have not already
        visit the [punctalyze](https://github.com/ocarmo/punctalyze) and click **"Fork"**
        this gives you a personal copy at `https://github.com/YOUR_USERNAME/punctalyze`
- [ ] clone your fork locally by clicking **"<>Code"** at the top right of the GitHub page, then **"Open with GitHub Desktop"**
- [ ] select your local path and open the repo by clicking the **"open in VS Code"** option
- [ ] continue to the checklist below

### If you already forked punctalyze: make your own image analysis repo
- [ ] click the **"Use this template"** button at the top right of this template repository
- [ ] name your project and click **"Create repository from template"**
- [ ] clone onto your local device
- [ ] open this repository (which you just cloned) in VS Code or your editor of choice
- [ ] uncomment raw_data folders in gitignore 
- [ ] delete placeholder files raw_data folders
- [ ] import the editable analysis submodule in vscode terminal
~~~ 
git submodule add https://github.com/YOUR_USERNAME/punctalyze.git punctalyze 
~~~
^^^ this adds your forked 'punctalyze' pipeline to this experiment repository
~~~ 
git submodule add -b branch_name https://github.com/YOUR_USERNAME/punctalyze punctalyze 
~~~
^^^^ this adds your forked 'punctalyze' pipeline to this experiment repository, specifically the branch specified with '-b <branch_name>'

- [ ] navigate to the submodule and add a new branch unique to this analysis (if you don't already have one)
~~~
cd punctalyze
git checkout -b dev_branch_name
~~~
- [ ] update *header* at top of README.md and *experiment details* below
- [ ] upload raw data, or update the input_path in ```1_initial_cleanup.py```

## Experiment details

**Purpose**: 
Detecting and analyzing intracellular puncta from light micrographs

**Cell Type**: 
Neuro2a cells

**Instrument/techniques**: 
LSM900, experiment block set up, 10% excitation for 40.51 sec, acquisitions at mCh 0.6% 600V.

**Data produced:** 
See results folder

**Analysis:** 
- scrape data from online if necessary
- initial wrangling of images into numpy arrays
- cell segmentation
- segmentation validation
- detecting puncta (foci of high-intensity pixels) and extracting feature information per punctum
- averaging puncta feature information per cell
- plotting


## Making contributions to punctalyze

Improvements developed during an experiment (e.g., faster batch processing, new segmentation methods, or better plotting) can be contributed to the shared [boey-lab/punctalyze](https://github.com/boey-lab/punctalyze) pipeline.

### Contribution workflow

```text
YOUR_USERNAME/punctalyze
    |
    └── dev_branch (experiment-specific development)
           |
           | Merge into fork's main
           v
YOUR_USERNAME/punctalyze:main
           |
           | Pull request (PR)
           v
boey-lab/punctalyze:main (shared pipeline)
```

### Checklist: contribute your changes

Run these commands from your standalone `punctalyze` clone, not the experiment repository.

**Save your development work**

- [ ] Commit and push your changes to your fork.

```bash
git switch dev_branch
git status
git add .
git commit -m "Describe your improvements"
git push -u origin dev_branch
```

Skip the commit if your working tree is already clean and all changes are committed.

**Update your local main**

- [ ] Verify your remotes with `git remote -v`. `origin` should be your fork; `upstream` should be `boey-lab/punctalyze`.
- [ ] If `upstream` is missing, add it:

```bash
git remote add upstream https://github.com/boey-lab/punctalyze.git
```

- [ ] Fetch and merge the latest upstream changes into `main`.

```bash
git fetch origin
git fetch upstream
git switch main
git pull --ff-only origin main
git merge upstream/main
```

**Merge and test your development branch**

- [ ] Merge `main` into your development branch to incorporate upstream changes.
- [ ] Resolve any conflicts in VS Code and test your code.
- [ ] Merge the development branch into your fork's `main` and push.

```bash
git switch dev_branch
git merge main

# After resolving conflicts and testing:
git switch main
git merge --no-ff dev_branch
git push origin main
```

**Important:** Only merge the entire development branch if all its changes belong in the shared pipeline. For experiment-specific branches, create a clean contribution branch from `upstream/main` and transfer only reusable improvements (e.g., using `git cherry-pick`).

**Submit a pull request**

- [ ] Go to [boey-lab/punctalyze](https://github.com/boey-lab/punctalyze/pulls) → **New pull request** → **compare across forks**.
- [ ] Set **base** to `boey-lab/punctalyze:main`.
- [ ] Set **compare** to `YOUR_USERNAME/punctalyze:main` (or your contribution branch).
- [ ] Review **Files changed** for unintended or experiment-specific modifications.
- [ ] Describe your changes, testing, and any compatibility concerns.
- [ ] Click **Create pull request**.

A lab maintainer can then review and merge your contribution.

## Synchronizing your fork with the shared pipeline

After your PR is merged, or whenever you want to incorporate upstream improvements:

```text
boey-lab/punctalyze:main
           |
           | Fetch and merge
           v
YOUR_USERNAME/punctalyze:main
           |
           | Push
           v
Your fork on GitHub
```

### Checklist: sync your fork

- [ ] Open your standalone `punctalyze` clone.
- [ ] Run:

```bash
git switch main
git fetch origin
git fetch upstream
git pull --ff-only origin main
git merge upstream/main
git push origin main
```

- [ ] Optionally verify synchronization:

```bash
git fetch origin
git fetch upstream
git log --oneline origin/main..upstream/main
git log --oneline upstream/main..origin/main
```

Both commands returning no output means the branches point to the same commit. Different histories can remain after a squash/rebase PR merge; do not force-push just to make them identical.

## Updating punctalyze in an experiment repository

Each experiment repository contains a `punctalyze` submodule pinned to a specific commit. **Updating your fork does not automatically update your experiment's submodule.**

```text
YOUR_USERNAME/punctalyze
        |
        | Update submodule
        v
ANA-XX_analysis_repo
    └── punctalyze (pinned commit)
```

### Checklist: update your experiment's submodule

From the **experiment repository root**:

- [ ] Check that `.gitmodules` points to your intended fork.
- [ ] Confirm which branch the submodule should follow (`main` or your experiment-specific branch).
- [ ] If needed, configure the branch:

```bash
git submodule set-branch --branch dev_branch punctalyze
```

- [ ] Update the submodule to the latest commit on its configured branch:

```bash
git submodule update --init --remote punctalyze
```

- [ ] Review and test the updated pipeline.
- [ ] Commit the new submodule reference (and `.gitmodules` if changed):

```bash
git add .gitmodules punctalyze
git commit -m "Update punctalyze submodule"
git push
```

**Note:** `git submodule update --remote` uses the configured branch, or the remote's default branch if none is configured. It does not automatically select your experiment-specific branch.

### Good practices

- [ ] Keep reusable improvements separate from experiment-specific parameters.
- [ ] Avoid committing raw data, intermediate files, or generated results.
- [ ] Document new dependencies, parameters, and breaking changes.
- [ ] Test your changes before submitting a PR.
- [ ] Preserve the exact submodule commit used for published or finalized analyses.

Thanks for contributing to `punctalyze`! For questions or feature requests, open an [issue](https://github.com/boey-lab/punctalyze/issues) or contact the maintainer.
