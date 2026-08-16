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
  '{ InputSourceKind = "Keyboard Layout"; "KeyboardLayout ID" = 14899; "KeyboardLayout Name" = "de-ch"; }'

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

## Why `de-ch` declares the Roman script

`de-ch` is `group="0"` with a positive `id`.
Ukelele writes `group="126"` and a negative `id` by default, and
[TN2056](https://developer.apple.com/library/archive/technotes/tn2056/_index.html)
is explicit about what that means:
layouts that generate "Unicode characters not associated with any of the
scripts listed above … are only available to Unicode applications,
and have negative id values. They should be assigned to group 126."

Second-class status is not what this layout wants.
It is a Latin layout that produces nothing outside the Roman script,
so it declares Roman (`group="0"`) and takes a positive `id`,
which makes it available to every application rather than only to Unicode ones.
The `id` collision rule is forgiving —
"if this ID collides with that of another keyboard,
the system will assign a new ID."

`ru-de-ch` stays at `group="126"` with its negative `id`:
it genuinely mixes Cyrillic with Latin,
so no single script code describes it and 126 is the honest answer.

**The symptom originally claimed for this change was not caused by it.**
The greyed-out **−** on the stock Apple layout, in
System Settings → Keyboard → Text Input → Input Sources → Edit…,
was read here as macOS refusing to be left with a `group="126"` layout as its
only keyboard layout. That was wrong, and the machine these layouts came from
disproves it: it ran both layouts at `group="126"` with negative IDs, as bare
`.keylayout` files, with the stock Swiss German removed and gone.

The real cause was ordinary. The custom layout was *selected* but never
*enabled* — absent from `AppleEnabledInputSources` while present in
`AppleInputSourceHistory` — so the stock layout was the only enabled one left,
and macOS will not remove the last of those. Enabling the layout for real makes
the **−** live.

The Roman declaration above stands on its own terms; it just never fixed
anything. If you have an `AppleEnabledInputSources` entry from before this
change, note that the `id` moved from `-14899` to `14899`: an entry naming an
ID no installed layout has is pruned at login without a word, which produces
exactly the selected-but-not-enabled state described above. Read the list back
after writing it.
