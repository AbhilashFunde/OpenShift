---
layout: default
title: Q1 – Configure HTPasswd Identity Provider
nav_order: 3
---

# Question 1 – Configure HTPasswd Identity Provider

## Task

Configure the OpenShift cluster authentication to use an **HTPasswd identity provider**.

---

## Requirements

Create an HTPasswd file containing the following users:

| Username   | Password  |
|-----------|-----------|
| bob       | indionce  |
| jobs      | catalog   |
| john      | warniak   |
| armstrong | gluengue  |
| natasha   | sestiver  |
| alice     | thankyou  |

Additional requirements:

- Create a secret named **`ex280-secret`**
- Store the HTPasswd file in the secret
- Configure OAuth with:
  - Identity Provider name: **`ex280-idp-secret`**
- Verify that users can authenticate successfully

---

## Solution

### 1. Create HTPasswd file

```bash
htpasswd -c -B -b /tmp/htpasswd bob indionce
htpasswd -B -b /tmp/htpasswd jobs catalog
htpasswd -B -b /tmp/htpasswd john warniak
htpasswd -B -b /tmp/htpasswd armstrong gluengue
htpasswd -B -b /tmp/htpasswd natasha sestiver
htpasswd -B -b /tmp/htpasswd alice thankyou
```
2. Create secret
```bash
oc create secret generic ex280-secret --from-file=htpasswd=/tmp/htpasswd -n openshift-config
```
3. Configure OAuth
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
4. Wait for authentication pods
```bash
oc get pods -n openshift-authentication
```
(All pods must be Running.)

5. Verify authentication
```bash
oc login -u bob -p indionce
```
```bash
oc get users
```
Result
HTPasswd identity provider configured successfully and users can authenticate.
