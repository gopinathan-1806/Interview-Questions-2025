# Unix / Linux Commands for DevOps Interviews

A practical interview-focused reference for Senior DevOps / Cloud DevOps engineers, emphasizing production troubleshooting scenarios.

## 1. File & Directory Management

```bash
pwd
ls -lah
cd /var/log
mkdir test
touch file.txt
cp file.txt /tmp/
mv file.txt file2.txt
rm file.txt
rm -rf directory/
```

Find hidden files and sizes:
```bash
ls -lah
```

## 2. Finding Files

Find by name:
```bash
find / -name "app.log" 2>/dev/null
```

Modified in last 24 hours:
```bash
find /var/log -type f -mtime -1
```

Larger than 1 GB:
```bash
find / -type f -size +1G 2>/dev/null
```

Older than 30 days:
```bash
find /var/log -type f -mtime +30
```

Production scenario — find files larger than 500 MB:
```bash
find / -type f -size +500M 2>/dev/null
```

## 3. Disk Management

Filesystem usage:
```bash
df -h
```

Directory usage:
```bash
du -sh /*
du -sh /var/*
```

Largest directories:
```bash
du -ah /var | sort -rh | head -10
```

Production disk-full sequence:
```bash
df -h
du -sh /var/*
du -sh /var/log/*
find /var/log -type f -size +500M
```

Deleted files still consuming disk:
```bash
lsof +L1
```

Never blindly delete production files. First identify what is consuming the space and why.

## 4. CPU & Memory

CPU/process monitoring:
```bash
top
htop
```

Memory:
```bash
free -h
```

CPU information:
```bash
lscpu
```

Load average:
```bash
uptime
```

Top CPU processes:
```bash
ps aux --sort=-%cpu | head
```

Top memory processes:
```bash
ps aux --sort=-%mem | head
```

Scenario — application is slow and CPU is 95%:
```bash
top
ps aux --sort=-%cpu | head
```

## 5. Process Management

List:
```bash
ps aux
```

Find:
```bash
ps aux | grep nginx
pgrep -a nginx
```

PID:
```bash
pgrep nginx
```

Graceful termination:
```bash
kill <PID>
```

Force kill, only when necessary:
```bash
kill -9 <PID>
```

Scenario:
```bash
ps aux --sort=-%cpu | head
pgrep -a <process>
kill <PID>
```

## 6. Logs

View:
```bash
cat app.log
```

Last 100 lines:
```bash
tail -100 app.log
```

Follow:
```bash
tail -f app.log
```

Search:
```bash
grep "ERROR" app.log
grep -i "error" app.log
grep -R "connection refused" /var/log/
grep -n "ERROR" app.log
```

Count errors:
```bash
grep -i "error" app.log | wc -l
```

Multiple patterns:
```bash
grep -E "ERROR|WARN|CRITICAL" app.log
```

For a 5 GB log, avoid `cat`. Use:
```bash
grep -i "500" app.log | tail -100
grep -i "ERROR" app.log | tail -100
grep -n "10:35" app.log
sed -n '5000,5100p' app.log
```

## 7. AWK

Example:
```text
192.168.1.10 GET /api 200
192.168.1.11 GET /login 401
192.168.1.12 GET /api 500
```

First column:
```bash
awk '{print $1}' access.log
```

Specific column:
```bash
awk '{print $4}' access.log
```

Sum a column:
```bash
awk '{sum += $5} END {print sum}' file
```

Classic interview question:
```bash
awk '{print $1}' file.txt
```

## 8. SED

Replace:
```bash
sed 's/old/new/g' file.txt
```

Delete lines containing ERROR:
```bash
sed '/ERROR/d' app.log
```

Print lines 10–20:
```bash
sed -n '10,20p' app.log
```

## 9. Networking

IP:
```bash
ip addr
hostname -I
```

Routes:
```bash
ip route
```

Connectivity:
```bash
ping 8.8.8.8
```

DNS:
```bash
nslookup google.com
dig google.com
```

HTTP:
```bash
curl https://example.com
curl -I https://example.com
curl -v https://example.com
```

TCP port:
```bash
nc -zv server.example.com 443
```

Database connectivity scenario:
```bash
ping <db-host>
nslookup <db-host>
ip route
nc -zv <db-host> 5432
curl -v <endpoint>
```

Then investigate firewalls, NSGs/security groups, routes, load balancers, proxies, and application configuration.

## 10. Listening Ports

```bash
ss -tulnp
ss -tulnp | grep 8080
```

Alternative:
```bash
netstat -tulnp
```

Scenario — application says it is running but users cannot connect:
```bash
ps aux | grep app
ss -tulnp
```

Confirm that the process is listening on the expected port/interface.

## 11. Linux Permissions

Check:
```bash
ls -l file.txt
```

Change permissions:
```bash
chmod 755 script.sh
```

Change ownership:
```bash
chown appuser:appgroup file.txt
```

Recursive ownership:
```bash
chown -R appuser:appgroup /opt/app
```

Identity/groups:
```bash
id appuser
```

Permission-denied scenario:
```bash
ls -ld /var/app/logs
id appuser
```

Avoid blindly using:
```bash
chmod 777
```

Use the minimum permissions required.

## 12. Environment Variables

```bash
env
printenv
echo $PATH
export ENV=prod
echo $ENV
```

## 13. Archives

Create:
```bash
tar -czvf backup.tar.gz /var/log/app
```

