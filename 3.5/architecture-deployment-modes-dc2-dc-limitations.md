---
layout: default
description: The datacenter to datacenter replication setup in ArangoDB has a few limitations
---
Limitations
===========

The _datacenter to datacenter replication_ setup in ArangoDB has a few limitations.
Some of these limitations may be removed in later versions of ArangoDB:

- All the machines where the ArangoDB Server processes run must run the Linux
  operating system using the AMD64 architecture. Clients can run from any platform.

- All the machines where the ArangoSync Server processes run must run the Linux
  operating system using the AMD64 architecture.
  The ArangoSync command line tools is available for Linux, Windows & macOS.

- The entire cluster is replicated. It is not possible to exclude specific
  databases or collections from replication.
