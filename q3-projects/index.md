---
layout: default
title: Q3 – Projects & Permissions
nav_order: 5
---

### Question 3. Manage Projects and Permissions

Configure project access as follows:

- Create the following projects:
  - `apollo`
  - `test`
  - `mercury`
  - `demo`
- User **natasha** must have **view-only** access to:
  - `apollo`
  - `test`
- User **armstrong** must have **admin** access to:
  - `apollo`

---

### Solution

### ✅ 1. Create projects

Make sure you are logged in as **bob** (cluster administrator):

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

### ✅ 2. Grant view access to natasha (apollo & test)
```bash
oc adm policy -h|more
oc adm policy add-role-to-user view natasha -n apollo
oc adm policy add-role-to-user view natasha -n test
```
---

### ✅ 3. Grant admin access to armstrong (apollo)
```bash
oc adm policy add-role-to-user admin armstrong -n apollo
```
Verification
Check role bindings:

```bash
oc get rolebinding -n test -o wide | grep -i natasha
oc get rolebinding -n apollo -o wide | grep -i natasha
oc get rolebinding -n apollo -o wide | grep -i armstrong
```
<img width="624" height="128" alt="image" src="https://github.com/user-attachments/assets/13e4b013-8e01-443f-bdda-f6a95eb03f6c" />

(If You Want) Test natasha permissions
```bash
oc login -u natasha -p sestiver
oc project apollo
oc new-app httpd
oc get pods
```
Attempt to delete a pod:
```bash
oc delete pod <pod-name>
```
Expected result:

oc get pods → allowed
oc delete pod → Forbidden

(If You Want) Test armstrong admin permissions

```bash
oc login -u armstrong -p gluengue
oc project apollo
oc create deployment test --image=nginx
```
Expected result:

Deployment creation succeeds

### Result

Projects and permissions configured successfully as per requirements.

---

### 🟨 Technical Explanation

### 1. Projects and Namespaces
In OpenShift:

- A project is implemented as a Kubernetes namespace
- Creating a project automatically creates a namespace

Example:

`oc new-project apollo`

This command creates a namespace named `apollo`.

---
### 2. RBAC Scope
Roles are namespace-scoped unless they are cluster roles.
Cluster roles apply across the entire cluster.

Common Roles:
- `view` – Read-only access
- `edit` – Modify resources (does not manage RBAC)
- `admin` – Full control within a project
- `cluster-admin` – Full cluster-wide access
  
---
### 3. RoleBinding
`oc adm policy add-role-to-user view natasha -n apollo`

This creates a RoleBinding that links:
- Role: `view`
- User: `natasha`
- Namespace: `apollo`

RoleBindings apply only to the specified project.

---
### 4. Project Isolation
Each OpenShift project is isolated:
- Separate RBAC rules
- Separate resources
- Permissions in one project do not affect other projects

This behavior is expected and enforced by OpenShift.

---
