# 🚀 Interview Preparation Guide — Chilukuri Rajesh
### Senior DevOps Engineer | AWS | Kubernetes | CI/CD | Terraform | Ansible | Prometheus & Grafana

---

## 👤 SELF INTRODUCTION (Memorize This!)

> *"Hi, my name is Rajesh Chilukuri. I'm a DevOps Engineer with 4+ years of hands-on experience in cloud infrastructure, CI/CD automation, and container orchestration on AWS.*
>
> *I started my career at Accenture, where I worked on two major projects. In the first project, I built end-to-end Jenkins CI/CD pipelines for a Python application — automating everything from code checkout to Docker image build, push to Amazon ECR, and Kubernetes deployment. I also implemented GitOps using ArgoCD, which eliminated configuration drift and enabled self-healing deployments. My biggest achievement there was reducing deployment time from 30 minutes to under 5 minutes — an 83% improvement.*
>
> *In my second project, I focused on Infrastructure as Code using Terraform. I built reusable modules to provision full AWS environments — VPC, EC2, IAM, security groups — and cut provisioning time from 3+ hours to under 10 minutes.*
>
> *Most recently at L&T Technology Services, I owned the full DevOps stack — Jenkins pipelines, Kubernetes on EKS, Prometheus & Grafana observability, and Ansible for configuration management.*
>
> *I'm confident with AWS services, Kubernetes operations, Terraform, and monitoring — and I'm looking for a role where I can continue building scalable, reliable infrastructure."*

---

## 📋 TABLE OF CONTENTS

