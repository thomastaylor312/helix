# Helix Fork: Inline Completion (Ghost Text) Support

## What This Fork Is

This is a personal fork of [helix-editor/helix](https://github.com/helix-editor/helix) that carries
[PR #14876](https://github.com/helix-editor/helix/pull/14876) (by @devmanuelli) rebased on top of
upstream `master`. The PR adds `textDocument/inlineCompletion` support (LSP 3.18), which enables
ghost text rendering for AI completion servers like `@github/copilot-language-server`.

## Why It Exists

Helix does not yet have native inline completion / ghost text support in mainline. This fork exists
to daily-drive that feature until the PR is merged upstream. The `master` branch tracks upstream
exactly; the `inline-completion` branch carries the PR commits on top.

## Branch Layout

- **`master`** — mirrors `upstream/master` exactly, never modified directly
- **`inline-completion`** — `master` + the 16 PR commits from `textDocument/inlineCompletion`

## Remotes

- **`origin`** — this fork (`thomastaylor312/helix`)
- **`upstream`** — the main repo (`helix-editor/helix`)
- **`pr-author`** — devmanuelli's fork (source of the PR branch)

## Rebasing onto Latest Upstream (using jj)

When upstream has new commits and you want to update:

```bash
# Fetch latest from upstream
jj git fetch --remote upstream

# master bookmark auto-advances since it tracks upstream/master.
# Rebase the inline-completion branch (and everything on top) onto the new master:
jj rebase -b inline-completion -d master

# If there are conflicts, jj will mark the conflicted commits. To resolve:
#   1. Edit the working copy to the conflicted commit:
jj edit <conflicted-change-id>
#   2. Resolve conflicts in the files (they'll have conflict markers)
#   3. Once resolved, squash into the commit:
jj squash
#   4. Repeat for any other conflicted commits
#   5. Move back to the tip:
jj edit inline-completion

# After a clean rebase, verify the build before pushing:
nix build

# Then push to origin:
jj git push --bookmark inline-completion --allow-new
# Push master too so the fork stays in sync:
jj git push --bookmark master
```

## If the PR Author Pushes Updates

```bash
# Fetch the latest PR branch
jj git fetch --remote pr-author

# View the new PR commits
jj log -r 'textDocument/inlineCompletion@pr-author'

# You may need to recreate the inline-completion branch by:
#   1. Abandoning the old PR commits on inline-completion
#   2. Rebasing the new pr-author commits onto master
# The exact steps depend on what changed. Compare the old and new commits carefully.
```

## When the PR Is Merged Upstream

Once PR #14876 is merged into upstream master, this fork is no longer needed. Remove the
`inline-completion` branch and switch back to using Helix from nixpkgs.
