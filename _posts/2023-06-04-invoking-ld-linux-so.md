---
title: Invoking ld-linux.so
description: A Radical Solution for Distributing ELF to Non-FHS Compliant System
tags: [Linux, ELF, LSB, FSH, Guix, Guix System, Nix, NixOS]
---

# The Curse of NixOS

Poeple seem to have a love-hate relationship with NixOS.  [The Curse of NixOS](https://blog.wesleyac.com/posts/the-curse-of-nixos) is an excellent article by Wesley Aptekar-Cassels, which tells the good and the bad of NixOS, and serves as a great reading for background on this article.

Neither am I a user of NixOS, nor I want to be.  Still, I got stormed by issues on GitHub from NixOS users, which can be summarized by a quote from [NixOS Wiki](https://nixos.wiki/wiki/Packaging/Binaries):

> Downloading and attempting to run a binary on NixOS will almost never work.

---

Here is a demonstration:

```
[nix-shell:~]# ./dart --version
bash: ./dart: No such file or directory

[nix-shell:~]# ls -l dart
-rwxr-xr-x 1 root root 5154328 May 31 23:59 dart
```

Running the `dart` executable downloaded from [dart.dev](https://dart.dev/) would fail in a very confusing way on NixOS.  At a glance it may look like `bash` is complaining about file `./dart` does not exist.  However, checking with `ls` would reject that idea immediately.  This is why many NixOS users get lost, where they have no choice but to seek help from the developer.  It is pretty common for developers to have no cue given the limited information that appear te be contradictory.  I don't blame puzzled NixOS users, as I would probably have no idea without my previous experience of dealing with three different libc on a single Linux system.

# Executable and Linking Format

Linux executable files are usually in [Executable and Linking Format](https://refspecs.linuxfoundation.org/elf/index.html) (ELF).  For dynamically linked programs, the loading of shared libraries is performed by `ld-linux.so`, the dynamic linker, whose location is hard coded in `PT_INTERP` program header.  Most of the Linux distributions have the dynamic linker in a common location, so that programs compiled on one distribution can run on other distributions, as long as all dependencies are satisfied.

```
[nix-shell:~]# ldd dart
	linux-vdso.so.1 (0x00007ffd53b6b000)
	libdl.so.2 => /nix/store/4nlgxhb09sdr51nc9hdm8az5b08vzkgx-glibc-2.35-163/lib/libdl.so.2 (0x00007f6b42fc7000)
	libpthread.so.0 => /nix/store/4nlgxhb09sdr51nc9hdm8az5b08vzkgx-glibc-2.35-163/lib/libpthread.so.0 (0x00007f6b42fc2000)
	libm.so.6 => /nix/store/4nlgxhb09sdr51nc9hdm8az5b08vzkgx-glibc-2.35-163/lib/libm.so.6 (0x00007f6b42ee2000)
	libc.so.6 => /nix/store/4nlgxhb09sdr51nc9hdm8az5b08vzkgx-glibc-2.35-163/lib/libc.so.6 (0x00007f6b42cd9000)
	/lib64/ld-linux-x86-64.so.2 => /nix/store/4nlgxhb09sdr51nc9hdm8az5b08vzkgx-glibc-2.35-163/lib64/ld-linux-x86-64.so.2 (0x00007f6b43677000)

[nix-shell:~]# ls -l /lib64/ld-linux-x86-64.so.2
ls: cannot access '/lib64/ld-linux-x86-64.so.2': No such file or directory
```

NixOS is different that the `ld-linux.so` does not exist at its common location hence the "no such file or directory" error.  This issue comes from a deliberate decision from Nix to ignore the [Filesystem Hierarchy Standard](https://refspecs.linuxfoundation.org/fhs.shtml) (FHS), and NixOS created a tool called [patchelf](https://github.com/NixOS/patchelf) to rectify the consequences.

```
[nix-shell:~]# patchelf --set-interpreter /nix/store/4nlgxhb09sdr51nc9hdm8az5b08vzkgx-glibc-2.35-163/lib64/ld-linux-x86-64.so.2 ./dart

[nix-shell:~]# ldd dart
	linux-vdso.so.1 (0x00007fff717bc000)
	libdl.so.2 => /nix/store/4nlgxhb09sdr51nc9hdm8az5b08vzkgx-glibc-2.35-163/lib/libdl.so.2 (0x00007f523a6ed000)
	libpthread.so.0 => /nix/store/4nlgxhb09sdr51nc9hdm8az5b08vzkgx-glibc-2.35-163/lib/libpthread.so.0 (0x00007f523a6e8000)
	libm.so.6 => /nix/store/4nlgxhb09sdr51nc9hdm8az5b08vzkgx-glibc-2.35-163/lib/libm.so.6 (0x00007f523a608000)
	libc.so.6 => /nix/store/4nlgxhb09sdr51nc9hdm8az5b08vzkgx-glibc-2.35-163/lib/libc.so.6 (0x00007f523a3ff000)
	/nix/store/4nlgxhb09sdr51nc9hdm8az5b08vzkgx-glibc-2.35-163/lib64/ld-linux-x86-64.so.2 (0x00007f523ada6000)

[nix-shell:~]# ./dart --version
Dart SDK version: 2.19.6 (stable) (Tue Mar 28 13:41:04 2023 +0000) on "linux_x64"
```

Once `PT_INTERP` program header is modified to the correct location for NixOS, the `dart` executable now works as expected.  However, even with the tools being available, it's still a trouble for developers who distribute precompiled Linux binaries.  On NixOS, the location of `ld-linux.so` changes every time glibc is updated, therefore distributing already modified ELF is unreliable.  Patching during at the runtime is also undependable as `patchelf` may not be available.

# Invoking `ld-linux.so`

The dynamic linker is a shared library, but it is a special runnable one, which can be explicitly invoked to execute an ELF.

```
[nix-shell:~]# ./dart --version
bash: ./dart: No such file or directory

[nix-shell:~]# /nix/store/4nlgxhb09sdr51nc9hdm8az5b08vzkgx-glibc-2.35-163/lib64/ld-linux-x86-64.so.2 ./dart --version
Dart SDK version: 2.19.6 (stable) (Tue Mar 28 13:41:04 2023 +0000) on "linux_x64"
```

The unmodified `dart` executable works on NixOS when explicitly invoked through `ld-linux.so`. As for finding the uncertain location of the dynamic linker on NixOS, it can be done by reading the program header from `/proc/self/exe`.

# The Radical Solution

Normally, when facing this kind of issues, NixOS users would have to face the challenge of either modifying downloaded programs or building programs from source, which the vast majority of people using NixOS are not familiar with.  Even after getting an offical repackaging in Nixpkgs, the compliants from NixOS users would not stop, as many users may still choose to download instead.

A radical solution was born to put a stop:

``` ruby
# Try to execute ELF.
begin
  exec(*COMMAND, *ARGV)
# Catch the "no such file or directory" error.
rescue Errno::ENOENT
  # Locate `ld-linux.so` by parsing `/proc/self/exe`.
  # See: https://github.com/ntkme/sass-embedded-host-ruby/blob/main/lib/sass/elf.rb
  require_relative 'elf'

  # Rethrow if `ld-linux.so` cannot be located.
  raise if ELF::INTERPRETER.nil?

  # Invoke `ld-linux.so` to execute ELF.
  exec(ELF::INTERPRETER, *COMMAND, *ARGV)
end
```

It has been shipped as part of the [sass-embedded](https://rubygems.org/gems/sass-embedded) gem, for which I've heard zero complaints from NixOS users since then.
