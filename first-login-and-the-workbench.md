# First login and the workbench

**The outcome:** you can find your way around
the web interface — the **workbench** — and
you know which parts of it are the machine's
controls and which are just its instruments.

## The dashboard

You land on the **Dashboard**: a wall of
widgets about the machine's health. The
widgets are not decorative choices made by a
designer somewhere; the lab read their
definitions in the package
(`workbench/dashboard.d/`), and the set is:
system information, system time, uptime,
system updates waiting, CPU and memory and
load graphs, network interfaces, file systems
and their status, services status, SMART
status, and disk temperatures. You can add,
remove and rearrange them from the dashboard's
own settings — a new admin usually checks
*System Updates* and *File Systems Status*
first, because those two complain earliest
when something needs doing.

Across the top of every page: the machine's
name, a bell for notifications, and — when you
have made changes that have not been applied
yet — a **yellow banner** telling you so.
That banner is the most important pixel in
the interface; chapter 11 is devoted to it.

## The menu tree

The left-hand menu, exactly as shipped (the
lab extracted it from the package's own
navigation files, `openmediavault-lab/navigation-tree.txt`):

- **System** — Workbench settings, Date &
  Time, Monitoring, Power Management,
  Notification, Certificates, Cron (scheduled
  jobs), Update Management, Plugins.
- **Network** — General, Interfaces, Proxy,
  Firewall. (Service Discovery lives here in
  the documentation's tree as well.)
- **Storage** — Disks, File Systems, SMART,
  Shared Folders.
- **Services** — SMB, NFS, Rsync, SSH. (Only
  these four are in the core; plugins add
  their own entries — FTP, for instance,
  appears here once its plugin is installed.)
- **User Management** — Users, Groups,
  Settings (home directories live here).
- **Diagnostics** — System Information,
  Performance Statistics, Processes, Services,
  System Logs, Report.
- **Account** (under your name) — your own
  password and settings, the only pages a
  non-admin user can reach.

Settings pages follow one rhythm everywhere:
change fields, press **Save**, and the change
is *staged* — not live — until the yellow
banner's **Apply** button is pressed. Lists
(shared folders, users, shares) are tables
with **Add / Edit / Delete** buttons above
them. Delete buttons sometimes stay greyed
out with no explanation; chapter 7 explains
the usual reason (something else is still
using the entry).

## Who you are when you log in

There are two kinds of workbench account,
and confusing them causes real grief:

- **`admin`** is the web interface's own
  administrator — the only built-in account
  that sees the whole menu. It is *not* the
  Linux `root` user, and its password is set
  inside OMV (and reset with `omv-firstaid`,
  chapter 13).
- **Users you create** (chapter 8) can log in
  to the workbench too, but by default see
  only their own Account pages — password and
  email. They exist mainly so the *services*
  (SMB, FTP, and friends) can recognise them.
  A user joins the `openmediavault-admin`
  group to become a full workbench
  administrator like `admin`.

There is no "log in as root to the web
interface", by design.

## First five minutes, in order

1. System → Workbench: optionally change the
   panel's port or force HTTPS later; leave
   it alone for now.
2. System → Date & Time: time zone correct,
   "Use a NTP server" on.
3. System → Notification: set up email
   (chapter 12) *before* a disk fails, not
   after.
4. Storage → Disks: confirm every disk you
   expect is listed (chapter 6).
5. Change root's SSH exposure per chapter 4,
   once your own user exists (chapter 8).

**Check yourself:** you can name where you'd
go to (a) see whether updates are waiting
(Dashboard widget, or System → Update
Management), (b) find a disk's health
(Storage → SMART), and (c) apply a staged
change (the yellow banner, anywhere).
