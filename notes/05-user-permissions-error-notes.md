# Notes – Ticket 05: User Permission Error

## Issue Overview
This ticket simulated a common Linux user permission problem: a non-root user unable to perform administrative actions such as editing system files or accessing protected directories. The exercise highlighted the importance of verifying user group memberships, sudo privileges, and safe privilege escalation practices.

---

## Environment
- Ubuntu 22.04 LTS VM  
- VirtualBox 7.1.6  
- Host OS: Windows 11  
- VM User Account: `jordan-bradfield` (non-root)

---

## Symptoms Observed
- Regular user cannot modify `/etc/hosts` (nano displays “file is unwritable”).  
- Regular user cannot view `/etc/shadow` (`Permission denied`).  
- User can escalate privileges using `sudo` with correct password.  

These symptoms indicate that the user account lacks default sudo privileges but can temporarily elevate permissions when needed.

---

## Key Diagnostic Commands and Their Purpose

### 1. Confirm current user
```bash
whoami
```
- **Purpose:** Verify which account is active.  
- **Finding:** `jordan-bradfield` (non-root)

### 2. Attempt to edit protected file without sudo
```bash
nano /etc/hosts
```
- **Purpose:** Test write access to system files.  
- **Finding:** File is unwritable.

### 3. Attempt to read restricted file
```bash
cat /etc/shadow
```
- **Purpose:** Test read access to sensitive files.  
- **Finding:** Permission denied.

### 4. Confirm sudo privilege works
```bash
sudo ls
```
- **Purpose:** Verify that privilege escalation is possible with correct password.  
- **Finding:** Command succeeded, confirming sudo works.

### 5. Check group memberships
```bash
groups
```
- **Purpose:** Confirm if user belongs to `sudo` group.  
- **Finding:** User was not initially in the `sudo` group.

### 6. Verify file permissions
```bash
ls -l /etc/hosts
ls -l /etc/shadow
```
- **Purpose:** Identify who can access critical system files.  
- **Finding:** Only root or sudo group can modify/read.

### 7. Review sudoers configuration
```bash
sudo cat /etc/sudoers
sudo visudo -c
```
- **Purpose:** Confirm which users/groups have sudo privileges and validate syntax.  
- **Finding:** Default sudo policy applied via `sudo` group; syntax valid.

---

## Investigation & Findings
- Regular user lacks membership in `sudo` group → no default administrative privileges.  
- Permissions on system files prevent non-privileged edits.  
- Sudo access available when correct password provided.  
- No unintended privilege escalations detected.  

Root cause: user account configured as standard user without sudo group membership.

---

## Resolution & Recovery
- Added user to `sudo` group to enable administrative actions:
```bash
sudo usermod -aG sudo jordan-bradfield
```
- No changes made directly to `/etc/sudoers`; default sudo group policy applied.  
- Verified user could successfully edit `/etc/hosts` and access `/etc/shadow` with `sudo`.

---

## Verification After Fix

### 1. Edit protected file
```bash
sudo nano /etc/hosts
```
- **Result:** File successfully opened in write mode.

### 2. Access restricted file
```bash
sudo ls /etc/shadow
```
- **Result:** File listed successfully.

### 3. Confirm group membership
```bash
groups
```
- **Result:** User now part of `sudo` group. No unnecessary groups present.

---

## Commands Cheat Sheet (Used During Troubleshooting)
- `whoami` — Confirms active user account.  
- `groups` — Shows user group memberships.  
- `ls -l` — Inspects file permissions.  
- `nano /etc/hosts` — Test file edit permission.  
- `cat /etc/shadow` — Test file read permission.  
- `sudo ls` — Confirms general sudo access.  
- `sudo cat /etc/sudoers` — Review sudo policy.  
- `sudo visudo -c` — Validate sudoers syntax.  
- `sudo usermod -aG sudo <user>` — Add user to sudo group.

---

## Lessons & Reflection
- Always verify group memberships and sudo privileges when troubleshooting permission errors.  
- Standard users cannot modify system files; this is intentional for security.  
- Use `visudo -c` to safely validate sudoers file syntax instead of editing directly.  
- Ensure minimal privilege principle: only grant sudo to users who require administrative access.  
- Early verification of permissions prevents wasted troubleshooting time and potential misconfigurations.
