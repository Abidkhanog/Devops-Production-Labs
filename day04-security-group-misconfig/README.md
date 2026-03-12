Day 4 – Debugging AWS Security Group Misconfiguration
# Day 4 – Debugging AWS Security Group Misconfiguration

## Objective

Simulate and troubleshoot a real-world production issue where a website hosted on an AWS EC2 instance becomes inaccessible due to a network configuration problem.

This lab focuses on diagnosing infrastructure-level issues and understanding how AWS security groups control traffic to EC2 instances.

---

# Architecture

Client (Browser)
      │
      │ HTTP Request (Port 80)
      ▼
Internet
      │
      ▼
AWS Security Group
      │
      │ (Traffic Filter)
      ▼
EC2 Instance (Ubuntu Linux)
      │
      ▼
Apache Web Server
      │
      ▼
HTTP Response

---

# Scenario

Users report that the website hosted on an EC2 instance is not accessible.

Example browser error:


This site can’t be reached


or


Connection timed out


However, the EC2 instance is running and the Nginx web server is active.

This indicates the issue may exist in the network or firewall configuration.

---

# Investigation

To troubleshoot the problem, several checks were performed.

---

## Step 1 – Verify EC2 Instance Status

Navigate to:

AWS Console → EC2 → Instances

Confirm that the instance state is:


Running


---

## Step 2 – Verify Web Server Service

Connect to the server using SSH.


ssh -i key.pem ubuntu@PUBLIC-IP


Check httpd service status:


systemctl status httpd


Expected output:


active (running)


This confirms that the web server is operational.

---

## Step 3 – Test Web Server Locally

Test the web server from within the server.


curl localhost


Expected output:


Welcome to httpd ( in my case it was a custom page )


This confirms the application layer is functioning.

---

## Step 4 – Verify Listening Ports

Check if the server is listening for HTTP requests.


ss -tulpn


Expected output should include:


LISTEN 0 511 *:80


This confirms httpd is listening on port 80.

---

## Step 5 – Identify Network-Level Issue

Even though:

- EC2 instance is running
- httpd service is active
- Port 80 is listening

The website is still unreachable externally.

This indicates a problem with **AWS security group rules**.

---

# Failure Simulation

To reproduce the issue, the HTTP rule was removed from the EC2 security group.

Security group configuration:


SSH (22) → Allowed
HTTP (80) → Removed


After removing the HTTP rule:

- SSH access still worked
- Web server was running
- Website became inaccessible

This simulated a real-world network configuration failure.

---

# Root Cause

The AWS security group did not allow inbound traffic on port 80.

Security groups act as **virtual firewalls** controlling which traffic can reach EC2 instances.

Without allowing HTTP traffic, external users cannot access the web server.

---

# Resolution

Add an inbound rule allowing HTTP traffic.

Security group configuration:


Type: HTTP
Port: 80
Source: 0.0.0.0/0


Save the rule.

---

# Verification

Test the website again using the browser.


http://EC2-PUBLIC-IP


The httpd page should now load successfully.

This confirms that network access has been restored.

---

# Custom Apache Landing Page

After restoring access, the default Nginx welcome page was replaced with a custom HTML page.

This simulates deploying application content after infrastructure issues are resolved.

---

## Default Apache Web Root


/var/www/html


## Deploy Custom HTML Page

Create a new index page.


sudo nano /var/www/html/index.html


Add custom HTML content.

Restart httpd:


sudo systemctl restart httpd


---

## Verification

Test locally:


curl localhost


Test from browser:


http://EC2-PUBLIC-IP


The custom landing page should now appear.

---

# Incident Timeline


10:00 – Users reported website inaccessible
10:02 – Verified EC2 instance running
10:04 – Checked httpd service status
10:06 – Tested web server locally with curl
10:08 – Verified server listening on port 80
10:10 – Investigated AWS security group rules
10:12 – Identified missing HTTP rule
10:14 – Added port 80 inbound rule
10:15 – Website access restored


---

# Commands Used

| Command | Purpose |
|------|------|
systemctl status httpd | verify web server service |
curl localhost | test web server locally |
ss -tulpn | check open ports |
systemctl restart httpd | restart web server |

---

# Lessons Learned

- AWS security groups act as firewalls for EC2 instances
- Infrastructure issues can block services even if applications are running
- Debugging should follow a layered approach:
  
Infrastructure → Network → Service → Application

- Always verify both service health and network configuration.

---

# Interview Questions

## What is a security group in AWS?

A security group is a virtual firewall that controls inbound and outbound traffic for EC2 instances.

---

## How would you debug a website not accessible on EC2?

Typical troubleshooting steps:

1. Verify instance state
2. Check web server service
3. Test server locally using curl
4. Verify listening ports
5. Inspect security group rules

---

## Why might a website be inaccessible even when the web server is running?

Because network-level configurations such as security groups or firewalls may block incoming traffic.
