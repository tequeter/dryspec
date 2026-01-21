# Installing DrySpec in RooCode

These instructions have been written for RooCode 3.41 and may have become obsolete by the time you read them.

If in doubt, consult the [official RooCode documentation](https://docs.roocode.com/).

## Importing the mode

Go to RooCode Settings > Modes and click the `Import Mode` button (downwards arrow in the upper right corner).

Select `dryspec-modes.yaml`.

## Installing the slash-commands

Create the global slash-commands folder if it doesn't exist yet. It is located:

- On Linux (or WSL): `~/.roo/commands`.
- On Windows: `%USERPROFILE%\.roo\commands`.

Then copy the other files there:

- `dryspec-change.md`
- `dryspec-init-existing.md`
- `dryspec-init-new.md`
- `dryspec-reconcile.md`
