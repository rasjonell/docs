---
layout: default
description: Important issues affecting the 3.6.x versions of the ArangoDB suite of products
title: ArangoDB v3.6 Known Issues
---
Known Issues in ArangoDB 3.6
============================

This page lists important issues affecting the 3.6.x versions of the ArangoDB
suite of products. It is not a list of all open issues.

Critical issues (ArangoDB Technical & Security Alerts) are also found at
[arangodb.com/alerts](https://www.arangodb.com/alerts/){:target="_blank"}.

ArangoSearch
------------

| Issue      |
|------------|
| **Date Added:** 2018-12-19 <br> **Component:** ArangoSearch <br> **Deployment Mode:** Single-server <br> **Description:** Value of `_id` attribute indexed by ArangoSearch view may become inconsistent after renaming a collection <br> **Affected Versions:** 3.5.x, 3.6.x <br> **Fixed in Versions:** - <br> **Reference:** [arangodb/backlog#514](https://github.com/arangodb/backlog/issues/514){:target="_blank"} (internal) |
| **Date Added:** 2018-12-03 <br> **Component:** ArangoSearch <br> **Deployment Mode:** Cluster <br> **Description:** Score values evaluated by corresponding score functions (BM25/TFIDF) may differ in single-server and cluster with a collection having more than 1 shard <br> **Affected Versions:** 3.4.x, 3.5.x, 3.6.x <br> **Fixed in Versions:** - <br> **Reference:** [arangodb/backlog#508](https://github.com/arangodb/backlog/issues/508){:target="_blank"} (internal) |
| **Date Added:** 2018-12-03 <br> **Component:** ArangoSearch <br> **Deployment Mode:** Cluster <br> **Description:** ArangoSearch index consolidation does not work during creation of a link on existing collection which may lead to massive file descriptors consumption <br> **Affected Versions:** 3.4.x, 3.5.x, 3.6.x<br> **Fixed in Versions:** - <br> **Reference:** [arangodb/backlog#509](https://github.com/arangodb/backlog/issues/509){:target="_blank"} (internal) |
| **Date Added:** 2018-12-03 <br> **Component:** ArangoSearch <br> **Deployment Mode:** All <br> **Description:** Using a loop variable in expressions within a corresponding SEARCH condition is not supported <br> **Affected Versions:** 3.4.x, 3.5.x, 3.6.x <br> **Fixed in Versions:** - <br> **Reference:** [arangodb/backlog#318](https://github.com/arangodb/backlog/issues/318){:target="_blank"} (internal) |
| **Date Added:** 2019-06-25 <br> **Component:** ArangoSearch <br> **Deployment Mode:** All <br> **Description:** The `primarySort` attribute in ArangoSearch View definitions can not be set via the web interface. The option is immutable, but the web interface does not allow to set any View properties upfront (it creates a View with default parameters before the user has a chance to configure it). <br> **Affected Versions:** 3.5.x, 3.6.x <br> **Fixed in Versions:** - <br> **Reference:** N/A |
| **Date Added:** 2019-11-06 <br> **Component:** ArangoSearch <br> **Deployment Mode:** Cluster <br> **Description:** There is a possibility to get into deadlocks during Coordinator execution if a custom Analyzer was created (and is present in the `_analyzers` system collection). It is recommended not to use custom Analyzers in production environments in affected versions. <br> **Affected Versions:** 3.5.x, 3.6.x <br> **Fixed in Versions:** - <br> **Reference:** [arangodb/backlog#651](https://github.com/arangodb/backlog/issues/651){:target="_blank"} (internal) |

AQL
---

| Issue      |
|------------|
| **Date Added:** 2018-09-05 <br> **Component:** AQL <br> **Deployment Mode:** Cluster <br> **Description:** In a very uncommon edge case there is an issue with an optimization rule in the cluster. If you are running a cluster and use a custom shard key on a collection (default is `_key`) **and** you provide a wrong shard key in a modifying query (`UPDATE`, `REPLACE`, `DELETE`) **and** the wrong shard key is on a different shard than the correct one, a `DOCUMENT NOT FOUND` error is returned instead of a modification (example query: `UPDATE { _key: "123", shardKey: "wrongKey"} WITH { foo: "bar" } IN mycollection`). Note that the modification always happens if the rule is switched off, so the suggested  workaround is to [deactivate the optimizing rule](aql/execution-and-performance-optimizer.html#turning-specific-optimizer-rules-off) `restrict-to-single-shard`. <br> **Affected Versions:** 3.4.x, 3.5.x <br> **Fixed in Versions:** - <br> **Reference:** [arangodb/arangodb#6399](https://github.com/arangodb/arangodb/issues/6399){:target="_blank"} |

Upgrading
---------

| Issue      |
|------------|
| **Date Added:** 2019-05-16 <br> **Component:** arangod <br> **Deployment Mode:** All <br> **Description:** Major upgrades such as 3.3.22 to 3.4.5 fail to upgrade the database directory under CentOS. The server log suggests to use the script `/etc/init.d/arangodb` but it is not available under CentOS. Workaround: Add `database.auto-upgrade = true` to `/etc/arangodb3/arangod.conf`, restart the service, remove `database.auto-upgrade = true` from the configuration and restart the service once more. <br> **Affected Versions:** 3.x.x (CentOS only) <br> **Fixed in Versions:** - <br> **Reference:** [arangodb/release-qa#50](https://github.com/arangodb/release-qa/issues/50){:target="_blank"} (internal) |
| **Date Added:** 2019-05-16 <br> **Component:** arangod <br> **Deployment Mode:** All <br> **Description:** Bugfix release upgrades such as 3.4.4 to 3.4.5 may not create a backup of the database directory even if they should. Please create a copy manually before upgrading. <br> **Affected Versions:** 3.4.x, 3.5.x, 3.6.x (Windows and Linux) <br> **Fixed in Versions:** - <br> **Reference:** [arangodb/planning#3745](https://github.com/arangodb/planning/issues/3745){:target="_blank"} (internal) |

Hot Backup
----------

| Issue      |
|------------|
| **Date Added:** 2019-10-09 <br> **Component:** Hot Backup API / arangobackup <br> **Deployment Mode:** All <br> **Description:** ArangoSearch Views are not backed up and thus not restored yet. Therefore, Views have to be dropped and recreated after a restore. This happens automatically in the background, but in particular in the presence of large amounts of data, the recreation of the ArangoSearch indexes can take some time after the restore. It is planned to rectify this limitation in one of the next releases.<br>Note furthermore that a running query with views can prevent a restore operation from happening whilst the query is running. <br> **Affected Versions:** 3.5.x, 3.6.x <br> **Fixed in Versions:** - <br> **Reference:** N/A |
| **Date Added:** 2019-10-09 <br> **Component:** Hot Backup API / arangobackup <br> **Deployment Mode:** All <br> **Description:** The Hot Backup feature is not supported in the Windows version of ArangoDB at this point in time. <br> **Affected Versions:** 3.5.x, 3.6.x <br> **Fixed in Versions:** - <br> **Reference:** N/A |
| **Date Added:** 2019-10-09 <br> **Component:** Hot Backup API / arangobackup <br> **Deployment Mode:** DC2DC <br> **Description:** Hot Backup functionality in Datacenter to Datacenter Replication setups is experimental and may not work. <br> **Affected Versions:** 3.5.x, 3.6.x <br> **Fixed in Versions:** - <br> **Reference:** N/A |
| **Date Added:** 2019-10-09 <br> **Component:** arangobackup <br> **Deployment Mode:** All <br> **Description:** The startup option `--operation` works as positional argument only, e.g. `arangobackup list`. The alternative syntax `arangobackup --operation list` is not accepted. <br> **Affected Versions:** 3.5.x, 3.6.x <br> **Fixed in Versions:** - <br> **Reference:** N/A |

Other
-----

| Issue      |
|------------|
| **Date Added:** 2019-05-16 <br> **Component:** Starter <br> **Deployment Mode:** All <br> **Description:** The ArangoDB Starter falls back to the IP `[::1]` under macOS. If there is no entry `::1  localhost` in the `/etc/hosts` file or the option `--starter.disable-ipv6` is passed to the starter to use IPv4, then it will hang during startup. <br> **Affected Versions:** 0.14.3 (macOS only) <br> **Fixed in Versions:** - <br> **Reference:** N/A |
| **Date Added:** 2019-05-24 <br> **Component:** Web UI <br> **Deployment Mode:** Active Failover <br> **Description:** The web interface shows a wrong replication mode in the replication tab in Active Failover deployments sometimes. It may display Master/Slave mode (the default value) because of timeouts if `/_api/cluster/endpoints` is requested too frequently. <br> **Affected Versions:** 3.5.x, 3.6.x <br> **Fixed in Versions:** - <br> **Reference:** N/A |
