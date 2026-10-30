# The command line

**The outcome:** the terminal stops being the
place you go when the panel fails, and becomes
what it is — the same machinery, without the
browser. You know the handful of `omv-*`
commands that matter and the rescue menu that
fixes lockouts.

OMV ships its tools as commands beginning
`omv-` in `/usr/sbin/`. The lab's list is from
the package itself (`openmediavault-lab/package-facts.txt`);
these are the ones a mere mortal will
actually meet:

- **`omv-firstaid`** — the rescue menu. Run
  it on the console or over SSH when the
  panel can't help you. Its modules (read
  from the package) are: configure network,
  configure time zone, configure the web
  interface, **reset the web interface
  password**, reset the failed-login counter,
  **restore a configuration backup**, fix the
  dirty-modules file, fix the statistics
  database, clean the package caches, and
  submit system info for support. The lost
  `admin` password — the classic lockout —
  is a two-minute job here, per the FAQ.
- **`omv-salt`** — the renderer's handle.
  `omv-salt deploy run <stage>` re-renders
  one part of the system from the database
  (chapter 11): `omv-salt deploy run samba`
  rewrites Samba's configuration and restarts
  it, exactly as Apply does for that module.
  The FAQ's own description: it is used "to
  pipe directives and values to service
  configuration files". Tab-completion lists
  the stages.
- **`omv-confdbadm`** — the database's own
  tool: `read`, `exists`, `update` and
  friends against `config.xml`, addressed by
  data-model ID (for example
  `omv-confdbadm read conf.service.smb`).
  This is the supported way to *inspect* what
  OMV believes, and — carefully, with the
  apply model in mind — to change values the
  panel doesn't expose. Its `populate`
  command fills in defaults after a manual
  install (chapter 4).
- **`omv-upgrade`** — apply all package
  updates from the terminal (chapter 12's
  Updates page without the browser).
- **`omv-rpc`** — call the engine directly:
  `omv-rpc -u admin 'Service' 'method' '{...}'`.
  Everything the workbench does goes through
  this door; it is how scripts — and the
  curious — ask the system questions the
  panel doesn't display.
- **`omv-sysinfo`** — a structured report of
  the whole system, the thing to attach to a
  support request (firstaid can submit it).
- **`omv-env`** — show and set the `OMV_*`
  environment defaults that steer the Salt
  templates (chapter 11's customisation
  layer).
- Supporting cast: `omv-mkworkbench` (rebuild
  the panel's assembled files after plugin
  surgery), `omv-mkaptidx` (rebuild the
  package index behind the Plugins page),
  `omv-mkrrdgraph` (the statistics graphs),
  `omv-sfsnapadm` (shared-folder snapshots),
  `omv-btrfs-scrub` / `omv-btrfs-dfree`
  (Btrfs housekeeping) and `omv-run` (run a
  command in OMV's environment).

## The troubleshooting short list

Most "OMV is broken" moments are one of
these, all met earlier in the guide:

- **"My change did nothing."** You didn't
  Apply — or the apply failed. Banner first
  (chapter 11); if the dirty state itself
  looks stuck, firstaid's *Fix dirty modules
  file*.
- **"I'm locked out of the panel."**
  `omv-firstaid` → reset the web interface
  password. Repeated failed logins lock the
  account; the same menu resets that counter.
- **"The delete button is grey."** Something
  still references the entry (chapter 7).
  Unlink it everywhere; there is no list of
  holders, by the project's own admission.
- **"My hand-edit vanished."** It was
  re-rendered (chapter 11). Put it in the
  extra-options box or the environment.
- **"A disk shows no SMART data."** USB
  enclosure or RAID controller hiding it
  (chapter 6); check `smartctl` by hand on
  the device to see whether the data exists
  at all.
- **"The panel shows pages for things I
  uninstalled"** (or missing fields): the
  workbench's assembled files are stale —
  the documentation's remedy is
  `omv-mkworkbench` and a cleared browser
  cache.
- **"GRUB failed during install."** The
  documented chroot-and-`grub-install`
  routine (chapter 4).

And the standing rule for all of it: change
things through the panel or the database
tools, let the renderer write the files, and
keep the terminal for *looking* — `omv-rpc`,
`omv-confdbadm read`, the logs under
Diagnostics — whenever you can.

**Check yourself:** `omv-firstaid` opens its
menu and you can leave it without changing
anything; `omv-confdbadm read
conf.service.smb` prints what the panel
shows you, from the database itself.
