# Shared folders

**The outcome:** you understand the idea the
whole system is built around, and you can
create a shared folder with the right people
on its access list.

The documentation opens its Shared Folders
page with the sentence this chapter is built
on: "The shared folders are the key
functionality in openmediavault around which
all services revolve."

A **shared folder** is a folder that OMV
knows about by name. Creating one registers
four things (the lab read the data model,
`openmediavault-lab/sharedfolder-datamodel.json`):

- a **name** — `Photos`, `Media`, `Documents`;
- a **filesystem** — which mounted disk it
  lives on (the model's `mntentref`, an
  internal reference to the mount entry);
- a **relative path** on that filesystem —
  creating `Photos` at the default path makes
  a real directory like
  `/srv/dev-disk-by-label-DATA/Photos`;
- a **privileges list** — which users and
  groups may use it, and how much.

Services never point at raw paths. An SMB
share, an NFS export, an FTP account and a
backup job all point at the *shared folder*,
by its internal ID. Move the folder to a
bigger disk later — edit its filesystem in
one place, and every service follows (though
note: editing the parent device does **not**
move the data for you; you copy that yourself,
and the NFS chapter of the docs warns its
bind mounts need re-checking afterwards).

On a **Btrfs** filesystem a shared folder is
created as a **subvolume** instead of a plain
directory, which buys snapshots: take one
manually, or schedule them under the shared
folder's snapshot options, with a lifecycle
(how many to keep, for how long). On other
filesystems it is an ordinary directory —
created with `root:users` ownership and mode
`2775` by default, setgid so new files inside
inherit the `users` group.

## Creating one

**Storage → Shared Folders → Add:**

- **Name** — pick the name your household
  will recognise; typing it fills the path
  with the same name.
- **File system** — the mounted data disk
  from chapter 6. The system disk is never
  offered (unless the ShareRootFs plugin,
  chapter 12, deliberately breaks that rule).
- **Path** — relative to the disk's root;
  `/` shares the whole disk, which makes
  tidier people than this guide nervous.
- **Permissions** (the filesystem mode for
  the new directory) — a plain-language list
  from "Administrator: read/write, Users:
  read/write, Everyone: read-only" (mode
  **775**, the default) down to "Everyone:
  read/write" (**777** — almost never what
  you want) or up to administrator-only
  (**700**).

Save, then **Apply** (chapter 11). The folder
now exists on disk *and* in the database.

## Privileges — the folder's own access list

Select the folder and press **Privileges**
to set, per user and per group, one of three
levels — stored in the model as numbers that
mirror Unix permissions: **7** read/write,
**5** read-only, **0** (or simply not listed)
no access. This is *service-level* access:
chapters 9 and 10's services consult it when
deciding who gets in.

Two layers, both must say yes — remember
this when access fails mysteriously:

1. **Privileges** (this list) govern what the
   *service* allows.
2. **Filesystem permissions** (the directory's
   mode, ownership, and any ACLs) govern what
   the *operating system* allows.

The form for layer 2 also lives here: the
**ACL** button opens a window with a file
browser for the folder, the current POSIX
permissions in friendly form, and optional
fine-grained access control lists applied
with `setfacl`. The documentation's advice
is sound: the average home user does not
need ACLs, and the **Reset Permissions**
tool — which recursively re-applies standard
permissions — fixes most "it worked before
I tidied" accidents better than hand-tuning.

## The greyed-out delete button

You cannot delete a shared folder that any
service is still using, and OMV will not tell
you which service is holding it — the FAQ
admits "there is no internal database backend
that can display information about which
service is holding which shares". The delete
button simply stays grey until you have
unlinked the folder from every SMB share,
NFS share, rsync job, plugin page and user
home setting that references it. The same
rule protects filesystems: a filesystem with
shared folders on it cannot be unmounted
from the panel.

## A sane starter set

For a household box, four shared folders
cover most lives: `Media` (read-only for
most, read/write for whoever curates it),
`Photos` (read/write for the family group),
`Documents` (private per the privileges
list), and `Backups` (write for the backup
jobs, read for almost nobody). Everything
else in this guide — users, shares, jobs —
hangs off folders like these.

**Check yourself:** the folder exists in
Storage → Shared Folders, exists on disk at
its `/srv/...` path, and its Privileges list
names exactly the people you intended — no
more.
