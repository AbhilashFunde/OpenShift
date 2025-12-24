---
layout: default
title: Q3 – Projects & Permissions
nav_order: 5
---

# Question 3. Manage Projects and Permissions

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

## Solution

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
✅ 2. Grant view access to natasha (apollo & test)
```bash
oc adm policy -h|more
oc adm policy add-role-to-user view natasha -n apollo
oc adm policy add-role-to-user view natasha -n test
```
✅ 3. Grant admin access to armstrong (apollo)
```bash
oc adm policy add-role-to-user admin armstrong -n apollo
```
Verification
Check role bindings:

```bash
oc get rolebinding -n apollo | grep -E 'natasha|armstrong'
oc get rolebinding -n test | grep natasha
```
(Optional) Test natasha permissions
```bash
oc login -u natasha -p sestiver
oc project apollo
oc get pods
```
Attempt to delete a pod:
```bash
oc delete pod <pod-name>
```
Expected result:

```bash
oc get pods → allowed
oc delete pod → Forbidden
```

(Optional) Test armstrong admin permissions
bash
Copy code
oc login -u armstrong -p gluengue
oc project apollo
oc create deployment test --image=nginx
Expected result:

Deployment creation succeeds

Result
Projects and permissions configured successfully as per requirements.

🟨 Technical Explanation
1. Projects and Namespaces
In OpenShift:

A project is implemented as a Kubernetes namespace

Creating a project automatically creates a namespace

Example:

bash
Copy code
oc new-project apollo
Creates namespace apollo.

2. RBAC Scope
Roles are scoped to namespaces unless they are cluster roles.

Common roles:

view → read-only access

edit → modify resources (not RBAC)

admin → full control within a project

cluster-admin → full cluster access

3. RoleBinding
The command:

bash
Copy code
oc adm policy add-role-to-user view natasha -n apollo
Creates a RoleBinding that links:

Role: view

User: natasha

Namespace: apollo

Role bindings apply only to the specified project.

4. Project Isolation
Each project is isolated:

Separate RBAC rules

Separate resources

Permissions in one project do not affect others

This behavior is expected and enforced by OpenShift.

yaml
Copy code

