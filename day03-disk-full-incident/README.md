# Day 3 – Debugging Disk Full Incident on AWS EC2

## Objective

Simulate and troubleshoot a real-world production issue where a Linux server runs out of disk space, causing applications and services to fail.

This lab focuses on identifying disk usage issues, investigating large files, and restoring system functionality.

---

# Architecture

Client (Browser)
      │
      │ HTTP Request
      ▼
Internet
      │
      ▼
AWS EC2 Instance
(Ubuntu Linux)
      │
      │
      ▼
Application / Web Server
      │
      ▼
EBS Volume (Disk Storage)

---

# Scenario

Applications on the EC2 server begin failing and logs show errors related to disk usage.

Typical error message observed in production environments:


No space left on device


When disk space reaches 100%, Linux systems cannot write logs, create temporary files, or update databases.

This often causes services to crash or behave unpredictably.

---

# Investigation

To diagnose the issue, disk usage was inspected.

### Step 1 – Check Disk Usage


df -h


Example output:


Filesystem Size Used Avail Use%
/dev/xvda1 20G 20G 0 100%


This confirms the disk is completely full.

---

### Step 2 – Identify Large Directories

To locate which directories consume the most space:


du -h --max-depth=1 /


Common directories that may grow large:


/var/log
/var/lib
/home
/tmp


---

### Step 3 – Inspect Log Files

Log files are often responsible for disk exhaustion.

Navigate to log directory:


cd /var/log
ls -lh


Large files such as the following may appear:


syslog
auth.log
nginx.log


---

# Failure Simulation

To simulate disk exhaustion, a large file was created.


fallocate -l 2G largefile


This increases disk usage and helps reproduce the issue.

Verify disk usage again:


df -h


Disk usage should now reflect increased utilization.

---

# Root Cause

Disk storage was exhausted due to large files occupying available space.

When disk reaches full capacity, the system cannot:

- write logs
- create temporary files
- store application data

This leads to service failures.

---

# Resolution

Remove unnecessary files to free disk space.

Example:


rm largefile


Log files can also be cleared if needed:


sudo truncate -s 0 /var/log/syslog


---

# Verification

Verify disk space has been restored.


df -h


Example output:


Filesystem Size Used Avail Use%
/dev/xvda1 20G 12G 8G 60%


Applications should now operate normally.

---

# Incident Timeline


10:00 – Application errors reported
10:02 – Checked server status
10:04 – Ran df -h and detected disk usage at 100%
10:06 – Investigated directories using du
10:08 – Identified large files consuming disk space
10:10 – Removed unnecessary files
10:11 – Disk space restored
10:12 – Applications functioning normally


---

# Commands Used

| Command | Purpose |
|------|------|
df -h | check disk usage |
du -h | find large directories |
find | locate large files |
rm | remove unnecessary files |
truncate | clear log files |

---

# Lessons Learned

- Disk monitoring is critical for production servers.
- Log files can grow rapidly and consume large amounts of disk space.
- Regular log rotation helps prevent disk exhaustion.
- Investigating disk usage should always start with `df` and `du`.

---

# Interview Questions

### How would you troubleshoot a disk full issue on a Linux server?

Typical steps:

1. Check disk usage using `df -h`
2. Identify large directories using `du`
3. Locate large files using `find`
4. Remove unnecessary files
5. Verify disk usage again

---

### What command checks disk usage?


df -h


---

### What command helps locate large directories?


du -h --max-depth=1 /


---

### Why can disk full issues cause application failures?

When the disk reaches full capacity, the system cannot write logs, temporary files, or application data, which can cause services to crash.
