# 🎯 DevOps Manager Round – Interview Prep
### Chilukuri Rajesh | DevOps Engineer | 4+ Years Experience

---

## 1. What Issue Have You Faced? How Did You Overcome It?

**Answer:**
In one of my projects at Accenture, deployments were taking 30+ minutes and were highly manual, which often caused human errors in production.

I overcame this by building an end-to-end Jenkins CI/CD pipeline that automated:
- Code checkout
- Unit tests
- Docker image build & push to ECR
- Kubernetes deployment via ArgoCD GitOps

**Result:** Deployment time dropped from **30 minutes → under 5 minutes (83% improvement)** and manual errors were completely eliminated.

---

## 2. Did You Get Appreciation From Your Team?

**Answer:**
Yes. After setting up Prometheus + Grafana monitoring, the dev team could see pod health, CPU, memory, and app metrics in one place — no more raising tickets for every issue.

- Alertmanager with Slack/email notifications gave the team production confidence
- My manager appreciated the Terraform automation that cut infra provisioning from **3+ hours → under 10 minutes**

---

## 3. How Will You Encrypt a File?

**Answer:**

**Linux (GPG):**
```bash
gpg -c filename.txt                          # Symmetric encryption
gpg --encrypt -r user@email.com filename.txt # Asymmetric encryption
```

**AWS:**
- S3 Server-Side Encryption (SSE-S3 or SSE-KMS) → data at rest
- AWS Secrets Manager → for credentials and secrets
- Kubernetes Sealed Secrets / HashiCorp Vault → for K8s secrets

**In Transit:**
- Configured **Nginx with TLS termination** for secure traffic routing (used in my projects)

---

## 4. How Many Tickets? Which Monitoring & Ticketing Tools?

**Answer:**
- Handled **8–12 tickets/week** (deployment issues, infra requests, alerts)
- **Monitoring:** Prometheus + Grafana (pod health, CPU, memory, app metrics) + Alertmanager (Slack/email) + AWS CloudWatch (EC2, EKS)
- **Ticketing:** Jira – logged incidents, tracked service requests and deployments

---

## 5. How to Extend LVM?

**Answer — 3 Steps:**

**Step 1: Extend Physical Volume (if new disk added)**
```bash
pvcreate /dev/sdb
vgextend my_vg /dev/sdb
```

**Step 2: Extend Logical Volume**
```bash
lvextend -L +10G /dev/my_vg/my_lv
# OR use all free space:
lvextend -l +100%FREE /dev/my_vg/my_lv
```

**Step 3: Resize the Filesystem**
```bash
resize2fs /dev/my_vg/my_lv    # for ext4
xfs_growfs /mount/point        # for xfs
```

**Verify:**
```bash
df -h
```

---

## 6. Difference Between `-l` (lowercase) and `-L` (uppercase) in LVM?

| Flag | Meaning | Example |
|------|---------|---------|
| `-l` (lowercase L) | Logical **extents** — relative units | `lvextend -l +100%FREE` |
| `-L` (uppercase L) | Absolute **size** in GB/MB/TB | `lvextend -L +10G` |

> `-l` = relative/extent-based | `-L` = size-based

---

## 7. Simple Ansible Playbook — Install & Start Nginx

```yaml
---
- name: Install and start Nginx
  hosts: webservers
  become: yes

  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Start and enable nginx
      service:
        name: nginx
        state: started
        enabled: yes
```

**Explanation:**
| Line | What it does |
|------|-------------|
| `hosts: webservers` | Targets the 'webservers' group in inventory |
| `become: yes` | Runs tasks as sudo/root |
| `apt: state: present` | Installs Nginx if not already installed |
| `service: state: started` | Starts Nginx and enables it on system boot |

> Used Ansible to configure EC2 instances after Terraform provisioning.

---

## 8. Biggest Issue Solved in Previous Organisation?

**Answer:**
At Accenture, the biggest issue was **production deployment failures and downtime** caused by fully manual, inconsistent deployments.

**What I did:**
- Built complete CI/CD pipeline: Jenkins → Docker → Kubernetes → ArgoCD GitOps
- Added readiness & liveness probes for zero-downtime rolling updates
- Configured automated rollback on ArgoCD sync failures

**Result:**
- Deployment time: **30 min → under 5 min (83% faster)**
- Manual errors: **Reduced to zero**
- Team went from dreading releases → **multiple deployments per day confidently**

---

## 9. After Getting the Job, How Will You Help the Team?

**Answer:**
- **Week 1–2:** Understand existing infra, pipelines, and pain points
- **Quick wins:** Improve monitoring dashboards, fix flaky pipelines
- **Contribute with:** Prometheus/Grafana observability, Jenkins/GitHub Actions CI/CD, Terraform IaC automation
- **Document everything** so the whole team benefits
- Open to **knowledge sharing and pair-working** on complex issues

---

## 10. What Are the Challenges You Have Faced?

**Challenge 1 — GitOps Adoption Resistance:**
Team was resistant to ArgoCD automated rollbacks. I set it up in staging first, demonstrated self-healing, showed the audit trail — adoption became smooth.

