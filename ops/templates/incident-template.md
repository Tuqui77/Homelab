
# Incident Postmortem

## Incident Metadata

* **Incident ID:** YYYY-MM-DD-short-description
* **Date:**
* **Severity:** (Low / Medium / High / Critical)
* **Duration:**
* **Status:** Resolved / Monitoring / Open

## **Affected Services**

**Infrastructure**

* Cluster:
* Namespace:
* Storage / Dependencies:

---

# Summary

Short description of the incident.
Explain what happened in 2–4 sentences.

---

# Impact

Describe the actual impact.

Examples:

* Service downtime
* API errors
* Data inconsistency
* Degraded performance

Details:

* **User impact:**
* **System impact:**
* **Data loss:** Yes / No / Unknown

---

# Timeline

Chronological sequence of events.

| Time (GMT-3) | Event                 |
| ---------- | --------------------- |
|            | Incident begins       |
|            | Detection             |
|            | Investigation started |
|            | Root cause identified |
|            | Mitigation applied    |
|            | Service restored      |

---

# Detection

How the incident was detected.

Examples:

* Alerting system
* Logs
* Manual observation
* User report

**Detection Method:**

**Detection Time:**

**Time to Detect (MTTD):**

---

# Root Cause

Technical explanation of the underlying problem.

Focus on **system behavior**, not people.

Example topics:

* infrastructure failure
* storage inconsistency
* configuration error
* dependency failure

---

# Contributing Factors

Conditions that made the incident more likely or worse.

Examples:

* missing monitoring
* lack of redundancy
* unsafe configuration
* insufficient testing

---

# Resolution

Steps taken to restore the service.

1.
2.
3.

---

# Recovery Verification

How you confirmed the system returned to normal operation.

Examples:

* health checks passing
* logs normal
* application responding correctly

---

# Action Items

Improvements to prevent recurrence or reduce impact.

| Priority | Action | Owner | Status |
| -------- | ------ | ----- | ------ |
| High     |        |       | Open   |
| Medium   |        |       | Open   |
| Low      |        |       | Open   |

---

# Metrics (Optional)

* **MTTD:** Mean Time To Detect
* **MTTR:** Mean Time To Recover
* **Total Downtime:**

---

# Lessons Learned

Key takeaways from the incident.

Examples:

* architectural limitations
* operational gaps
* improvements for monitoring or automation

---

# References

Links to related materials:

* dashboards
* logs
* PRs
* documentation
* runbooks
