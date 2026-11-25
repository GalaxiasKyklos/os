# Updating the running system

This image is [bootc](https://github.com/bootc-dev/bootc) / ostree based, so updates are
"pull a new image, stage it, reboot" — not `dnf upgrade`.

## Normal update

```bash
sudo bootc switch ghcr.io/galaxiaskyklos/os:latest   # pull + stage the newest build
bootc status                                          # the new build should show as "staged"
sudo systemctl reboot                                 # boot into it; the old one stays as rollback
```

`bootc switch` names the image directly, so it works no matter how the current deployment was
originally created. After one `switch`, `sudo bootc upgrade` will also work for later updates —
but `switch` always works, so it is the safe default.

## Roll back if something is wrong

The previous deployment is kept as the rollback. To go back:

```bash
sudo bootc rollback && sudo systemctl reboot
```

## Checking where you are

```bash
bootc status        # booted commit, version, and the staged/rollback images
rpm-ostree status   # lower-level deployment list
```

## A note on `bootc upgrade`

If `sudo bootc upgrade` errors with
*"Deployment contains local rpm-ostree modifications"*, that does **not** mean you changed
anything. It means the booted deployment was created by plain `rpm-ostree` (not `bootc`), so
`bootc` will not auto-upgrade it. Just use
`sudo bootc switch ghcr.io/galaxiaskyklos/os:latest` instead — no need to run `rpm-ostree reset`.
