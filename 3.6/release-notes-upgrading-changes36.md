---
layout: default
description: It is recommended to check the following list of incompatible changes before upgrading to ArangoDB 3.6
---
Incompatible changes in ArangoDB 3.6
====================================

It is recommended to check the following list of incompatible changes **before**
upgrading to ArangoDB 3.6, and adjust any client programs if necessary.

The following incompatible changes have been made in ArangoDB 3.6:

Restricted ranges for date/time values in AQL
---------------------------------------------

ArangoDB 3.6 enforces valid date ranges for working with date/time in AQL. 
The valid date ranges for any AQL date/time function are:

- for string date/time values: `"0000-01-01T00:00:00.000Z"` (including) up to `"9999-12-31T23:59:59.999Z"` (including)
- for numeric date/time values: -62167219200000 (including) up to 253402300799999 (including). 
  These values are the numeric equivalents of `"0000-01-01T00:00:00.000Z"` and `"9999-12-31T23:59:59.999Z"`.

Any date/time values outside the given range that are passed into an AQL date
function will make the function return `null` and trigger a warning in the query,
which can optionally be escalated to an error and stop the query.

Any date/time operations that produce date/time outside the valid ranges stated
above will make the function return `null` and trigger a warning too. Example:

```js
DATE_SUBTRACT("2018-08-22T10:49:00+02:00", 100000, "years") // null
```
