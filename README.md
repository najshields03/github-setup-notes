# GitHub setup notes (Windows)

Notes from setting up the GitHub CLI on a clean Windows 11 machine, written down while
doing it. Nothing here is theoretical — every step and every error below actually came up.

Environment these were written against: Windows 11 Pro, Windows PowerShell 5.1,
Git 2.55, GitHub CLI 2.97.

## Install

Git and the GitHub CLI are separate installs. Git handles commits and branches locally;
`gh` handles the GitHub side — authentication, creating repos, opening pull requests.

```
winget install --id GitHub.cli
```

Then **open a new terminal**. The installer updates PATH, but a terminal that was already
running holds a stale copy and won't find `gh` until it restarts. This is the single most
common "it installed but doesn't work" moment.

Confirm:

```
gh --version
```

## Authenticate

```
gh auth login
```

Four prompts, in order:

| Prompt | Answer |
| --- | --- |
| What account do you want to log into? | **GitHub.com** |
| Preferred protocol for Git operations? | **HTTPS** |
| Authenticate Git with your GitHub credentials? | **Yes** |
| How would you like to authenticate? | **Login with a web browser** |

The last one prints a one-time code like `ABCD-1234` and waits. Copy it, press Enter, and
your browser opens `https://github.com/login/device` — a page with a single text box.
Paste the code there, click **Continue**, then **Authorize github**.

Back in the terminal you'll see `✓ Logged in as <username>`. Verify any time with:

```
gh auth status
```

That also prints your token scopes. `repo` and `workflow` are the ones that matter for
normal work — `repo` covers creating repos and opening pull requests.

Answering **Yes** to the Git credentials prompt is what lets `git push` work over HTTPS
without a separate credential setup. Skipping it is a common cause of push failures later.

## Set your commit identity

Git refuses to commit without a name and email:

```
git config --global user.name "your-username"
git config --global user.email "you@example.com"
```

**On public repositories, consider using GitHub's no-reply address instead of your real
one.** Commit metadata is permanent and publicly readable, and scrapers harvest it. GitHub
issues every account an alias in this form:

```
<user-id>+<username>@users.noreply.github.com
```

Your numeric ID comes from `gh api user`. Drop `--global` to scope the identity to one
repository rather than every repository on the machine.

## Verify the whole chain

```
gh auth status
git config user.name
git config user.email
```

If all three answer, you're ready to create repositories and open pull requests.

## Troubleshooting

Errors encountered during this setup, and what they actually meant:
[docs/troubleshooting.md](docs/troubleshooting.md)
</content>
