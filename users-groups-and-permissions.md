# Users, groups and permissions

**The outcome:** the people in your household
exist as users on the box, grouped sensibly,
and you understand the two permission layers
that decide what each of them can touch.

Everything here is under **User Management** —
**Users**, **Groups**, and **Settings**.

## Users

A user in OMV is a real Linux user underneath
(the engine's `usermgmt` service wraps the
system's own user database), created so the
*services* can tell people apart. Creating
one asks for:

- **Name and password** — the credentials
  they will use for SMB, FTP and the like.
  These are *not* the `admin` workbench
  password, and not `root`.
- **Email** — used by notifications and by
  some plugins; optional but worth setting.
- **Groups** — see below.
- **Shell** — most users should keep the
  no-login shell OMV offers by default; they
  are file users, not terminal users.
- **SSH public key / password** — for the
  few users who will use SSH itself
  (chapter 10).

Users can also be **imported** in bulk from
a simple text format, for the rare household
that needs forty accounts at once.

By default a user logging into the
**workbench** sees only their Account pages —
their own password and email. That is a
feature: the file users of the house do not
need the machine's controls. Two group
memberships change a user's powers, and both
deserve respect:

- **`openmediavault-admin`** — full workbench
  administrator, equal to `admin`.
- **`sudo`** (and **`_ssh`**) — the route to
  real system power over SSH: log in as
  yourself, then `sudo su`. The installation
  documentation recommends exactly this as
  the replacement for direct root SSH.

## Groups

Groups are how you avoid editing privileges
one person at a time. Create `family`,
`kids`, `media-managers` — whatever matches
your house — under User Management → Groups,
and put users in them. Shared-folder
privileges (chapter 7) accept groups as well
as users, so "the family group may write to
Photos" is one line, maintained in one place
when people arrive or leave. OMV creates
some groups of its own (`users` is the one
you will meet in folder ownership; the
service groups like `_ssh` appear as needed).

## Settings — home directories

User Management → Settings holds one switch
with long consequences: **user home
directories**. Enabled, and pointed at a
shared folder, each user gets a private home
folder there — used by SSH sessions, by the
SMB "homes" feature (chapter 9 offers each
user their home as a personal share), and by
services that need somewhere per-person to
stand. Decide early; moving homes later is
manual work.

## The two layers, again — and a third

Access trouble on an OMV box is almost
always one of these, in order:

1. **Privileges** — the shared folder's own
   list (chapter 7): per user/group,
   read/write (7), read-only (5), or nothing.
   The services enforce this.
2. **Filesystem permissions** — the actual
   directory mode and ownership (`root:users`,
   `2775` by default on new shared folders),
   plus ACLs if you set them. The operating
   system enforces this, underneath the
   services.
3. **The service's own settings** — an SMB
   share can be read-only even for people
   with write privileges, a guest setting can
   widen or break things, an NFS export has
   its own client rules (chapter 9).

Both layers must permit the access for it to
happen — the shared-folders documentation
says it exactly: "the permissions must be
sufficient on both the service level and the
filesystem level".

One more boundary worth stating: OMV users
are **this machine's** users. There is no
built-in directory service, no joining a
Windows domain as a member with central
accounts in the base product — for a home or
small office, the list you keep here *is*
the directory. Keep it short, keep passwords
real, and remove people who leave.

**Check yourself:** log in to the workbench
as a normal user — you should see only the
Account pages; then confirm that user's SMB
access (next chapter) matches their
privileges, not their workbench rights.
