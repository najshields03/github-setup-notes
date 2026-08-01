# The pull request workflow

Setup gets you authenticated. This is what you actually do afterwards, every time you
change something.

The rule underneath all of it: **`main` stays clean, work happens on a branch.** A branch
is a disposable line of work you can throw away without touching anything else. A pull
request is a proposal to merge that branch back, with a place to review it first.

## 1. Branch

Start from an up-to-date `main`:

```
git checkout main
git pull
git checkout -b docs/add-pr-workflow
```

`-b` creates the branch and switches to it in one step. Names are conventionally
`area/short-description` — `docs/`, `fix/`, `feat/`. The prefix isn't enforced by Git; it
just makes a branch list readable later.

Confirm where you are before editing:

```
git branch --show-current
```

## 2. Commit

```
git add -A
git commit -m "Add pull request workflow guide"
```

`git add -A` stages everything including deletions. Check what you're about to commit with
`git status` first — committing a file you didn't mean to is far more annoying to undo
than to avoid.

Write the subject line as an instruction: *"Add X"*, not *"Added X"* or *"changes"*. If
the change needs explaining, leave a blank line after the subject and write a paragraph.
The subject says what; the body says why.

## 3. Push

```
git push -u origin docs/add-pr-workflow
```

`-u` links the local branch to the remote one, so later pushes on this branch are just
`git push`. You only need `-u` the first time.

## 4. Open the pull request

```
gh pr create --fill
```

`--fill` reuses your commit message as the PR title and body — fine for a single-commit
change. For anything larger, write them out:

```
gh pr create --title "Add pull request workflow guide" --body "Explains the branch, commit, push, PR, merge cycle."
```

`gh pr view --web` opens it in the browser.

## 5. Merge

```
gh pr merge --squash --delete-branch
```

`--squash` collapses every commit on the branch into one commit on `main`, so the history
reads as one change per feature rather than a trail of "fix typo" commits.
`--delete-branch` cleans up the branch on both sides.

Then get your local copy back in sync:

```
git checkout main
git pull
```

Skipping that last `pull` is the most common way to end up confused — your local `main`
still predates the merge, and the next branch you cut starts from stale code.

## Undoing things

| Situation | Command |
| --- | --- |
| Discard changes to a file you haven't staged | `git restore <file>` |
| Unstage a file but keep the edits | `git restore --staged <file>` |
| Fix the last commit's message (before pushing) | `git commit --amend` |
| Abandon a branch entirely | `git checkout main` then `git branch -D <branch>` |

These are safe **before** you push. Rewriting history that's already on GitHub and shared
with others is a different and much more disruptive operation — avoid `--amend` and
`push --force` on anything someone else may have pulled.
</content>
