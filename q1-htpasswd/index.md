---
layout: default
title: Q1 – Configure HTPasswd Identity Provider
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

### ✅ 1. Create HTPasswd file

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

### ✅ 2. Create secret

```bash
oc create secret generic ex280-secret --from-file=htpasswd=/tmp/htpasswd -n openshift-config
```
```bash
oc get secret -n openshift-config | grep 280
```
### ✅ 3. Configure OAuth

```bash
oc get ouath
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

### ✅ 4. Wait for authentication pods

```bash
oc get pods -n openshift-authentication
```
Pods will go through Terminating → Running again.

<img width="623" height="206" alt="image" src="https://github.com/user-attachments/assets/21000f72-e528-401f-8d21-9372c9ee1e98" />

### ✅ 5. Verify authentication

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
### Result

HTPasswd identity provider configured successfully and users can authenticate.
