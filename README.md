btrfs-snappy
============

btrfs-snappy smartly creates and maintains a history of snapshots of BtrFS filesystems by taking a snapshot of a BtrFS mountpoint or subvolume when there is new or changed data in your files since the last snapshot. It will NOT take a new snapshot when there are only deleted or renamed files/folders since the last snapshot.

Installation:
-------------
Place btrfs-snappy in /usr/local/sbin and make it executable, "chmod +x /usr/local/sbin/btrfs-snappy"

Features:
---------

* only takes snapshots when there are important changes to your files (changed data in your files or the creation of non-empty new files, but not deleted or renamed files/folders).  In other words, it only takes a snapshot when there are changes to one or more data blocks irrespective of any changes to any inode(s).
* can snapshot inside of the filesystem or rooted in a base directory.
* can purge old snapshots when too many exist.
* can keep multiple snapshot schedules for the same filesystem.

btrfs-snappy is designed to be called from cron on a regular schedule.

Usage:
------

btrfs-snappy [-r] [-b basedir] mountpoint prefix count

* mountpoint is the filesystem to snapshot
* prefix is the prefix, which usually corresponds to a schedule (e.g. daily, weekly, monthly, etc.)
* count is the number of snapshots with the same prefix to keep
* -r makes the snapshot readonly (requires btrfs-tools v0.20)
* -b basedir places the snapshot in a like-named directory rooted in the basedir.  Without -b, snapshots are placed in a directory called .snapshots at the top of the mountpoint

mountpoint must be a mounted btrfs filesystem (i.e. appears in the output of
"mount -t btrfs") or an unmounted btrfs subvolume (which must exit 0 when
"btrfs su sh" is called on it).  Unmounted subvolumes require a recent
version of btrfs-progs; see "OS Support" below for specifics.

Cron Examples:
--------------

```cron
*/5   *   *   *   *   btrfs-snappy -r -b "/Snapshots/" "/My Documents" hourly 30;
0     0   *   *   *   btrfs-snappy -r -b /Snapshots /home daily 7;
```

The above cronjob would

* Assess whether to create a snapshot of "/My Documents" every 5 minutes, taking the snapshot only when there is changed data, keeping 30 generations in "/Snapshots/My Documents"
* Assess whether to create a snapshot of /home every day, taking the snapshot only when there is changed data, keeping 7 generations in /Snapshots/home

OS Support
----------

Tested on

* Fedora 21/22
* CentOS 7

License
-------

This program is distributed under the GNU General Public License

http://www.gnu.org/licenses/gpl.txt

Authors
-------

Originally by Birger Monsen <birger@birger.sh>

Readonly and basedir additions by James FitzGibbon <james@nadt.net>

VFS snapshot naming support by gitmopp (https://github.com/gitmopp)

Support for snapshotting unmounted btrfs subvolumes by Brian Kloppenborg (https://github.com/bkloppenborg)

Check for changes between current mountpoint or subvolume and last snapshot by artfulrobot (http://serverfault.com/users/96883/artfulrobot)

Take snaphots only when there are new or changed files, but not deleted or renamed files/folders by Mr. Carriere (<redwoodcare@hotmail.com>)
