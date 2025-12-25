---
layout: default
title: Q1 – HTPasswd IDP
nav_order: 3
---

# Q1 – Configure HTPasswd Identity Provider

---

## Question – Manage Identity Providers

Configure **OAuth** to use **HTPasswd** as the identity provider with the following requirements:

* **Secret name:** `ex280-secret`
* **Identity Provider name:** `ex280-idp-secret`

### Users to Create

| User      | Password |
| --------- | -------- |
| bob       | indionce |
| jobs      | catalog  |
| john      | warniak  |
| armstrong | gluengue |
| natasha   | sestiver |
| alice     | thankyou |

---

## Solution – HTPasswd Identity Provider

### 1. Create the HTPasswd file

Verify current user:

```bash
oc whoami
```

Create the htpasswd file (use `-c` **only for the first user**):

```bash
htpasswd -c -B -b /tmp/htpasswd bob indionce
htpasswd -B -b /tmp/htpasswd jobs catalog
htpasswd -B -b /tmp/htpasswd john warniak
htpasswd -B -b /tmp/htpasswd armstrong gluengue
htpasswd -B -b /tmp/htpasswd natasha sestiver
htpasswd -B -b /tmp/htpasswd alice thankyou
```

Verify file contents:

```bash
cat /tmp/htpasswd
```

---

### 2. Create the secret in `openshift-config`

```bash
oc create secret generic ex280-secret \
--from-file=htpasswd=/tmp/htpasswd \
-n openshift-config
```

Verify:

```bash
oc get secrets -n openshift-config | grep 280
```

---

### 3. Edit the OAuth cluster configuration

Check OAuth object:

```bash
oc get oauth
```

Edit OAuth:

```bash
oc edit oauth cluster
```

Add the following block **under `spec:`**
(Indentation is critical):

```yaml
identityProviders:
- name: ex280-idp-secret
  mappingMethod: claim
  type: HTPasswd
  htpasswd:
    fileData:
      name: ex280-secret
```

Save and exit.

> **Mnemonic:** *I Need My Tea Hot For Now* ☕
> (identityProviders → name → mappingMethod → type → htpasswd → fileData → name)

---

### 4. Wait for authentication pods to restart

```bash
oc get pods -n openshift-authentication -w
```

Pods will transition:

```
Terminating → Running

<img width="623" height="206" alt="image" src="https://github.com/user-attachments/assets/e37280c0-90e2-433d-a429-2092d8323553" />

```

This is **expected behavior**.

---

### 5. Verify user logins

```bash
oc login -u bob -p indionce
oc login -u jobs -p catalog
oc login -u john -p warniak
oc login -u armstrong -p gluengue
oc login -u natasha -p sestiver
oc login -u alice -p thankyou
```

✔ Successful login confirms HTPasswd IDP is working.

---

### 6. List users

Log back in as cluster admin
(`kubeadmin` in exam, `admin` in lab):

```bash
oc get users
```
<img width="623" height="130" alt="image" src="https://github.com/user-attachments/assets/46735e7a-1379-40db-8782-5f2f98bfed83" />

---

## Explanation

### Create HTPasswd File

* Stores users in **bcrypt** format
* OpenShift **requires bcrypt**
* Acts as a local user database for OAuth

### Secret in `openshift-config`

* OAuth reads secrets **only** from `openshift-config`
* Secret **must** be named `ex280-secret`
* Uses `generic` (Opaque) secret because htpasswd is a normal file

### Editing OAuth

* `oauth cluster` is the **global authentication config**
* `identityProviders` enables new login mechanisms
* Provider name = `ex280-idp-secret`

### Authentication Operator Restart

After OAuth change:

* Authentication operator detects update
* Pods in:

  * `openshift-authentication`
  * `openshift-oauth-apiserver`
    restart automatically
* Once pods are `Running`, IDP is active

---

## Technical Notes (Exam Important)

### htpasswd flags

* `-c` → create file (**first user only**)
* `-B` → bcrypt hashing (**mandatory**)
* `-b` → password provided inline

### Common Failures

* Secret in wrong namespace ❌
* Missing bcrypt ❌
* Wrong indentation in OAuth ❌

---

## Exam Tips (DO280)

* HTPasswd is **cluster-wide**
* Secret **must** be in `openshift-config`
* OAuth changes always restart auth pods
* Login test is the **final verification**
