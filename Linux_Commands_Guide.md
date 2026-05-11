# 🐧 Linux Commands — Complete Guide with Examples
### DevOps Engineer Quick Reference | Chilukuri Rajesh

---

## 📋 Quick Reference Table

| Command | What it does |
|---|---|
| `systemctl status nginx` | Check if a service is running |
| `journalctl -u nginx` | View service logs |
| `lsof -i :8080` | Find which process is using a port |
| `kill -9 <PID>` | Force-stop a process |
| `df -h` | Check disk space (human readable) |
| `free -m` | Check memory usage in MB |
| `top / htop` | Real-time CPU & memory usage |
| `grep -i "error" app.log` | Search for errors in logs |
| `tail -f app.log` | Watch logs in real time |
| `chmod 600 key.pem` | Restrict file to owner-only access |
| `chown appuser /app` | Change file/folder owner |
| `crontab -e` | Edit scheduled cron jobs |
| `ps aux` | List all running processes |
| `netstat -tulnp` | Show all open ports |
| `ssh user@ip` | Connect to remote server |

---

## 1. `systemctl status nginx`

**What it does:** Checks if a service is running, stopped, or failed.

```bash
systemctl status nginx
```

**Output looks like:**
```
● nginx.service - A high performance web server
   Active: active (running) since Mon 2024-01-01 10:00:00
```

**More commands:**
```bash
systemctl status jenkins    # check Jenkins
systemctl status docker     # check Docker
systemctl start nginx       # start a service
systemctl stop nginx        # stop a service
systemctl restart nginx     # restart a service
systemctl enable nginx      # auto-start on boot
```

**Real use:**
> After deploying your app, run this to confirm Nginx is up and running.

---

## 2. `journalctl -u nginx`

**What it does:** Shows logs of a specific service — very useful when a service crashes.

```bash
journalctl -u nginx
journalctl -u nginx --since "1 hour ago"   # last 1 hour logs
journalctl -u nginx -n 50                  # last 50 lines only
journalctl -u nginx -f                     # watch logs live
```

**Real use:**
> Nginx is not starting → run `journalctl -u nginx` → see error like `port 80 already in use` → fix it.

---

## 3. `lsof -i :8080`

**What it does:** Finds which process is using a specific port.

```bash
lsof -i :8080
```

**Output:**
```
COMMAND   PID   USER   FD   TYPE  NODE NAME
java     1234  ubuntu  TCP  *:8080 (LISTEN)
```

**More examples:**
```bash
lsof -i :3000     # check port 3000
lsof -i :443      # check HTTPS port
lsof -i :80       # check HTTP port
```

**Real use:**
> Jenkins won't start because port 8080 is busy → run `lsof -i :8080` → find which process → kill it.

---

## 4. `kill -9 <PID>`

**What it does:** Force-stops a process using its Process ID (PID).

```bash
kill -9 1234
```

**Step by step:**
```bash
lsof -i :8080     # step 1 — find PID (say it shows PID 1234)
kill -9 1234      # step 2 — kill that process
```

**Difference between kill and kill -9:**
```bash
kill 1234       # politely ask process to stop (can be ignored)
kill -9 1234    # force kill immediately — no questions asked
```

**Real use:**
> A process is frozen and not responding → `kill -9 <PID>` → process gone instantly.

---

## 5. `df -h`

**What it does:** Shows disk space usage on all drives. `-h` means human readable (shows GB/MB).

```bash
df -h
```

**Output:**
```
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1       20G   15G  4.5G  77% /
tmpfs           3.9G     0  3.9G   0% /dev/shm
```

**More examples:**
```bash
df -h /           # check root partition only
df -h /var        # check /var folder space
```

**Real use:**
> Deployment failing? Server might be out of disk space.
> Run `df -h` → if `Use%` is 95%+ → clean up old logs or Docker images.

---

## 6. `free -m`

**What it does:** Shows RAM (memory) usage in MB.

```bash
free -m
```

**Output:**
```
              total   used   free   shared  available
Mem:           7982   5200   1200      100       2782
Swap:          2047    100   1947
```

**How to read it:**
- `total` = total RAM your server has
- `used` = how much is currently in use
- `free` = available right now
- `Swap` = disk used as extra memory (slow — bad if high)

**Real use:**
> App running slow? Run `free -m` → if free memory is very low → a process is eating RAM → use `htop` to find it.

---

## 7. `top` / `htop`

