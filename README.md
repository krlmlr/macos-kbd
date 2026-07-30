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

Then log out and back in,
and add the layout under System Settings → Keyboard → Input Sources.
