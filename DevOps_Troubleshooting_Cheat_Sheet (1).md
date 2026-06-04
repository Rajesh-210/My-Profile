# 🛠️ DevOps Troubleshooting Master Cheat Sheet
> Common Issues, Flow Diagrams, Commands & Interview Q&A

---

## 📋 Table of Contents
1. [Application Not Working](#1-application-not-working)
2. [Website Not Accessible](#2-website-not-accessible)
3. [SSH Connection Failed](#3-ssh-connection-failed)
4. [High CPU Usage](#4-high-cpu-usage)
5. [High Memory Usage](#5-high-memory-usage)
6. [Disk Full](#6-disk-full)
7. [Docker Container Down](#7-docker-container-down)
8. [Kubernetes Pod CrashLoopBackOff](#8-kubernetes-pod-crashloopbackoff)
9. [Git Push Failed](#9-git-push-failed)
10. [Jenkins Build Failed](#10-jenkins-build-failed)
11. [AWS EC2 Troubleshooting](#11-aws-ec2-troubleshooting)
12. [Service Not Restarting](#12-service-not-restarting)
- [Universal Troubleshooting Formula](#-universal-troubleshooting-formula)
- [Golden Commands](#-golden-commands-memorize)
- [Common Log Locations](#-common-log-locations)
- [Interview Tips](#-interview-tips)

---

## 1. Application Not Working

### Flow Diagram

```mermaid
flowchart TD
    A([👤 User Reports Issue]) --> B[Check App Process]
    B --> C[Check Logs]
    C --> D[Check Port]
    D --> E[Check Connectivity]
    E --> F([✅ Fix & Validate])

    style A fill:#534AB7,color:#EEEDFE,stroke:#3C3489
    style F fill:#0F6E56,color:#E1F5EE,stroke:#085041
```

### Commands
```bash
ps -ef | grep app
top / htop
systemctl status app
journalctl -u app -f
tail -f /var/log/app.log
ss -tulpn
curl localhost:8080
```

### Interview Q
> **Q: Application is down. Where do you start?**

---

## 2. Website Not Accessible

### Flow Diagram

```mermaid
flowchart TD
    A([🌐 Website Down]) --> B{DNS?}
    B -->|Resolving| C{Network?}
    B -->|Failing| B1[Fix DNS / nslookup]
    C -->|OK| D{Port Open?}
    C -->|Failing| C1[Check routing / firewall]
    D -->|Yes| E{Service Running?}
    D -->|No| D1[Open port / check firewall]
    E -->|Yes| F[Check Logs]
    E -->|No| E1[Start service]

    style A fill:#0F6E56,color:#E1F5EE,stroke:#085041
    style F fill:#534AB7,color:#EEEDFE,stroke:#3C3489
```

### Commands
```bash
ping google.com
nslookup example.com
dig example.com
curl -I website.com
ss -tulpn
netstat -tulpn
systemctl status nginx
```

### Interview Q
> **Q: What will you check if website is not accessible?**

---

## 3. SSH Connection Failed

### Flow Diagram

```mermaid
flowchart TD
    A([🔐 SSH Failed]) --> B{VM Running?}
    B -->|No| B1[Start / reboot VM]
    B -->|Yes| C{Port 22 Open?}
    C -->|No| C1[Open port 22 in firewall]
    C -->|Yes| D{Security Group?}
    D -->|Blocked| D1[Allow inbound SSH rule]
    D -->|OK| E{SSH Key?}
    E -->|Missing/Wrong| E1[Fix key permissions]
    E -->|OK| F[Check SSH Logs]

    style A fill:#534AB7,color:#EEEDFE,stroke:#3C3489
    style F fill:#2C2C2A,color:#D3D1C7,stroke:#444441
```

### Commands
```bash
ping SERVER_IP
nc -zv SERVER_IP 22
ssh -i key.pem user@ip
sudo systemctl status ssh
sudo journalctl -u ssh
```

### Interview Q
> **Q: You are unable to SSH into EC2. How will you troubleshoot?**

---

## 4. High CPU Usage

### Flow Diagram

```mermaid
flowchart TD
    A([🔥 CPU High]) --> B[Find Top Process]
    B --> C[Analyze — top / ps aux]
    C --> D{Rogue Process?}
    D -->|Yes| E[Kill Process]
    D -->|No| F[Optimize / tune app]
    E --> G([✅ Monitor])
    F --> G

    style A fill:#993C1D,color:#FAECE7,stroke:#712B13
    style G fill:#0F6E56,color:#E1F5EE,stroke:#085041
```

### Commands
```bash
top
ps aux --sort=-%cpu
ps -ef
kill PID
kill -9 PID
```

### Interview Q
> **Q: CPU is 100%. How will you find the cause?**

---

## 5. High Memory Usage

### Flow Diagram

```mermaid
flowchart TD
    A([🧠 Memory High]) --> B[Find Process — ps aux --sort=-%mem]
    B --> C[Analyze — free -h / vmstat]
    C --> D{Memory Leak?}
    D -->|Yes| E[Restart / patch app]
    D -->|No| F[Add swap / upgrade RAM]
    E --> G([✅ Free / Optimize])
    F --> G

    style A fill:#534AB7,color:#EEEDFE,stroke:#3C3489
    style G fill:#0F6E56,color:#E1F5EE,stroke:#085041
```

### Commands
```bash
free -h
top
ps aux --sort=-%mem
vmstat
cat /proc/meminfo
```

### Interview Q
> **Q: System is slow due to high memory usage. What to do?**

---

## 6. Disk Full

### Flow Diagram

```mermaid
flowchart TD
    A([💾 Disk Full]) --> B[Check Space — df -h]
    B --> C[Find Large Files — du -sh]
    C --> D{Log bloat?}
    D -->|Yes| E[journalctl --vacuum-time=7d]
    D -->|No| F[Delete / Archive files]
    E --> G[Free Space]
    F --> G
    G --> H([✅ Validate — df -h])

    style A fill:#3B6D11,color:#EAF3DE,stroke:#27500A
    style H fill:#0F6E56,color:#E1F5EE,stroke:#085041
```

### Commands
```bash
df -h
du -sh *
find / -size +500M
sudo du -ahx / | sort -rh | head -20
journalctl --vacuum-time=7d
```

### Interview Q
> **Q: Disk is full. How will you free up space?**

---

## 7. Docker Container Down

### Flow Diagram

```mermaid
flowchart TD
    A([🐳 Container Stopped]) --> B[docker ps -a]
    B --> C[Check Logs — docker logs]
    C --> D[Inspect — docker inspect]
    D --> E{Config / Env issue?}
    E -->|Yes| F[Fix config / env vars]
    E -->|No| G[Check Ports & Volumes]
    F --> H[docker restart CONTAINER]
    G --> H
    H --> I([✅ Fix & Restart])

    style A fill:#185FA5,color:#E6F1FB,stroke:#0C447C
    style I fill:#0F6E56,color:#E1F5EE,stroke:#085041
```

### Commands
```bash
docker ps -a
docker logs CONTAINER
docker inspect CONTAINER
docker exec -it CONTAINER bash
docker restart CONTAINER
```

### Interview Q
> **Q: Container exits immediately. How will you troubleshoot?**

---

## 8. Kubernetes Pod CrashLoopBackOff

### Flow Diagram

```mermaid
flowchart TD
    A([☸️ Pod Failing]) --> B[kubectl describe pod POD]
    B --> C[kubectl logs POD]
    C --> D[kubectl get events]
    D --> E{Root Cause?}
    E -->|Config error| F[Fix ConfigMap / Secret]
    E -->|Image issue| G[Fix image tag / registry]
    E -->|Resource limit| H[Adjust requests/limits]
    F --> I[kubectl apply]
    G --> I
    H --> I
    I --> J([✅ Fix & Redeploy])

    style A fill:#185FA5,color:#E6F1FB,stroke:#0C447C
    style J fill:#0F6E56,color:#E1F5EE,stroke:#085041
```

### Commands
```bash
kubectl get pods
kubectl describe pod POD
kubectl logs POD
kubectl get events
kubectl exec -it POD --bash
```

### Interview Q
> **Q: Pod is in CrashLoopBackOff. How will you fix it?**

---

## 9. Git Push Failed

### Flow Diagram

```mermaid
flowchart TD
    A([🔀 Push Failed]) --> B[git status]
    B --> C[git remote -v]
    C --> D{Remote ahead?}
    D -->|Yes| E[git pull origin main]
    D -->|No| F[Check SSH key — ssh -T git@github.com]
    E --> G{Merge conflict?}
    G -->|Yes| H[Resolve conflicts]
    G -->|No| I[git push origin main]
    H --> I
    F --> I
    I --> J([✅ Push Again])

    style A fill:#854F0B,color:#FAEEDA,stroke:#633806
    style J fill:#0F6E56,color:#E1F5EE,stroke:#085041
```

### Commands
```bash
git status
git remote -v
git pull origin main
git push origin main
ssh -T git@github.com
```

### Interview Q
> **Q: Why is push rejected? How will you fix it?**

---

## 10. Jenkins Build Failed

### Flow Diagram

```mermaid
flowchart TD
    A([🔧 Build Failed]) --> B[Check Console Output]
    B --> C{Error type?}
    C -->|Compile error| D[Fix code]
    C -->|Credential error| E[Update Jenkins credentials]
    C -->|Agent offline| F[Restart Jenkins agent]
    C -->|Dependency issue| G[Fix pom.xml / package.json]
    D --> H[Rebuild]
    E --> H
    F --> H
    G --> H
    H --> I([✅ Fix & Rebuild])

    style A fill:#993C1D,color:#FAECE7,stroke:#712B13
    style I fill:#0F6E56,color:#E1F5EE,stroke:#085041
```

### Commands
```bash
# Check Console Output in Jenkins UI
systemctl status jenkins
journalctl -u jenkins -f
docker logs jenkins
```

### Interview Q
> **Q: Jenkins build failed. What will you check?**

---

## 11. AWS EC2 Troubleshooting

### Flow Diagram

```mermaid
flowchart TD
    A([☁️ Issue Reported]) --> B[Check Instance Status]
    B --> C{Status checks OK?}
    C -->|Fail| D[Reboot / Stop-Start EC2]
    C -->|Pass| E[Check Security Group]
    E --> F{Port allowed?}
    F -->|No| G[Add inbound rule]
    F -->|Yes| H[Check Network / Route Table]
    H --> I[Check Logs & Services]
    I --> J([✅ Fix & Validate])
    D --> J

    style A fill:#185FA5,color:#E6F1FB,stroke:#0C447C
    style J fill:#0F6E56,color:#E1F5EE,stroke:#085041
```

### Commands
```bash
ping SERVER_IP
ss -tulpn
systemctl status nginx
aws ec2 describe-instances
aws ec2 describe-security-groups
```

### Interview Q
> **Q: EC2 is not reachable. How will you troubleshoot?**

---

## 12. Service Not Restarting

### Flow Diagram

```mermaid
flowchart TD
    A([⚙️ Service Down]) --> B[systemctl status SERVICE]
    B --> C[journalctl -u SERVICE -f]
    C --> D{Config error?}
    D -->|Yes| E[Fix /etc/systemd/system/SERVICE]
    D -->|No| F[Check dependencies]
    E --> G[systemctl daemon-reload]
    F --> G
    G --> H[systemctl restart SERVICE]
    H --> I([✅ Start Service])

    style A fill:#3B6D11,color:#EAF3DE,stroke:#27500A
    style I fill:#0F6E56,color:#E1F5EE,stroke:#085041
```

### Commands
```bash
systemctl status SERVICE
journalctl -u SERVICE -f
cat /etc/systemd/system/SERVICE
systemctl daemon-reload
systemctl restart SERVICE
```

### Interview Q
> **Q: Service fails to start even after restart?**

---

## 🔄 Universal Troubleshooting Formula

```mermaid
flowchart LR
    A[1. Understand] --> B[2. Reproduce]
    B --> C[3. Check Logs]
    C --> D[4. Check Process]
    D --> E[5. Check Network]
    E --> F[6. Check Resources]
    F --> G[7. Root Cause]
    G --> H[8. Fix]
    H --> I[9. Validate]
    I --> J[10. Document]

    style A fill:#534AB7,color:#EEEDFE
    style J fill:#0F6E56,color:#E1F5EE
```

1. Understand the issue
2. Reproduce the issue
3. Check logs
4. Check process / service
5. Check networking
6. Check resources (CPU, RAM, Disk)
7. Identify root cause
8. Fix the issue
9. Validate the fix
10. Document & Monitor

---

## ⭐ Golden Commands (Memorize)

| Category | Commands |
|----------|----------|
| **System** | `top / htop`, `df -h`, `du -sh`, `free -h`, `ps -ef`, `ss -tulpn`, `netstat -tulpn` |
| **Network** | `ping`, `curl`, `nslookup`, `curl localhost` |
| **Logs** | `journalctl -xe`, `systemctl status`, `tail -f` |
| **Docker** | `docker ps -a`, `docker logs`, `docker inspect` |
| **Kubernetes** | `kubectl get pods`, `kubectl describe pod`, `kubectl logs` |
| **Misc** | `grep`, `cat` |

---

## 📁 Common Log Locations

| Log | Path |
|-----|------|
| Syslog | `/var/log/syslog` |
| Messages | `/var/log/messages` |
| Auth | `/var/log/auth.log` |
| Nginx Error | `/var/log/nginx/error.log` |
| Apache Error | `/var/log/apache2/error.log` |
| Jenkins | `/var/log/jenkins/jenkins.log` |
| Docker | `/var/log/docker.log` |
| Bash History | `~/.bash_history` |

---

## 💡 Interview Tips

- ✅ Stay calm and gather information.
- ✅ Always check logs first.
- ✅ Narrow down the problem.
- ✅ Explain your thought process.
- ✅ Communicate clearly.
- ✅ Document and learn.

---

> 💪 *The more you troubleshoot, the better you become!*
