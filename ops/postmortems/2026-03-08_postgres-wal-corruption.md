# Incident Postmortem

## Incident Metadata

* **Incident ID:** 2026-03-08_postgres-wal-corruption
* **Date:** 08/03/2026 ~ 12:00
* **Severity:** Medium
* **Duration:** 20:11:00
* **Status:** Resolved

## **Affected Services**

**Infrastructure**

* Cluster: ProxTalos
* Namespace: torrent

---

# Summary

A power outage occurred the day 08/03/2026 around 12:00. The power was back up at 12:35 and once uptime-kuma started it was notified bitmagnet's postgreSQL pod couldn't start (12:41). The cause was a corrupted WAL checkpoint. The database was recovered manually using 'pg_resetwal' in a recovery pod.

---

# Impact

* PostgreSQL database couldn't start.
* Services that depends on the database were down for the duration of the incident.
* No significant data loss was detected.

---

# Timeline

Chronological sequence of events.

| Time (GMT-3) | Event|
| ---------- | --------------------- |
|12:00|Incident begin|
|12:35|Power back u|
|12:41|Detection|
|18:00|Investigation started|
|18:15|Root cause identified|
|09/03 - 08:06|Mitigation applied|
|08:11|Service restored|

---

# Detection

* Alerting system
* Logs
* Manual observation

**Detection Method:** Alerting system

**Detection Time:** 12:41

**Time to Detect (MTTD):** 5 minutes

---

# Root Cause

The system shutting down abruptly when the DB was writing data left the WAL in an inconsistent state. When PostgreSQL tried to start couldn't find a valid checkpoint and aborted the startup process leaving the pod in CrashLoopBackOff state.

---

# Contributing Factors

* Lack of UPS.
* The usage of NFS as storage backend.

---

# Resolution

Steps taken to restore the service.

1. Create a recovery pod.
2. Log into the database.
3. Execute a `pg_resetwal`
4. Force the recreation of both database and app pods.

---

# Recovery Verification

* uptime-kuma notified the service was back online.
* The app was responding as expected.

---

# Action Items

Improvements to prevent recurrence or reduce impact.

| Priority | Action | Owner | Status |
| -------- | ------ | ----- | ------ |
|High|Implement automated backups|Tomas| Open   |
| Medium   |Add an UPS to the setup|Tomas| Open   |

---

# Metrics (Optional)

* **MTTD:** 5 minutes.
* **MTTR:** 20:06:00
* **Total Downtime:** 20:11:00

---

# Lessons Learned

* The lack of a UPS can't be overlooked as power outages are becoming increasingly frequent.
* Database backups aren't optional, this time the recovery was easy but that may not be the case in the future.
* Always having the means to interact with the cluster and troubleshoot/fix problems as they arise would greatly decrease downtimes.

---

# References

Runbooks:
* [PostgreSQL WAL Recovery](../runbooks/PostgreSQL-WAL-Recovery.md)
