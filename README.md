# OpenMediaVault for Mere Mortals

A network attached storage box — a **NAS** — is
just a computer whose whole job is holding files
and handing them to the other machines in the
house. **OpenMediaVault** (almost everyone calls
it **OMV**) is the software that turns an ordinary
PC, an old desktop, or a small single-board
computer into one. Underneath it is **Debian
Linux**, unchanged and fully present; on top sits
a **web control panel** — the *workbench* — where
you manage disks, folders, users and sharing from
a browser, without memorising commands.

This guide explains how OpenMediaVault is put
together, how to install it, and how to do the
everyday jobs: preparing disks, creating the
*shared folders* everything else revolves around,
adding users, and serving those folders to Windows,
Mac and Linux machines. Then it does something
most NAS guides skip: it opens the machine's own
filing cabinet. Every setting you change in the
workbench lands in one database file, and nothing
actually happens on the system until you press
**Apply** — chapter 11 shows that machinery in the
project's own code, because understanding it turns
the panel's strangest behaviours (the yellow
banner, the overwritten config file, the greyed-out
delete button) into things you can predict.

One honesty note, up front. The lab behind this
guide did **not** install OpenMediaVault. The
current release, OMV 8, runs on exactly one base —
Debian 13 — and this lab's machine is a shared
Ubuntu sandbox where installing it would have
broken the machine for everyone else on it. So
instead of a fake walkthrough, the lab **dissected
the real thing**: the project's own source tree
(the same files the install package ships), its
official install script, its package metadata, and
its official documentation, each checked against
the others. Where those sources disagreed — and
they did, twice — the guide follows the shipped
product and says so. `criteria.md` keeps the full
ledger of what was read and what was not run.

## What this guide covers

- [**What OpenMediaVault is**](what-openmediavault-is.md) —
  a control panel on top of Debian, not a
  from-scratch system, and what that buys you
- [**The moving parts**](the-moving-parts.md) —
  Debian, the configuration database, the engine
  behind the panel, the renderer that writes real
  system files, and the web server that serves it
- [**Before you install**](before-you-install.md) —
  what hardware it needs, and the one rule that
  surprises everyone: the system disk is for the
  system only
- [**Installing OpenMediaVault**](installing-openmediavault.md) —
  the ISO route onto a dedicated drive, the
  install-on-Debian route, and the first login
- [**First login and the workbench**](first-login-and-the-workbench.md) —
  the dashboard, the menu tree, and the difference
  between the admin account and everyone else
- [**Storage — disks, filesystems and SMART**](storage-disks-filesystems-and-smart.md) —
  wiping and preparing drives, the filesystems it
  can make, drive health checks, and where RAID
  actually lives now
- [**Shared folders**](shared-folders.md) — the
  central idea of the whole system: a named folder
  on a chosen disk, with its own access list,
  that every service then shares
- [**Users, groups and permissions**](users-groups-and-permissions.md) —
  who may log in, who may see what, and the two
  layers of permission that must both say yes
- [**Sharing files — SMB and NFS**](sharing-with-smb-and-nfs.md) —
  the Windows/Mac way and the Linux way of serving
  a shared folder, setting by setting
- [**SSH, rsync and FTP**](ssh-rsync-and-ftp.md) —
  logging into the box itself, copying on a
  schedule, and the FTP service that now arrives
  as a plugin
- [**The apply step**](the-apply-step.md) — why
  nothing changes until you press Apply, what the
  button actually does, and why hand-edited files
  get rewritten
- [**Updates, plugins and backups**](updates-plugins-and-backups.md) —
  keeping the system current, official plugins,
  the third-party OMV-Extras shelf, scheduled
  jobs, notifications, and the honest truth about
  backing up the configuration
- [**The command line**](the-command-line.md) —
  the `omv-*` tools, and **omv-firstaid**, the
  menu that rescues a locked-out admin
- [**The neighbours**](the-neighbours.md) —
  TrueNAS, a bought appliance, plain Debian with
  Samba, and when each beats OMV
- [**Limits**](limits.md) — one base system,
  one system disk, no containers, no built-in
  config restore, and the rest, stated plainly
- [**Glossary**](glossary.md) — the terms,
  collected in one place

## What it doesn't cover

Running OMV in a container — the project does not
support it, at all. Building a media server empire
on top (the plugins exist; a media library is its
own project). ZFS in depth: OMV meets it through a
third-party plugin, and ZFS itself has its own
guide in this series. Recovering data from a dead
array — backup is taught here *before* you need
it, and recovery is a specialist job. Grading how
fast any of this runs: the lab installed nothing,
so it measured nothing, and the guide claims no
speeds.

## How to use this guide

Read chapter 1 for the idea and chapter 2 before
installing anything — knowing which part holds
what turns later confusion into a named problem.
Chapter 3 is short and saves hardware: read it
before you buy or wipe a disk. Chapters 4 and 5
get you a running box and a first look around.
Chapter 7 is the heart; everything in chapters 8
to 10 hangs off it. Chapter 11 is the one to read
slowly even if the panel already makes sense to
you — it explains the behaviour every OMV owner
eventually trips over. The lab's evidence —
package facts, the workbench's own menu files,
the install script's version gate, and the full
verification ledger — lives in `openmediavault-lab/`.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Linux for Mere Mortals](https://paulsullivan33-dev.github.io/linux-for-mere-mortals/)
- [Samba and NFS for Mere Mortals](https://paulsullivan33-dev.github.io/samba-and-nfs-for-mere-mortals/)
- [Proxmox for Mere Mortals](https://paulsullivan33-dev.github.io/proxmox-for-mere-mortals/)
- [Backups for Mere Mortals](https://paulsullivan33-dev.github.io/backups-for-mere-mortals/)
- [ZFS for Mere Mortals](https://paulsullivan33-dev.github.io/zfs-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/openmediavault-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/openmediavault-for-mere-mortals/blob/main/LICENSE-CODE).
