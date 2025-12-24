---
layout: default
title: Q1 – Configure HTPasswd Identity Provider
nav_order: 3
---

Question 1 – Configure HTPasswd Identity Provider
Task

Configure the OpenShift cluster authentication to use an HTPasswd identity provider.

Requirements

Create an HTPasswd file containing the following users:

Username	Password
bob	indionce
jobs	catalog
john	warniak
armstrong	gluengue
natasha	sestiver
alice	thankyou

Create a secret named ex280-secret

Store the HTPasswd file in the secret

The secret must be created in the openshift-config namespace

Configure OAuth to use:

Identity Provider name: ex280-idp-secret

Mapping method: claim

Verify that users can successfully authenticate

Solution
1. Create HTPasswd file
htpasswd -c -B -b /tmp/htpasswd bob indionce
htpasswd -B -b /tmp/htpasswd jobs catalog
htpasswd -B -b /tmp/htpasswd john warniak
htpasswd -B -b /tmp/htpasswd armstrong gluengue
htpasswd -B -b /tmp/htpasswd natasha sestiver
htpasswd -B -b /tmp/htpasswd alice thankyou

2. Create secret
oc create secret generic ex280-secret \
  --from-file=htpasswd=/tmp/htpasswd \
  -n openshift-config

3. Configure OAuth
oc edit oauth cluster

spec:
  identityProviders:
  - name: ex280-idp-secret
    mappingMethod: claim
    type: HTPasswd
    htpasswd:
      fileData:
        name: ex280-secret

4. Wait for authentication pods
oc get pods -n openshift-authentication


(All pods must be Running.)

5. Verify authentication
oc login -u bob -p indionce


(Optional check)

oc get users

Result

HTPasswd identity provider configured successfully and users can authenticate.
