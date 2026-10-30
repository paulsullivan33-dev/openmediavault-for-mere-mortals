# Before you install

**The outcome:** you know what hardware
OpenMediaVault 8 wants, and you have absorbed
the one rule that shapes every install — the
system disk and the data disks are different
disks.

## The system disk rule

OpenMediaVault divides the world into the
**system drive** (the disk Debian and OMV live
on) and **data drives** (the disks your files
live on). The official requirements state it
flatly: the system drive "cannot officially be
used for shared resources or as user data
drive", and a data drive "must be a different
disk drive than the system disk drive".

The ISO installer enforces this the blunt
way: it takes over the **whole** disk you
point it at, partitioning it for the system
(roughly: a small `/boot`, the root filesystem,
and swap) and erasing whatever was there. The
installation page's warning deserves quoting:
"The operating system/root filesystem will
consume the whole space of the picked disk
device. All existing data on that disk device
will get lost." The same page's first
instruction is to **disconnect every other
disk** before installing, so the installer —
and you — cannot pick the wrong one.

Practical shapes that respect the rule:

- A small, cheap SSD (the minimum root size is
  4 GiB; the docs suggest far more, and any
  small modern SSD is plenty) as the system
  disk, plus one or more big disks for data.
- A USB flash drive as the system disk is
  possible but unwise: the documentation warns
  the system writes to its disk with no
  flash-friendly optimisations and a stick
  "will most likely start failing within a few
  weeks". If you must, install the **Flash
  Memory plugin** (from OMV-Extras) to cut
  the writes — or use an SSD in a USB enclosure
  instead.
- One disk total is possible only by
  installing plain Debian first, partitioning
  by hand, and adding OMV on top (chapter 4) —
  the project calls this a skilled-user route
  and offers no help partitioning.

## The rest of the requirements

From the project's prerequisites page, for
OMV 8 (Debian 13):

- **CPU:** almost anything Debian runs —
  x86-64 (amd64), 32-bit x86 (published but no
  longer officially supported), ARMv7 and
  ARM64. The install-on-Debian script is
  stricter: it accepts **amd64 and arm64
  only** (it checks, and exits if not).
- **RAM:** 1 GiB is the stated minimum and the
  docs are honest that it takes expertise;
  "at least 4 GiB for novice administrators"
  and 8 GiB recommended is their advice.
- **Network:** a wired gigabit card if you
  can. A NAS can never serve files faster
  than its network connection, and several
  users divide whatever there is.
- **Boot:** BIOS or UEFI both work on a
  Debian-first install; Secure Boot is best
  disabled. (OMV's ready-made ISO has
  historically been BIOS-boot only on x86 —
  another reason the Debian-first route
  exists.)
- **An internet connection during install.**
  The ISO page is blunt: "An offline
  installation will lead to problems later
  on", because the installer and the package
  system fetch software as they go.

## Choosing data disks

Any disk Debian can see, OMV can use — SATA,
SCSI, USB, NVMe. Two honest caveats:

- **SMR vs CMR.** For RAID-like setups and
  parity tools, the docs recommend **CMR**
  (conventional recording) drives over **SMR**
  (shingled) ones, which "can stall during
  rebuilds and parity sync" — and the model
  number rarely tells you which you are
  buying, so check a reference list first.
- **SMART needs a sensible connection.**
  OMV reads drive health through
  smartmontools; for disks behind a RAID
  controller it only works when the controller
  passes the disk through, and some cheap USB
  enclosures hide the health data entirely
  (chapter 6).

## Not supported, before you ask

- **Containers.** "openmediavault does not
  support installation in LXC or any other
  container based solution." A virtual machine
  is fine (performance caveats apply); a
  container is not.
- **Another base.** One Debian release per
  OMV version, no Ubuntu, no Debian Testing.
  The FAQ promises a broken web interface to
  anyone who tries.
- **A desktop on the same machine.** The
  package conflicts with desktop login
  managers; the machine becomes a server.

**Check yourself:** before installing, you
should be able to point at one disk and say
"the system eats this one, and that's fine" —
and at the others and say "these are data".
If you can't, this chapter just saved you a
disk.
