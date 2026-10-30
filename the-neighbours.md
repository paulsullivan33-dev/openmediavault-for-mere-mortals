# The neighbours

**The outcome:** you can say when
OpenMediaVault is the wrong tool — because
the honest answer to "which NAS software?"
is "it depends what you are optimising for".

## TrueNAS

The other famous free NAS system, and the
comparison everyone makes. TrueNAS is an
**appliance built around ZFS**: the storage
pool comes first, the interface is polished
and more guided, and the whole product is
aimed at people who want the filesystem to
be the star. Its costs: heavier hardware
expectations (ZFS rewards RAM), a more
opinionated way of doing everything, and a
commercial sibling (TrueNAS Enterprise) that
the free version visibly orbits. OMV's
counter-virtues: it runs happily on small
and odd hardware, the base is *just Debian*
(every skill you have transfers), filesystems
are your choice per disk rather than one
pool religion, and there is no commercial
variant to be steered toward — the FAQ
makes a point of it ("you are not a beta
tester for a commercial variant"). Choose
TrueNAS when ZFS *is* the requirement;
choose OMV when the machine is small, the
disks are mixed, or you want Debian with a
panel rather than an appliance.

## A bought appliance — Synology, QNAP

A Synology or QNAP box is OMV's problem
solved with money: quiet, tiny, warrantied,
with a slicker interface and phone apps, and
a vendor who answers (sometimes) when it
breaks. The costs are the hardware premium,
drives and features fenced by the vendor's
choices, a software lifecycle the vendor
ends on its schedule, and years of security
headlines about internet-facing NAS
appliances being ransomed — a risk class
that exists for any NAS you expose to the
internet, and the reason this guide never
suggests doing so. Choose the appliance when
your time is worth more than the premium and
backups-of-the-NAS are someone else's
problem. Keep the receipt either way.

## Plain Debian with Samba, no panel

Everything OMV does can be done by hand on
the same Debian: install Samba, edit
`smb.conf`, manage users with `useradd`.
What you lose is the *system* of it — the
one database, the staged apply, the
privileges model that all services share,
the rescue menu, the update and notification
plumbing, and the property that a beginner
can re-derive the whole configuration from
one panel. What you gain is total control
and nothing re-rendering your edits. This
guide's own Samba and NFS volume teaches
the by-hand route; OMV is for people who
would rather the machine kept its own
notes. (Chapter 11's warning applies in
reverse here: on a hand-run box, *you* are
the apply step.)

## Proxmox, with OMV as a guest

If the machine also needs to be a small
homelab's virtualisation host, the common
pattern is Proxmox underneath and OMV as
one virtual machine among several, with the
data disks passed through to it. OMV is a
model guest (a VM is fully supported, unlike
a container), and Proxmox has its own guide
in this series. The honest cost: two
systems to maintain, and storage questions
(which layer owns the disks?) that this
guide's single-machine story avoids.

## When OMV is simply the wrong tool

- You need it **in a container**: unsupported,
  full stop.
- Your base must be **Ubuntu or another
  Debian release**: each OMV version is
  welded to one Debian (chapter 3).
- You want a **directory service / domain**
  at the centre: OMV's user list is local by
  design.
- The job is **object storage, a media
  empire, or a hundred users**: OMV can host
  pieces of those as plugins, but it stops
  being the simple answer.
- Nobody will **maintain it**: a NAS is a
  small server with big responsibilities —
  updates, disk health, backups. An
  unmaintained OMV is worse than a boring
  external drive with a backup schedule.

**Check yourself:** you can name the axis —
ZFS-first, appliance convenience, total
hand control, virtualisation, or small
Debian with a panel — on which your own
choice actually turns.
