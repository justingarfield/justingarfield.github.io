---
title: Contributing to Talos Linux projects
categories: talos github oss
tags: oss ci cd cicd
---

# Overview

Having worked mainly on projects "behind-closed-doors" at Enterprises and Small-businesses alike; I don't often get to spend much time in the OSS project side of the world, so some concepts seem foreign to me at times.

While this is somewhat of a simple set of steps included in the post, it breaks-down some parts of modern OSS pipelines that we might not otherwise see/use internally on closed-door systems/pipelines. Hopefully helping others with a similar background to mine, understand "what the hell is this stuff?!"

## Prerequisites

* Docker Desktop w/ Docker BuildX available
  * See: https://www.talos.dev/v1.4/advanced/developing-talos/ (Prepare step is all that's needed for simple changes (documentation fixes, etc.))
* GitHub CLI installed
* (Windows users) Windows Subsystem for Linux (WSL)
  * Also need to configure the following under Docker Desktop -> Docker Engine settings config file...
    ```json
    {
    "builder": {
        "Entitlements": {
        "security-insecure": true
        },
        "features": {
        "buildkit": true
        }
    }
    ```

## Fork-and-Grab the source

```shell
#!/bin/sh
GHUB_USERNAME=<your gh username>
REPO_DIR=$HOME/src/$GHUB_USERNAME

mkdir -p $REPO_DIR
cd $REPO_DIR

gh repo fork --clone --default-branch-only siderolabs/talos
```

## Look at the PULL_REQUEST_TEMPLATE

Lots of OSS projects will include a `PULL_REQUEST_TEMPLATE` file; usually located in a `.github` folder.

This is a great place to start if you plan on creating PRs against a repository, as it usually provides you with a list of pre-established checks and tests to run before throwing your changes over-the-wall to the rest of the team _(so that we don't waste everyone's time due to simple mistakes)_.

Let's now take a peek at the `PULL_REQUEST_TEMPLATE` file for the Talos Linux project itself...

```shell
cat $REPO_DIR/talos/.github/PULL_REQUEST_TEMPLATE.md
```

### General items

```markdown
<!--
## Note to the Contributor

We encourage contributors to go through a proposal process to discuss major changes.
Before your PR is allowed to run through CI, the maintainers of Talos will first have to approve the PR.
-->

## What? (description)

## Why? (reasoning)
```

We can see a few things here:

* The team is asking that you submit a proposal (email, Slack thread, GitHub issue, etc.) if you'll be submitting a PR with major changes. This makes perfect sense, as you wouldn't want to spend 1000-hours developing something, only to find out the team already went in another direction.

* You're also informed that, "Hey, no matter how many times you press buttons, your stuff isn't going to consume our CI/CD pipeline(s) until we're cool with allowing it."

* The 'What?' section is simply asking you to state 'What' is affected by your PR. Is it a certain documentation page? A full folder or module of code files?

* The 'Why?' section wants you to supply 'Why' you're submitting the changes for the 'What'.

Okay, nothing too crazy at this point. Seems pretty straight-forward. It's just important to hit-home that on some projects, you're going to be submitting PRs to potentially _thousands_ of eyeballs across the world, in different timezones, in different locales and cultures; so even the most mundane of information can help clarify and provide context to everyone that may see your PR.

### Acceptance

```markdown
- [ ] you linked an issue (if applicable)
- [ ] you included tests (if applicable)
- [ ] you ran conformance (`make conformance`)
- [ ] you formatted your code (`make fmt`)
- [ ] you linted your code (`make lint`)
- [ ] you generated documentation (`make docs`)
- [ ] you ran unit-tests (`make unit-tests`)

> See `make help` for a description of the available targets.
```

### you linked an issue

If you're simply fixing some documentation typos, chances are probably good you don't have a corresponding issue opened _(unless it was part of a larger initiative already started by the team itself)_.

