# Day 2 – Debugging Nginx Service Failure on AWS EC2

## Objective

Simulate and troubleshoot a real-world production issue where a website hosted on an AWS EC2 instance stops responding due to a web server failure.

This lab focuses on identifying the issue, investigating the root cause, and restoring the service.

---

# Architecture

Client (Browser)
      │
      │ HTTP Request (Port 80)
      ▼
Internet
      │
      ▼
AWS EC2 Instance
(Ubuntu Linux)
      │
      │
      ▼
Nginx Web Server
      │
      ▼
HTTP Response

---

# Scenario

A website hosted on an AWS EC2 instance suddenly becomes unreachable.

Users attempting to access the website encounter connection errors.

Example:


This site can’t be reached


or


Connection refused


This type of incident is very common in production environments where services may crash or stop unexpectedly.

---

# Investigation

To troubleshoot the issue, the following checks were performed.

### Step 1: Verify Web Server Service

Check the status of the Nginx service.


systemctl status nginx


Output indicated the service was not running.

---

### Step 2: Verify Running Processes

Check if Nginx processes were active.


ps aux | grep nginx


No active Nginx processes were found.

---

### Step 3: Check Listening Ports

Verify if the server was listening on port 80.


ss -tulpn


Port 80 was not listed, indicating that no service was listening for HTTP requests.

---

### Step 4: Test Web Server Locally

Attempt to access the web server locally.


curl localhost


No response was returned.

---

# Failure Simulation

To reproduce the incident, the Nginx service was manually stopped.


sudo systemctl stop nginx


After stopping the service:

- The server stopped listening on port 80
- Website became inaccessible

---

# Root Cause

The Nginx web server service had stopped running.

Without the service running, the server could not accept HTTP requests.

---

# Resolution

Restart the Nginx service.


sudo systemctl start nginx


Verify service status:


systemctl status nginx


---

# Verification

Confirm that the server is listening on port 80.


ss -tulpn


Test the website locally.


curl localhost


The Nginx welcome page response confirms the service is working again.

---

# Troubleshooting Workflow

Typical debugging flow for web server issues:

User reports website down  
↓  
Check server status  
↓  
Check web server service  
↓  
Check open ports  
↓  
Check logs  
↓  
Restart service  
↓  
Verify application response  

---

# Commands Used

| Command | Purpose |
|------|------|
systemctl status nginx | Check service status |
systemctl start nginx | Start nginx service |
systemctl stop nginx | Stop nginx service |
ss -tulpn | View open ports |
ps aux | Check running processes |
curl localhost | Test web server locally |

---

# Lessons Learned

- Web servers must be actively running to serve HTTP requests
- Checking service status is the first step in debugging server issues
- Port verification helps identify whether services are listening
- Local testing using curl helps isolate network vs service problems

---

# Interview Questions

### How would you debug a website not responding on a Linux server?

Typical steps:

1. Verify server availability
2. Check web server service status
3. Inspect open ports
4. Check system logs
5. Restart the service if necessary

---

### What command checks if a service is running?


systemctl status service_name


---

### What command checks listening ports?


ss -tulpn


---

### How can you test a web server locally?


curl localhost


---

# DevOps Takeaway

When troubleshooting infrastructure problems, always investigate issues in layers:

Infrastructure → Network → Service → Ports → Logs

This approach helps isolate failures quickly and restore services efficiently.
