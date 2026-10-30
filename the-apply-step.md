# The apply step

**The outcome:** the yellow "pending changes"
banner stops being a nuisance and becomes
what it really is — the visible edge of the
system's design. You know what Apply does,
what Revert does, and why your hand-edits to
service files keep disappearing.

## The two-step life of every change

Every change in OpenMediaVault lives twice.

1. **Staged.** You edit a form and press
   Save. The engine writes the new values
   into the configuration database
   (`/etc/openmediavault/config.xml`) and
   marks the affected parts of the system as
   **dirty** — changed in the database, not
   yet real. The workbench raises the yellow
   banner: "You have to apply the changes in
   order for them to take effect."
2. **Applied.** You press **Apply**. The
   engine's `config` service (its methods,
   read in the lab, include `applyChanges`,
   `isDirty`, and `revertChanges`) runs the
   render: for each dirty module — Samba,
   NFS, fstab, nginx, shared folders, and
   the rest of the engine's module list — it
   calls **Salt** (`omv-salt deploy run
   <module>`), which regenerates that
   service's real configuration files from
   the database and restarts or reloads the
   service. The banner clears.

Or you press **Revert**, and the staged
changes are discarded — the database goes
back to describing the system as it
currently runs. Between Save and Apply, the
running system is *untouched*: a half-made
set of changes cannot break a working Samba
at 6 pm, because Samba does not know yet.
That is the entire point of the design, and
the reason for its one annoyance: a change
you saved and forgot to apply is a change
that does not exist, no matter what the
forms show.

Small print, honestly:

- Applying restarts real services. Applying
  a workbench setting restarts the web
  server — the documentation warns the
  resulting error popups "can be ignored".
  Applying an NFS or shared-folder change
  restarts those daemons. Apply at a moment
  when a few seconds' interruption is fine.
- Some changes are bigger than they look.
  The daily housekeeping even includes a
  cron job (`openmediavault-pending_config_changes`,
  in the package's cron list) that mails the
  admin about changes left pending — the
  system nags because unapplied changes are
  the number-one "OMV ignored me" report.
- **Dirty modules** are tracked in a file on
  disk; if it ever disagrees with reality
  (a crashed apply, a power cut mid-render),
  `omv-firstaid` has a menu entry, "Fix dirty
  modules file", for exactly that
  (chapter 13).

## Why hand-edits disappear

The render runs one way: database → files.
`/etc/samba/smb.conf`, `/etc/exports`,
`/etc/fstab`, the nginx and PHP configs —
all are *outputs*. Edit one by hand and it
will work, briefly, until the next apply
re-renders it from the database and your
edit evaporates. The project's FAQ, asked
directly why the system rewrites manually
edited files, answers that OMV "takes full
control of some system services" — Samba,
NFS's companions, monit, nginx, the network,
and more — and points at the supported ways
to customise:

- **Extra options boxes.** Many settings
  pages (SMB settings and shares, SSH, and
  others) end with a free-text field whose
  contents are merged into the generated
  configuration. This is the front door —
  use it first.
- **Environment variables** for the renderer
  itself: OMV's Salt templates honour a set
  of `OMV_*` defaults (adjustable with
  `omv-env`) for values the panel doesn't
  expose.
- **Your own Salt states / config snippets**
  in the places OMV reserves for them, for
  the genuinely advanced — accepting that
  you now co-own the rendering.

What you may edit freely: anything OMV does
not manage. It is a Debian system; your own
scripts, your own nginx site elsewhere on
the box (the FAQ even explains adding one
without touching OMV's), your own cron jobs
outside its database — all fine. The line is
ownership, and `omv-confdbadm` (chapter 13)
lets you inspect the database to see what
OMV thinks it owns.

## A worked example, end to end

You add Sam to the *Photos* folder's
privileges as read-only. Save: `config.xml`
gains a `<privilege>` entry — user Sam,
perms 5 — and the `samba` and `sharedfolders`
modules are marked dirty; the banner appears.
Samba is still serving the old rules. Apply:
Salt re-renders `smb.conf` from the database,
the shared-folder permissions are laid down,
Samba reloads. Now Sam is read-only, the
banner is gone, and if you hand-edit
`smb.conf` to make Sam writable, the next
apply — for any reason, weeks later — will
quietly take it away again.

**Check yourself:** make a harmless change
(an SMB server description string), Save,
confirm the banner appears and the running
service does *not* show the change yet; then
Apply and watch it land. You have now seen
the whole machine work once, on purpose.
