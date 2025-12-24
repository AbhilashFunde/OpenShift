---
layout: default
title: Q1 – HTPasswd Identity Provider
nav_order: 3
---

### Question 1. Manage Identity Providers

- Configure the OAuth to use **HTPasswd** as the identity provider.
- Secret name should be **ex280-secret** and Identity Provider name should be **ex280-idp-secret**.

### Create Users

- Create user **bob** with password **indionce**
- Create user **jobs** with password **catalog**
- Create user **john** with password **warniak**
- Create user **armstrong** with password **gluengue**
- Create user **natasha** with password **sestiver**
- Create user **alice** with password **thankyou**

---

## Solution

### ✅ 1. Create the htpasswd file with all users

```bash
htpasswd -c -B -b /tmp/htpasswd bob indionce
htpasswd -B -b /tmp/htpasswd jobs catalog
htpasswd -B -b /tmp/htpasswd john warniak
htpasswd -B -b /tmp/htpasswd armstrong gluengue
htpasswd -B -b /tmp/htpasswd natasha sestiver
htpasswd -B -b /tmp/htpasswd alice thankyou
```
Check:

```bash
cat /tmp/htpasswd
```

### ✅ 2. Create the secret using the htpasswd file

```bash
oc create secret generic ex280-secret --from-file=htpasswd=/tmp/htpasswd -n openshift-config
```
```bash
oc get secret -n openshift-config | grep 280
```
### ✅ 3. Edit the OAuth cluster configuration

```bash
oc get oauth
```
```bash
oc edit oauth cluster
```
```bash
spec:
  identityProviders:
  - name: ex280-idp-secret
    mappingMethod: claim
    type: HTPasswd
    htpasswd:
      fileData:
        name: ex280-secret
```
Save & exit. 
(mnemonic you should remember - I Need My Tea Hot For Now) ☕️🔥

### ✅ 4. Wait for authentication pods to restart

```bash
oc get pods -n openshift-authentication
```
Pods will go through Terminating → Running again.

<img width="623" height="206" alt="image" src="https://github.com/user-attachments/assets/21000f72-e528-401f-8d21-9372c9ee1e98" />

### ✅ 5. Verify all users can log in

```bash
oc login -u bob -p indionce
```
```bash
oc login -u jobs -p catalog
```
```bash
oc login -u john -p warniak
```
```bash
oc login -u armstrong -p gluengue
```
```bash
oc login -u natasha -p sestiver
```
```bash
oc login -u alice -p thankyou
```

### ✅ 6. List users
Login back by using cluster/admin user (ex: kubeadmin in the exam and admin in lab) 
```bash
oc get users
```
### ✅ Result

HTPasswd identity provider configured successfully and users can authenticate.

### 🟨 TECHNICAL EXPLANATION

### 1. HTPasswd Creation

- `-c` → create a new file (use only for the first user)
- `-B` → use bcrypt hashing (**OpenShift requires bcrypt**)
- `-b` → supply password inline

This creates the local user database that OAuth will consume.

---

### 2. Secret in `openshift-config`

OpenShift authentication pulls `htpasswd` **only** from:

- Namespace: **openshift-config**

If you put it anywhere else → **OAuth will not find it**.

- Secret must be named **ex280-secret**

A **generic secret** is the default Kubernetes secret type (`type: Opaque`) used to store any file or key-value data, such as `htpasswd` files.

We use it because:
- `htpasswd` is a normal file
- It does not belong to special secret types like TLS or docker-registry

`--from-file` uploads the file into the secret so OAuth can read it.

---

### 3. Editing OAuth

The object **`oauth cluster`** is the global authentication configuration.

The `identityProviders` block tells OpenShift:

- Enable **HTPasswd** provider
- Use secret named **ex280-secret**
- Name this provider **ex280-idp-secret**

---

### 4. Authentication Operator Restart

After modifying OAuth:

- The authentication operator detects changes
- It automatically restarts pods in:
  - `openshift-authentication`
  - `openshift-oauth-apiserver`
- Once pods return to **Running** → new IDP is active

This behavior is **normal and expected**.

