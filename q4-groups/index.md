---
layout: default
title: Q4 – Managing Groups
nav_order: 5
---
# Q4 – Manage Groups

---

## Question – Managing Groups

Configure groups and permissions as follows:

### Groups to Create

* `site-users`
* `guest-users`

### Group Membership

* Add **john** to `guest-users`
* Add **jobs** and **natasha** to `site-users`

### Permissions

* Grant **edit** role to `site-users` group in **test** project
* Grant **view** role to `guest-users` group in:

  * `demo`
  * `test`

---

## Solution – Group Management

---

### 1. Create Groups

Ensure you are logged in as **cluster-admin** (bob):

```bash
oc whoami
```

Create the groups:

```bash
oc adm groups new site-users
oc adm groups new guest-users
```

---

### 2. Add Users to Groups

```bash
oc adm groups add-users site-users jobs natasha
oc adm groups add-users guest-users john
```

---

### Verify Groups

```bash
oc get groups
```

✔ Confirms groups are created successfully.

---

### 3. Assign Roles to Groups

#### Grant **edit** role to `site-users` in `test` project

```bash
oc adm policy add-role-to-group edit site-users -n test
```

#### Grant **view** role to `guest-users` in `demo` and `test`

```bash
oc adm policy add-role-to-group view guest-users -n demo
oc adm policy add-role-to-group view guest-users -n test
```

---

## Verification

---

### Check RoleBindings

```bash
oc get rolebinding -n test -o wide
oc get rolebinding -n demo -o wide
```

✔ RoleBindings should show group-based assignments.

---

### Test Permissions (Optional)

#### Login as `john` (guest-user)

```bash
oc login -u john -p warniak
oc project test
oc get pods
oc delete pod <pod-name>
```

Expected:

* `get pods` → ✅ Allowed
* `delete pod` → ❌ Forbidden

---

#### Login as `natasha` (site-user)

```bash
oc login -u natasha -p sestiver
oc project test
oc create deployment demo --image=nginx
```

Expected:

* Deployment creation → ✅ Successful

---

## Explanation

---

### Group Management

Groups simplify RBAC by:

* Assigning permissions **once**
* Automatically applying permissions to **all group members**

---

### Role: `edit` (site-users)

Permissions include:

* Create, update, delete workloads
* Manage pods, deployments, services
* ❌ Cannot manage RBAC

---

### Role: `view` (guest-users)

Permissions include:

* Read-only access
* List and describe resources
* ❌ Cannot modify resources

---

### Project-Scoped Permissions

RBAC is **namespace-scoped**:

* Permissions in `test` do not affect `demo`, `mercury`, or other projects

---

## Technical Notes (Exam Important)

---

### Group Object

Groups are OpenShift objects that store:

* Group name
* List of users

---

### Group-Based RoleBinding

Command:

```bash
oc adm policy add-role-to-group edit site-users -n test
```

Creates a RoleBinding with:

* **Role:** edit
* **Group:** site-users
* **Namespace:** test

---

## Final Status

| Group       | Users         | Permissions               |
| ----------- | ------------- | ------------------------- |
| site-users  | jobs, natasha | edit on `test`            |
| guest-users | john          | view on `demo` and `test` |

---

## Exam Tips (DO280)

* Groups simplify RBAC management
* Assign roles to **groups**, not individual users (best practice)
* `edit` ≠ RBAC control
* `view` = read-only
* Always verify using:

  * `oc get groups`
  * `oc get rolebinding`