**What it does:** Real-time view of CPU and memory usage — like Task Manager in Windows.

```bash
top       # basic (built-in on all Linux)
htop      # colorful, easier to read
```

**Install htop:**
```bash
sudo apt install htop -y
```

**Keyboard shortcuts inside top/htop:**
```
q   → quit
k   → kill a process
M   → sort by memory usage
P   → sort by CPU usage
/   → search for a process
```

**Real use:**
> Server is slow → run `htop` → sort by CPU → find which process is using 99% CPU → press `k` to kill it.

---

## 8. `grep -i "error" app.log`

**What it does:** Searches for a word inside a file. `-i` means case-insensitive (finds ERROR, Error, error).

```bash
grep -i "error" app.log
grep -i "failed" /var/log/syslog
grep -i "exception" app.log
```

**More useful options:**
```bash
grep -i "error" app.log | tail -20      # last 20 error lines
grep -n "error" app.log                 # show line numbers
grep -c "error" app.log                 # count total errors
grep -i "error\|failed" app.log         # search error OR failed
grep -i "error" app.log > errors.txt    # save results to file
```

**Real use:**
> App crashed → `grep -i "error" app.log` → find the exact error line → fix it.

---

## 9. `tail -f app.log`

**What it does:** Watches a log file in real time — new lines appear as they are written.

```bash
tail -f app.log
tail -f /var/log/nginx/access.log
tail -f /var/log/syslog
```

**More options:**
```bash
tail -100 app.log              # show last 100 lines
tail -f app.log | grep ERROR   # watch only ERROR lines live
```

**Real use:**
> Deploying right now? Open another terminal → run `tail -f app.log` → watch logs live as deployment happens.

---

## 10. `chmod 600 key.pem`

**What it does:** Changes file permissions — controls who can read/write/execute a file.

```bash
chmod 600 key.pem       # owner: read+write only. Others: nothing
chmod 755 script.sh     # owner: all, group+others: read+execute
chmod +x script.sh      # make any file executable
chmod -x script.sh      # remove execute permission
```

**Permission numbers explained:**
```
7 = read + write + execute  (rwx)
6 = read + write            (rw-)
5 = read + execute          (r-x)
4 = read only               (r--)
0 = no permission           (---)

chmod 600 = owner(6) group(0) others(0)
          = only owner can read and write. Nobody else.

chmod 755 = owner(7) group(5) others(5)
          = owner has full access. Others can read and run.
```

**Real use:**
> AWS says `WARNING: key.pem permissions are too open` → run `chmod 400 key.pem` → warning gone.

---

## 11. `chown appuser /app`

**What it does:** Changes the owner of a file or folder.

```bash
chown appuser /app                   # change owner to appuser
chown appuser:appgroup /app          # change owner AND group
chown -R appuser /app                # change owner of folder + everything inside
```

**Real use:**
> Deployed app but it can't write to `/app/logs` → run `chown -R appuser /app` → app now owns its folder → works fine.

```bash
chown ubuntu:ubuntu /var/www/html    # give ubuntu user ownership of web root
chown -R jenkins /var/lib/jenkins    # give jenkins ownership of its data folder
```

---

## 12. `crontab -e`

**What it does:** Opens the cron scheduler to add or edit scheduled/automated jobs.

```bash
crontab -e        # edit your cron jobs (opens in editor)
crontab -l        # list all existing cron jobs
crontab -r        # delete ALL cron jobs (be careful!)
sudo crontab -e   # edit root user's cron jobs
```

**Cron time format:**
```
*  *  *  *  *   command_to_run
|  |  |  |  |
|  |  |  |  └── Day of week  (0-7, where 0 and 7 = Sunday)
|  |  |  └───── Month        (1-12)
|  |  └──────── Day of month (1-31)
|  └─────────── Hour         (0-23)
└────────────── Minute       (0-59)
```

**Real examples:**
```bash
# Every day at 2 AM — backup
0 2 * * * /scripts/backup.sh >> /var/log/backup.log 2>&1

# Every 5 minutes — health check
*/5 * * * * /scripts/health_check.sh

# Every Monday at 9 AM — weekly report
0 9 * * 1 /scripts/weekly_report.sh

# 1st of every month at midnight
0 0 1 * * /scripts/monthly_cleanup.sh

# Every hour
0 * * * * /scripts/sync.sh
```

