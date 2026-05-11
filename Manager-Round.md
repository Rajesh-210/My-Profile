# 🚀 Manager Round Interview Cheat Sheet
### Chilukuri Rajesh — DevOps Engineer | AWS · Kubernetes · CI/CD · Terraform · Linux · Ansible

---

## 🏆 Your Power Numbers (Use These in Every Answer!)

| Achievement | Number |
|---|---|
| Deployment time reduced | **83%** (30 min → under 5 min) |
| Infrastructure provisioning | **3+ hours → under 10 minutes** |
| Manual deployment errors | **Zero** |
| Years of experience | **4+ years** |
| Companies | **Accenture + L&T Technology Services** |

---

## 1. 👋 Tell Me About Yourself

> *"I'm Rajesh, a DevOps Engineer with 4+ years of experience at Accenture and L&T Technology Services.
> I specialise in CI/CD pipelines using Jenkins and ArgoCD, Kubernetes on AWS EKS, and infrastructure
> automation with Terraform. My biggest win was reducing deployment time from 30 minutes to under
> 5 minutes — an 83% improvement — while eliminating all manual deployment errors.
> I work closely with dev teams to ship reliable software faster."*

✅ **Keep it under 60 seconds. Practice 3 times tonight!**

---

## 2. 🎯 Behavioural Questions

### Q: What is your biggest achievement?
> *"At Accenture I built an end-to-end Jenkins pipeline with Docker, EKS, and ArgoCD GitOps.
> Deployment time dropped from 30 minutes to under 5 minutes — 83% improvement — and we achieved
> zero manual deployment errors. The team could release multiple times a day confidently."*

✅ Always say the number. Numbers impress managers.

---

### Q: Tell me about a challenging situation.
> *"At L&T I had to set up the full observability stack from scratch on Kubernetes. No dashboards existed
> and the team had zero visibility into pod health or resource usage. I deployed Prometheus and Grafana,
> built custom dashboards for CPU, memory, and application metrics, and set up Alertmanager with Slack
> notifications. After this, the team detected critical incidents proactively instead of reacting after
> users complained."*

✅ Shows ownership and problem-solving — exactly what managers love.

---

### Q: Have you ever made a mistake? What happened?
> *"Early in my career I applied a Terraform change in production without fully verifying the plan output.
> It caused a brief service disruption. I quickly rolled back, documented what happened, and from then on
> I made it a rule to always run terraform plan, get a peer review, and apply during low-traffic windows.
> That incident made me a much more careful engineer."*

✅ Managers want self-awareness, not perfection.

---

### Q: How do you work with development teams?
> *"I treat DevOps as a collaboration, not just a tools team. At Accenture and L&T I worked with devs
> to resolve deployment blockers, helped them understand Kubernetes manifests, and built pipelines
> that gave fast feedback on their code. When issues arose I sat with the dev team, understood their
> pain points, and automated those steps."*

---

### Q: Why should we hire you?
> *"Because I solve business problems — not just set up tools. I reduced deployment time 83%, cut
> infrastructure provisioning from 3 hours to 10 minutes, and achieved zero-downtime deployments.
> I'm hands-on with AWS, Kubernetes, Terraform, Jenkins, and Prometheus — and I take full ownership
> of results."*

---

### Q: Where do you see yourself in 2-3 years?
> *"Growing into a Senior DevOps or Platform Engineer role — architecting larger-scale solutions,
> mentoring junior engineers, and driving DevOps culture across teams. I also want to deepen expertise
> in cloud-native technologies."*

---

### Q: Why did you leave your previous job?
> *"At L&T I was on a project-based contract that ended in May 2025. The project was completed
> successfully and I'm now looking for a long-term opportunity where I can contribute deeper
> and grow with the team."*

---

## 3. 🔁 CI/CD Pipeline

### Q: Walk me through your CI/CD pipeline.
> *"Developer pushes code to GitHub → GitHub Webhook triggers Jenkins → Jenkins runs unit tests,
> builds Docker image, pushes to Amazon ECR → ArgoCD detects the change and automatically deploys
> to Kubernetes EKS → If deployment fails, ArgoCD rolls back automatically using liveness probes.
> Whole process: under 5 minutes."*

