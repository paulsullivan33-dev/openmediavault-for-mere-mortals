# Installing OpenMediaVault

**The outcome:** a machine running
OpenMediaVault 8, reachable in a browser, with
the default password already changed. This
guide's lab installed nothing (see
`criteria.md`), so this chapter is written
from the project's installation documentation
and its official install script, both read in
full — every step below is sourced from them,
and nothing here pretends to be a transcript.

There are two honest routes in.

## Route 1: the ISO, onto a dedicated drive

For x86-64 hardware, this is the recommended
path: an installer image that lays down Debian
and OMV together.

1. **Disconnect all disks except the future
   system disk** (chapter 3 explains why —
   the installer will consume it entirely).
2. Download the ISO from the project's site
   (the images are hosted on SourceForge) and
   write it to a USB stick — Etcher, or `dd`
   on Linux — or burn it to a DVD.
3. Boot the stick and run the installer. It
   is the Debian installer wearing OMV
   colours: language, hostname, the **root
   password** (remember it; it is the
   machine's master key), and it partitions
   the disk for you.
4. Leave the network connected throughout;
   the installer downloads packages as it
   goes.
5. Reboot when told. The console's login
   screen shows the machine's **IP address**.

One known snag, from the project's own
troubleshooting notes: if the install fails
with "Unable to install GRUB", the fix is to
drop to a shell from the installer menu,
`chroot /target`, run `grub-install` for your
disk and `update-grub`, then continue without
a boot loader from the menu — the docs walk
it step by step.

## Route 2: on top of Debian 13

For ARM boards, UEFI-only machines, a
one-disk layout you partitioned yourself, or
any hardware the ISO dislikes: install plain
**Debian 13 "Trixie"** first (no desktop —
OMV conflicts with it), then add OMV.

The community's install script (maintained in
the project's plugin-developers organisation)
does the whole job. Read — in this lab's copy
of the script — exactly what "the whole job"
means:

- It checks the Debian codename and **stops**
  unless it is `trixie`: the verbatim gate is
  in `openmediavault-lab/install-script-debian-gate.txt`,
  ending "Unsupported version. Only 13
  (Trixie) is supported. Exiting..."
- It refuses architectures other than amd64
  and arm64.
- It adds the OMV package repository
  (`https://packages.openmediavault.org/public`),
  installs the `openmediavault-keyring` package
  so the repository's packages can be verified,
  and installs OMV and its dependencies — the
  long list from chapter 2.
- It **removes network-manager, dhcpcd5 and
  cloud-init**, because OMV manages networking
  itself (through systemd-networkd and netplan).
- It applies the network configuration with
  `omv-salt deploy run systemd-networkd` —
  your SSH session may drop at this point,
  which the script warns about — and schedules
  a reboot.

The manual alternative is the same steps by
hand: add the repository and keyring, then
`apt-get install openmediavault`, then run
`omv-confdbadm populate` and the first-boot
salt stages the package provides. The script
exists because doing this in the wrong order
strands machines; if you go manual, follow
the documentation's "Debian Operating System"
variant page exactly.

## First login

Browse to the machine's IP address. The
default workbench credentials, from the
project's documentation, are:

- **User:** `admin`
- **Password:** `openmediavault`

(On prebuilt ARM images the root password is
the same as the admin password.) Do these
three things immediately:

1. **Change the admin password** — the whole
   neighbourhood knows the default. (Chapter
   13 shows `omv-firstaid`, the rescue menu,
   if you ever lose it.)
2. **Set the time zone and check the clock**
   under System → Date & Time; scheduled jobs,
   log timestamps and certificates all believe
   the clock.
3. **Decide about root SSH.** OMV enables SSH
   access for `root` by default so a headless
   box is never unreachable — the documentation
   says to disable it under Services → SSH once
   you have a normal user in the `_ssh` and
   `sudo` groups (chapter 8), and this guide
   agrees.

**Check yourself:** you can log in to the
workbench as `admin` with a password that is
not the default, and the dashboard (next
chapter) shows your machine's name, uptime
and services.
