---
layout: default
title: Q2 - Cluster RBAC
nav_order: 4
---
# Q2 – Manage Cluster Permissions

---

## Question – Manage Cluster Permissions

Configure role-based access control (RBAC) with the following requirements:

* **bob** → must have **cluster administrator** rights
* **john** → must be able to **create projects**
* **jobs** → must **NOT** be able to create projects
* **alice** → must have **cluster-reader** access across the cluster
* **kubeadmin** → **must not exist**

---

## Solution – Cluster Permissions

---

### 1. Grant Cluster Admin to `bob`

Verify current user:

```bash
oc whoami
```

Grant cluster-admin role:

```bash
oc adm policy add-cluster-role-to-user cluster-admin bob
```

#### Verify

```bash
oc login -u bob -p indionce
oc get nodes
```

✔ If nodes are visible → `bob` has **cluster-admin** access.

---

### 2. Disable Default Project Creation for All Users

By default, OpenShift allows authenticated users to create projects via the
**self-provisioner** role.

Remove this default behavior:

```bash
oc adm policy remove-cluster-role-from-group self-provisioner system:authenticated:oauth
```

---

### 3. Confirm Self-Provisioner Role Binding

```bash
oc get clusterrolebinding | grep self-provisioner
```

This confirms which binding controls project creation.

---

### 4. Verify `jobs` Cannot Create Projects

```bash
oc login -u jobs -p catalog
oc new-project test
```

Expected result:

```
Error from server (Forbidden)
```

✔ Confirms `jobs` **cannot** create projects.

---

### 5. Allow Only `john` to Create Projects

Grant `self-provisioner` role **only** to `john`:

```bash
oc adm policy add-cluster-role-to-user self-provisioner john
```

#### Verify

```bash
oc login -u john -p warniak
oc new-project test
```

✔ If project is created → permission is correct.

---

### 6. Grant Cluster-Reader Role to `alice`

```bash
oc adm policy add-cluster-role-to-user cluster-reader alice
```

✔ Allows read-only access to all cluster resources.

---

### 7. Remove `kubeadmin` User

Login as cluster-admin (`bob`):

```bash
oc login -u bob -p indionce
```

Check kubeadmin user and secret:

```bash
oc get user kubeadmin
oc get secret kubeadmin -n kube-system
```

Delete kubeadmin secret:

```bash
oc delete secret kubeadmin -n kube-system
```

#### Verify kubeadmin no longer works

```bash
oc get user kubeadmin
oc login -u kubeadmin -p password
```

Expected result:

```
Login failed
```

✔ Confirms `kubeadmin` is removed.

---

## Explanation

---

### Cluster Admin (`bob`)

Granting `cluster-admin` provides:

* Full cluster control
* Node management
* User and RBAC management
* Security configuration

---

### Disable Default Project Creation

Removing:

```text
self-provisioner from system:authenticated:oauth
```

Means:

* No user can create projects **unless explicitly allowed**

This improves cluster security.

---

### Allow Only `john` to Create Projects

Granting `self-provisioner` **only to john** enforces:

> **Principle of Least Privilege**

---

### `jobs` User

* No provisioning role granted
* Project creation remains blocked

---

### Remove `kubeadmin`

`kubeadmin` is a temporary bootstrap user created during installation.

Deleting its secret:

* Removes authentication credentials
* Prevents emergency backdoor access
* Forces usage of configured Identity Providers

---

## Technical Notes (Exam Important)

### RBAC Model

```text
Role → Bound to → User / Group
```

Examples:

* `cluster-admin` → bob
* `self-provisioner` → john
* `cluster-reader` → alice

---

### self-provisioner Role

Controls:

* Project creation permissions

Removing from:

```text
system:authenticated:oauth
```

Ensures:

* Only explicitly assigned users can create projects

---

## Final Status

| User      | Access Level               |
| --------- | -------------------------- |
| bob       | Cluster Admin              |
| john      | Can Create Projects        |
| jobs      | Cannot Create Projects     |
| alice     | Cluster Reader (Read-only) |
| kubeadmin | Deleted                    |

---

## Exam Tips (DO280)

* `cluster-admin` = full control
* `self-provisioner` = project creation
* Remove default access for better security
* kubeadmin removal is **commonly tested**
* Always **verify with login + action**
