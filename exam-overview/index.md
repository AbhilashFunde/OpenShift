---
layout: default
title: Exam Overview & Rules
nav_order: 2
---

# Introduction & Exam Overview

**DO280 / EX280 – OpenShift Administration**

---

## Section 1 — Welcome & Exam Overview

### EX280 / DO280 Exam Structure

* **Duration:** 3 hours
* **Total Score:** 300 points
* **Passing Score:** 210 / 300 (**70%**)
* **Fail Condition:** Anything below 70% (even 69.99%)
* **Total Questions:** 22 (all are hands-on lab tasks)

> **Important:**
> You must be able to perform **all 22 tasks confidently**, at least **7 times without reference**, before attempting the exam.

Without sufficient hands-on practice, passing the exam is **extremely difficult**.

---

### OpenShift Versions in Exam

* Older exams: OpenShift **4.10**, **4.12**
* Training environment commonly uses: **4.14**
* Latest known exam version: **4.18**

Even if the version changes:

* Commands remain the same
* YAML structures remain the same
* Core concepts remain the same

✔ **Version upgrades do NOT change exam difficulty**

---

## Section 2 — Certification Validity & Versions

### Certification Validity

* EX280 certification is valid for **3 years**
* Certificate clearly displays:

  * Issue date
  * Expiry date

After expiry:

* Certification automatically becomes invalid
* You must **reappear for the exam**

> Each successful reattempt gives **another full 3 years** of validity.

---

### Should You Worry About Version Differences?

**No. You should NOT worry.**

Why?

* `oc` commands remain consistent
* YAML objects (Deployment, Route, SCC, NetworkPolicy, OAuth, etc.) remain unchanged
* Only UI appearance may slightly differ

Your preparation remains **safe across versions**.

---

## Section 3 — Exam Options

### Option 1 — Remote Exam (Not Recommended)

Remote exam uses a **Red Hat bootable ISO (USB)**.

#### Common Problems

* Hardware compatibility issues:

  * Wi-Fi / LAN may fail
  * Camera or microphone issues
  * External monitor detection problems
* **Internet disconnection = exam failure**
* Proctors are extremely strict:

  * Camera angle
  * Desk cleanliness
  * Movement and noise
* **External camera is mandatory**

> ❗ Avoid remote exam unless your laptop is 100% compatible with Red Hat ISO.

---

### Option 2 — Red Hat Exam Center (Recommended)

Best and safest option.

Advantages:

* Preconfigured exam systems
* Stable internet
* Power backup
* No USB boot or camera issues
* Immediate staff support

> **Strong recommendation:**
> Always choose a Red Hat Authorized Training Center if possible.

---

## Section 4 — Pre-Exam Checks (15-Minute Validation)

Before the exam starts, you get **15 minutes** for mandatory system verification.

If you fail any check:

* Exam will NOT start
* You may need to reschedule
* Attempt may be counted

---

### Pre-Exam Validation Steps

#### 1. Camera Check

* Webcam + external camera must work
* Face must be clearly visible

#### 2. Microphone Check

* Mic must work
* Background noise should be minimal

#### 3. Room Scan

You must show:

* Desk
* Floor
* Ceiling
* Behind monitor
* Left & right sides
* Under the table

❌ No books, papers, phones, watches, notes, or extra devices allowed.

---

#### 4. ID Verification

* Must be valid government-issued ID
* Clear photo, no reflection

❌ **Do NOT use laminated IDs** (camera glare causes rejection)

Accepted IDs (region-dependent):

* Passport
* Aadhaar
* PAN
* Voter ID
* Driving License

---

#### 5. Desk Check

Your desk must contain **only**:

* Keyboard
* Mouse

Nothing else.

> Even a small sticky note can cause issues.

---

## Section 5 — Exam Desktop Interface

After validation, the exam desktop loads.

### Exam Environment

* OS: **Red Hat Enterprise Linux (RHEL)**
* No Windows / Ubuntu
* Locked-down environment

---

### Key Applications

#### 1. Terminal (MOST IMPORTANT)

Used for:

* SSH into workbench VM
* All `oc` commands
* YAML creation and editing
* Troubleshooting

> **90% of the exam is done in Terminal**

---

#### 2. Firefox Browser

Used for:

* Exam questions
* Instructions
* Cluster details

---

#### 3. Proctor Chat Window

* Used only if proctor contacts you
* Do NOT use unless there is a technical emergency

---

### Recommended Layout

* **Left:** Terminal
* **Right:** Firefox (exam questions)

This minimizes time loss and confusion.

---

## Section 6 — Important Instructions Page (CRITICAL)

The **first page** you must read carefully.

Contains:

* Workbench hostname
* Workbench credentials
* Root password
* Cluster API URL
* kubeadmin credentials
* Exam rules

> Spend **at least 5 minutes** reading this page carefully.

---

### Key Items on Instructions Page

#### 1. Workbench Machine

* Intermediate VM where all work is done
* You SSH into this first

#### 2. kubeadmin.txt

Located in:

```bash
~/kubeadmin.txt
```

Contains:

* Cluster username
* Cluster password
* API URL

> Without this file, you cannot log in to the cluster.

---

## Section 7 — How Exam Questions Work

* Total **22 clickable questions**
* Questions are **NOT locked**
* You can jump between questions freely

### Strategy

* Solve questions one by one, as a few questions will depend on others.
* Skip difficult ones
* Return later

---

### No Answer Submission

* No answer box
* No typing explanations
* **Your cluster state is your answer**

Everything you apply using:

* `oc create`
* `oc apply`
* `oc edit`

is automatically saved.

---

## Section 8 — Accessing the Cluster (VERY IMPORTANT)

### Step 1 — SSH into Workbench

```bash
ssh <username>@<workbench-host>
```

---

### Step 2 — Locate kubeadmin.txt

```bash
ls -l
cat kubeadmin.txt
```

---

### Step 3 — oc login

```bash
oc login -u <user> -p <password> <API_URL>
```

---

### Step 4 — Verify Login

```bash
oc whoami
oc get projects
```

---

### Step 5 — Open Console (See URL In Instruction Page )

```bash
oc whoami --show-console -- Will Not Work
```
---

## Final Exam Tips

* Do not panic
* Read instructions carefully
* Solve what you know first
* Use Terminal for actions
* Keep files in your **home directory only**
* Never argue with the proctor

> **Calm + practice = pass DO280**