✅ Draw it on a whiteboard if they offer. Shows confidence.

---

### Q: What is GitOps / ArgoCD?
> *"GitOps means Git is the single source of truth for deployments. ArgoCD watches the GitHub repo
> and automatically syncs the Kubernetes cluster to match. If someone manually changes something in
> the cluster, ArgoCD detects the drift and corrects it — giving us auditable, self-healing deployments."*

---

### Q: What is a Docker image vs a container?
> *"A Docker image is like a blueprint — it's a read-only template. A container is a running instance
> of that image. I build images in Jenkins, push them to Amazon ECR, and Kubernetes pulls them to
> run as containers."*

---

## 4. 🏗️ Terraform

### Q: Tell me about your Terraform experience.
> *"I've used Terraform to provision AWS infrastructure — VPCs, subnets, EC2, IAM roles, security groups.
> I wrote modular, reusable Terraform code using S3 remote backend with DynamoDB for state locking
> so teams could work safely together. I also integrated Terraform into Jenkins pipelines for automated,
> peer-reviewed infra changes. This cut provisioning from 3+ hours to under 10 minutes."*

---

### Q: What is Terraform remote state?
> *"Remote state stores the Terraform state file in S3 instead of locally, so the whole team shares
> the same view of infrastructure. DynamoDB state locking prevents two people from running
> terraform apply at the same time and corrupting the state. Essential for team-based work."*

---

### Q: Terraform vs Ansible — difference?

| Terraform | Ansible |
|---|---|
| **Creates** infrastructure | **Configures** infrastructure |
| Provisions EC2, VPC, S3 | Installs software, deploys apps |
| Infrastructure as Code | Configuration Management |
| State-based | Agentless / SSH-based |

> *"Simple way to think about it — Terraform builds the house, Ansible furnishes it."*

✅ Very common question — nail this answer!

---

### Q: What are Terraform workspaces?
> *"Workspaces let you manage multiple environments — dev, staging, production — from the same
> Terraform code with different variable values. I used this at Accenture to eliminate configuration
> drift between environments."*

---

## 5. 🐧 Linux

### Q: How comfortable are you with Linux?
> *"Very comfortable. All my work at Accenture and L&T was on Ubuntu and Amazon Linux — managing
> servers, writing Bash scripts, checking logs, troubleshooting services, and working with
> Docker and Kubernetes daily."*

---

### 📋 Linux Command Quick Reference

| Command | What it does |
|---|---|
| `systemctl status nginx` | Check if a service is running |
| `journalctl -u nginx` | View service logs |
| `lsof -i :8080` | Find which process is using a port |
| `kill -9 <PID>` | Force-stop a process |
| `df -h` | Check disk space (human readable) |
| `free -m` | Check memory usage in MB |
| `top` / `htop` | Real-time CPU & memory usage |
| `grep -i "error" app.log` | Search for errors in logs |
| `tail -f app.log` | Watch logs in real time |
| `chmod 600 key.pem` | Restrict file to owner-only access |
| `chown appuser /app` | Change file/folder owner |
| `crontab -e` | Edit scheduled cron jobs |
| `ps aux` | List all running processes |
| `netstat -tulnp` | Show all open ports |
| `ssh user@ip` | Connect to remote server |

---

### Q: How do you troubleshoot a service that's down?
> *"Step 1: `systemctl status <service>` to see if it's running or failed.*
> *Step 2: `journalctl -u <service>` to read the error logs.*
> *Step 3: Check config files for syntax errors.*
> *Step 4: Restart with `systemctl restart <service>` and verify.*
> At L&T I used this exact flow when Nginx stopped routing traffic — found a config syntax error,
> fixed it, tested with `nginx -t`, and restarted."*

---

### Q: What is a cron job?
> *"A cron job is a scheduled task in Linux. I used cron to automate Docker image cleanup,
> log rotation, and backups. For example `0 2 * * *` runs a script every day at 2 AM.
> At Accenture I scheduled a script to clean old ECR images and avoid unnecessary storage costs."*

---

## 6. 📦 Ansible

