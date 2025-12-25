---
layout: default
title: Q3 – Projects & Permissions
nav_order: 5
---

# Q3 – Manage Projects and Permissions

---

## Question – Manage Projects and Permissions

Configure project access with the following requirements:

### Projects to Create

* `apollo`
* `test`
* `mercury`
* `demo`

### Access Requirements

* **natasha** → can **view resources only** in:

  * `apollo`
  * `test`
* **armstrong** → must have **admin access** to:

  * `apollo`

---

## Solution – Project Permissions

---

### 1. Create Projects

Ensure you are logged in as **cluster-admin** (bob):

```bash
oc whoami
```

Create the projects:

```bash
oc new-project apollo
oc new-project test
oc new-project mercury
oc new-project demo
```

Verify:

```bash
oc get projects
```

---

### 2. Grant VIEW Access to `natasha`

Grant **read-only (view)** role in required projects:

```bash
oc adm policy add-role-to-user view natasha -n apollo
oc adm policy add-role-to-user view natasha -n test
```

---

### 3. Grant ADMIN Access to `armstrong`

Grant **admin** role in the `apollo` project:

```bash
oc adm policy add-role-to-user admin armstrong -n apollo
```

---

## Verification

---

### Check RoleBindings

Verify bindings in projects:

```bash
oc get rolebinding -n apollo | grep -i natasha
oc get rolebinding -n test | grep -i natasha
oc get rolebinding -n apollo | grep -i armstrong
```

---

### Test `natasha` Permissions (Optional)

```bash
oc login -u natasha -p sestiver
oc project apollo
oc get pods
oc delete pod <pod-name>
```

Expected behavior:

* `oc get pods` → ✅ Allowed
* `oc delete pod` → ❌ Forbidden

---

### Test `armstrong` Admin Permissions (Optional)

```bash
oc login -u armstrong -p gluengue
oc project apollo
oc create deployment test --image=nginx
```

Expected behavior:

* Deployment creation → ✅ Successful

---

## Explanation

---

### Role: `view` (natasha)

Provides:

* Read-only access
* Can list and describe resources
* Cannot create, modify, or delete resources

Typical use cases:

* Auditors
* Observers
* Support teams

---

### Role: `admin` (armstrong)

Provides:

* Full control within the project
* Create, update, delete resources
* Manage secrets, services, routes, deployments
* Create role bindings inside the project

---

### Project Isolation

Each project (namespace) is isolated:

* Separate RBAC rules
* Separate resources
* Access in one project does **not** affect others

---

## Technical Notes (Exam Important)

---

### 1. Project = Namespace

In OpenShift:

```bash
oc new-project apollo
```

Creates:

```text
Namespace: apollo
```

---

### 2. RBAC Scopes

| Role          | Scope / Permissions        |
| ------------- | -------------------------- |
| view          | Read-only access           |
| edit          | Modify resources (no RBAC) |
| admin         | Full project control       |
| cluster-admin | Full cluster control       |

---

### 3. RoleBinding

Command:

```bash
oc adm policy add-role-to-user view natasha -n apollo
```

Creates:

* **Role:** view
* **User:** natasha
* **Namespace:** apollo

This binding is **namespace-scoped**.

---

## Final Status

| Project | natasha   | armstrong |
| ------- | --------- | --------- |
| apollo  | View      | Admin     |
| test    | View      | No Access |
| mercury | No Access | No Access |
| demo    | No Access | No Access |

---

## Exam Tips (DO280)

* `view` = read-only
* `admin` = full project control
* RBAC is **namespace-scoped** unless cluster roles are used
* Always verify with:

  * `oc get rolebinding`
  * login + action test
