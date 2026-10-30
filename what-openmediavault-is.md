# What OpenMediaVault is

**The outcome:** you can say, in one sentence,
what OpenMediaVault is — and what it is not —
and why that difference shapes everything else
in this guide.

OpenMediaVault is a **control panel and a
management layer** that runs on top of **Debian
Linux**. It is not a separate operating system
built from scratch. Debian — the same large,
conservative Linux distribution that powers
servers all over the world — is installed first
(or by OMV's own installer), and OpenMediaVault
moves in as a set of Debian packages: a web
interface, a background engine, and a small
army of helper commands. The project's own
one-line summary is "a complete network
attached storage (NAS) solution based on Debian
Linux".

That has three consequences worth knowing
before anything else.

**First, nothing is hidden from you.** Because
the base is ordinary Debian, the machine can
always be administered the ordinary Debian way:
log in over SSH, and every file OMV manages is
a normal file in a normal place — Samba's
configuration in `/etc/samba`, exports in
`/etc/exports`, and so on. Chapter 11 explains
why you should usually let OMV write those
files rather than editing them yourself, but
the door is never locked.

**Second, OMV is opinionated about roles.**
It assumes one machine with one job: hold
files, serve files. It wants its own system
disk that holds no user data (chapter 3), it
rewrites the configuration files of the
services it manages, and it conflicts —
deliberately, in its package metadata — with
desktop environments: installing it on a
Debian machine with a graphical desktop
removes the desktop's login manager. It is a
server, and it expects the machine to be one.

**Third, it is modular.** The base install is
deliberately small: disks, filesystems, shared
folders, users, and the SMB, NFS, SSH and rsync
services. Everything else — FTP, software RAID
management, a UPS monitor, antivirus, backups
to USB drives, containers — arrives as a
**plugin**, an extra Debian package that plugs
its pages into the same web interface and its
settings into the same configuration database.
Chapter 12 covers the plugin shelf, including
the third-party **OMV-Extras** collection.

The current release line is **openmediavault 8,
codename "Synchrony"**, stable since December
2025, built on **Debian 13 "Trixie"** (the
version this lab read, in October 2026, was
8.6.0, with 8.6.1 in development). Each OMV
major version is tied to exactly one Debian
release — OMV 7 was Debian 12, and reached
end-of-life in June 2026 — and moving between
them is a planned upgrade, not a routine update.
The project is free software, developed openly,
and aimed — in its own words — "primarily at
home environments or small home offices",
though nothing in it limits it to that.

A quick map of the territory, so the rest of
the guide has somewhere to hang:

- You administer the box from a browser, in
  the web interface OMV calls the **workbench**
  (chapter 5).
- Your files live in **shared folders** —
  named folders on data disks, each with a
  list of who may do what (chapter 7). Almost
  every other feature is a way of sharing one.
- People are **users**, collected into
  **groups**, created in the panel (chapter 8).
- Changes you make in the panel are staged in
  a database and only take effect when you
  **apply** them (chapter 11).

**Check yourself:** if you can finish the
sentence "OpenMediaVault is ______ running on
______" (a management layer and web control
panel; Debian Linux), you have this chapter.
