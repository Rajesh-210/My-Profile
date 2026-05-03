# 🎯 DevOps Interview Questions & Answers
### Prepared for: Chilukuri Rajesh | DevOps Engineer

---

## 📌 Table of Contents

1. [👋 Self Introduction](#-self-introduction)
2. [🐳 Docker](#-docker)
3. [☸️ Kubernetes](#-kubernetes)
4. [🔁 CI/CD & Jenkins](#-cicd--jenkins)
5. [🔀 ArgoCD & GitOps](#-argocd--gitops)
6. [🌍 Terraform & AWS](#-terraform--aws)
7. [📊 Prometheus & Grafana](#-prometheus--grafana)
8. [💬 Behavioral Questions](#-behavioral-questions)

---

## 👋 Self Introduction

### How to Introduce Yourself in an Interview

> *"Hello, my name is **Chilukuri Rajesh**. I am a DevOps Engineer with **4+ years of experience** in CI/CD automation, cloud infrastructure on AWS, containerization, and observability.*
>
> *I hold a **B.Tech in Computer Science & Engineering** from Jawaharlal Nehru Technological University, Hyderabad.*
>
> *I started my career at **Accenture**, where I spent over 3 years building end-to-end Jenkins CI/CD pipelines, containerizing microservices with Docker, deploying workloads on Amazon EKS, and setting up full-stack monitoring using Prometheus and Grafana.*
>
> *One of my key highlights there was reducing deployment time from **30 minutes to under 5 minutes — an 83% improvement** — and cutting infrastructure provisioning from **3+ hours to under 10 minutes** using Terraform IaC automation.*
>
> *Most recently, I worked at **L&T Technology Services**, where I continued to build on those skills — implementing GitOps workflows with ArgoCD, managing Kubernetes at scale, and driving zero-downtime deployments with rolling updates and self-healing configurations.*
>
> *I am passionate about automation, reliability, and making deployments faster and safer. I am now looking for an opportunity where I can continue solving real infrastructure challenges and contribute to a high-performing DevOps team."*

---

### 💡 Introduction Tips

| Tip | Why it matters |
|---|---|
| Mention your total experience upfront | Sets expectations immediately |
| Highlight your top achievement with numbers | Makes you memorable |
| Name the tools you've used confidently | Shows hands-on experience |
| End with what you're looking for | Shows clarity and direction |
| Keep it under 2 minutes | Interviewers lose attention after that |

---

## 🐳 Docker

### Q1. What is Docker?
> Docker is a tool that packages an application and its dependencies into a **container** so it runs consistently across any environment — dev, staging, or production.

---

### Q2. What is the difference between an Image and a Container?
| Image | Container |
|---|---|
| Blueprint / template | Running instance of an image |
| Read-only | Has a writable layer |
| Like a class | Like an object |

---

### Q3. What is Docker Compose?
> Docker Compose is a tool to define and run **multi-container applications** using a single `docker-compose.yml` file. It starts all services with one command: `docker-compose up`.

---

### Q4. How did you containerize a microservice with Docker?

> I write a Dockerfile that builds the app step by step:

```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 8080
CMD ["python", "app.py"]
```

> **Example:** At Accenture, I containerized a Python microservice this way, pushed the image to **Amazon ECR**, and deployed it on **EKS**.

---

### Q5. What is Amazon ECR?
> Amazon ECR (Elastic Container Registry) is AWS's managed Docker image registry. I pushed Docker images to ECR and pulled them during Kubernetes deployments on EKS.

---

## ☸️ Kubernetes

### Q6. What is Kubernetes?
> Kubernetes is a **container orchestration tool** that automates deployment, scaling, and management of containerized applications across a cluster of machines.

---

### Q7. What is a Pod?
> A Pod is the **smallest deployable unit** in Kubernetes. It holds one or more containers that share the same network and storage.

---

### Q8. What is the difference between a Deployment and a Service?
| Deployment | Service |
|---|---|
| Manages how many pods run | Exposes pods to network traffic |
| Handles rolling updates | Acts as a stable endpoint |
| Controls replicas | Load balances across pods |

---

### Q9. What is the difference between a Deployment and a StatefulSet?
| Deployment | StatefulSet |
|---|---|
| For stateless apps | For stateful apps |
| Random pod names | Fixed names (pod-0, pod-1) |
| Shared storage | Each pod gets its own storage |
| Example: Web APIs | Example: Databases, Kafka |

> **Example:** I used Deployments for all our stateless Python microservices at Accenture.

---

### Q10. What is a Namespace?
> A Namespace **logically separates** resources within a Kubernetes cluster. For example, I used separate namespaces for `dev`, `staging`, and `prod` environments.

---

### Q11. How do Readiness and Liveness Probes work?

> - **Liveness Probe** — checks if the app is alive. If it fails, Kubernetes **restarts** the pod.
> - **Readiness Probe** — checks if the app is ready to receive traffic. If it fails, pod is **removed from load balancer**.

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 10

readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
```

> **Example:** I configured both probes on EKS deployments so during rolling updates, new pods only received traffic after they were fully ready — achieving **zero downtime**.

---

### Q12. What is Helm?
> Helm is a **package manager for Kubernetes**. It bundles Kubernetes manifests into reusable "charts" and allows environment-specific deployments using values files.

```bash
helm upgrade --install myapp ./chart -f values-prod.yaml
```

> **Example:** At L&T, I used Helm with separate `values-dev.yaml`, `values-staging.yaml`, and `values-prod.yaml` to deploy the same chart across all environments.

---

### Q13. What is Kubernetes HPA?
> HPA (Horizontal Pod Autoscaler) **automatically scales** the number of pods up or down based on CPU or memory usage.

> **Example:** At Accenture, I configured HPA on EKS so pods scaled automatically during peak traffic without manual intervention.

---

## 🔁 CI/CD & Jenkins

### Q14. What is CI/CD?
> - **CI (Continuous Integration)** — automatically build and test code on every commit
> - **CD (Continuous Delivery/Deployment)** — automatically deploy tested code to production

---

### Q15. What is Jenkins?
> Jenkins is an open-source **automation server** used to build CI/CD pipelines. It automates code build, test, and deployment workflows.

---

### Q16. Walk me through the Jenkins pipeline you built.

> My Jenkins pipeline had these stages:
> 1. **Code Checkout** — pulls latest code from GitHub
> 2. **Unit Tests** — runs automated tests
> 3. **Docker Build** — builds the Docker image
> 4. **Push to ECR** — pushes image to Amazon ECR
> 5. **Deploy to Kubernetes** — applies K8s manifests to EKS

> Triggered via **GitHub Webhook** — whenever a developer pushes code, Jenkins automatically starts the pipeline.

> **Example:** At Accenture, this pipeline reduced deployment time from **30 minutes to under 5 minutes (83% improvement)**.

---

### Q17. How did you reduce the release cycle by 60%?

> **Before:** Deployments were manual — SSH into servers, build Docker images by hand, deploy step by step. Slow and error-prone.

> **After:** I automated the entire build-test-deploy workflow using Jenkins + GitHub Webhooks. No manual steps, no human errors.

> **Example:** At L&T Technology Services, this automation cut the release cycle by over **60%**.

---

### Q18. How do you handle a failed build in Jenkins? What is your rollback strategy?

> If a build fails:
> - Jenkins **stops the pipeline** immediately — bad code never reaches production
> - In **Kubernetes**: `kubectl rollout undo deployment/<name>` rolls back instantly
> - In **ArgoCD**: sync failure policies automatically revert to the last healthy Git commit

> **Example:** At Accenture, ArgoCD auto-rollback meant zero manual intervention on failed deployments.

---

## 🔀 ArgoCD & GitOps

### Q19. What is GitOps?
> GitOps means your **Git repository is the single source of truth** for both application and infrastructure state. Any change goes through Git first, then gets automatically applied to the cluster.

---

### Q20. What is ArgoCD and how does it implement GitOps?

| Traditional CI/CD | GitOps with ArgoCD |
|---|---|
| Jenkins pushes code to cluster | ArgoCD pulls state from Git |
| Manual or scripted deploy | Automatic sync |
| Hard to audit | Every change tracked in Git |

> **Example:** At Accenture, after every merge to `main`, ArgoCD automatically synced the new Kubernetes manifests to EKS — no manual deployment needed.

---

### Q21. What happens when ArgoCD detects drift? How does self-healing work?

> **Drift** happens when someone manually changes something in the cluster that doesn't match Git.

> With **self-healing enabled**, ArgoCD detects the drift and automatically reverts the cluster back to the Git state within minutes.

> **Example:** I enabled self-healing so even if someone accidentally deleted a pod or changed a config manually, ArgoCD restored it automatically from the Git repo.

---

### Q22. How did you configure ArgoCD sync failure policies for automated rollbacks?

```yaml
syncPolicy:
  automated:
    selfHeal: true
    prune: true
  retry:
    limit: 3
    backoff:
      duration: 30s
```

> **Example:** This ensured that if a bad deployment went out, ArgoCD would auto-rollback without anyone having to manually intervene.

---

## 🌍 Terraform & AWS

### Q23. What is Terraform?
> Terraform is an **Infrastructure as Code (IaC)** tool that lets you define and provision cloud infrastructure using configuration files instead of doing it manually through the AWS console.

---

### Q24. What is the difference between `terraform plan` and `terraform apply`?
| Command | What it does |
|---|---|
| `terraform plan` | Shows what changes WILL be made (dry run) |
| `terraform apply` | Actually makes those changes |

---

### Q25. How did you structure your Terraform modules for reusability?

```
modules/
  vpc/
  ec2/
  iam/
  security-groups/
```

> Each module takes input variables and returns outputs. I reuse the same module for dev, staging, and prod by passing different values.

> **Example:** At Accenture, I built a VPC module that provisioned subnets, route tables, and NAT gateways — reused across all environments by just changing the CIDR values.

---

### Q26. What is Terraform remote state and why did you use S3 + DynamoDB?

> Terraform stores a **state file** that tracks existing infrastructure. Remote state stores this file in a shared location.

> - **S3** → stores the state file safely
> - **DynamoDB** → handles **state locking** so two engineers can't run `terraform apply` at the same time and corrupt the state

> **Example:** At Accenture, without remote state locking, two engineers could have corrupted the state file simultaneously. S3 + DynamoDB prevented that.

---

### Q27. How did you use Terraform workspaces?

> Terraform workspaces maintain **separate state files** for each environment using the same code.

```bash
terraform workspace new dev
terraform workspace new staging
terraform workspace new prod
terraform workspace select prod
terraform apply
```

> **Example:** I used workspaces at Accenture so changes in dev never accidentally affected prod.

---

### Q28. Walk me through a VPC setup you provisioned on AWS.

> I provisioned a full VPC using Terraform that included:
> - **VPC** with custom CIDR (e.g., `10.0.0.0/16`)
> - **Public subnets** for load balancers
> - **Private subnets** for EC2/EKS nodes
> - **Internet Gateway** for public traffic
> - **NAT Gateway** so private instances can reach the internet
> - **Route tables** and **Security Groups** for access control

---

### Q29. What AWS services have you worked with most?

| Service | Purpose |
|---|---|
| EC2 | Virtual servers for running applications |
| S3 | Object storage for files and Terraform state |
| IAM | Access control for AWS resources |
| VPC | Private network setup on AWS |
| EKS | Managed Kubernetes service |
| ECR | Docker image registry |
| CloudWatch | AWS-native monitoring and logging |
| Route 53 | DNS management |
| ELB | Load balancing across instances |

---

## 📊 Prometheus & Grafana

### Q30. What is Prometheus?
> Prometheus is a **monitoring tool** that collects metrics from applications and infrastructure at regular intervals (scraping).

---

### Q31. What is Grafana?
> Grafana is a **visualization tool** that connects to Prometheus and displays metrics as interactive dashboards and graphs.

---

### Q32. What is Alertmanager?
> Alertmanager handles alerts fired by Prometheus and routes **notifications** to Slack, email, or PagerDuty when thresholds are breached.

---

### Q33. How did you configure Prometheus scrape targets?

```yaml
scrape_configs:
  - job_name: 'kubernetes-pods'
    kubernetes_sd_configs:
      - role: pod
```

> **Example:** I configured scrape targets for all EKS pods and EC2 node exporters so Prometheus collected CPU, memory, and custom app metrics every 15 seconds.

---

### Q34. What custom dashboards did you build in Grafana?

> I built dashboards tracking:
> - **Pod health** — running vs crashed pods
> - **CPU & Memory usage** — per pod and per node
> - **Request rate & error rate** — application-level metrics
> - **Deployment status** — rollout progress

> **Example:** At L&T, Grafana dashboards gave the team real-time visibility during every deployment — we could immediately spot if a new version caused memory spikes.

---

### Q35. How did you configure Alertmanager with alert rules?

```yaml
- alert: HighCPUUsage
  expr: cpu_usage > 80
  for: 5m
  labels:
    severity: warning
  annotations:
    summary: "CPU usage above 80% for 5 minutes"
```

> Alertmanager then routes this alert to **Slack or email**.

> **Example:** I set up Slack notifications for critical pod crashes and email alerts for high CPU/memory — this significantly reduced our **MTTD (Mean Time to Detect)**.

---

## 💬 Behavioral Questions

### Q36. Tell me about a critical production incident.

> "At Accenture, one of our pods went into **CrashLoopBackOff** during peak hours.
> - Prometheus fired an alert to our Slack channel via Alertmanager
> - I checked logs using `kubectl logs` and found an **out-of-memory issue**
> - I increased memory limits in the deployment YAML and applied the fix
> - ArgoCD synced it automatically
> - Post-incident, I added a Grafana alert for memory usage above 80% to catch it earlier next time"

---

### Q37. How did you transition from a non-IT role to DevOps?

> "I started at Accenture as a Content Moderator. I was always curious about technology, so I began learning **Linux, Git, and AWS** in my own time. I took online courses on Docker and Kubernetes, built personal projects, and gradually moved into a DevOps Engineer role within the same company. My results — like cutting deployment time by **83%** — proved I was ready."

---

### Q38. How do you collaborate with development teams on deployment blockers?

> "I treat developers as partners. When a deployment is blocked:
> 1. I check **Jenkins logs** and **Kubernetes events** to isolate the issue
> 2. I share findings clearly — is it the app code or the infrastructure?
> 3. I suggest fixes and if needed, pair with the developer to resolve it
> 4. I document the root cause so it doesn't happen again"

---

### Q39. How do you manage multiple environments without configuration drift?

> "I use **Terraform workspaces** for infrastructure and **Helm values files** for Kubernetes deployments. Each environment has its own isolated state and configuration. This ensures dev, staging, and prod stay consistent and drift-free."

---

### Q40. Why should we hire you as a DevOps Engineer?

> "I bring 4+ years of hands-on experience across the full DevOps stack — CI/CD, containers, Kubernetes, IaC, and observability. I don't just know the tools theoretically; I've used them to deliver real results:
> - **83% faster deployments** using Jenkins + ArgoCD
> - **Infrastructure provisioning cut from 3 hours to 10 minutes** using Terraform
> - **Zero-downtime deployments** using Kubernetes rolling updates
>
> I'm also a self-starter who transitioned from a non-IT role through continuous learning — which means I'll keep growing with your team."

---

## 🏆 Key Numbers to Remember in Interviews

| Achievement | Metric |
|---|---|
| Deployment time reduction | **30 min → under 5 min (83% faster)** |
| Infrastructure provisioning | **3+ hours → under 10 minutes** |
| Release cycle reduction | **60% faster with Jenkins automation** |
| Deployment errors | **Eliminated entirely with automation** |

---

> 💡 **Pro Tip:** In every answer, follow this formula:
> **Tool Used → What You Did → Result/Impact**
> Example: *"I used ArgoCD to implement GitOps, which automated Kubernetes sync from GitHub and eliminated manual deployments entirely."*
