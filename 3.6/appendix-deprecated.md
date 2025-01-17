---
layout: default
description: Features listed in this section should no longer be used, because they are considered obsolete and may get removed in a future release
title: Deprecated Features
redirect_from:
  - /3.6/appendix-deprecated-actions.html # 3.4 -> 3.5
  - /3.6/appendix-deprecated-actions-html-example.html # 3.4 -> 3.5
  - /3.6/appendix-deprecated-actions-json-example.html # 3.4 -> 3.5
  - /3.6/appendix-deprecated-actions-modifying.html # 3.4 -> 3.5
---
Deprecated
==========

Features listed in this section should no longer be used, because they are
considered obsolete and may get removed in a future release. They are currently
kept for backward compatibility. There are usually better alternatives to
replace the old features with:

- **Simple Queries**: Idiomatic interface in arangosh to perform trivial queries.
  They are superseded by [AQL queries](../aql/), which can also
  be run in arangosh. AQL is a language on its own and way more powerful than
  *Simple Queries* could ever be. In fact, the (still supported) *Simple Queries*
  are translated internally to AQL, then the AQL query is optimized and run
  against the database in recent versions, because of better performance and
  reduced maintenance complexity.

- **Actions**: Snippets of JavaScript code on the server-side for minimal
  custom endpoints. Since the Foxx revamp in 3.0, it became really easy to
  write [Foxx Microservices](foxx.html), which allow you to define
  custom endpoints even with complex business logic.

  From v3.5.0 on, the system collections `_routing` and `_modules` are not
  created anymore when the `_system` database is first created (blank new data
  folder). They are not actively removed, they remain on upgrade or backup
  restoration from previous versions.

  You can still find the
  [Actions documentation](https://www.arangodb.com/docs/3.4/appendix-deprecated-actions.html){:target="_blank"}
  in 3.4 or older versions of the documentation.
