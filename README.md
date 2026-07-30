# macos-kbd

My favorite keyboard layouts.

## Installation

The layouts install system-wide into `/Library/Keyboard Layouts`,
so they are available to every account and at the login window.
Run the script as yourself, not under `sudo`:
it downloads into a temporary directory unprivileged
and uses `sudo` only for the two commands that write to `/Library`,
so it prompts for your password.
Inspect [`bootstrap`](https://github.com/krlmlr/macos-kbd/blob/main/bootstrap) to see what's going on.

```sh
curl -fsS https://raw.githubusercontent.com/krlmlr/macos-kbd/main/bootstrap | sh
```

Then log out and back in.
The layout directories are scanned at login,
so the layouts are not offered under
System Settings → Keyboard → Input Sources before that.

## Enabling from the CLI

Adding the layout to the input sources is a preference write,
so it does not have to be a click.
These are per-user preferences —
run them as yourself, never under `sudo`,
or they land in root's preferences instead.
The IDs come from the `<keyboard>` element of each `.keylayout` file.

```sh
defaults write com.apple.HIToolbox AppleEnabledInputSources -array-add \
  '{ InputSourceKind = "Keyboard Layout"; "KeyboardLayout ID" = -14899; "KeyboardLayout Name" = "de-ch"; }'

defaults write com.apple.HIToolbox AppleEnabledInputSources -array-add \
  '{ InputSourceKind = "Keyboard Layout"; "KeyboardLayout ID" = -15232; "KeyboardLayout Name" = "ru-de-ch"; }'
```

To make `de-ch` the layout the session starts with:

```sh
defaults write com.apple.HIToolbox AppleCurrentKeyboardLayoutInputSourceID \
  -string org.unknown.keylayout.de-ch
```

Order matters: run these *after* the logout that follows the install.
A layout that is not registered yet cannot be selected,
and the write silently sticks without taking effect.
They apply from the next login;
check what is enabled with

```sh
defaults read com.apple.HIToolbox AppleEnabledInputSources
```

and fall back to the Input Sources pane if an entry does not take.
