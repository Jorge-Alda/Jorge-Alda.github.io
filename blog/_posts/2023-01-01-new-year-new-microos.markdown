---
layout: post
title: "New year, new [Micro]OS"
date: 2023-01-01 16:30:25
categories: blog
tags: [articles]
---

Happy 2023!

Wow, quite a long time since the last time writing here. I guess one of my New Year resolutions will be being more active in the blog (and also on my [Mastodon](https://mastodon.cloud/@jalda), check it out!). I could start with some Physics - 2022 ended with quite a bang - but as it is still holidays, today's post will be about operating systems.

<!--more-->

## Containers and immutable systems

Containerization is quite trendy in the linux world. The idea is that one single linux kernel is able to run several small user-spaces (containers) which are completely isolated from each other. Containers are lighter than virtual machines, as they can directly been managed by the kernel instead of needing to "fake" an OS inside an OS.

Some of the uses of containers are reproducibility (for example, check the [docker containers that I used for my PhD thesis](https://github.com/Jorge-Alda/SMEFT19#how-to-run-this-code)), compatibilty (using a single`flatpak` for all linux distros) and sandboxing.

The idea of sandboxing is so appealing, that several distros have been created around it. Fedora has Silverblue and Kinoite, and openSUSE has MicroOS.

## Using MicroOS

I decided to check MicroOS on my old laptop, with the idea of using it in the future as a OS for a work computer.

The process to install MicroOS is like any other modern linux distro. You [download an ISO](https://microos.opensuse.org/), burn it to an USB device, and follow a simple graphical installation wizard, that allows you to choose between GNOME and KDE. Once you complete the wizard, the system installs the flatpak version of `firefox`.

I installed the KDE version, and one thing that surprised me is the small number of installed applications. In addition to `firefox`, only `dolphin`, `konsole` and `kalculator`. So we'll have to install some more!

### `Flatpak`s

`Flatpak`s should be the standard way to install most applications: `firefox`, `Libre`/`Only-office`, `spotify`, `discord`, and even `steam`.

### `distrobox`

Another thing that is already installed is `distrobox`, a terminal tool that allows you to run any containerized distro in a seamless way: use GUI applications, access the network, and read and modify your `home` directory. Pretty nice.

You can use `distrobox` for any application that isn't available as a `flatpak`, but you can get in your favorite `distro`. That is the case of many CLI utilities.

Once you have installed something inside a `distrobox`, you can create an access to it from the outside. For a GUI application (we will use `latte-dock`), run the following command **inside** the `distrobox`:

```bash
distrobox-export --app latte-dock
```

and the application will appear in the start menu, `krunner`, etc.

For a CLI application, for example `python`, we'll use, again inside `distrobox`,

```bash
distrobox-export --bin /usr/bin/python3 --export-path $HOME/.local/bin
```

and it will create an access at the `.local/bin` subdirectory of your `home` directory, which is in the search path. With the `--bin` command it is mandatory to include the export path.

In practice, you shouldn't need to use the terminal for the host system much (or at all), so it is best to create a different terminal profile for your favorite `distrobox` with its own keyboard shortcut.

### Transactional updates

The last way to install things requires modifying the actual SO, so it should be used as a last resource.

I needed it to install [`bismuth`](https://github.com/Bismuth-Forge/bismuth), a window tiler for KDE. It is a `kwin` script, and as far as I know, those live in `/usr/share/kwin/scripts` (maybe `~/.local/share/kwin/scripts` also works, i have to investigate further), so it can't be used with `flatpak` or `distrobox`.

The command to prepare the installation is

```bash
sudo transactional-update pkg install bismuth
```

### VSCode

It seems that VSCode causes [some (mild) headaches in container-based setups](https://distrobox.privatedns.org/posts/integrate_vscode_distrobox.html). That link suggest using VSCode inside `distrobox`, but in my experience, I wasn't able to log-in with github to sync my settings, so this was a big no-no.

The alternative then is using the `flatpak` version. Having `export`-ed `python`, it seems to work out of the box fine, but maybe this setup is a bit hacky. Instead, one can use the `flatpak` VSCode to work inside `distrobox` using the Remote Container extension, but that requires some tinkering. I'll think about it.

### Atomic updates

Updates are atomic, i.e. it updates one thing at a time, and when the update is completed, the system checks that everything works fine. If it works, it creates a new snapshot for that update. This includes both system and installation through `transactional-update`. Updates are applied on the next reboot, and can be rolled back. Everything is automated, so you shouldn't worry about mantaining your computer.
