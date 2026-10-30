# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   The lab's example users and folders are stock household
   fixtures.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 15 names what the tool
   cannot do: one welded Debian base, no container installs,
   a system disk that holds no data, configuration files it
   re-renders over hand edits, no regular config backup/restore
   procedure, RAID and FTP as plugins rather than core, and
   hardware truth limited by Debian and smartmontools.
8. **The ethics rule is in the text, early.** A NAS holds other
   people's files as readily as your own, so chapter 1 and the
   sharing chapters state the line plainly: guest access is a
   deliberate, named choice, never a default to drift into;
   other people's access is granted by name, through the
   privileges list, and can be removed the same way.
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

**Nothing was installed, and this section exists to make sure
nobody mistakes this guide for one whose lab ran the product.**
Two independent reasons, both stated in the README: the current
release, openmediavault 8 "Synchrony", runs only on Debian 13
"Trixie", and this lab machine is a shared Ubuntu sandbox; and
installing OMV reconfigures its host (its package conflicts with
desktop login managers and its dependencies take over nginx,
Samba, networking and more), which would break the machine for
the other work sharing it. Running the installer or an apt
install was therefore not merely impossible here but out of
bounds.

What the lab did instead is a **dissection**, with the full
record in `openmediavault-lab/evidence.txt`:

- **The package's own source tree** — the files the
  `openmediavault` Debian package is built from, read from the
  project's GitHub repository: branch `master` at commit
  `9d8530ffa6912ece116bc6c74d0f3d06045b6c26` (changelog head
  `8.6.1-1`, the 8.6 line in development; the current release,
  per the project's website on the same day, is **8.6.0**), with
  the `7.x` branch (changelog `7.7.24-7`) read to pin down what
  changed between OMV 7 and 8. Read closely: `debian/control`
  (the full dependency list and the desktop conflicts), the
  `usr/sbin/` command set, all 71 JSON **data models** in
  `usr/share/openmediavault/datamodels/`, the database template
  `templates/config.xml`, the engine's RPC and module lists
  (including `config.inc`'s `applyChanges`, `isDirty`,
  `revertChanges`), the Salt stage tree under
  `srv/salt/omv/deploy/` (some forty per-service stages), the
  workbench's 74 navigation files and its dashboard widget
  definitions, the `mkfs.d` filesystem list (btrfs, ext4, f2fs,
  jfs, xfs), and the omv-firstaid module list — whose restore
  module confirmed that configuration backups are timestamped
  siblings of `config.xml` in `/etc/openmediavault/`.
- **The official install script** (OpenMediaVault-Plugin-Developers'
  installScript, master, fetched 2026-10-30): its help text
  ("This script will always install: OMV 8.x (synchrony) on
  Debian 13 (Trixie)"), its codename gate — quoted verbatim in
  `openmediavault-lab/install-script-debian-gate.txt`, refusing
  anything but `trixie` and any architecture but amd64/arm64 —
  its repository and keyring steps, its removal of
  network-manager/dhcpcd5/cloud-init, and its finishing
  `omv-salt deploy run systemd-networkd` and reboot.
- **The package repository**: `packages.openmediavault.org`
  now redirects to `packages.openmediavault.io` and onward to
  `openmediavault.github.io/packages/`. Through this lab's HTTP
  proxy the pool and index files returned the host's 404 page
  and a sandboxed `apt-get update` timed out, so **the .deb
  files themselves were not downloaded**; the dissection used
  the source tree above, which is the same content in the form
  the project publishes. The pool's published version list
  (search index) showed the OMV 7 line ending at 7.7.24-1,
  consistent with OMV 7's end-of-life in June 2026.
- **The official documentation** at docs.openmediavault.org,
  8.x tree, read 2026-10-30: releases table (8.0 Synchrony on
  Debian 13, stable December 2025; 7.0 Sandworm, EOL June
  2026), prerequisites, installation variants and the ISO
  page's warnings, workbench/storage/shared-folder/services/
  users administration pages, the official plugin list, and
  the FAQ.

Cross-checks that changed the text:

- The documentation's Shared Folders page prints a
  `<sharedfolder>` XML example; the shipped datamodel defines
  exactly those fields, with privileges restricted to
  0, 5 and 7. The guide teaches the model the product ships.
- The documentation still shows a core **RAID** page and a
  core **FTP** chapter. The shipped product has neither in its
  navigation, data models or engine services; the project's
  own plugin list carries "MD" (RAID) and "FTP" as official
  plugins. Chapters 6 and 10 follow the product and say why.
- The install script's target (OMV 8, Debian 13) matches the
  documentation's releases table and the website's current
  version, 8.6.0. An early finding that "OMV 7 is current" —
  from a cached package listing — was wrong and was corrected
  from these primary sources before writing.

Documented only — everything about the running product,
labelled as such in the text: the installation itself on both
routes, the workbench's appearance and behaviour beyond what
its shipped definition files state, all service behaviour,
all performance, the OMV 7 → 8 upgrade experience, and every
neighbour in chapter 14 (characterised, not run). No
screenshots exist; no output is quoted that did not come from
a file named above.

Fixtures are generic: shared folders named Media, Photos,
Documents and Backups, and household users referred to by
first names only in examples — no real person, network or
organisation is modelled.
