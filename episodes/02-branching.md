---
title: Branching Models
teaching: 15
exercises: 5
---

::::::::::::::::::::::::::::::::::::::: objectives

- Describe the Feature Branch and Forking models.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Which branching model is best for me?

::::::::::::::::::::::::::::::::::::::::::::::::::

In the git-novice lesson you learnt how to develop features
on a branch and use a pull-request to merge the changes
back into the `main` branch. You were unknowingly using
a Git branching model called **feature branch workflow**.

As a reminder, we develop on branches to ensure that our development code
doesn't affect the production code on the `main` branch.
Branches also allow your team to develop features in parallel.

A branching model (sometimes also called strategies or workflows) is the model your team adopts when writing, merging and deploying code when using a version control system.
It is a set of rules that you must follow which outline how
your team and collaborators interact with a shared codebase.

Having a clear model helps avoid merge conflicts, more on that later,
and clearly sets out to new collaborators how they can contribute
to your repository.

In this and the following episodes, we will outline some of the branching models that teams use in order to organize their work.
We will look at their pros and cons and help decide which model you should
choose based on your teams needs.

A branching model aims to:

- Enhance productivity by ensuring proper coordination among developers
- Enable parallel development
- Help organize a series of planned, structured releases
- Map a clear path when making changes from development through to production
- Maintain a bug-free code where developers can quickly fix issues and get these changes back to production without disrupting the development workflow

## Git Branching Models

Some version control systems are more geared towards certain branching models.
When using git you have a wide range of models to pick from.
This means the first rule when collaborating using git is:
“Talk about your branching model.”

A repository's `CONTRIBUTING` file may include details of their branching model.
This information might also be in a repository's `README` file.
If in doubt ask!
You can also look at how other people appear to be contributing to the repository.

Below are a few models:

-----------------------------------------

### Feature Branch

In this model every small change or “feature” gets its own branch
where the developers make changes.
Once the feature is done, they submit a pull request and
merge it into the `main` branch after review.
Feature branches should be relatively short-lived.
Each collaborator must have **write** level, or higher, permissions
on the repository.
There are 2 repositories for each person to be mindful of, ***local*** and ***origin***.

#### Pros

- Developers create each feature  away from `main` so you don't affect production code.
- Developers can create many features in parallel feature branches.
- It's a simple model that's easy for those new to git and your project.
- Easy to set up with continuous integration testing and deployment.

#### Cons

- If you don't regularly merge changes to `main` into your feature branch
  it can become outdated, leading to merge conflicts.
- You may struggle if you need to maintain multiple production versions
  simultaneously in the same repository.

The Feature Branch model is sometimes called GitHub Flow.
Note that all branches and commits exist within the single GitHub, origin repository.
They are  visible to anyone who has read access to the repository.
Any contributor can write to any branch that is not explicitly protected.

```mermaid
---
title: Feature Branch Model
---
flowchart TD
  accDescr {A flowchart showing the feature branch model.
    Here developers work on their local copies of the
    GitHub repository. All developers have write access
    or higher. Developers work on feature branches,
    and push these branches to the origin repository.}
  subgraph subGraph0["Remote Server (GitHub)"]
    r1[("MetOffice/git-training-demo Origin Repository")]
  end
  subgraph subGraph1["<div style=margin-top:>Computer 3</div>"]
    r2[("Local Repository")]
  end
  subgraph subGraph2["<div style=margin-top:>Computer 2</div>"]
    r3[(" Local Repository")]
  end
  subgraph subGraph3["<div style=margin-top:>Computer 1</div>"]
    r4[("Local Repository")]
  end
    r1 -- fetch/pull --> r2 & r3 & r4
    r2 -. push .-> r1
    r3 -. push .-> r1
    r4 -. push .-> r1

style subGraph1 fill:#A9640A
style subGraph2 fill:#A9640A
style subGraph3 fill:#A9640A
```

```mermaid
---
config:
  gitGraph:
    showCommitLabel: false
---
    gitGraph
        accDescr {A git graph showing four branches including the default
        main branch.
        Each circle is a commit.
        A circle with an outline but no fill colour is a merge commit 
        where one branch has been merged into another.
        The two feature branches and the bug_fix branch 
        all branch off of main at the same commit.
        The bug_fix and small_feature branches
        are merged back into main after
        being developed on their branches.
        The large_feature branch merges in the
        changes to main to fix any conflicts
        before the feature is ready to be merged
        back into the main branch via a pull request.}
        commit
        branch bug_fix
        checkout main
        branch small_feature
        checkout main
        branch large_feature
        checkout bug_fix
        commit
        checkout large_feature
        commit
        checkout main
        merge bug_fix
        checkout small_feature
        commit
        checkout large_feature
        commit
        checkout small_feature
        commit
        checkout main
        merge small_feature
        checkout large_feature
        commit
        merge main
        checkout main
        merge large_feature
```

