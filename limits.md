# Limits

**The outcome:** nothing in this chapter
surprises you later. These are the edges of
OpenMediaVault 8 as the lab found them in
the product and its documentation.

- **One base, welded.** OMV 8 runs on Debian
  13 only. Not Ubuntu (the FAQ promises a
  broken web interface), not Debian Testing,
  not the previous Debian once it ages out.
  Major-version upgrades are migrations, not
  updates.
- **No containers.** Installing OMV itself
  in LXC or any container is unsupported —
  its installers and its hardware model
  assume a real or virtual machine. (Running
  containers *on* OMV, via plugins, is a
  different thing and is supported.)
- **The system disk is the system's.** By
  design, no shared folders on the OS disk
  without a plugin whose express purpose is
  breaking that rule, and the ISO takes the
  whole disk. Small-system-disk planning is
  not optional (chapter 3).
- **It takes over what it manages.** Samba,
  NFS's companions, nginx, networking, and
  friends have their configuration files
  re-rendered from the database on every
  apply. Coexistence with hand-managed
  services on the same box requires knowing
  exactly where OMV's fences are (chapter
  11). A desktop environment cannot coexist
  at all — the package conflicts with it.
- **No real config backup/restore.** By the
  project's own FAQ: "There is no regular
  backup/restore procedure." Timestamped
  copies of the database exist and firstaid
  can restore one on a living system; a
  point-and-click restore onto a fresh
  install does not (chapter 12). Keep
  `config.xml` yourself.
- **Reference blindness.** The database
  knows a shared folder is *in use*, but
  nothing can list *by what* — hence greyed
  delete buttons with no explanation
  (chapter 7). This is a real, admitted gap.
- **RAID and FTP are not in the box.** In
  OMV 8 both are plugins (official ones).
  The documentation had not caught up at the
  time of writing — its storage section
  still showed a RAID chapter and its
  services section an FTP one — so older
  tutorials will send you looking for pages
  that are not there until the plugins are
  installed.
- **Hardware truth is upstream's truth.**
  Drivers are Debian's; SMART data is
  smartmontools' auto-detection, which some
  USB enclosures and RAID cards defeat.
  OMV shows you what the stack can see —
  no more.
- **Privileges are OMV-deep, not
  network-wide.** Users and groups are local
  to the box. No domain join, no central
  directory in the base product; twenty
  boxes means twenty user lists.
- **The panel is an admin tool, not a user
  portal.** Ordinary users get an account
  page, not a file manager (FileBrowser, if
  wanted, is a plugin). Sharing is by
  protocol — SMB, NFS, FTP — not by web
  link.
- **Small print from the lab.** This guide
  installed nothing (criteria.md), so it
  reports no performance, no upgrade
  experience, and no hardware quirks beyond
  what the documentation itself admits. A
  limit of the guide, stated the same way as
  the product's.

**Check yourself:** if none of these edges
cuts your plan, OMV's shape fits your
problem — and you now know where the fences
are before you lean on one.
