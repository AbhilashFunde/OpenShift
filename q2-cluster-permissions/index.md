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
________________________________________
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
### 🟦 EXPLANATION
🧑‍💼 Cluster Admin (bob)
Granting role cluster-admin gives:
•	Full cluster control
•	Node management
•	User management
•	Security controls
________________________________________
### 🚫 Disable Default Project Creation
OpenShift normally allows all logged-in users to create projects.
Removing role:
self-provisioner from system:authenticated
Means:
•	Users CANNOT create projects unless explicitly allowed
________________________________________
### ✅ Allow Only john to Create Projects
Now only john has project creation rights.
This follows:
Principle of Least Privilege
________________________________________
❌ jobs User
jobs was NOT granted any provisioning role → remains blocked.
________________________________________
### 🧹 Remove kubeadmin
kubeadmin is an emergency admin account.
Removing it:
•	Improves security
•	Prevents unauthorized access
•	Enforces identity provider-based authentication
________________________________________
### 🟨 TECHNICAL EXPLANATION
1. Role-Based Access Control (RBAC)
OpenShift permissions work as:
Role → Bound to → User / Group
Example:
cluster-admin → bob
self-provisioner → john
________________________________________
2. self-provisioner Role
This role controls:
•	Who can create projects
Removing from group:
system:authenticated:oauth
means:
Only explicitly assigned users can create projects.
________________________________________
3. kubeadmin User
OpenShift installer creates kubeadmin once.
Deleting the secret:
kubeadmin secret → gone
No authentication → access denied.
________________________________________
### ✅ FINAL STATUS
User	Access Level
bob	Cluster Admin
john	Can Create Projects
jobs	Cannot Create Projects
alice	Cluster Reader
kubeadmin	Deleted