If you do have a corresponding issue, there are manual and automatic ways to 'link an issue' to a Pull Request on GitHub. See [Linking a pull request to an issue](https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue) for all of the details on how this can be accomplished.

My own personal preference would be to simply add one of the keyword variations to my PR description and let it automatically link _(e.g. `Fixes #1234`)_.

See https://github.com/siderolabs/talos/pull/7340 and https://github.com/siderolabs/talos/pull/7027 for real examples.

### you included tests

See https://github.com/siderolabs/talos/pull/7028 and https://github.com/siderolabs/talos/pull/6423 for real examples.

### you ran conformance (`make conformance`)

When you run `make conformance` on the talos repository, you're going to fire-up a container that will analyze your active branch and check things like your commit message structure, commit message capitalization, commit message spelling, existence of DCO in commit messages, etc.

If you run these checks on a fresh fork/clone of the repository, you should see output of everything passing like so...

```shell
POLICY         CHECK                        STATUS        MESSAGE
commit         Header Length                PASS          Header is 32 characters
commit         Imperative Mood              PASS          Commit begins with imperative verb
commit         Header Case                  PASS          Header case is valid
commit         Header Last Character        PASS          Header last character is valid
commit         DCO                          PASS          Developer Certificate of Origin was found
commit         GPG                          PASS          GPG signature found
commit         GPG Identity                 PASS          Signed by "Noel Georgi <noel.georgi@siderolabs.com>"
commit         Conventional Commit          PASS          Commit message is a valid conventional commit
commit         Spellcheck                   PASS          Commit contains 0 misspellings
commit         Number of Commits            PASS          HEAD is 0 commit(s) ahead of refs/heads/main
commit         Commit Body                  PASS          Commit body is valid
license        File Header                  PASS          All files have a valid license header
```

You can find the full details of what each of these do by checking out [siderolabs/conform](https://github.com/siderolabs/conform).

You can also find the most up-to-date version of settings that talos is using for itself [over here](https://github.com/siderolabs/talos/blob/main/.conform.yaml).

I'll recap the ones used above, so that you can keep moving along.

#### Header is 32 characters

This is simply applying a policy that commit subjects need to be no longer than 32-characters.

#### Commit begins with imperative verb

This might seem a little odd for some folks at-first, but the rule does exactly as you would think...makes sure you're writing commit messages that have an 'imperative mood'. Say out-loud, "If applied, this commit will <your subject line here>."

Commit messages that are imperative:

```text

```

Commit messages that are NOT imperative:

```text

```

https://github.com/dcoapp/app#how-it-works

### you formatted your code

### you linted your code

### you generated documentation

### you ran unit-tests

## Some useful commands

In the event you forgot to properly format your commit message, or need to add your DCO...

```shell
git commit --amend
```

Automatically add DCO with commit...

```shell
git commit -s -m 'This is my commit message'
```



If I'm submitting a PR to fix a typo and a broken link on one of the Documentation pages, do I need to fully pass the make conformance tests? (just markdown changes)


[Cosign OpenID Connect signing](https://docs.sigstore.dev/cosign/openid_signing/)

# Pull Request

## What? (description)

`Documentation` -> `Advanced Guides` -> `Verifying Images` pages of the **v1.4** and **v1.5** documentation.

## Why? (reasoning)

Spotted a typo and broken link on the pages mentioned above.

On investigating the broken link, found that the referenced documentation had been migrated into a new page...

* https://github.com/sigstore/cosign/blob/main/KEYLESS.md
* https://github.com/sigstore/cosign/issues/822

## Acceptance

Please use the following checklist:

- [ ] you linked an issue (if applicable)
- [ ] you included tests (if applicable)
- [ ] you ran conformance (`make conformance`)
- [x] you formatted your code (`make fmt`)
- [x] you linted your code (`make lint`)
- [x] you generated documentation (`make docs`)
- [x] you ran unit-tests (`make unit-tests`)

> See `make help` for a description of the available targets.




 fix-typo-on-verifying-images-docs