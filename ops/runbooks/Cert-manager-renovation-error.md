# Runbook: Cert manager renovation error

## Overview

This runbook describes the procedure to fix the certificate renovation loop that happens every now and then.

Typical symptoms include ArgoCD showing the infrastructure app in "pending" status, and if it's not fixed the certificate will expire causing the HTTPS connections to fail.

---

# When To Use This Runbook

Use this procedure if:

* The renovation of the certificate fails and enters a loop.

* The issue occurred after:

  * The renovation date

---

# Diagnosis

Confirm the issue with:

* The infrastructure app in ArgoCD is "pending".
* 7 days before the certificate expiration, grafana will fire an alert.

---

# Recovery Procedure

Deleting the certificate's secret forces cert-manager to renew it:

```
kubectl -n networking delete secret wildcard-tls
```

# Verification

After deleting the secret it will be recreated and the certificate renewed. It can be verified using:

```
kubectl -n networking describe certificate wildcard-tls
```

Status.Conditions.Message should inform "Certificate is up to date and has not expired"