Extract:
```bash
tar -xzvf backup.tar.gz
```

List:
```bash
tar -tzvf backup.tar.gz
```

## 14. SSH

Connect:
```bash
ssh user@server
```

Specific key:
```bash
ssh -i key.pem user@server
```

Copy file:
```bash
scp file.txt user@server:/tmp/
```

Copy directory:
```bash
scp -r mydir user@server:/tmp/
```

## 15. Systemd Services

```bash
systemctl status nginx
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl enable nginx
```

Logs:
```bash
journalctl -u nginx
journalctl -u nginx -f
```

## 16. Text Processing

Count lines:
```bash
wc -l file.txt
```

Sort:
```bash
sort file.txt
```

Unique values:
```bash
sort file.txt | uniq
```

Count occurrences:
```bash
sort file.txt | uniq -c
```

CSV column:
```bash
cut -d',' -f1 file.csv
```

## 17. Classic DevOps One-Liners

Top 10 largest files:
```bash
find /var -type f -printf '%s %p\n' 2>/dev/null | sort -nr | head -10
```

Count HTTP 500 responses:
```bash
grep -c " 500 " access.log
```

Top IP addresses:
```bash
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head
```

Top memory processes:
```bash
ps aux --sort=-%mem | head -10
```

Top CPU processes:
```bash
ps aux --sort=-%cpu | head -10
```

Disk:
```bash
df -h
```

Largest directories:
```bash
du -sh /var/* | sort -hr
```

Listening ports:
```bash
ss -lntp
```

DNS:
```bash
dig api.example.com
```

HTTP:
```bash
curl -I https://api.example.com
```

## 18. Senior-Level Production Scenario

**Problem:** A production Linux server has 95% disk utilization and users see intermittent failures.

### Step 1 — Confirm
```bash
df -h
```

### Step 2 — Locate usage
```bash
du -sh /var/*
du -sh /var/log/*
```

### Step 3 — Find large files
```bash
find /var/log -type f -size +500M
```

### Step 4 — Check deleted-but-open files
```bash
lsof +L1
```

### Step 5 — Identify the source

Potential causes:
- Application logs
- Container logs
- Docker images/layers
- Application data
- Temporary files
- Core dumps
- Deleted-but-open files

### Step 6 — Recover safely

Determine:
- What created the files?
- Is the application still writing?
- Is log rotation working?
- Can old logs be archived safely?
- Is a process holding deleted files open?
- Could deletion corrupt application/database data?

### Step 7 — Prevent recurrence

Implement:
- Log rotation
- Retention policies
- Disk alerts
- Container log limits
- Automated cleanup where appropriate
- Capacity planning

## 19. Interview Troubleshooting Framework

Use this mental model for production incidents:

```text
INCIDENT
   |
   v
Confirm impact
   |
   v
CPU / Memory / Disk / Network
   |
   v
Process & Service
   |
   v
Logs
   |
   v
DNS / Port / Route / HTTP
   |
   v
External dependencies
   |
   v
Root cause
   |
   v
Safe recovery
   |
   v
Prevention
```

## 20. High-Priority Commands to Memorize

| Area | Commands |
|---|---|
| Files | `ls`, `cp`, `mv`, `rm`, `find` |
| Disk | `df`, `du`, `lsof` |
| CPU | `top`, `ps`, `uptime` |
| Memory | `free` |
| Processes | `ps`, `pgrep`, `kill` |
| Logs | `grep`, `tail`, `less`, `sed` |
| Text | `awk`, `sed`, `cut`, `sort`, `uniq`, `wc` |
| Network | `ip`, `ping`, `dig`, `nslookup`, `curl`, `nc` |
| Ports | `ss`, `netstat` |
| Permissions | `ls -l`, `chmod`, `chown`, `id` |
| Services | `systemctl`, `journalctl` |
| Remote access | `ssh`, `scp` |
| Archives | `tar` |
| Environment | `env`, `printenv`, `export` |

## 21. Interview Tips

Don't just state a command.

Weak:
> "I will use `df -h`."

Strong:
> "First I'll use `df -h` to confirm filesystem utilization. Then I'll use `du` to identify which directory is consuming space, `find` to locate unusually large files, and `lsof +L1` to check for deleted files still held open. I won't delete anything until I understand the source because this is a production system."

Think in layers:

```text
Application
    ↓
Process
    ↓
Service
    ↓
OS
    ↓
CPU / Memory / Disk
    ↓
Network
    ↓
DNS
    ↓
External dependency
```

Correlate **metrics + logs + process state + network behavior** before deciding on root cause.

## Quick Revision Checklist

- [ ] File and directory operations
- [ ] `find`
- [ ] `df` vs `du`
- [ ] `top` / `ps`
- [ ] `free`
- [ ] Process management
- [ ] `grep`
- [ ] `tail -f`
- [ ] `awk`
- [ ] `sed`
- [ ] `curl`
- [ ] `dig` / `nslookup`
- [ ] `ss`
- [ ] `ip route`
- [ ] `chmod` / `chown`
- [ ] `systemctl`
- [ ] `journalctl`
- [ ] `ssh` / `scp`
- [ ] `tar`
- [ ] `lsof +L1`
- [ ] Production troubleshooting methodology

## Final Goal

Don't memorize hundreds of commands.

Be able to take a production symptom, choose the right command, interpret the output, narrow the fault domain, recover safely, and explain how you would prevent the issue from recurring.
