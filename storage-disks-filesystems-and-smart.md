# Storage — disks, filesystems and SMART

**The outcome:** your data disks are wiped,
formatted, mounted and health-checked — the
foundation every shared folder in chapter 7
stands on.

Everything here lives under **Storage** in
the workbench.

## Disks

**Storage → Disks** lists every drive the
system can see, with model, serial number,
capacity and device name. Two jobs live here:

- **Power options** per disk: the APM level,
  spindown time, and write-cache switch
  (backed by the `conf.system.hdparm` data
  model). Spinning disks down saves power and
  noise; it also makes the first access after
  idle slow, and very aggressive spindown
  wears drives. Home users usually leave this
  alone unless a disk is genuinely idle for
  hours.
- **Wipe**: erases a disk — a quick wipe of
  the partition table, or a secure one that
  overwrites everything (slow on big disks).
  Wiping is how a disk with an old life (an
  old RAID signature, a foreign filesystem)
  becomes a blank disk OMV will offer you in
  the next step. It is unrecoverable. Read
  the serial number twice before confirming.

The **system disk is not in your way here**:
it is listed, but OMV will not let you wipe
or format the disk it is running from, and
it never appears as a place to put a
filesystem for sharing (chapter 3's rule,
enforced).

## File systems

**Storage → File Systems** is where a blank
disk becomes a place files can live. The
core can create five filesystems — the lab
read the list from the package's own `mkfs`
definitions: **Btrfs, EXT4, F2FS, JFS and
XFS**. Honest guidance:

- **EXT4** is the safe default: boring,
  understood by everything, repaired by
  tools that exist everywhere.
- **Btrfs** earns its keep if you want its
  snapshots — shared folders on Btrfs are
  created as **subvolumes** and can be
  snapshotted, manually or on a schedule
  (chapter 7) — and its profiles can mirror
  or stripe across disks from inside the
  filesystem.
- **XFS** suits very large filesystems and
  cannot be shrunk; **F2FS** is designed for
  flash media; **JFS** is a quiet legacy
  option most new builds skip.

Creating one asks for the device and a
**label**, then formats the whole device —
no partitioning step: OMV's habit is one
filesystem per disk (or per array).

After creation, select the filesystem and
**Mount** it. OMV records the mount in its
database and in `/etc/fstab`, and mounts
data filesystems under `/srv/` — older
versions under a long UUID-based name; the
FAQ notes that naming has been deprecated in
current releases in favour of label-based
paths like `/srv/dev-disk-by-label-<label>`.
Mounting is also where **quotas** (per-user
size limits, Storage → File Systems → Quota)
attach, for the filesystems that support
them.

Existing filesystems — a disk arriving with
data already on it, in any format Debian can
read, including NTFS or exFAT — can be
mounted the same way without formatting.
Mounting is not formatting; check which
button you are pressing.

## SMART — drive health

**Storage → SMART** has three pages:

- **Settings**: turn monitoring on, set the
  check interval, the temperature thresholds,
  and what power state still gets checked.
- **Devices**: every disk, its SMART status,
  temperature and the full attribute table —
  the raw material of "is this disk dying".
  A disk whose overall status is not PASSED,
  or whose *Reallocated Sector Count* keeps
  climbing, is a disk to replace on your
  schedule rather than its own.
- **Scheduled Tests**: short and long
  self-tests on a timetable. The long test is
  the one that reads the whole surface; run
  it monthly per disk, staggered.

Honest limits, from the documentation: OMV
relies on smartmontools' auto-detection,
which means some USB enclosures report
nothing useful, and disks behind a RAID
controller are only visible in pass-through
mode. OMV updates the smartmontools drive
database weekly by itself.

## Where is RAID?

If you know OMV from older versions, you are
looking for a RAID page that is not there.
**In OMV 8, software RAID is a plugin** —
the official **MD** plugin ("Create, manage,
and monitor software RAIDs based on Linux MD
devices") adds it back; the package still
ships `mdadm` underneath. The documentation's
storage section still shows a RAID chapter —
the shipped product disagrees, and this guide
follows the product (the lab's reasoning is
in `openmediavault-lab/evidence.txt`).

The honest RAID briefing, whichever tool
makes it: RAID levels 0/1/5/6/10 trade disks
for uptime, **RAID is not a backup** (it
copies deletions and corruption just as
faithfully as files), array creation and
rebuilds are the dangerous hours, and CMR
drives (chapter 3) survive rebuilds better
than SMR. **LVM** (volume groups and logical
volumes) is likewise the **LVM2 plugin**, and
**ZFS** arrives via OMV-Extras (chapter 12);
ZFS has its own guide in this series.

**Check yourself:** Storage → File Systems
shows your data filesystem mounted with an
absolute path under `/srv/`, and Storage →
SMART → Devices shows its disk as PASSED.
Chapter 7 can now build on it.