-----------------------------------------

### Forking

In this model you make a [**fork**](https://gitprotect.io/blog/git-forking-workflow/) (copy) of the whole repository you want
to contribute to on GitHub in your personal space.
You develop your changes using this fork.
When a change is ready you open a pull request to contribute the changes
back to the original repository.
There are 3 repositories for each person to be mindful of,
***local***, ***origin*** (your fork), and ***upstream*** (the original repository).

#### Pros

- Removes the need to give all collaborators write level or higher permissions
  on your repository.
  Anyone with read access to a repository can contribute.
- Only project maintainers can approve new code.
- You can use any other model within your main repository and
  forks to develop changes.

All branches and commits exist within the collaborators fork, not the ***upstream*** repository. They are harder to find for anyone who has read access to the upstream repository.

Collaborators can use their fork to test more complex changes. For example testing github actions within a dummy-PR.

```mermaid
---
title: Forking + Feature Branch
---
flowchart TB
  accDescr {A flowchart showing the use of forks
    and the feature branch model.
    Here developers work on their forks (GitHub copies, labelled origin) of the
    main GitHub repository (upstream).
    Developers work on feature branches in their fork,
    and push these branches to the origin repository.
    Pull requests are used to contribute changes from
    the origin to the upstream repository.
    Only one developers fork and local repository
    are shown in this diagram.}
  subgraph top["Remote Server (GitHub)"]
    direction LR
    r1[("MetOffice/git-training-demo Upstream Repository")]
    r2[("username/git-training-demo Origin Repository")]

    r2 -. Pull Request .-> r1
  end
  subgraph bottom["Computer"]
    r3[("Local Repository")]
  end
    r1 -- fetch/pull --> r3
    r3 -- push --> r2
    r2 -- fetch/pull --> r3

style bottom fill:#A9640A
```

-----------------------------------------

### Git Flow

In this model the main development occurs in a `develop` branch.
Feature branches are created from this `develop` branch.
When the `develop` branch is ready for a release,
you create a `release` branch which is then tested and
merged onto the `develop` and `main` branches.

#### Pros

- There is a clear purpose for each branch.
- Handles complex projects well.

#### Cons

- Steeper learning curve, novices may require more help.

```mermaid
    gitGraph
        accDescr {A git graph showing the GitFlow model.}
        commit tag:"0.1"
        branch hotfix
        checkout main
        branch release
        branch develop
        checkout hotfix
        commit
        checkout develop
        commit
        branch small_feature
        checkout develop
        merge hotfix
        branch large_feature
        checkout small_feature
        commit
        checkout large_feature
        commit
        commit
        checkout main
        merge hotfix tag:"0.2"
        checkout small_feature
        commit
        checkout develop
        merge small_feature
        checkout release
        merge develop
        checkout large_feature
        commit
        checkout release
        commit
        commit
        checkout main
        merge release tag:"1.0"
        checkout develop
        merge release
```

## Recommendations

For repositories where collaborators are a small and trusted group the Feature Branch
model is normally sufficient.

A Forking model may be preferable if:

- There are more collaborators, because the number of branches may become unwieldy. 
- There are external collaborators whose contribution is valued,
  but the repository owners need to retain control of the original.
  For this reason most open source projects use a Forking Model.

These working pattern are conventions not rules.
It is easy to switch from one working pattern to the other.
Or even to use the feature branch pattern for trusted colleagues and the forking pattern for outside contributors.

This wasn't an exhaustive list of branching models!
You can find more information using the links below:

- [From Novice to Pro: Understanding Git Branching Strategies, GitProtect](https://gitprotect.io/blog/from-novice-to-pro-understanding-git-branching-strategies/)
- [What is a Git workflow?, GitLab](https://about.gitlab.com/topics/version-control/what-is-git-workflow/#forking-git-workflow)

:::::::::::::::::::::::::::::::::::::::  challenge

## Terminology

Use the glossary to explain the differences between the following:

- A branch
- A remote
- A fork

:::::::::::::::  solution

## Solution

Learners are often confused by the difference
between branches, remotes, and forks.

- Branches are pointers to commits in a repository.
  A repository will have multiple branches where
  developers can work on features in parallel.
- Remotes are **links** to other repositories.
  These remotes usually link to repositories on
  cloud platforms such as GitHub.
  A repository typically has one or more remotes,
  with the first called **origin**.
- A fork is a copy of a repository created on GitHub.
  When linking your local repository your fork becomes
  the origin remote. The original repository you forked from
  becomes the upstream remote.

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: keypoints

- A clearly communicated branching model helps developers.
- For small projects use the Feature Branch flow.
- For larger projects or those with external collaborators use
  forks with feature branches.

::::::::::::::::::::::::::::::::::::::::::::::::::
