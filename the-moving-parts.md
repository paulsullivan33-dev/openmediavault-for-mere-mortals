# The moving parts

**The outcome:** when something in OpenMediaVault
behaves oddly, you can name which part is
responsible — the database, the engine, the
renderer, or the service itself.

Five parts do nearly all the work. This guide's
lab read each of them in the project's source
tree; the file names below are real, and the
excerpts are in `openmediavault-lab/`.

## Debian, underneath everything

The base system is Debian 13 with its ordinary
package manager. OMV's own package
(`openmediavault`, architecture-independent)
declares its world in its dependency list, and
the list is a fair portrait of the product: it
pulls in **Samba** (SMB sharing), the **NFS
kernel server**, **OpenSSH**, **rsync**,
**smartmontools** (disk health), **mdadm**,
**postfix** (mail for notifications), **monit**
(watching the services), **nginx** and
**PHP-FPM** (the web interface), **netplan**
(network configuration), and **Salt** (about
which more below). Its `Conflicts:` line names
seven desktop login managers — installing OMV
on a desktop Debian removes the desktop's
greeter. Chapter 1's claim, in the package's
own words.

## The configuration database

Every setting OMV knows lives in **one XML
file**: `/etc/openmediavault/config.xml`. The
project's FAQ calls it, plainly, "the database
configuration store file for openmediavault".

The shape of the data is defined by **data
models** — 71 JSON files shipped in
`/usr/share/openmediavault/datamodels/` — one
per kind of thing: a shared folder, an SMB
share, a user, a cron job, a filesystem mount.
A shared folder's model, for example, says it
has a `name`, a `mntentref` (which filesystem
it lives on), a `reldirpath` (the path relative
to that filesystem), and a list of `privileges`
— per user or group, one of **0** (no access),
**5** (read-only) or **7** (read/write). When
chapter 7 shows a shared folder, it is showing
this model wearing a web form.

A fresh system's database starts from a
template shipped in the package
(`usr/share/openmediavault/templates/config.xml`),
which is why some defaults — like automatic
security updates being on — are what they are.

## The engine

Behind the web pages sits a background daemon,
**omv-engined**. The workbench does not change
the system directly; it sends requests —
**RPC** calls, remote procedure calls — to the
engine, which reads and writes the database
and does the work. The engine's service list,
read from its source, is a table of contents
for the whole product: `smb`, `nfs`, `ssh`,
`rsync`, `sharemgmt`, `usermgmt`, `diskmgmt`,
`filesystemmgmt`, `smart`, `cron`, `apt`,
`pluginmgmt`, and the one this guide cares
about most, `config` — whose methods include
`applyChanges`, `isDirty` and `revertChanges`.
Chapter 11 is that service's story.

## The renderer: Salt

Changing the database does not change Samba.
Something must translate "shared folder
*Photos*, read-only for Sam" into Samba's own
configuration file, restart Samba, and do the
same for every affected service. That something
is **Salt**, a configuration-management tool
OMV drives through its `omv-salt` command. The
package ships one render stage per service —
`srv/salt/omv/deploy/` contains folders named
`samba`, `nfs`, `ssh`, `rsync`, `fstab`,
`nginx`, `postfix`, `smartmontools`, and some
forty more — each holding the templates and
rules for turning database entries into that
service's real configuration files and running
state.

This is why hand-editing `/etc/samba/smb.conf`
on an OMV box is a losing game: the file is a
*printout* of the database, reprinted whenever
the database changes. The project's FAQ says
it without blinking: OMV takes full control of
the services it manages and rewrites their
configuration files.

## The workbench

The web interface itself — OMV calls it the
**workbench** — is served by **nginx** and
**PHP-FPM** (Apache has not been the engine
since OMV 0.5). Even its menus are data: the
package ships one small file per menu item in
`usr/share/openmediavault/workbench/navigation.d/`.
The lab counted them: top-level sections
**Dashboard**, **System**, **Network**,
**Storage**, **Services**, **User Management**
and **Diagnostics**, with entries like
`storage.shared-folders` → "Shared Folders".
Chapter 5 walks that tree. Plugins add their
own files to the same folders, which is how
their pages appear in the same menus.

And around the edges: **monit** watches the
services and restarts or reports them, the
system's own **cron** runs scheduled jobs,
**collectd/rrdtool** draw the performance
graphs, and a set of `omv-*` commands
(chapter 13) gives the terminal access to the
same machinery.

**Check yourself:** a setting is wrong on the
running service but right in the panel —
which parts do you suspect? (The renderer and
the apply step: the database is the truth, the
service file is a printout, and chapter 11
shows how printouts get made.)