**Challenge 2 — Terraform State Conflicts in Team Environment:**
Multiple engineers were running `terraform apply` simultaneously causing state corruption. I solved this by setting up **S3 remote state backend + DynamoDB state locking**.

---

## 11. Tell Me Something About Yourself

**Answer:**
*"I'm Chilukuri Rajesh, a DevOps Engineer with 4+ years of experience based in Hyderabad. I started my career at Accenture in an operations role, and I self-transitioned into DevOps through continuous learning and hands-on practice — which I'm really proud of.*

*Over the years I've worked extensively on AWS, CI/CD pipelines with Jenkins and GitHub Actions, containerization with Docker and Kubernetes on EKS, GitOps with ArgoCD, infrastructure automation with Terraform, and full-stack monitoring using Prometheus and Grafana.*

*Most recently at L&T Technology Services, I designed and maintained Jenkins pipelines that cut release cycles by over 60%, and deployed a complete observability stack on Kubernetes.*

*My biggest achievement is reducing deployment time by 83% — from 30 minutes to under 5 minutes — by building end-to-end automation. I'm someone who takes ownership, documents my work, and constantly looks for ways to improve the team's efficiency. I'm excited about this role because it aligns perfectly with the kind of work I enjoy doing every day."*

---

## 12. Tell Me About Your Project Architecture

**Answer:**
*"In my CI/CD and Observability project at Accenture, here's the architecture I built:*

```
Developer pushes code to GitHub
        ↓
GitHub Webhook triggers Jenkins Pipeline
        ↓
Jenkins stages:
  1. Code Checkout
  2. Unit Tests
  3. Docker Image Build
  4. Push Image to Amazon ECR
  5. Update Kubernetes manifest in GitHub repo
        ↓
ArgoCD detects change in GitHub repo (GitOps)
        ↓
ArgoCD syncs and deploys to Amazon EKS cluster
        ↓
Kubernetes: Rolling update with readiness/liveness probes
        ↓
Nginx (Reverse Proxy + TLS Termination) routes traffic
        ↓
Prometheus scrapes metrics → Grafana Dashboards
        ↓
Alertmanager → Slack/Email alerts on threshold breach
```

**For Infrastructure (Terraform project):**
```
Terraform modules (VPC, Subnets, EC2, IAM, Security Groups)
        ↓
Remote state stored in S3 + DynamoDB state locking
        ↓
Terraform plan/apply triggered via Jenkins pipeline
        ↓
Provisioned EC2s monitored by Prometheus node exporters
        ↓
Grafana dashboards for infra-level visibility
```

*Everything was version-controlled in GitHub, peer-reviewed, and fully auditable."*

---

## 13. What Is Your Team Size?

**Answer:**
*"At Accenture, our DevOps team had around **8–10 members** in total. The broader project team including developers, QA, and business analysts was around 25–30 people.*

*At L&T Technology Services, it was a smaller, focused team of about **5–6 DevOps engineers** working closely with development squads.*

*In both cases, I collaborated closely not just within the DevOps team but also directly with developers, QA engineers, and sometimes client stakeholders to resolve deployment blockers and improve release velocity."*

---

## 14. Roles & Responsibilities — What Did You Do Day to Day?

**Answer:**
*"My day-to-day responsibilities included:*

**CI/CD & Deployments:**
- Maintaining and improving Jenkins pipelines for build, test, and deploy
- Managing ArgoCD GitOps sync and handling deployment failures
- Doing rolling updates and rollbacks on Kubernetes clusters

**Infrastructure:**
- Writing and maintaining Terraform modules for AWS environments
- Managing EC2, EKS clusters, VPCs, IAM roles, security groups
- Handling Kubernetes workloads — Deployments, Services, ConfigMaps, HPA

**Monitoring & Incidents:**
- Monitoring Prometheus/Grafana dashboards daily for anomalies
- Responding to Alertmanager alerts and resolving production issues
- Reducing MTTD and MTTR through proactive alert tuning

**Collaboration:**
- Daily standups with dev teams to resolve deployment blockers
- Reviewing infrastructure change requests via Jira tickets
- Writing runbooks and documentation for the team

**In short — I owned the full lifecycle from code commit to production monitoring."*

---

## 💪 Key Achievements to Mention Anytime

| Achievement | Impact |
|-------------|--------|
| CI/CD Pipeline (Jenkins + Docker + K8s + ArgoCD) | **83% faster deployments, zero manual errors** |
| Terraform IaC Automation | **Infra provisioning: 3 hrs → 10 mins** |
| Zero-downtime deployments | Rolling updates + readiness probes + ArgoCD self-healing |
| Full observability stack | Prometheus + Grafana + Alertmanager → reduced MTTD & MTTR |

---

## 🗣️ Tips for Tomorrow's Interview

1. **Start answers with:** *"In my project at Accenture / L&T..."* — always ground in real experience
2. **Speak slowly** — confidence comes from pace, not speed
3. **If unsure:** *"I haven't worked with that specifically, but here's how I'd approach it..."*
4. **Lead with numbers:** 83%, 10 minutes, zero errors — managers love measurable impact
5. **Take a breath** before answering — it's okay to pause for 2–3 seconds

---

> **You have 4+ years of solid DevOps experience. You've done this work. Trust yourself. You've got this, Rajesh! 🙌**
