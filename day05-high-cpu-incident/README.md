# Day 5 – Debugging High CPU Usage on AWS EC2

## Objective

Simulate and troubleshoot a real-world production issue where a Linux server experiences high CPU usage, causing system performance degradation.

This lab focuses on identifying CPU-intensive processes, analyzing system performance, and restoring normal operation.

---

# Architecture

Client (User)
      │
      │ HTTP Request
      ▼
AWS EC2 Instance (Ubuntu Linux)
      │
      ▼
Application / Web Server
      │
      ▼
CPU Resources (Overloaded)

---

# Scenario

The server becomes slow and unresponsive.

Typical symptoms:

- High response time from applications
- SSH sessions lagging
- Commands taking longer to execute

This indicates potential CPU exhaustion.

---

# Investigation

To diagnose the issue, system performance was monitored.

---

## Step 1 – Check CPU Usage


top


Observation:

- CPU usage close to 100%
- System performance degraded

---

## Step 2 – Use htop for Better Visualization


htop


This provides a clearer view of running processes and CPU utilization.

---

## Step 3 – Identify High CPU Process

List processes sorted by CPU usage:


ps aux --sort=-%cpu | head


A process consuming excessive CPU was identified.

---

# Failure Simulation

To simulate high CPU usage, a CPU-intensive process was created:


yes > /dev/null


This command continuously generates output and consumes CPU resources.

After running this command:

- CPU usage increased significantly
- System performance degraded

---

# Root Cause

A process was consuming excessive CPU resources.

In production environments, this could be caused by:

- Infinite loops in applications
- Misconfigured services
- Background jobs consuming resources
- Faulty deployments

---

# Resolution

Identify the process ID (PID):


ps aux | grep yes


Terminate the process:


kill PID


If the process does not stop:


kill -9 PID


---

# Verification

Check CPU usage again:


top


CPU utilization should return to normal levels.

System responsiveness should improve.

---

# Incident Timeline


10:00 – System performance degraded
10:02 – Checked CPU usage using top
10:03 – Observed CPU usage near 100%
10:04 – Used ps to identify high CPU process
10:05 – Identified "yes" process consuming CPU
10:06 – Terminated process
10:07 – CPU usage normalized


---

# Commands Used

| Command | Purpose |
|------|------|
top | monitor CPU usage |
htop | advanced process monitoring |
ps aux | list running processes |
kill | terminate process |
kill -9 | force terminate process |

---

# Lessons Learned

- High CPU usage can significantly impact system performance
- Identifying the root cause process is critical before taking action
- Monitoring tools like top and htop are essential in production environments
- Resource management is a key responsibility of DevOps engineers

---

# Interview Questions

## How do you troubleshoot high CPU usage in Linux?

Typical steps:

1. Monitor CPU usage using `top` or `htop`
2. Identify processes consuming high CPU
3. Analyze the process behavior
4. Terminate or fix the process
5. Verify system performance

---

## What command is used to monitor CPU usage?


top


---

## How do you find processes consuming high CPU?


ps aux --sort=-%cpu


---

## What does the `kill` command do?

The `kill` command is used to terminate processes by sending signals to a process ID.

---

# DevOps Takeaway

High CPU incidents are common in production systems.

Effective troubleshooting requires:

Infrastructure awareness → System monitoring → Process analysis → Corrective action

Understanding system performance behavior is essential for DevOps and SRE roles.
