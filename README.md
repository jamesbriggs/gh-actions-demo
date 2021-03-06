# gh-actions-demo

## Introduction

Github Actions PoC demo repo for several programming languages and associated linters in one repo simultaneously:

1.  [![Shell](https://github.com/jamesbriggs/gh-actions-demo/actions/workflows/shell.yml/badge.svg)](https://github.com/jamesbriggs/gh-actions-demo/actions/workflows/shell.yml)
 Bash (shellcheck)
1. [![Python](https://github.com/jamesbriggs/gh-actions-demo/actions/workflows/python.yml/badge.svg)](https://github.com/jamesbriggs/gh-actions-demo/actions/workflows/python.yml) Python (flake)
1. [![Perl](https://github.com/jamesbriggs/gh-actions-demo/actions/workflows/perl.yml/badge.svg)](https://github.com/jamesbriggs/gh-actions-demo/actions/workflows/perl.yml) Perl (perl -c, podchecker and prove)
1. [![Ruby](https://github.com/jamesbriggs/gh-actions-demo/actions/workflows/ruby.yml/badge.svg)](https://github.com/jamesbriggs/gh-actions-demo/actions/workflows/ruby.yml) Ruby (ruby -wc)

See `.github/workflows/` for the workflow build scripts.

<p align="center"> 
<img src="images/ss_gh_actions_demo.png" alt="Screenshot" title="Screenshot" width="90%">
<br>
<br>
[Screenshot: GitHub Actions UI showing 4 workflow run results]
</p>

## How Github Actions Works

Github Actions works by starting ["runner"](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners) servers (VMs<sup>1</sup>, possibly using Docker containers) for each OS or programming language version (aka "matrix-build") you specify using YAML "workflow" scripts. If you're using Github anyway for source control, and your automation is amenable to Github Actions, then it's a very useful option compared to self-hosted Jenkins or CircleCI, etc., and reduces administration.

When there's multiple workflows, all scripts are executed in parallel. The workflow script has to decide by directory or file extension what the run actions are. After three build errors, execution automatically stops to conserve resources.

[1] It's outside the scope of this document and subject to change, but for clarity, Github Actions VMs for Linux and Windows are Azure instances, and for Mac OS a Github-specific container. On top of those you can run Docker containers, possibly containing services including Jenkins, etc.

## Workflow Security

Only push events (not pull) are specified in this repo to avoid reported security problems with cloned repos used in [unauthorized bitcoin mining via pull requests.](https://dev.to/thibaultduponchelle/the-github-action-mining-attack-through-pull-request-2lmc)

## Limitations

Note that as of July 2021, workflow scripts do not know which specific files were changed in a git commit, so it's up to the developer to:
1. process all files, new and old or
1. specify directories in workflow events (configured per language in this repo, reduces cost by pruning matrix-build options) or
1. calculate the git commit change list. Two different techniques are:
   * [Get changed files in github actions](https://dev.to/scienta/get-changed-files-in-github-actions-1p36)
   * [Is it possible to run the job only when a specific file changes?](https://github.community/t/is-it-possible-to-run-the-job-only-when-a-specific-file-changes)

Also, to delete an old workflow name in the left-hand workflow menu, all related workflow logs must be deleted one-by-one first.

## PoC Results

The motivation for this PoC was to evaluate basic functionality for linting and/or testing a multi-language REST API SDK repo after each commit (push.)

The positive results are that:

1. The functionality meets the basic PoC requirements for linting files by language for multiple languages in the same repo.
1. The matrix-style permutation of Docker containers by OS and language version is remarkably powerful and easy to use. The built-in Linux, Mac OS and Windows runners (remotes) (Docker images) are very handy, and for most people who manage Jenkins and don't want to manage those operating systems or license Windows, an increase in capability.
1. As a hosted build solution, it eliminates the need to manage Jenkins master and remote instances, for example.
1. The Actions UI is very responsive and clear for programmers with no training needed. Note that writing workflows in YAML has a moderate initial learning curve.

The remaining concerns are:

1. It should be easier to enumerate the file changes list in a commit, but there are workarounds (see above.)
1. Github Actions is Github-specific, so there's lock-in for complex workflow scripts.
1. There are costs/limits for using Github Actions, so either the duration of tests and matrix options need to be limited, or the cost budgeted.
1. Although Github Actions was released on Aug. 8, 2019 and is fairly capable, it is still evolving, so is not a mature feature yet. In contrast, Jenkins has been around since 2004, with the current "Pipeline" feature enabled by default since April 20, 2016. You can see my Jenkins Pipeline Docker plugin sample scripts [here.](https://github.com/RackPing/api_client_samples/tree/main/Jenkins)

## Future PoC Tasks

In addition to linting, test the entire SDK in a Docker container, then upload to Docker Hub. (Currently done on demand using Jenkins Pipeline with the Docker plugin.)

## References

* [docs.github.com: working-dir](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#jobsjob_iddefaultsrun)
* [docs.github.com: Security hardening for GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/security-hardening-for-github-actions)
* [www.datree.io: Top 7 GitHub Actions best practices guide](https://www.datree.io/resources/github-actions-best-practices)
* [github.com: Awesome Actions](https://github.com/sdras/awesome-actions)

