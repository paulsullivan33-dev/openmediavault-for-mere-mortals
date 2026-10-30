# Sharing files — SMB and NFS

**The outcome:** your shared folders are
reachable from the other machines in the
house — Windows and Mac by SMB, Linux by
NFS — with guests kept exactly as far out as
you intended.

Both services follow the same pattern: turn
the service on and set its global options
once, then attach **shares** — one per
shared folder you want to serve. The service
pages live under **Services → SMB** and
**Services → NFS**, each split into
**Settings** and **Shares**.

## SMB/CIFS — the Windows and Mac way

SMB is the protocol Windows speaks natively
and macOS speaks happily; for most households
it is *the* file service. The backend is
Samba, and OMV's data model for it (read in
the lab) shows the honest essentials.

**Settings** worth knowing:

- **Workgroup** — `WORKGROUP` by default;
  matters mostly to older Windows browsing.
- **Home directories** — offer each user
  their OMV home (chapter 8) as a personal
  share, with its own options (browseable,
  recycle bin, macOS compatibility, inherit
  ACLs/permissions).
- **Time server** — let the NAS be the
  house's clock source for Windows clients.
- **Minimum protocol** and the free-text
  **extra options** box — the escape hatch
  for Samba settings OMV doesn't expose;
  chapter 11 explains why the box exists
  instead of editing `smb.conf`.

**Shares** — one entry per shared folder,
with (from the shipped model): the shared
folder itself, a comment, **guest access**
(none, guest-only, or guests allowed),
**read-only**, **browseable** (visible when
browsing the server, or hidden), a **recycle
bin** with size/age limits, **hide dot
files**, **inherit ACLs / inherit
permissions**, **hosts allow/deny** lists,
**audit** logging, **Time Machine** support
with a size cap (Apple backups over SMB are
a supported, sized thing), **transport
encryption**, and extra options.

Two behaviours to internalise:

- A share's read-only switch beats the
  folder's privileges: privileges 7 plus a
  read-only share is read-only (chapter 8's
  layer 3).
- Guest access is per-share and public
  means *public* — anyone on the network,
  no password. On a home network that is a
  choice; on anything else it is a leak.

From a client: Windows Explorer to
`\\<nas-name>\`, macOS Finder → Go → Connect
to Server → `smb://<nas-name>/`, logging in
with an OMV user (chapter 8), not `admin`,
not `root`.

## NFS — the Linux way

NFS exports folders to Unix-like clients,
identified by **machine**, not by user —
the protocol largely trusts the client to
say who it is, which is why NFS belongs on
trusted networks.

**Settings** is mostly an on switch and a
thread count (plus NFS version policy). The
**Shares** page is where an export is made:
pick the shared folder, then the **client**
— a single host, a network like
`192.168.1.0/24`, or a host name — and the
options, of which these matter:

- **Read-only / Read-write**, per export.
- **Squash** options — `root_squash` (the
  default sense) maps a client's root user
  to nobody on the server; `no_root_squash`
  believes a remote root, and should need a
  very good reason.
- **NFSv4:** OMV builds a **pseudo
  filesystem** — all exports appear under
  one root, implemented with bind mounts —
  so clients mount the server once and see
  the exports as subfolders. The docs' own
  warning bears repeating: because of the
  bind mounts, changing a shared folder's
  parent filesystem does not automatically
  fix the NFS view; re-save the share so the
  bind is remounted.
- The underlying file permissions and the
  shared folder's privileges still apply —
  NFS is not a way around chapter 8.

From a Linux client: `mount -t nfs
<nas>:/<export> /mnt/point` (for NFSv4, the
export path is relative to the pseudo-root),
or an `/etc/fstab` line with `nofail` so a
sleeping NAS never hangs a boot.

## Choosing

- Mixed house, phones, TVs, Windows, Mac:
  **SMB**, full stop.
- All-Linux, especially with real user IDs
  matched across machines: **NFS** is leaner.
- Serving the same folder by both is normal
  and supported — privileges are shared, so
  the rules stay in one place.

**Check yourself:** from a real client, open
the share as a real user, write a file, and
confirm it appears on the NAS under the
shared folder's `/srv/...` path — and that a
guest (or the wrong user) cannot.