**Real use:**
> At Accenture, I used crontab to schedule a script that cleaned old Docker images from Amazon ECR every day at 2 AM — saved storage costs automatically.

---

## 13. `ps aux`

**What it does:** Lists all currently running processes on the server.

```bash
ps aux
ps aux | grep nginx        # find nginx process
ps aux | grep java         # find java/app process
ps aux | grep jenkins      # check if jenkins is running
```

**Output columns explained:**
```
USER    PID   %CPU  %MEM   COMMAND
root    123    0.5   1.2   nginx: master process
ubuntu  456    2.1   5.3   java -jar myapp.jar
```

- `PID` = Process ID (use this with kill command)
- `%CPU` = CPU usage percentage
- `%MEM` = Memory usage percentage
- `COMMAND` = what the process is running

**Real use:**
> Want to check if your app is running? `ps aux | grep app.jar` → if you see the process, it is running.

---

## 14. `netstat -tulnp`

**What it does:** Shows all open/listening ports on the server.

```bash
netstat -tulnp
```

**What the flags mean:**
```
-t  = show TCP connections
-u  = show UDP connections
-l  = show only listening ports
-n  = show numbers (not names)
-p  = show which process owns the port
```

**Output:**
```
Proto  Local Address    PID/Program
tcp    0.0.0.0:80       1234/nginx
tcp    0.0.0.0:8080     5678/jenkins
tcp    0.0.0.0:22       910/sshd
tcp    0.0.0.0:443      1234/nginx
```

**Real use:**
> Check which ports are open on your server → confirm Jenkins(8080), Nginx(80), SSH(22) are all listening correctly.

```bash
# If netstat not available, use ss (modern replacement)
ss -tulnp
```

---

## 15. `ssh user@ip`

**What it does:** Connects to a remote Linux server securely over the network.

```bash
ssh ubuntu@192.168.1.100                        # connect with password
ssh -i key.pem ubuntu@192.168.1.100             # connect with key file (AWS)
ssh ubuntu@ec2-xx-xx-xx.amazonaws.com           # connect to AWS EC2
```

**More useful options:**
```bash
ssh -p 2222 ubuntu@192.168.1.100    # connect on custom port
ssh -v ubuntu@192.168.1.100         # verbose — debug connection issues
exit                                 # disconnect from server
```

**Before connecting to AWS:**
```bash
chmod 400 mykey.pem                              # fix permissions first
ssh -i mykey.pem ubuntu@<EC2-public-ip>          # then connect
```

**Real use:**
> Every time you manage AWS EC2 → `ssh -i mykey.pem ubuntu@<public-ip>` → you are inside the server.

---

## 🎯 Interview One-Liners

| Command | Say this in interview |
|---|---|
| `systemctl status nginx` | "I check if a service is running or failed" |
| `journalctl -u nginx` | "I read service logs to find root cause of crashes" |
| `lsof -i :8080` | "I find which process is blocking a port" |
| `kill -9 PID` | "I force-stop a stuck or frozen process" |
| `df -h` | "I check if the server is running out of disk space" |
| `free -m` | "I check available RAM on the server" |
| `htop` | "I see real-time CPU and memory like Task Manager" |
| `grep -i "error" app.log` | "I search logs quickly to find errors" |
| `tail -f app.log` | "I watch logs live during deployments" |
| `chmod 600 key.pem` | "I restrict file access for security" |
| `chown appuser /app` | "I give the right user ownership of files" |
| `crontab -e` | "I schedule automated tasks like backups and cleanup" |
| `ps aux` | "I check all running processes on the server" |
| `netstat -tulnp` | "I check which ports are open and listening" |
| `ssh -i key.pem user@ip` | "I connect to remote servers securely" |

---

## 🔥 Troubleshooting Flow (Use in Interview!)

```
App not working?
│
├── Is the service running?       → systemctl status nginx
├── What do the logs say?         → journalctl -u nginx
├── Is the port being used?       → lsof -i :8080
├── Is disk full?                 → df -h
├── Is memory full?               → free -m
├── Which process is causing it?  → htop / ps aux
└── Search for errors in logs     → grep -i "error" app.log
```

---

> 💡 **Tip for Interview:** When asked about Linux troubleshooting, always say:
> *"First I check the service status, then logs, then resource usage — disk, memory, CPU. That tells me 90% of the time what went wrong."*

---

> 💪 **Rajesh — You use ALL these commands daily in your DevOps work. Speak confidently! 🚀**
