---
layout: default
title: Q4 – Managing Groups
nav_order: 5
---
## Question 4. Managing Groups

Configure groups and permissions as follows:

- Create groups:
  - `site-users`
  - `guest-users`

- Add users to groups:
  - Add `john` to `guest-users`
  - Add `jobs` and `natasha` to `site-users`

- Assign permissions:
  - Give `edit` role to `site-users` group in `test` project
  - Give `view` role to `guest-users` group in:
    - `demo` project
    - `test` project

---

## 🟩 SOLUTION

### ✅ 1. Create Groups

Make sure the current user is `bob` (cluster administrator):

```bash
oc whoami
oc adm -h | more
```

Create the groups:

```bash
oc adm groups new site-users
oc adm groups new guest-users
```
---

### ✅ 2. Add Users to Groups

Add users to the groups:
```bash
oc adm groups -h
oc adm groups add-users site-users jobs natasha  
oc adm groups add-users guest-users john
```
---

### ✅ VERIFICATION (Group Creation)

Verify that groups exist:
```bash
oc get groups
```
<img width="620" height="202" alt="image" src="https://github.com/user-attachments/assets/e177742f-b807-41a8-847b-1a5b2aed0e4d" />

---

### ✅ 3. Assign Roles to Groups

Grant **edit** access to `site-users` in `test` project:
```bash
oc adm policy -h
oc adm policy add-role-to-group edit site-users -n test
```
Grant **view** access to `guest-users` in `demo` and `test` projects:
```bash
oc adm policy add-role-to-group view guest-users -n demo
oc adm policy add-role-to-group view guest-users -n test
```
---

### ✅ VERIFICATION

Check RoleBindings in projects:
```bash
`oc get rolebinding -n test -o wide`  
`oc get rolebinding -n demo -o wide`
```
Expected:
- RoleBindings exist for `site-users` and `guest-users`

<img width="623" height="128" alt="image" src="https://github.com/user-attachments/assets/3ee10968-31d6-47f3-8952-4bb2369ef594" />

---

### ✅ Test Permissions (if You Want)

Login as `john` (guest-user):
```bash
oc login -u john -p warniak
oc project test
oc get pods
oc delete pod <pod-name>
```
Expected:
- `get` → SUCCESS
- `delete` → FORBIDDEN

---

Login as `natasha` (site-user):
```bash
oc login -u natasha -p sestiver 
oc project test
oc create deployment demo --image=nginx
```
Expected:
- Deployment creation → SUCCESS ✔

---

### 🟨 TECHNICAL EXPLANATION

- 👥 Group Management

Groups simplify user management by:
- Assigning permissions once to a group
- Automatically applying them to all users in the group

---

- ✏️ edit Role (`site-users`)

Permissions include:
- Create workloads
- Update workloads
- Delete workloads
- Cannot manage RBAC

---

- 👁️ view Role (`guest-users`)

Permissions include:
- Read-only access
- Cannot modify resources

---

- 📦 Project Scoped Permissions

Each project has independent RBAC rules.
- Roles in `demo` do not affect `test`
- Roles in `test` do not affect other projects

---
### 1. Group Object (OpenShift)

A **Group** is a cluster-scoped object in OpenShift.

- Used to organize users
- Does not grant permissions by itself
- Permissions are applied through RoleBindings

Groups can be listed using:
`oc get groups`

---

### 2. RoleBinding (Group-based)

Permissions are granted using **RoleBindings**.

Example:
`oc adm policy add-role-to-group edit site-users -n test`

This creates a RoleBinding that links:
- Role → `edit`
- Group → `site-users`
- Namespace → `test`

---

### 3. Permission Inheritance

Users inherit permissions from groups automatically.

Flow:
Role → Group → User

Example:
- `natasha` inherits `edit` permissions via `site-users`

---

### 4. Namespace Enforcement

RBAC is enforced per project:
- Permissions in `demo` do not affect `test`
- Same group can have different roles in different projects

---

## ✅ FINAL STATUS

| Group        | Users               | Permissions              |
|-------------|--------------------|--------------------------|
| site-users  | jobs, natasha      | edit on test             |
| guest-users | john               | view on demo & test      |

