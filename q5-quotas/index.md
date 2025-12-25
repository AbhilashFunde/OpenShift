---
layout: default
title: Q5 – ResourceQuota & LimitRange
nav_order: 6
---
# Q5 – ResourceQuota & LimitRange

---

## Part A – ResourceQuota (Project: `rocky`)

### Question

Create a **ResourceQuota** in project **rocky** with the following limits:

| Resource               | Limit         |
| ---------------------- | ------------- |
| Pods                   | 3             |
| CPU                    | 2             |
| Memory                 | 1Gi           |
| Services               | 6             |
| Secrets                | 6             |
| ReplicationControllers | 6             |
| **Quota Name**         | quota-project |

---

### Lab Setup (Not in Exam)

```bash
oc new-project rocky
```

---

### Solution – ResourceQuota

#### 1. Switch to project `rocky`

```bash
oc whoami
# Ensure user is bob (cluster-admin)

oc project rocky
```

---

#### 2. Create ResourceQuota

```bash
oc create quota quota-project \
--hard=cpu=2,memory=1Gi,pods=3,services=6,replicationcontrollers=6,secrets=6
```

---

#### 3. Verify ResourceQuota

```bash
oc describe resourcequotas
```

---

### Explanation – ResourceQuota

#### Purpose

`ResourceQuota` restricts **total resource consumption** at the **project level**.

It limits:

* Total CPU
* Total memory
* Total number of objects (pods, services, secrets, etc.)

#### Behavior

Once limits are exceeded:

* ❌ New objects are **blocked**
* ⚠️ Error message is displayed
* ✅ Existing running resources are **not affected**

#### Notes

* `cpu=2` → total of 2 CPU cores
* `memory=1Gi` → total memory across project
* Object count limits restrict Kubernetes objects

---

## Part B – LimitRange (Project: `darpa`)

### Question

Create a **LimitRange** in project **darpa** with the following limits:

#### Pod Limits

* Memory: `5Mi – 300Mi`
* CPU: `5m – 300m`

#### Container Limits

* Memory: `5Mi – 300Mi`
* CPU: `5m – 300m`

#### Default Requests

* Memory: `100Mi`
* CPU: `100m`

**LimitRange Name:** `darpa-limit`

---

### Lab Setup (Not in Exam)

```bash
oc new-project darpa
```

---

### Solution – LimitRange

#### 1. Switch to project `darpa`

```bash
oc project darpa
```

---

#### 2. Create LimitRange YAML

Create file `limits.yaml`:

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: darpa-limit
spec:
  limits:
  - type: Pod
    min:
      cpu: 5m
      memory: 5Mi
    max:
      cpu: 300m
      memory: 300Mi
  - type: Container
    min:
      cpu: 5m
      memory: 5Mi
    max:
      cpu: 300m
      memory: 300Mi
    defaultRequest:
      cpu: 100m
      memory: 100Mi
```

---

#### 3. Apply LimitRange

```bash
oc create -f limits.yaml
```

---

#### 4. Verify LimitRange

```bash
oc get limitranges
oc describe limitranges darpa-limit
```

---

### Explanation – LimitRange

#### Purpose

`LimitRange` controls:

* Min/Max resources **per pod or container**
* Default resource requests when user does not specify them

---

### ResourceQuota vs LimitRange

| Feature     | ResourceQuota        | LimitRange              |
| ----------- | -------------------- | ----------------------- |
| Scope       | Project-wide         | Pod / Container level   |
| Controls    | Total usage          | Per object limits       |
| Enforcement | When quota exhausted | When value out of range |
| Defaults    | ❌ No                 | ✅ Yes                   |

---

### Enforcement Examples

| Scenario                 | Result              |
| ------------------------ | ------------------- |
| Pod requests 1Gi memory  | ❌ Blocked           |
| Pod requests 50Mi memory | ✅ Allowed           |
| No memory specified      | ✅ Auto set to 100Mi |
| CPU request = 1m         | ❌ Below minimum     |

---

## Final Status

### `rocky` Project

* Total resource usage capped using **ResourceQuota**

### `darpa` Project

* Pod and container sizes strictly enforced using **LimitRange**

---

## Exam Tips (DO280)

* ✅ **ResourceQuota** → caps total usage
* ✅ **LimitRange** → controls per-pod/container limits & defaults
* CPU units:

  * `1` = 1 core
  * `1000m` = 1 core
* Memory units:

  * `Mi`, `Gi` → binary
  * `M`, `G` → decimal
