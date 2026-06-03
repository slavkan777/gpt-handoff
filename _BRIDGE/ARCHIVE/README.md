# ARCHIVE

Timestamped snapshots of past bridge exchanges. Each closed request is archived as:

```
ARCHIVE/<YYYY-MM-DD>-<REQUEST_ID>/
├── ACTIVE_REQUEST.md     ← the request as executed
└── LATEST_REPORT.md      ← the report returned for it
```

Retention: keep the newest ~10 folders; older ones may be removed. The live
`_BRIDGE/ACTIVE_REQUEST.md` and `_BRIDGE/LATEST_REPORT.md` are always overwritten
in place — this folder is the audit trail.
