---
title: DO280 / EX280 Exam Overview
---

# 🎓 DO280 / EX280 – Introduction & Exam Overview

This page explains the **real exam environment, structure, and rules**
for the **Red Hat OpenShift DO280 / EX280** certification exam.

Read this fully **before starting Q1**.

---

## ⭐ SECTION 1 — Exam Timing & Structure

- **Exam duration:** 3 hours  
- **Total marks:** 300  
- **Passing score:** 210 / 300 (**70% exactly**)  
  > Even **69.99% = FAIL**
- **Total questions:** 22

> ⚠️ You MUST prepare **all 22 questions**.  
> Do not attempt the exam unless you can perform the lab tasks  
> **at least 5 times without any reference**.

Without proper practice, passing is extremely difficult.

---

### OpenShift Versions Used
- Older exams: **4.10 / 4.12**
- Training & practice: **4.14**
- Latest available version: **4.18**

📌 Even if versions change, **exam pattern remains the same**.

---

## ⭐ SECTION 2 — Certification Validity & Versions

### ✔ Certification Validity
- Certification validity: **3 years**
- Certificate shows:
  - Issued Date
  - Expiry Date
- After expiry, you must **reappear for the exam**
- Every renewal gives **another full 3 years**

---

### ✔ Should You Worry About Version Changes?
**NO.**

- Commands remain the same
- YAML structure remains the same
- Core concepts remain the same:
  - SCC
  - NetworkPolicy
  - OAuth
  - HPA
  - Routes
  - Deployments

Only **UI appearance** may change slightly.

---

## ⭐ SECTION 3 — Exam Options

### 🖥️ Option 1 — Remote Exam (NOT Recommended)

Remote exam requires booting from a **Red Hat ISO USB**.

🚨 Common problems:
- Wi-Fi / LAN not detected
- Camera or mic issues
- External monitor not detected
- Internet drops = **exam failure**
- Extremely strict proctoring

External camera is **mandatory**.

> ⚠️ If your laptop is not 100% compatible,  
> you will suffer badly.

---

### 🏫 Option 2 — Exam Center (Strongly Recommended)

Red Hat Authorized Training Centers provide:
- Preconfigured systems
- Stable internet
- Power backup
- No USB boot issues
- Immediate staff support

> ✔ Safer  
> ✔ Easier  
> ✔ No technical surprises  

If possible, **always choose an exam center**.

---

## ⭐ SECTION 4 — Pre-Exam Checks (15 Minutes)

Before the exam timer starts, Red Hat performs **mandatory validation**.

If you fail → exam **will not start**.

---

### 🔍 Pre-Exam Checks Include

#### 📸 Camera Check
- Internal + external camera
- Face clearly visible
- No obstruction

#### 🎤 Microphone Check
- Must work
- Low background noise

#### 🏠 Room Scan
You must show:
- Desk
- Floor
- Ceiling
- Behind monitor
- Under the table

🚫 Not allowed:
- Papers
- Books
- Notes
- Extra devices
- Watches
- Mobile phones

---

#### 🪪 ID Verification
Accepted IDs:
- Passport
- Aadhaar
- PAN
- Voter ID
- Driving License (region dependent)

⚠️ **Do NOT use laminated IDs**  
Camera glare may cause rejection.

---

#### 🖱️ Desk Check
Your desk must contain **only**:
- Keyboard
- Mouse

Even a sticky note can cause failure.

---

## ⭐ SECTION 5 — Exam Desktop Interface

After checks, the exam environment loads.

### 🖥️ Exam System
- OS: **RHEL (Red Hat Enterprise Linux)**
- NOT Windows
- NOT Ubuntu

---

### 📂 Key Applications

#### 🧑‍💻 1. Terminal (MOST IMPORTANT)
Used for:
- SSH into workbench
- `oc` commands
- YAML editing
- Logs & troubleshooting

> “Your terminal is your best friend.”

---

#### 🌐 2. Firefox (Exam Paper)
Contains:
- 22 questions
- Instructions
- Cluster details

---

#### 💬 3. Proctor Chat
- Only for proctor messages
- Do NOT chat unless necessary

---

### 🧠 Working Style Tip
Arrange screen like:
- **Left:** Terminal
- **Right:** Firefox

This saves time and reduces mistakes.

---

## ⭐ SECTION 6 — Important Instructions Page

This is the **most important page of the exam**.

Spend **at least 5 minutes** reading it.

It contains:
- Workbench details
- Cluster API URL
- Credentials
- Exam rules

---

## ⭐ SECTION 7 — How Exam Questions Work

- 22 clickable questions
- Questions are **NOT locked**
- You can solve in **any order**

> Solve easy ones first.  
> Skip hard ones.  
> Return later.

---

### Key Rules
- No answer writing
- No submit button
- Your cluster state = your answer
- Everything auto-saves

---

## ⭐ SECTION 8 — Logging Into the Cluster

### Step 1 — SSH to Workbench
```bash
ssh <username>@workbench.lab.example.com
