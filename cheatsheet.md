# jj Cheat Sheet

### Getting started

| [Start a new repo](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-git-init) | [Clone an existing repo](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-git-clone) |
|---------|-------|
| `jj git init` | `jj git clone <url>`    |

### Make a change

| [Make a new change](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-new)   | [Describe the content of the current change](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-describe) |
|---------|-------|
| `jj new` | `jj describe -m "description"`    |
| [Finish the current change and create a new one](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-commit) | [Chose which parts to commit](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-commit) |
| `jj commit -m "description"` | `jj commit -i`    |
| [Make a merge change](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-new) | [Split a large change in two smaller changes](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-split) |
| `jj new <id-a> <id-b> <...>` | `jj split <file> -r <rev>` |
| [Modify a change](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-edit) | |
| `jj edit <id>` | |

### Move between changes

There is no command to "move" between changes, the recommanded way is to create
a new change with `jj new <id>`. If the change stay empty you can execute the
command multiple times without creating new changes, the same empty one will
be re-used.

If you don't want to create a new change you can also run `jj edit <id>`. This
is not the recommanded way and may force you to use the `--ignore-immutable`
argument if you're moving to a change that was pushed.

### Manipulate boorkmarks

| [List all bookmarks](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-bookmark-list) | [Create or update a bookmark](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-bookmark-set) |
|---------|-------|
| `jj bookmark list` | `jj bookmark set <bookmark> -r <id>`    |

### Ways to refer to a change

Everytime I say `<id>`, you can use any of these:
| A bookmark | `main` |
|---------|-------|
| A tag | `v0.1` |
| A change ID | `3e887ab` |
| A remote bookmark/branch | `origin/main` |
| The current change | `@` |
| 3 changes ago | `@---` |

See the [revset language](https://jj-vcs.github.io/jj/latest/revsets/) for more
informations.

### Discard a change

| [Discard everything made in the current change](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-abandon) | [Delete all changes made to one file only](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-restore) |
|---------|-------|
| `jj abandon` | `jj restore <path-to-file>` |
| [Undo the last `jj` operation you made](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-undo) | [Restore the state of the project to a previous state](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-operation-restore) |
| `jj undo` | `jj operation restore <op-id>` |

> [!TIP]
> To find the right operation identifier, use `jj op log`

### Combine diverged changes

| [Create a merge change](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-new) | [Rebase changes](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-rebase) |
|---------|-------|
| `jj new <id-a> <id-b> <...>` | `jj rebase --source <id> --destination <id>`    |
| [Copy one change on top of another change](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-duplicate) | |
| `jj duplicate <id> --destination <id>` | |

### Work online

| [Fetch the latest changes](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-git-fetch) | [Push your changes](https://jj-vcs.github.io/jj/latest/cli-reference/#jj-git-push) |
|---------|-------|
| `jj git fetch` | `jj git push` |
