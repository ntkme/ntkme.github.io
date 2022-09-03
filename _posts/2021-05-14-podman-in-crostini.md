---
title: Podman in Crostini
description: Run Rootless Containers under Chrome OS
tags: [Chrome OS, Crostini, Linux, Podman, Container, Rootless, LXC, termina, penguin]
image: /assets/img/2021/05/14/podman-in-crostini/rootless-podman@2.6666666666x.png
---

![Rootless Podman]({{ page.image }}){: srcset="{{ page.image }} 2.6666666666x" }

Crostini, a.k.a. Linux on Chrome OS, runs a virtual machine named `termina`. Inside `termina`, a container named `penguin` running under `lxc` is exposed to users via the Terminal app.

# Install Podman in Crostini

The `penguin` container is based on Debian. The [Kubic project](https://build.opensuse.org/project/show/devel:kubic:libcontainers:stable) provide `podman` packages for Debain. 

``` sh
curl -fsSL "https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/Debian_$(. /etc/os-release && echo "$VERSION_ID")/Release.key" | sudo gpg --dearmor --yes -o /usr/share/keyrings/kubic-libcontainers-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/kubic-libcontainers-archive-keyring.gpg] https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/Debian_$(. /etc/os-release && echo "$VERSION_ID")/ /" | sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
sudo apt update -qq
sudo apt install -qq -y podman buildah skopeo
```

# Issues

Unfortunately, `podman` does not function properly out of box in Crostini. Below are a few common issues and how to fix them.

---

```
Error: mount `proc` to '/proc': Operation not permitted: OCI permission denied
```

This error is due to the following `lxc` config for `penguin` container:

``` yaml
config:
  security.nesting: "false"
```

The solution is to set `lxc` config `security.nesting` to `"true"`.

---

```
ERRO[0000] cannot find UID/GID for user linuxbrew: No subuid ranges found for user "linuxbrew" in /etc/subuid - check rootless mode in man pages. 
WARN[0000] using rootless single mapping into the namespace. This might break some images. Check /etc/subuid and /etc/subgid for adding sub*ids 
```

This error is due to `/etc/subuid` and `/etc/subgid` missing entry for the current user.

The solution is to add a range for current user in `/etc/subuid` and `/etc/subgid`.

---

```
Error: kernel does not support overlay fs: unable to create kernel-style whiteout: operation not permitted
```

This error is due to Linux kernel in Crostini does not have OverlayFS support.

The solution is to use `btrfs` storage driver.

---

# Fixes

![crosh](/assets/img/2021/05/14/podman-in-crostini/crosh@2.6666666666x.png){: srcset="/assets/img/2021/05/14/podman-in-crostini/crosh@2.6666666666x.png 2.6666666666x" }

Open Google Chrome, press Ctrl + Alt + T to get the `crosh` shell, and run:

``` sh
vsh termina
```

Once inside the `termina` virtual machine shell, run:

``` sh
lxc config set penguin security.nesting true
lxc restart penguin
lxc exec penguin -- /bin/sh -c "printf '%s\n' '1000:100000:65536' | tee /etc/subuid /etc/subgid"
lxc exec penguin -- /bin/sed -i -e 's/^driver[[:space:]]*=.*$/driver = "btrfs"/' /etc/containers/storage.conf
lxc exec penguin -- /bin/rm -rf /var/lib/containers/storage
```

Now, rootless `podman` should work in Crostini!
