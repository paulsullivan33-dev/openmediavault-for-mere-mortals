# Updates, plugins and backups

**The outcome:** the box stays current, gains
new abilities through plugins (with the
official and third-party shelves clearly
separated), and its own configuration is as
safe as its design honestly allows.

## Updates

**System → Update Management** has two pages:
**Updates**, listing available Debian and OMV
package updates with an install button, and
**Settings** — including the automatic
options. The shipped default database has
**unattended security upgrades enabled** and
update notifications scheduled (daily by
default), and the **apt** machinery under OMV
is plain Debian apt — `omv-upgrade` on the
command line does the same job as the page.

Keep the two kinds of "new" apart:

- **Updates** (7.x → 7.y, package fixes) are
  routine and expected weekly-ish.
- **Release upgrades** (OMV 7 → OMV 8) are a
  migration to a new Debian base — a planned
  event with its own tool (`omv-release-upgrade`
  on the 7 line) and its own release notes,
  never something to click idly. Read the
  notes, back up the configuration first
  (below), and do it at a calm hour.

A notification email when updates wait is
the difference between a maintained box and
a museum piece; set it up under **System →
Notification**: SMTP server details on the
Settings page, and the **Events** page
chooses which happenings mail you — SMART
warnings, filesystem events, update notices,
service monitor alerts (monit) among them.

## Plugins — the official shelf

**System → Plugins** lists installable
extras; each is a Debian package that adds
menu pages, database models and Salt stages
to the same system. The official list for
OMV 8 (from the project's plugin page)
includes:

- **MD** — software RAID (chapter 6).
- **LVM2** — volume groups and logical
  volumes.
- **FTP** — the FTP/FTPS server (chapter 10).
- **NUT** — a UPS (battery backup) monitor
  that can shut the box down cleanly when
  the power fails. For a NAS, arguably the
  most valuable plugin on the shelf.
- **USB Backup** — copy to an external disk
  when it is plugged in, or on a schedule.
- **ClamAV** antivirus, **SNMP**, **TFTP**,
  **Shairport** (AirPlay audio), **Forked-daap**
  (music server), **Diskstats**, **BCache**,
  **ShareRootFs** (the deliberate exception
  to chapter 3's rule: it lets shared folders
  live on the system disk — know why before
  you want it).
- **Podman** and container-based plugins —
  **FileBrowser**, **PhotoPrism**, **S3**
  (MinIO object storage), **WeTTY** (a
  terminal in the browser), **OwnTone** —
  and **K8s**, a lightweight Kubernetes for
  running containers properly. OMV itself
  cannot live in a container; it can,
  through these, *host* them.

Removing a plugin removes its pages and,
usually, asks what to do with its settings;
the data on your disks is yours and stays.

## OMV-Extras — the third-party shelf

**OMV-Extras** (omv-extras.org) is a separate,
community-run repository, enabled by
installing its small release package from
their site. It is not part of the OMV project
— widely used, long-lived, and still a
different trust decision, so make it
separately. It adds the plugins the core
doesn't carry, most famously the **Docker
Compose** plugin (the standard way to run
containers on OMV), **ZFS**, **SnapRAID**
(parity for media collections), **Mergerfs**
(pooling disks into one big folder tree),
**Kernel** (the Proxmox kernel, often for
ZFS users), **Backup** tools, and a long tail
of smaller things. The install script this
guide read even installs an OMV-Extras-adjacent
plugin (writecache) by default on its route.

Rule of thumb: official shelf first; Extras
when the official shelf genuinely lacks the
thing — ZFS and Docker being the usual two.

## Scheduled jobs

**System → Cron** holds scheduled jobs —
run a command or script at a time, as a
user, with optional mailed output. Rsync
tasks (chapter 10), SMART self-tests
(chapter 6) and Btrfs snapshot schedules
(chapter 7) are separate faces of the same
cron machinery; your own housekeeping
belongs here too, where the panel can see
it, rather than in a hand-edited crontab.

## Backing up — the honest version

Two different things get called "backup" on
a NAS; keep them separate.

**Your data** is backed up by *copying it
somewhere else*: rsync tasks to another
machine or a cloud target, the USB Backup
plugin to a rotating external disk, SnapRAID
parity for the media pool, or a proper
backup tool pointed at the shares. RAID is
not this (chapter 6). This guide's series
has a whole Backups guide; the OMV-specific
point is only that every mechanism above
takes a **shared folder** as its unit, which
is chapter 7 paying off again.

**The OMV configuration** — everything in
the database — has a deliberately modest
story, and the project's FAQ tells it
straight: "There is no regular backup/restore
procedure." What exists:

- The system keeps **auto-generated,
  timestamped sibling copies of `config.xml`**
  beside the original in `/etc/openmediavault/`
  — the lab confirmed this from the first-aid
  module that restores them (chapter 13) —
  recent history, not an archive.
- **Keep a copy of `/etc/openmediavault/config.xml`
  off the box.** The FAQ recommends exactly
  this "for reference purposes" when doing a
  clean reinstall: it won't restore itself,
  but it answers every "how was that share
  configured?" question after a rebuild, and
  `omv-firstaid`'s "Restore configuration
  backup" can put a saved database back on a
  living system of the same version.
- OMV-Extras' backup plugin (dd/block-level
  and file-based images of the system disk)
  exists for people who want a restorable
  system image; it is the common route to a
  full "rebuild in an hour" story.

**Check yourself:** updates are not waiting
unnoticed (the dashboard widget is quiet or
the mail arrived), you can say which of your
plugins came from which shelf, and a copy of
`config.xml` exists somewhere that is not
the NAS.