1. [AWS — RDS Multi-AZ](#1-aws--rds-multi-az)
2. [AWS — RDS Tables and Rows](#2-aws--rds-tables-and-rows)
3. [AWS — S3 Storage Classes](#3-aws--s3-storage-classes)
4. [AWS — IAM](#4-aws--iam-identity-and-access-management)
5. [AWS — Restrict S3 using IAM](#5-aws--restrict-s3-access-using-iam)
6. [Docker — COPY vs ADD](#6-docker--copy-vs-add)
7. [Terraform — Which Module to Change](#7-terraform--which-module-to-modify-aws-cloud)
8. [Jenkins & CI/CD](#8-jenkins--cicd-pipeline)
9. [Docker & Containers](#9-docker--containers)
10. [Kubernetes](#10-kubernetes)
11. [Terraform](#11-terraform)
12. [Ansible](#12-ansible)
13. [Prometheus & Grafana](#13-prometheus--grafana)
14. [ArgoCD & GitOps](#14-argocd--gitops)
15. [Linux & Bash](#15-linux--bash-scripting)
16. [Scenario-Based Questions](#16-scenario-based-questions)
17. [HR Questions](#17-hr--behavioral-questions)

---

## 1. AWS — RDS Multi-AZ

### Q: Why do we use RDS Multi-AZ?

**Short Answer:**
RDS Multi-AZ means AWS keeps TWO copies of your database — one primary (active) and one standby (passive) — in **different Availability Zones (data centers)**. If the primary fails, AWS automatically switches to the standby.

**Simple Example to Explain:**
> "Imagine your database is running in AZ-1 (one data center). Suddenly that data center has a power failure. Without Multi-AZ, your app goes down and stays down until you fix it manually. With Multi-AZ, AWS automatically points your app to the standby database in AZ-2 within 1–2 minutes — no manual work, no data loss."

**Key Points to Say:**
- Used for **High Availability (HA)**
- **Automatic failover** — no manual intervention needed
- Standby is **not used for reads** — it's only for failover (this is different from Read Replicas)
- Synchronous replication — data is written to both primary and standby at the same time

**Difference — Multi-AZ vs Read Replica:**
| Feature | Multi-AZ | Read Replica |
|---|---|---|
| Purpose | High Availability | Performance / Read Scaling |
| Standby accessible? | ❌ No | ✅ Yes (read-only) |
| Failover | ✅ Automatic | ❌ Manual |

**Interview Line:**
> *"In my Terraform setup, I enabled Multi-AZ for our production RDS by setting `multi_az = true`. It ensured zero downtime even if an entire Availability Zone went down."*

---

## 2. AWS — RDS Tables and Rows

### Q: Can we add tables and rows in RDS?

**Short Answer:** YES. RDS is a fully managed database service (MySQL, PostgreSQL, etc.). You can do all normal SQL operations — create tables, insert rows, run queries — exactly like any database.

**Simple Example:**
```sql
-- Connect to your RDS endpoint using MySQL client
mysql -h my-rds-endpoint.amazonaws.com -u admin -p

-- Create a table
CREATE TABLE employees (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  role VARCHAR(50)
);

-- Insert rows
INSERT INTO employees VALUES (1, 'Rajesh', 'DevOps Engineer');
INSERT INTO employees VALUES (2, 'Kumar', 'Backend Developer');

-- Query data
SELECT * FROM employees;
```

**What RDS Manages for You:**
- Automatic backups
- OS patching
- Hardware maintenance
- Multi-AZ failover
- Storage scaling

**Interview Line:**
> *"RDS is fully managed but functionally it's just MySQL or PostgreSQL. We can create tables, insert rows, write queries — everything normal. AWS just handles the infrastructure so we don't have to manage the database server ourselves."*

---

## 3. AWS — S3 Storage Classes

### Q: What are the types of S3 storage classes?

**Simple Explanation:**
S3 has different storage types based on **how often you access data** and **how much you want to pay**.

| Storage Class | When to Use | Retrieval Time | Cost |
|---|---|---|---|
| **S3 Standard** | Frequently accessed files (app data, images) | Instant | Highest |
| **S3 Standard-IA** | Accessed once a month (backups) | Instant | Medium |
| **S3 One Zone-IA** | Infrequent access, single AZ only (less safe) | Instant | Lower |
| **S3 Intelligent-Tiering** | Unknown access patterns — auto-moves between tiers | Instant | Smart/Auto |
| **S3 Glacier Instant** | Archives accessed rarely but instantly | Instant | Low |
| **S3 Glacier Flexible** | Long-term archives, okay to wait minutes/hours | Minutes–Hours | Very Low |
| **S3 Glacier Deep Archive** | Rarely accessed, wait up to 12 hours okay | Up to 12 hrs | Lowest |

**Real Example from Your Work:**
- Terraform state files → **S3 Standard** (accessed frequently by pipelines)
- Application logs older than 30 days → **S3 Glacier** (archive, rarely accessed)
- Docker build artifacts → **S3 Standard** (frequently pulled by Jenkins)

**Interview Line:**
> *"In my project, I stored Terraform remote state in S3 Standard because Jenkins pipelines access it frequently. For older build logs that we rarely check, we set up S3 Lifecycle policies to automatically move them to Glacier after 30 days to save costs."*

---

## 4. AWS — IAM (Identity and Access Management)

### Q: What is IAM and how is it used?

**Simple Explanation:**
IAM is the **security gate** of AWS. It controls **WHO can do WHAT** on which AWS resources.

**Three Main IAM Concepts:**

| Concept | What it is | Example |
|---|---|---|
| **User** | A person/account | Developer "Rajesh" |
| **Role** | Permissions for an AWS service | EC2 role to access S3 |
| **Policy** | The actual rules (JSON) | Allow/Deny specific actions |

**Simple Real Example:**
> "I want my Jenkins EC2 server to push Docker images to Amazon ECR. Instead of storing AWS credentials on the server (risky!), I create an **IAM Role** with ECR permissions and attach it to the EC2. Now Jenkins can push images securely — no passwords stored anywhere."

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ecr:GetAuthorizationToken",
        "ecr:BatchCheckLayerAvailability",
        "ecr:PutImage"
      ],
      "Resource": "*"
    }
  ]
}
```

**Key Principle — Least Privilege:**
Always give the **minimum permissions needed** — not admin access for everything.

**From Your Resume:**
You created IAM roles using Terraform for:
- EKS nodes to pull images from ECR
- EC2 instances to access S3 for Terraform state
- Jenkins pipeline roles for AWS deployments

**Interview Line:**
> *"I used Terraform to create IAM roles and attach policies — following least privilege. For example, our EKS worker nodes had a role that only allowed ECR pull and S3 read access — nothing more. This reduces the blast radius if something gets compromised."*

---

## 5. AWS — Restrict S3 Access Using IAM

### Q: Can we restrict S3 access using IAM?

**Short Answer:** YES — two ways to do it.

**Way 1 — IAM Policy (controls who can access):**
Attach a policy to a user/role that **denies** or **allows** S3 access.

```json
{
  "Effect": "Deny",
  "Action": "s3:*",
  "Resource": "arn:aws:s3:::my-terraform-state/*",
  "Principal": "*"
}
```
→ Blocks everyone from accessing this bucket.

**Way 2 — S3 Bucket Policy (controls who can access THIS bucket):**
```json
{
  "Effect": "Allow",
  "Principal": {
    "AWS": "arn:aws:iam::123456789:role/JenkinsPipelineRole"
  },
  "Action": ["s3:GetObject", "s3:PutObject"],
  "Resource": "arn:aws:s3:::my-terraform-state/*"
}
```
→ ONLY the Jenkins pipeline role can read/write. All other users are blocked.

**Real Example from Your Project:**
> "Our Terraform state was stored in S3. We restricted it using a bucket policy so only the Jenkins role could access it. No developer could accidentally delete or modify the state file. We also enabled S3 versioning so we could recover if state got corrupted."

**Interview Line:**
> *"Yes. For our Terraform state S3 bucket, I combined IAM role-based access with an S3 bucket policy and made the bucket private with public access blocked. Only the Jenkins pipeline role had read/write — everyone else was denied by default."*

---

## 6. Docker — COPY vs ADD

### Q: What is the difference between COPY and ADD in Docker?

**Simple Comparison:**

| Feature | `COPY` | `ADD` |
|---|---|---|
| Copy local files | ✅ Yes | ✅ Yes |
| Copy from URL | ❌ No | ✅ Yes |
| Auto-extract .tar.gz | ❌ No | ✅ Yes (auto!) |
| Recommended for? | Most cases | Special cases only |

**Example:**
```dockerfile
# COPY - simple, predictable, preferred
COPY app.py /app/app.py
COPY requirements.txt /app/

# ADD - auto-extracts tar files
ADD myarchive.tar.gz /app/
# This will EXTRACT the tar contents into /app/ automatically!

# ADD - download from URL
ADD https://example.com/config.json /app/config.json
```

**Best Practice:**
Always use `COPY` as the default. Only switch to `ADD` when you specifically need tar extraction or URL downloading. Using `ADD` when `COPY` is enough makes your Dockerfile unpredictable.

**Interview Line:**
> *"In my Dockerfiles, I always used COPY for adding application files — it's explicit and predictable. ADD has hidden behavior like auto-extracting tar files which can cause confusion. Best practice is COPY by default, ADD only when needed."*

---

## 7. Terraform — Which Module to Modify AWS Cloud?

### Q: Which module would you change in a Terraform file to modify AWS cloud resources?

**Simple Explanation:**
In Terraform, you organize your infrastructure into **modules** — each module handles one specific AWS resource or feature. To modify something, you go to its module.

**Typical Terraform Project Structure (like yours):**
```
terraform/
├── modules/
│   ├── vpc/           ← Modify for networking (subnets, route tables, NAT)
│   ├── ec2/           ← Modify for servers (instance type, AMI, count)
│   ├── iam/           ← Modify for permissions (roles, policies)
│   ├── eks/           ← Modify for Kubernetes cluster
│   ├── s3/            ← Modify for storage buckets
│   └── security_groups/ ← Modify for firewall rules
├── main.tf            ← Calls all modules
├── variables.tf       ← Input values
├── outputs.tf         ← Output values
├── terraform.tfvars   ← Environment-specific values
└── backend.tf         ← Remote state config (S3 + DynamoDB)
```

**Example — Change EC2 instance type:**
```hcl
# modules/ec2/main.tf
resource "aws_instance" "app_server" {
  ami           = var.ami_id
  instance_type = var.instance_type   # Change t2.micro to t3.medium here
  subnet_id     = var.subnet_id
}

# terraform.tfvars — change the value here
instance_type = "t3.medium"
```

**Workflow to Apply Changes:**
```bash
# 1. Make your change in the module
# 2. Preview what will change
terraform plan

# 3. Apply the change
terraform apply

# 4. Confirm by typing "yes"
```

**Interview Line:**
> *"In my project, I organized Terraform into modules — one for VPC, one for EC2, one for EKS, one for IAM. To change any AWS resource, I go to its module, update the configuration, run terraform plan to preview, and terraform apply to deploy. I used Terraform workspaces to keep dev, staging, and prod completely isolated."*

---

## 8. Jenkins & CI/CD Pipeline

### Q: Explain your CI/CD pipeline. How does it work?

**Your Pipeline (from resume):**
```
Developer pushes code to GitHub
        ↓
GitHub Webhook triggers Jenkins
        ↓
Jenkins: Code Checkout from GitHub
        ↓
Jenkins: Run Unit Tests
        ↓
Jenkins: Build Docker Image
        ↓
Jenkins: Push Image to Amazon ECR
        ↓
Jenkins: Deploy to Kubernetes (EKS)
        ↓
ArgoCD: Sync and verify deployment
        ↓
Prometheus + Grafana: Monitor health
```

**Simple Jenkinsfile Example:**
```groovy
pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps { git 'https://github.com/myrepo/app.git' }
    }
    stage('Test') {
      steps { sh 'pytest tests/' }
    }
    stage('Build Docker Image') {
      steps { sh 'docker build -t my-app:${BUILD_NUMBER} .' }
    }
    stage('Push to ECR') {
      steps { sh 'docker push 123456789.dkr.ecr.ap-south-1.amazonaws.com/my-app:${BUILD_NUMBER}' }
    }
    stage('Deploy to K8s') {
      steps { sh 'kubectl apply -f k8s/deployment.yaml' }
    }
  }
}
```

**Interview Line:**
> *"I built a Jenkins pipeline where GitHub webhooks trigger the job on every code push. It checks out code, runs tests, builds a Docker image, pushes to ECR, and deploys to EKS. This reduced our deployment time from 30 minutes to under 5 minutes."*

### Q: What is the difference between CI and CD?

| Term | Full Form | Meaning |
|---|---|---|
| **CI** | Continuous Integration | Automatically build and test code on every commit |
| **CD** | Continuous Delivery | Automatically deploy to staging; manual approval for prod |
| **CD** | Continuous Deployment | Automatically deploy to production — no manual step |

---

## 9. Docker & Containers

### Q: What is Docker and why do we use it?

**Simple Explanation:**
Docker packages your application + all its dependencies into a **container** — a lightweight, portable box that runs the same way everywhere.

**Problem it solves:**
> "Works on my machine but not on server" — Docker fixes this. The container has everything the app needs.

**Basic Commands You Should Know:**
```bash
# Build an image
docker build -t my-app:v1 .

# Run a container
docker run -d -p 8080:80 my-app:v1

# List running containers
docker ps

# View logs
docker logs <container-id>

# Stop a container
docker stop <container-id>

# Push to ECR
docker push 123456789.dkr.ecr.ap-south-1.amazonaws.com/my-app:v1
```

### Q: What is a Dockerfile? Explain a simple one.

```dockerfile
# Base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy dependencies file
COPY requirements.txt .

# Install dependencies
RUN pip install -r requirements.txt

# Copy application code
COPY . .

# Expose port
EXPOSE 8080

# Start the app
CMD ["python", "app.py"]
```

### Q: Difference between Docker Image and Docker Container?

| Docker Image | Docker Container |
|---|---|
| Blueprint / Template | Running instance of an image |
| Static (like a class) | Dynamic (like an object) |
| Stored in ECR/DockerHub | Runs on a server |

---

## 10. Kubernetes

### Q: What is Kubernetes and why do we use it?

**Simple Explanation:**
Kubernetes (K8s) is a **container orchestration tool** — it manages running your Docker containers at scale. It handles starting, stopping, scaling, and healing your containers automatically.

**Key Kubernetes Objects You Use:**

| Object | What it does |
|---|---|
| **Pod** | Smallest unit — runs one or more containers |
| **Deployment** | Manages multiple pods, handles rolling updates |
| **Service** | Exposes pods to network traffic (internal/external) |
| **ConfigMap** | Stores non-secret configuration |
| **Secret** | Stores sensitive data (passwords, tokens) |
| **Namespace** | Logical isolation between environments |
| **HPA** | Auto-scales pods based on CPU/memory |

### Q: What is a Deployment YAML? Show an example.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: production
spec:
  replicas: 3               # Run 3 copies of the app
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: 123456789.dkr.ecr.ap-south-1.amazonaws.com/my-app:v1
        ports:
        - containerPort: 8080
        readinessProbe:     # Only send traffic when app is ready
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 5
        livenessProbe:      # Restart container if it becomes unhealthy
          httpGet:
            path: /health
            port: 8080
          periodSeconds: 10
```

### Q: What are liveness and readiness probes?

| Probe | Purpose |
|---|---|
| **Readiness Probe** | "Is this pod ready to receive traffic?" — if fails, removed from load balancer |
| **Liveness Probe** | "Is this pod still alive?" — if fails, Kubernetes restarts the container |

**Interview Line:**
> *"I used readiness and liveness probes in all our Kubernetes deployments. This enabled zero-downtime rolling updates — Kubernetes only routes traffic to a new pod after the readiness probe passes, and automatically restarts unhealthy pods."*

### Q: What is the difference between Rolling Update and Blue-Green Deployment?

| Strategy | How it works |
|---|---|
| **Rolling Update** | Replace old pods one by one with new ones (K8s default) |
| **Blue-Green** | Run new version alongside old, then switch traffic all at once |
| **Canary** | Send a small % of traffic to new version first, then increase |

---

## 11. Terraform

### Q: What is Terraform and why do we use it?

**Simple Explanation:**
Terraform is **Infrastructure as Code (IaC)** — instead of clicking in the AWS console to create servers, you write code and Terraform creates it automatically.

**Benefits:**
- Repeatable — create the same infra in dev, staging, prod
- Version controlled — infra changes tracked in Git
- Fast — create full environment in minutes not hours

### Q: What is Terraform remote state? Why do we use it?

**Problem without remote state:**
By default, Terraform saves state in a local file (`terraform.tfstate`). If two team members run Terraform at the same time — state gets corrupted.

**Solution — Remote state in S3 + DynamoDB:**
```hcl
# backend.tf
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "ap-south-1"
    dynamodb_table = "terraform-state-lock"  # Prevents simultaneous applies
  }
}
```

**Interview Line:**
> *"I set up Terraform remote state in S3 with DynamoDB state locking. S3 stores the state file, and DynamoDB ensures only one person can run terraform apply at a time — preventing state corruption in team environments."*

### Q: What are Terraform workspaces?

Workspaces let you use the **same Terraform code** for multiple environments (dev, staging, prod) without duplicating files.

```bash
# Create workspaces
terraform workspace new dev
terraform workspace new staging
terraform workspace new prod

# Switch between them
terraform workspace select prod

# See which workspace you're in
terraform workspace show
```

**Interview Line:**
> *"I used Terraform workspaces to manage dev, staging, and prod with the same codebase. Each workspace has its own state file, so changes in dev don't affect prod. Combined with terraform.tfvars per environment, it was clean and safe."*

### Q: What is the difference between terraform plan and terraform apply?

| Command | What it does |
|---|---|
| `terraform init` | Download providers and set up backend |
| `terraform plan` | Preview what WILL be created/changed/destroyed — no actual changes |
| `terraform apply` | Actually make the changes to AWS |
| `terraform destroy` | Destroy all managed resources |

---

## 12. Ansible

### Q: What is Ansible and how did you use it?

**Simple Explanation:**
Ansible is a **configuration management tool** — it automates software installation and server configuration across multiple machines at once using YAML files called **playbooks**.

**Real Example — Install Nginx on 10 servers:**

Without Ansible: SSH into each server one by one and run commands manually.

With Ansible:
```yaml
# install_nginx.yml
- name: Install and start Nginx
  hosts: webservers        # All servers in the "webservers" group
  become: yes              # Run as root
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present

    - name: Start Nginx service
      service:
        name: nginx
        state: started
        enabled: yes
```

```bash
# Run the playbook — installs on ALL servers at once
ansible-playbook -i inventory.ini install_nginx.yml
```

**Ansible vs Terraform:**
| Ansible | Terraform |
|---|---|
| Configure software on existing servers | Create/destroy infrastructure |
| Push-based | Declarative |
| Agentless (uses SSH) | Uses providers/APIs |

**Interview Line:**
> *"I used Ansible to configure EC2 instances after Terraform provisioned them. Terraform creates the server, Ansible installs the required software — Node Exporter, application dependencies — using playbooks. This separated infrastructure creation from configuration management cleanly."*

---

## 13. Prometheus & Grafana

### Q: How did you set up monitoring? Explain Prometheus and Grafana.

**Simple Explanation:**
- **Prometheus** = Data collector. Scrapes metrics from your apps and servers every few seconds and stores them.
- **Grafana** = Dashboard tool. Reads data from Prometheus and displays beautiful graphs.
- **Alertmanager** = Sends alerts (email/Slack) when metrics cross thresholds.

**How it flows:**
```
Your App / K8s Pods
       ↓ (exposes /metrics endpoint)
Prometheus (scrapes every 15s)
       ↓ (stores time-series data)
Grafana (reads and visualizes)
       ↓
Alertmanager (fires alerts if CPU > 80%, pod down, etc.)
       ↓
Email / Slack notification to team
```

**What metrics you monitored:**
- Pod CPU and memory usage
- Pod health (up/down)
- Deployment success/failure
- Application response time
- Node (server) resource utilization

**Interview Line:**
> *"I deployed Prometheus and Grafana on Kubernetes. Prometheus scrapes metrics from all pods and nodes every 15 seconds. I built Grafana dashboards showing pod health, CPU, memory, and application SLIs. I configured Alertmanager to send Slack notifications when any pod was down or CPU exceeded 80% — this significantly reduced our incident detection time."*

---

## 14. ArgoCD & GitOps

### Q: What is GitOps? How did you implement it with ArgoCD?

**Simple Explanation:**
GitOps = **Git is the single source of truth for everything** — including Kubernetes configurations.

**Without GitOps:**
> Someone manually runs `kubectl apply` to deploy. No audit trail. Anyone can change anything. Easy to lose track of what's deployed where.

**With ArgoCD (GitOps):**
```
Developer pushes K8s YAML to GitHub
         ↓
ArgoCD watches the GitHub repo
         ↓
ArgoCD detects a change
         ↓
ArgoCD automatically syncs the change to Kubernetes cluster
         ↓
If someone manually changes K8s directly → ArgoCD reverts it back (self-healing!)
```

**Benefits:**
- Full audit trail — every change is a Git commit
- Self-healing — ArgoCD corrects drift automatically
- Easy rollback — `git revert` to go back to a previous state

**Interview Line:**
> *"I implemented GitOps using ArgoCD at Accenture. Every Kubernetes deployment YAML was stored in GitHub. ArgoCD monitored the repo and automatically synced any changes to the cluster. If someone manually changed a Kubernetes resource, ArgoCD would detect the drift and revert it. This eliminated configuration drift entirely and gave us full audit history of every deployment."*

---

## 15. Linux & Bash Scripting

### Q: What Linux commands do you use daily as a DevOps engineer?

```bash
# Check disk usage
df -h

# Check memory usage
free -m

# Check running processes
top / htop

# Find a process using a port
netstat -tulnp | grep 8080
lsof -i :8080

# View logs in real-time
tail -f /var/log/app.log

# Search inside files
grep -r "ERROR" /var/log/

# Check system resource usage
ps aux | sort -rk 3 | head -10

# Copy files between servers
scp file.txt user@server:/path/

# Check network connectivity
curl -I https://example.com
ping google.com
```

### Q: Write a simple bash script to check if a service is running.

```bash
#!/bin/bash
SERVICE="nginx"

if systemctl is-active --quiet $SERVICE; then
    echo "$SERVICE is running ✅"
else
    echo "$SERVICE is NOT running ❌ — restarting..."
    systemctl start $SERVICE
fi
```

---

## 16. Scenario-Based Questions

### Q: Your production deployment failed. How do you rollback?

**Answer (using your real experience):**
> *"First, I check the deployment status in Kubernetes using `kubectl rollout status`. If it's failing, I immediately rollback using `kubectl rollout undo deployment/my-app`. Since we use ArgoCD, I can also revert the Git commit and ArgoCD will sync the previous version back automatically. Meanwhile, I check pod logs with `kubectl logs` and Grafana dashboards to identify the root cause — so we can fix it before redeploying."*

```bash
# Rollback Kubernetes deployment
kubectl rollout undo deployment/my-app

# Check rollout history
kubectl rollout history deployment/my-app

# Check pod logs for errors
kubectl logs -f deployment/my-app
```

---

### Q: Your Jenkins pipeline is failing at the Docker build stage. How do you debug?

**Steps:**
1. Check Jenkins console output — read the error message
2. Try running the `docker build` command manually on the server
3. Check if Dockerfile has errors
4. Check if base image is available/accessible
5. Check disk space on the Jenkins server (`df -h`)
6. Check if ECR credentials are valid

**Interview Line:**
> *"I always start with the Jenkins console output to find the exact error line. Most Docker build failures are either a Dockerfile syntax error, a dependency download failure, or disk space issues on the Jenkins agent. I reproduce it manually first to isolate the cause."*

---

### Q: How do you handle a pod that is in CrashLoopBackOff?

```bash
# Step 1: Check pod status
kubectl get pods

# Step 2: Describe the pod to see events
kubectl describe pod <pod-name>

# Step 3: Check the logs of the crashed container
kubectl logs <pod-name> --previous

# Common causes:
# - Application error at startup
# - Wrong environment variable or missing config
# - Image pull error
# - Resource limits too low (OOMKilled)
```

**Interview Line:**
> *"CrashLoopBackOff means the container is starting and crashing repeatedly. I first run `kubectl describe pod` to check events, then `kubectl logs --previous` to see what error the last crash produced. Most times it's a missing environment variable, wrong config, or the app hitting memory limits."*

---

### Q: How do you reduce AWS costs in your infrastructure?

**Your answers based on your experience:**
- Use **S3 Lifecycle policies** to move old logs to Glacier
- Use **Kubernetes HPA** (Horizontal Pod Autoscaler) to scale down pods when not needed
- Use **Spot Instances** for non-production workloads
- Use **Terraform** to automatically tear down dev environments after hours
- **Right-size EC2 instances** based on actual usage from CloudWatch metrics
- Use **S3 Intelligent-Tiering** for storage where access patterns are unknown

---

## 17. HR & Behavioral Questions

### Q: Tell me about your biggest achievement.

> *"My biggest achievement was reducing deployment time by 83% at Accenture — from 30 minutes to under 5 minutes. I built a fully automated Jenkins pipeline that handled everything from code checkout to Docker build, ECR push, and Kubernetes deployment. Combined with ArgoCD for GitOps, we also eliminated manual deployment errors completely. This allowed the development team to deploy multiple times a day with confidence."*

---

### Q: Why did you choose DevOps?

> *"I transitioned into DevOps from a non-IT background with determination. I was fascinated by how automation and infrastructure could directly impact development speed and system reliability. Once I started learning AWS, Docker, and Kubernetes, I was hooked. Seeing my Terraform code spin up a full AWS environment in 10 minutes — something that used to take 3+ hours manually — was very motivating. I enjoy the mix of cloud, automation, and problem-solving that DevOps brings."*

---

### Q: Where do you see yourself in 2 years?

> *"I want to deepen my expertise in cloud-native architecture — specifically around platform engineering and SRE practices. I'd like to get AWS Solutions Architect or CKA (Certified Kubernetes Administrator) certified, and take ownership of larger infrastructure at scale — designing multi-region, highly available systems."*

---

### Q: What is your biggest weakness?

> *"Early in my career, I used to spend too much time perfecting a solution before sharing it with the team. I've learned to balance perfectionism with practicality — ship a working solution, gather feedback, then iterate. Agile and CI/CD have actually helped me adopt this mindset naturally."*

---

## 📝 QUICK CHEAT SHEET

| Topic | Your Key Line |
|---|---|
| **RDS Multi-AZ** | High availability — automatic failover if AZ goes down |
| **RDS Tables** | Yes, fully managed but SQL works normally |
| **S3 Types** | Standard → IA → Glacier, based on access frequency |
| **IAM** | Controls who can do what on AWS — users, roles, policies |
| **S3 + IAM** | Bucket policy + IAM role restricts access to specific services |
| **COPY vs ADD** | COPY is simple file copy; ADD also extracts tar and fetches URLs |
| **Terraform Modules** | Each AWS resource has its own module — change the right one |
| **Jenkins** | GitHub webhook → build → test → Docker → ECR → K8s deploy |
| **Kubernetes** | Manages containers at scale — deployments, services, probes |
| **Terraform** | IaC — write code, create AWS infra automatically |
| **Ansible** | Configuration management — install software on servers |
| **Prometheus/Grafana** | Collect and visualize metrics, alert on issues |
| **ArgoCD** | GitOps — GitHub is source of truth, auto-syncs to K8s |

---

## 🎯 KEY NUMBERS TO REMEMBER FROM YOUR RESUME

- **83%** reduction in deployment time (30 min → 5 min)
- **4+ years** of DevOps experience
- **3+ hours → 10 minutes** infrastructure provisioning with Terraform
- **60%+** reduction in release cycles at L&T
- **Zero** manual deployment errors after ArgoCD GitOps adoption

---

*Prepared based on resume of Chilukuri Rajesh | Senior DevOps Engineer*
*Good luck with your interviews! You have strong real-world experience — just explain what you actually did! 🚀*
