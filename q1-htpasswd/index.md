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
