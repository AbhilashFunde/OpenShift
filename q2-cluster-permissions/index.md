---
layout: default
title: Q2 – Manage Cluster Permissions
nav_order: 4
---
### Question 2. Manage Cluster Permissions
Configure role-based access as follows:
-	**bob** user should have **cluster administrator** rights.
-	**john** user should be able to **create projects**.
-	**jobs** user should **NOT be able** to create projects.
-	**alice** user should have **cluster-reader** permissions to view all resources across the entire cluster.
-	**kubeadmin** user must not exist.

________________________________________

### 🟩 SOLUTION
### ✅ 1. Grant Cluster Admin to bob
```bash
oc whoami 
oc adm policy -h
oc adm policy add-cluster-role-to-user cluster-admin bob
```
### ✅ Verify bob has cluster-admin privileges
```bash
oc login -u bob -p indionce
oc get nodes
```
If nodes are visible → cluster-admin access is confirmed ✔

________________________________________

### ✅ 2. Remove project creation rights for normal users
By default, OpenShift allows authenticated users to create projects via the self-provisioner role.
Remove this default behavior:
```bash
oc adm policy remove-cluster-role-from-group self-provisioner system:authenticated:oauth
```
________________________________________

### ✅ 3. Confirm role binding exists
Check which object controls project creation:
```bash
oc get clusterrolebinding | grep self
```
This confirms that self-provisioner controls project creation permissions.

________________________________________

### ✅ 4. Verify jobs cannot create project
```bash
oc login -u jobs -p catalog
oc new-project test
```
Expected output:
error: Forbidden (no permission)
This confirms jobs cannot create projects ✔

________________________________________

### ✅ 5. Allow john to create projects manually (project creation requires a ClusterRoleBinding)
Grant self-provisioner role only to john:
```bash
oc adm policy add-cluster-role-to-user self-provisioner john
```

### ✅ Verify john can create project
```bash
oc login -u john -p warniak
oc new-project test
```
If project gets created → permission is correct ✔

________________________________________


### ✅ 6. Give alice user cluster-reader permissions
```bash
oc adm policy add-cluster-role-to-user cluster-reader alice
```

________________________________________

### ✅ 7. Remove kubeadmin user
Delete kubeadmin credentials:
```bash
oc login -u bob -p indionce (User bob is cluster-admin now)
os get user kubeadmin
oc get secret kubeadmin -n kube-system
oc delete secrets kubeadmin -n kube-system
```

________________________________________

### ✅ Verify kubeadmin no longer works
```bash
os get user kubeadmin
oc login -u kubeadmin -p password
```
Expected output:
Login failed
This confirms kubeadmin is removed ✔
Verify console login by using bob credentials

________________________________________

## 🟨 Technical Explanation

### 1. Role-Based Access Control (RBAC)

OpenShift permissions are managed using RBAC.

- Permissions are defined in **Roles / ClusterRoles**
- Roles are assigned using **RoleBindings / ClusterRoleBindings**
- Access is always evaluated as:

Role → Bound to → User / Group

Examples:
- `cluster-admin` → `bob`
- `self-provisioner` → `john`
- `cluster-reader` → `alice`

---

### 2. cluster-admin Role

The `cluster-admin` role provides:

- Full cluster-wide permissions
- Node and namespace management
- User and security administration

This role should be granted only to trusted users.

---

### 3. self-provisioner Role

The `self-provisioner` role controls **project creation**.

- By default, it is bound to the group `system:authenticated:oauth`
- This allows all authenticated users to create projects

Removing this role ensures:
- Users **cannot** create projects by default
- Only explicitly assigned users (e.g., `john`) can create projects

This follows the **Principle of Least Privilege**.

---

### 4. cluster-reader Role

The `cluster-reader` role provides:

- Read-only access across all namespaces
- Visibility into cluster resources
- No permission to create or modify objects

This role is suitable for audit or monitoring users.

---

### 5. kubeadmin User (Exam Note)

- `kubeadmin` is a temporary emergency admin user
- It is created during cluster installation
- Access exists only while the `kubeadmin` secret is present

Deleting the secret:
- Immediately disables kubeadmin access
- Forces authentication through configured identity providers

This behavior is **expected and secure**.

