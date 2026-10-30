# Glossary

**ACL (access control list)** — fine-grained
permission entries on a file or folder, beyond
the usual owner/group/others. OMV applies
them with `setfacl`, from the shared folder's
ACL window.

**Apply** — the workbench action that turns
staged database changes into running system
changes, via Salt. The yellow banner means
changes are waiting for it.

**CMR / SMR** — two ways hard drives record
data: Conventional Magnetic Recording, and
Shingled Magnetic Recording (overlapping
tracks, slower rewrites). CMR is preferred
for RAID and parity work.

**Configuration database** — OMV's single
store of settings: the XML file
`/etc/openmediavault/config.xml`, shaped by
the data models.

**Data model** — a JSON definition of one
kind of configuration entry (a shared folder,
an SMB share, a user…): its fields, types and
allowed values. OMV 8 ships 71.

**Data drive / system drive** — OMV's two
disk roles: disks that hold your files, and
the one disk that holds Debian and OMV and
may not hold shared data.

**Dirty module** — a part of the system whose
database settings have changed but have not
yet been applied. The banner counts these.

**Engined (omv-engined)** — the background
daemon that receives the workbench's RPC
calls, maintains the database, and runs the
apply machinery.

**Extra options** — free-text fields on many
settings pages whose contents are merged
into the generated service configuration.
The supported way to customise beyond the
panel.

**First aid (omv-firstaid)** — the console
rescue menu: reset the admin password,
restore a configuration backup, fix the
dirty-modules file, and more.

**Guest access** — an SMB share mode that
lets people in without an OMV account.
"Guests allowed" and "guest only" differ;
both are public within the network.

**LVM** — the Linux Logical Volume Manager:
volume groups and resizable logical volumes
over physical disks. In OMV 8, the LVM2
plugin.

**MD / mdadm** — Linux's software RAID layer
and its management tool. In OMV 8, managed
through the official MD plugin.

**NAS** — network attached storage: a machine
whose job is holding files for a network.
The box this guide builds.

**NFS** — Network File System: the Unix-style
file-sharing protocol, trusting clients by
machine rather than by user. NFSv4 serves all
exports under one pseudo-root via bind
mounts.

**OMV-Extras** — the community-run third-party
plugin repository (omv-extras.org): Docker
Compose, ZFS, SnapRAID, Mergerfs and more.
Separate project, separate trust decision.

**Plugin** — an extra Debian package that adds
pages, database models and Salt stages to
OMV. Official ones install from System →
Plugins; the rest come from OMV-Extras.

**Privileges** — a shared folder's own access
list: per user or group, read/write (7),
read-only (5), or no access (0). Enforced by
the services, on top of filesystem
permissions.

**Pseudo filesystem (NFSv4)** — the single
virtual root under which all NFSv4 exports
appear to clients.

**RAID** — combining disks for redundancy
(or speed): levels 0, 1, 5, 6, 10. Not a
backup.

**Revert** — discarding staged changes,
returning the database to match the running
system.

**RPC** — remote procedure call: the
workbench's way of asking the engine to do
something. `omv-rpc` calls it from the
terminal.

**Rsync** — the file-copy tool OMV builds
scheduled tasks and a server mode around.
Copies changes, not whole files, by default.

**Salt** — the configuration renderer OMV
drives with `omv-salt`: it turns database
entries into services' real configuration
files and restarts them.

**Shared folder** — OMV's central object: a
named folder on a chosen filesystem, with a
privileges list, that services share by
reference.

**SMART** — drives' self-reported health
system (attributes, temperatures, self-tests),
read by smartmontools under Storage → SMART.

**SMB/CIFS** — the Windows-native file-sharing
protocol ("Samba" is the Linux server that
speaks it). The default choice for mixed
networks; also carries Time Machine targets.

**Squash (NFS)** — mapping a client's users
to less powerful server-side identities;
`root_squash` turns a client's root into
nobody.

**Workbench** — OMV's name for its web
administration interface, and for the System
page that configures it.

**ZFS** — a combined filesystem and volume
manager with pools, datasets and snapshots.
On OMV it arrives via OMV-Extras, not the
core.
