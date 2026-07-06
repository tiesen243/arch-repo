# my-arch-repo

A small template repository for building a personal Arch Linux binary repository from AUR-style package directories and publishing it to **GitHub Releases** for free. This repository is meant for users who want a simple self-hosted pacman repo without maintaining a separate server.

## Purpose

This repository is designed to:

- Build selected packages from local PKGBUILD directories.
- Publish the built `.pkg.tar.zst` files and repo database files to a fixed GitHub Release.
- Let Arch-based systems install those packages through `pacman` using the GitHub Release URL as a package repository.

This is useful when:

- A package is only available in AUR and you want prebuilt binaries for your own machines.
- You want a lightweight personal binary repo.
- You want a free setup using GitHub Actions + GitHub Releases.

## How to use this repository

Add this repository to `/etc/pacman.conf`:

```ini
[my-arch-repo]
SigLevel = Optional TrustAll
Server = https://github.com/nhktmdzhg/my-arch-repo/releases/download/repository
```

Then refresh package databases:

```bash
sudo pacman -Sy
```

After that, packages published by this repo can be installed like normal packages:

```bash
sudo pacman -S <package-name>
```

## How the repo works

- Each package lives in its own directory and contains a `PKGBUILD`.
- GitHub Actions builds packages automatically.
- The workflow creates `repository.db.tar.gz` and related repo metadata with `repo-add`.
- The built packages and database files are uploaded to the GitHub Release tag named `repository`.

## Create your own repo

1. Fork this repository.
2. Delete package directories you do not want.
3. Add your own package directories, each containing a `PKGBUILD`.
4. Edit `packages.txt` to match the packages you want to manage.
5. Push to `main`, or run the workflow manually from GitHub Actions.
6. Use your own GitHub repo URL in `pacman.conf`:

```ini
[my-arch-repo]
SigLevel = Optional TrustAll
Server = https://github.com/<your-username>/<your-repo-name>/releases/download/repository
```

## Notes

- This repository is intended for personal use or small-scale use.
- `SigLevel = Optional TrustAll` is convenient for a private or personal repo, but it is less strict than signed packages.
- If a package fails to build, the workflow continues with other packages as long as at least one package builds successfully.
