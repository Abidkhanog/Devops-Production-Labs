Day 1 — Debugging SSH Access Failure on AWS EC2

Objective
Simulate and troubleshoot a real-world production issue where engineers are unable to SSH into an EC2 Linux server.

Architecture
Local Machine
    │
    │ SSH
    ▼
AWS EC2 Instance (Ubuntu)

Scenario
A Linux server running on AWS EC2 suddenly becomes inaccessible via SSH.
Attempting to connect results in connection errors.
ssh ubuntu@public-ip
Error:
connection refused
This scenario simulates a common production incident where engineers cannot access a server remotely.

Investigation
First step is to determine whether the server itself is reachable.
Check:
EC2 instance status
Security group rules
SSH service status
The instance was running and security group allowed port 22.
Next step was to inspect the SSH service.

Commands Used
Check SSH service status:
systemctl status ssh
Stop SSH service:
sudo systemctl stop ssh
Check logs:
journalctl -u ssh
Check open ports:
ss -tulnp

Failure Simulation
The SSH service was manually stopped:
sudo systemctl stop ssh
After this, attempts to connect from the local machine failed.
ssh ubuntu@public-ip
Result:
connection refused

Debugging Process
Verified EC2 instance was running.


Confirmed security group allowed SSH (port 22).


Checked SSH service status.


systemctl status ssh
Output showed the SSH service was inactive.
Next step was to inspect system logs.
journalctl -u ssh
Logs confirmed the SSH service had been stopped.

Root Cause
SSH daemon (sshd) was not running on the server.
Without the SSH service, the server cannot accept incoming SSH connections.

Resolution
Restart the SSH service.
sudo systemctl start ssh
Verify service status:
systemctl status ssh

Verification
Attempt SSH connection again.
ssh ubuntu@public-ip
Connection succeeded after restarting the service.

Lessons Learned
SSH service must be running for remote access.
Logs are critical for diagnosing service failures.
Always verify infrastructure (EC2 status, security groups) before debugging the OS.

DevOps / SRE Takeaway
When remote access fails, always troubleshoot in layers:
Infrastructure Layer → AWS EC2
Network Layer → Security Groups
Service Layer → SSH daemon
System Logs → journalctl
This layered debugging approach is standard practice in production environments.

