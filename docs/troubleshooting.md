# Troubleshooting

Things that went wrong, and what they actually meant.

## `gh` is not recognized, but it installed fine

The installer wrote `gh.exe` to `C:\Program Files\GitHub CLI\` and updated the system
PATH. Processes read PATH once at startup, so any terminal that was already open when the
install ran has a stale copy.

Open a new terminal. If you can't, use the full path:

```
& 'C:\Program Files\GitHub CLI\gh.exe' auth status
```

The `&` is PowerShell's call operator — required when the executable path contains spaces,
which `Program Files` does.

## The device page asks for a username and password

Expected. You cannot authorize a device for an account the browser isn't signed into, so
GitHub sends you through a normal login first.

Two things worth knowing:

- **Check the address bar reads exactly `github.com` before typing.** Device-code pages
  are a known phishing target precisely because people expect to paste a code into them.
- **After signing in you may land on your dashboard** instead of back at the code box.
  Navigate to `https://github.com/login/device` again — you'll be signed in now and it
  goes straight to the prompt.

## The one-time code expired

Codes last roughly 15 minutes, and signing in for the first time can eat most of that.

Press `Ctrl+C` in the terminal, run `gh auth login` again, and take the fresh code. The
second pass is quick because the browser session already exists.

## `python` exists but does nothing

On a clean Windows 11 install, `C:\Users\<you>\AppData\Local\Microsoft\WindowsApps\python.exe`
is a **0-byte App Execution Alias**, not an interpreter. It's a stub that opens the
Microsoft Store. `where.exe python` finds it, `Get-Command` reports a path, and
`python --version` prints nothing at all.

Check the file size — zero bytes means it's the stub:

```
(Get-Item (Get-Command python).Source).Length
```

Install a real interpreter with `winget install Python.Python.3.13`, or turn the alias off
under Settings → Apps → Advanced app settings → App execution aliases.

## `git commit` complains about identity

```
Author identity unknown
*** Please tell me who you are.
```

Git has no name/email configured. See the identity section in the [README](../README.md).

## PowerShell chaining doesn't work like bash

Windows PowerShell 5.1 — the version that ships with Windows 11 — has no `&&` or `||`
operators. `command-a && command-b` is a parser error, not a failed command.

Use `;` to chain unconditionally, or check the status variable:

```
command-a; if ($?) { command-b }
```

PowerShell 7 added `&&` and `||`, so a snippet copied from a newer tutorial may fail on
the shell you actually have. `$PSVersionTable.PSVersion` tells you which one is running.
</content>
</invoke>
