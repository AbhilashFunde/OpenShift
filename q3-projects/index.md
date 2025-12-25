---
layout: default
title: Q3 – Projects & Permissions
nav_order: 5
---

**�� Q3. Manage Projects and Permissions**

**Question 3. Manage Projects and Permissions**

Configure project access as follows:

- Create projects:

  - **apollo**

  - **test**

  - **mercury**

  - **demo**

<!-- -->

- **natasha** user can only **view resources** in:

  - apollo

  - test

<!-- -->

- **armstrong** user should have **admin access** to apollo project.

**�� SOLUTION**

**✅ 1. Create Projects**

**oc whoami (Make sure the user is bob, because bob is currently the
cluster administrator.)**

oc new-project apollo

oc new-project test

oc new-project mercury

oc new-project demo

oc get projects

**✅ 2. Grant VIEW access to natasha for apollo & test**

cc adm policy -h

oc adm policy add-role-to-user view natasha -n apollo

oc adm policy add-role-to-user view natasha -n test

**✅ 3. Grant ADMIN access to armstrong for apollo**

oc adm policy add-role-to-user admin armstrong -n apollo

**✅ VERIFICATION**

**�� Check role bindings in apollo**

oc get rolebinding -n test -o wide \| grep -i natasha

oc get rolebinding -n apollo -o wide \| grep -i natasha

oc get rolebinding -n apollo -o wide \| grep -i armstrong

<img src="43c5f7f3d7175745db91efdd09b9056f5e8ee02a.png"
style="width:10.46875in;height:2.125in" alt="image" />

**✅ Test natasha permissions (If you want)**

oc login -u natasha -p sestiver

oc project apollo

oc get pods

oc delete pod \<pod-name\>

Expected:

- oc get pods → works

- oc delete → Forbidden

**✅ Test armstrong admin permissions (If you want)**

oc login -u armstrong -p gluengue

oc project apollo

oc create deployment test --image=nginx

Expected:

- Deployment should succeed ✔

**�� EXPLANATION**

**�� Role: view (natasha)**

Provides:

- Read-only access

- Cannot create/edit/delete

- Can list and describe resources

Used for:

- Auditors

- Inspectors

- Observers

**�� Role: admin (armstrong)**

Provides:

- Full control inside project

- Create/update/delete resources

- Create role bindings

- Manage secrets, pods, services, routes, etc.

**�� Project Isolation**

Each project is isolated:

- Separate RBAC

- Separate objects

- Role in apollo does not affect mercury or demo

**�� TECHNICAL EXPLANATION**

**1. Namespace = Project in OpenShift**

Each project maps to a namespace:

oc new-project apollo

→ creates namespace: apollo

**2. RBAC Scopes**

|               |                     |
|---------------|---------------------|
| **Role**      | **Scope**           |
|               |                     |
| view          | Read only           |
| admin         | Full control        |
|               |                     |
| edit          | Modify but not RBAC |
|               |                     |
| cluster-admin | Full cluster        |
|               |                     |

**3. RoleBinding**

Command creates resource:

oc adm policy add-role-to-user view natasha -n apollo

Creates:

RoleBinding:

Role → view

User → natasha

Namespace → apollo

**✅ FINAL STATUS**

|             |             |               |
|-------------|-------------|---------------|
| **Project** | **natasha** | **armstrong** |
|             |             |               |
| apollo      | View        | Admin         |
| test        | View        | No Access     |
|             |             |               |
| mercury     | No Access   | No Access     |
|             |             |               |
| demo        | No Access   | No Access     |
|             |             |               |
