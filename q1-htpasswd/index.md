---
layout: default
title: Q1 – Configure HTPasswd Identity Provider
nav_order: 3
---

Question 1. Manage Identity Providers:
•	Configure the Oauth to use HTPasswd as the identity provider.
•	Secret name should be ex280-secret and Identity Provider name should be ex280-idp-secret.
Create Users:
•	Create user bob with password indionce
•	Create user jobs with password catalog
•	Create user john with password warniak
•	Create user armstrong with password gluengue
•	Create user natasha with password sestiver
•	Create user alice with password thankyou


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
### 2. Create secret

```bash
oc create secret generic ex280-secret --from-file=htpasswd=/tmp/htpasswd -n openshift-config
```
### 3. Configure OAuth

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
### 4. Wait for authentication pods

```bash
oc get pods -n openshift-authentication
```
(All pods must be Running.)

### 5. Verify authentication

```bash
oc login -u bob -p indionce
```
```bash
oc get users
```
### Result

HTPasswd identity provider configured successfully and users can authenticate.
