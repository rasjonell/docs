---
layout: default
description: Introduction to ArangoDB's backup feature.
title: ArangoDB Backup and Restore Feature
---
Introduction
============

{% hint 'info' %}
Please review the [requirements and limitations](hot-backup-restore-limitations.html)
of backups, specifically regarding storage engine, deployment, scope
and storage space.
{% endhint %}

Creation
--------

The process of creating backups is ideally an instantaneous event during
normal operations, that consists of a few subsequent steps behind the scenes:

- Stop all write accesses to the entire installation using a write transaction lock.
- Create a new local directory under `<data-dir>/backups/<timestamp>_<backup-label>`.
- Create hard links to the active database files in `<data-dir>` in the newly
  created backup directory.
- Release the write transaction lock to resume normal operation.
- Report success of the operation.

The above quite precisely describes the tasks in a single instance installation
and could technically finish in under a millisecond. The unknown factor above is
of course, when the backup process is able to obtain the write transaction lock.

When considering the ArangoDB cluster two more steps need to integrate while
others just become slightly more exciting. On the coordinator tasked with the
backup the following is done:

- Using the agency, make sure that no two backups collide.
- Obtain a dump of the Agency's `Plan` key.
- Stop all write access to the **entire cluster** installation using a
  global write transaction lock, this amounts to get each local write
  transaction lock on each DBserver, all at the same time.
- Getting all the locks on the DBservers is tried using subsequently growing
  time periods, and if not all local locks can be acquired during a period,
  all locks are released again to allow writes to continue. If it is not
  possible to acquire all local locks in the same period, and this continues
  for an extended, configurable amount of time, the coordinator gives
  up. With the `forceBackup` option set to `true`, it proceeds instead
  to create a potentially non-consistent backup.
- **On each database server** create a new local directory under
  `<data-dir>/backups/<timestamp>_<backup-label>`.
- **On each database server** create hard links to the active database files
  in `<data-dir>` in the newly created backup directory.
- **On each database server** store a redundant copy of the above agency dump.
- Release the global write transaction lock to resume normal operation.
- Report success of the operation.

Again under good conditions, a complete backup could be obtained from a
cluster with many database servers within a very short time in the range
of that of the single server installation.

### The Global Write Transaction Lock

The global write transaction lock mentioned above is such a determining factor,
that it needs a little detailed attention. 

It is obvious that in order to be able to create a consistent snapshot of the
ArangoDB world on a specific single server or cluster deployment, one must
stop all transactional write operations at the next possible time or else
consistency would no longer be given.

On the other hand it is also obvious, that there is no way for ArangoDB to
known, when that time will come. It might be there with the next attempt a
nanosecond away, but it could of course not come for the next 2 minutes.

ArangoDB tries to obtain that lock over and over again. On the single server
instances these consecutive tries will not be noticeable. At some point the
lock is obtained and the backup is created then within a very short
amount of time.

In clusters things are a little more complicated and noticeable.
A coordinator, which is trying to obtain the global write transaction
lock must try to get local locks
on all _DBServers_ simultaneously; potentially succeeding on some and not
succeeding on others, leading to apparent dead times in the cluster's write
operations.

This process can happen multiple times until success is achieved.
One has control over the length of the time during which the lock is tried to
be obtained each time prolonging the last wait time by 10%.

### Agency Lock

Less of a variable, however equally important is to obtain a freeze on the
cluster's structure itself. This is done through the creation of a simple key
lock in the cluster's configuration to stop all ongoing background tasks,
which are there to handle fail overs, shard movings, server removals etc.
Its role is also to prevent multiple simultaneous backup operations.
The acquisition of this key is predictably done within a matter of a few seconds.

### Operation's Time Scope

Once the global write transaction lock is obtained, everything goes very quickly.
A new backup directory is created, the write ahead lock is flushed and
hard links are made on file system level to all persistent files.
The duration is not affected by the amount of data in ArangoDB and is near
instantaneous.

### Point in Time Recovery

One of the great advantages of the method is the consistent snapshot nature.
It gives the operator of the database the ability to persist a true and
complete time freeze at near zero impact on the ongoing operation.
The recovery is easy and restores the entire ArangoDB installation to a
desired snapshot.

Apart from the ability of creating such snapshots it offers a great and easy
to use opportunity to experiment with ArangoDB with a means to protect
against data loss or corruption.

### Remote Upload and Download

We have fully integrated the
[Rclone](https://rclone.org/) sync for cloud storage. Rclone is a very
versatile inter site sync facility, which opens up a vast field of transport
protocols and remote syncing APIs from Amazon's S3 over Dropbox, WebDAV,
all the way to the local file system and network storage.

One can use the upload and download functionalities to migrate entire cluster
installations in this way, copy cluster and single server snapshots all
over the world, create an intuitive and easy to use quick access safety
backbone of the data operation. 

Rclone is open source and available under the MIT license, is battle tested
and has garnered close to 15k stars on GitHub professing to the confidence
of lots of users.