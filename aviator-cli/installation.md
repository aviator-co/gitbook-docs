---
description: >-
  Learn how to install Stacked PRs CLI. The Aviator command line tool can
  streamline and automate common tasks within your Git and GitHub workflows.
---

# Installation

The Aviator command line tool (invoked as `av`) can be used to streamline and automate common tasks within your Git and GitHub workflows. Currently, the tool is used primarily to manage Stacked PRs<mark style="color:blue;">.</mark>

### macOS (Homebrew)

First, if not already done, [install Homebrew](https://brew.sh).

Then install using Homebrew tap.

```sh
brew install gh aviator-co/tap/av
```

### Arch Linux (AUR)

Published as [`av-cli-bin`](https://aur.archlinux.org/packages/av-cli-bin) in AUR.

```sh
yay av-cli
```

### Debian/Ubuntu

Add Aviator to your APT repositories.

```sh
echo "deb [trusted=yes] https://apt.fury.io/aviator/ /" > \
/etc/apt/sources.list.d/fury.list
```

And then apt install.

```sh
sudo apt update
sudo apt install av
```

#### Alternatively

If you'd prefer you can download the `.deb` file from the [releases page](https://github.com/aviator-co/av/releases).

```sh
apt install ./av_$VERSION_linux_$ARCH.deb
```

### RPM-based systems

Add the following file `/etc/yum.repos.d/fury.repo`.

```conf
[fury]
name=Gemfury Private Repo
baseurl=https://yum.fury.io/aviator/
enabled=1
gpgcheck=0
```

Run the following command to confirm the configuration is working.

```sh
yum --disablerepo=* --enablerepo=fury list available
```

And then run yum install.

```sh
sudo yum install av
```

#### Alternatively

If you'd prefer you can download the `.rpm` file from the [releases page](https://github.com/aviator-co/av/releases).

```sh
rpm -i ./av_$VERSION_linux_$ARCH.rpm
```

### Binary download

Download the binary from the [releases page](https://github.com/aviator-co/av/releases). Extract the archive and add the executable to your PATH.

## Setup

1.  Set up the GitHub CLI for GitHub authentication:

    ```sh
    gh auth login
    ```

    Or you can create a Personal Access Token as described in the [Configuration](https://docs.aviator.co/aviator-cli/configuration#github-personal-access-token) section.
2.  Set up the `av` CLI autocompletion:

    ```sh
    # Bash
    source <(av completion bash)
    # Zsh
    source <(av completion zsh)
    ```
3.  Initialize the repository:

    ```sh
    av init
    ```

## Upgrade

### macOS (Homebrew)

```sh
brew update
brew upgrade av
```

### Debian/Ubuntu

```sh
sudo apt update
sudo apt upgrade
```

### RPM-based systems

```sh
yum update
```