### Q: Have you used Ansible? What for?
> *"Yes — I used Ansible for configuration management and application deployment. I wrote playbooks
> to automate server setup — installing packages, configuring Nginx, deploying application code,
> and managing environment variables across multiple servers at once. Much faster than SSHing
> into each server manually."*

---

### Q: What is a Playbook?
> *"A YAML file that defines what tasks Ansible should run on which servers. For example, I wrote a
> playbook to install Docker on all app servers, configure it, and start the service — across
> 10 servers simultaneously in one run."*

---

### Q: What is Ansible Inventory?
> *"A list of servers Ansible manages. Can be static (IP list) or dynamic. I used dynamic inventory
> with the AWS EC2 plugin — when Auto Scaling launched new servers, Ansible automatically knew
> about them without any manual updates."*

---

### Q: Is Ansible agentless? Why does that matter?
> *"Yes — Ansible connects over SSH, no software needed on target servers. Nothing to maintain
> on managed servers. Compare this to Chef or Puppet which need an agent running on every server."*

---

### 📋 Ansible Concepts Quick Reference

| Concept | Simple Explanation |
|---|---|
| **Task** | One single action: "install nginx" |
| **Play** | Group of tasks for specific servers |
| **Playbook** | File with one or more plays |
| **Inventory** | List of servers Ansible manages |
| **Role** | Reusable, organized collection of tasks |
| **Idempotent** | Safe to run multiple times — same result |
| **Agentless** | Uses SSH — no agent on target servers |
| **Handler** | Task that runs only when notified (e.g. restart nginx) |

---

### Q: What happens if an Ansible task fails mid-playbook?
> *"By default Ansible stops on that host. You can use `ignore_errors: yes` to continue, or
> `block/rescue` which works like try/catch — if the main task fails, the rescue section runs
> a cleanup or rollback. I used this pattern for safe deployments."*

---

## 7. 🙋 Questions to Ask the Manager

> Always ask at least 2 questions — shows you're serious and thinking about contributing.

- *"What does the current DevOps setup look like and what's the biggest pain point for the team?"*
- *"What would success look like for me in the first 90 days?"*
- *"How does the team handle on-call and incident response today?"*
- *"What is the deployment frequency and how mature is the CI/CD pipeline?"*
- *"What does the on-call rotation look like?"*

---

## 8. ✅ Do's and Don'ts

| ✅ DO | ❌ DON'T |
|---|---|
| Use STAR method (Situation → Task → Action → Result) | Blame teammates or managers |
| Quantify: 83%, 5 min, 10 min, zero errors | Give vague or theoretical answers |
| Say "I" not "we" — own your work | Say "I don't know" without offering to learn |
| Speak slowly — nervousness makes us rush | Panic if you don't know something |
| Smile — managers hire people they like | Leave without asking any questions |
| "I haven't done that yet but I learn quickly" | Over-explain or go off-topic |

---

## 9. 🧘 Tonight's Preparation Plan

- [ ] Read this file once fully
- [ ] Practice **"Tell me about yourself"** out loud — 3 times
- [ ] Remember your numbers: **83% · 5 min · 10 min · Zero errors**
- [ ] Pick 2 questions to ask the manager
- [ ] Sleep well — rest = confidence

---

## 10. 🔧 Your Tech Stack Summary

```
Cloud         : AWS (EC2, S3, IAM, VPC, EKS, CloudWatch, Route53, ELB)
CI/CD         : Jenkins, GitHub Actions, ArgoCD (GitOps)
Containers    : Docker, Docker Compose, Amazon ECR
Orchestration : Kubernetes (EKS), kubectl, Helm
Monitoring    : Prometheus, Grafana, Alertmanager, CloudWatch
IaC           : Terraform (modules, remote state, workspaces)
Config Mgmt   : Ansible
Version Ctrl  : Git, GitHub
Scripting     : Bash, Linux (Ubuntu, Amazon Linux)
Web/Proxy     : Nginx (reverse proxy, TLS, load balancing)
```

---

> 💪 **You have REAL achievements. Real numbers. Real experience.**
> **Go in there, tell YOUR story confidently — YOU'VE GOT THIS, RAJESH! 🚀**
