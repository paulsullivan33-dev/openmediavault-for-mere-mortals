# SSH, rsync and FTP

**The outcome:** you can log into the box
itself safely, copy files to and from it on a
schedule, and you know the truth about FTP in
OMV 8 — it is a plugin now.

All three live under **Services**.

## SSH — the front door with a key

SSH is how administrators reach the machine
itself: a terminal, as a real user, with the
machine's full power one `sudo` away. **Services
→ SSH** offers: the port (22 by default),
**Permit root login** (on at install time so
a headless box is reachable — chapter 4 said
to turn it off once a normal user is in the
`_ssh` and `sudo` groups; do it),
**password authentication** (turn off once
keys work), **public key authentication**,
TCP forwarding, compression, and an extra
options box.

House rules that travel well:

- Keys beat passwords. A user's public key
  goes on their account (chapter 8); then
  password authentication can go off and the
  internet's password-guessing noise becomes
  irrelevant.
- The user who can SSH is in `_ssh`; the
  user who can administer is also in `sudo`.
  Everyone else is a file user and never
  sees a shell (their account's shell is a
  no-login shell by default).
- SSH is also the transport under other
  tools: `scp`, `sftp`, and rsync-over-SSH
  below all ride through this one door and
  this one set of settings.

## Rsync — copies on a schedule

OMV carries rsync in both directions of its
life (the engine has separate `rsync` and
`rsyncd` services):

- **Tasks** (Services → Rsync → Tasks):
  scheduled copy jobs — a shared folder as
  source or destination, a local path or a
  remote machine (over SSH, or to another
  rsync server), with the classic switches:
  recursive, preserve times/permissions,
  compress in transit, **delete** files at
  the destination that vanished at the
  source (powerful, dangerous, off unless
  you mean it), dry-run for rehearsing a job
  before trusting it, and email on failure.
  This is the tool most OMV backups are
  actually made of (chapter 12).
- **Server** (Services → Rsync → Server):
  OMV as an rsync *daemon*, publishing
  **modules** (named shares) that other
  machines' rsync clients can pull from or
  push to, optionally with per-module users.
  Useful for fast, unauthenticated-by-design
  copies inside a trusted network; on
  anything less trusted, prefer rsync over
  SSH.

Jobs are cron-driven underneath (the
configuration model is a general `cron.job`,
and System → Cron shows the same machinery),
so "every night at 2" is a native idea here.

## FTP — a plugin since OMV 8

Older guides show FTP in the core Services
menu. In OMV 8 it is not there: the lab found
no FTP service in the shipped product — no
data model, no engine service, no menu item —
because FTP moved into the official **FTP
plugin** ("Provides a modular FTP/FTPS
server", built on ProFTPD, the daemon the
project's FAQ has always named). Install it
from System → Plugins (chapter 12), and a
Services → FTP page appears with the expected
shape: settings (port, passive port range,
anonymous login switch) and shares bound to
shared folders, honouring the same privileges
as every other service.

Plain advice about FTP itself:

- Plain FTP sends passwords **unencrypted**.
  On a home LAN, tolerable; anywhere else,
  use the plugin's **FTPS** (FTP over TLS,
  with a certificate from System →
  Certificates), or skip FTP and use SFTP —
  which is not FTP at all, but the file
  transfer built into SSH, already running.
- **Anonymous login** exists for genuinely
  public files. If you have to think about
  whether your files are public, they are
  not.

**Check yourself:** SSH in as your own user
(not root), run one rsync task in dry-run
mode and read its file list before letting
it copy for real, and confirm plain-FTP
access is either off or knowingly confined
to your LAN.
