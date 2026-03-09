# Runbook: PostgreSQL WAL Recovery

## Overview

This runbook describes the procedure to recover a PostgreSQL instance
that fails to start due to WAL corruption after an abrupt shutdown.

Typical symptoms include PostgreSQL pods stuck in `CrashLoopBackOff`
with logs showing:

```
PANIC: could not locate a valid checkpoint record
```

---

# When To Use This Runbook

Use this procedure if:

* PostgreSQL pod is in `CrashLoopBackOff`
* Logs contain:

  * `invalid checkpoint record`
  * `could not locate a valid checkpoint record`
* The issue occurred after:

  * power outage
  * node crash
  * abrupt shutdown

Do **not** use this runbook if:

* There is a recent backup that should be restored instead

---

# Prerequisites

Optional but recommended:
* Snapshot or backup of the PVC

---

# Diagnosis

Confirm the issue with:

```
kubectl logs <postgres-pod>
```

Expected error pattern:

```
invalid checkpoint record
PANIC: could not locate a valid checkpoint record
```

---

# Recovery Procedure

## 1. Create a recovery pod

Apply a temporary pod mounting the same PVC.

```
kubectl apply -f postgres-recovery.yaml
```

Example recovery pod:

```
apiVersion: v1
kind: Pod
metadata:
  name: postgres-recovery
  namespace: #The same namespace as the corrupt pod to be able to access the database
spec:
  restartPolicy: Never
  containers:
    - name: postgres-recovery
      image: postgres:16-alpine #The same image as the one used in the corrupt pod
      command: ["sleep", "infinity"]
      volumeMounts:
        - mountPath: /var/lib/postgresql/data
          name: data
  volumes:
    - name: data
      persistentVolumeClaim:
        claimName: postgres-data
```

---

## 2. Execute WAL reset

Enter the recovery container:

```
kubectl exec -it postgres-recovery -- bash
```

Run:

```
su - postgres
pg_resetwal -f /var/lib/postgresql/data
```

Expected output:

```
Write-ahead log reset
```

---

## 3. Delete recovery pod

```
kubectl delete pod postgres-recovery
```

---

## 4. Restart PostgreSQL

Delete the failing pod:

```
kubectl delete pod <postgres-pod>
```

Kubernetes will recreate it automatically.

---

# Verification

Confirm PostgreSQL started correctly:

```
kubectl logs <postgres-pod>
```

Expected log message:

```
database system is ready to accept connections
```

---

# Risks

* `pg_resetwal` may discard uncommitted transactions
* Some recent data may be lost

This should only be used when WAL recovery fails.

---

# Related Incidents

* [2026-03-08 PostgreSQL WAL corruption after power outage](../postmortems/2026-03-08_postgres-wal-corruption.md)

---

# References

* Internal postmortem: PostgreSQL WAL corruption
