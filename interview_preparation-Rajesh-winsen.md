# 🎯 Interview Preparation Guide — Chilukuri Rajesh
### Senior DevOps Engineer | AWS | Kubernetes | Terraform | CI/CD

---

## 👋 Introduction (Tell Me About Yourself)

> Practice this until it feels natural. Keep it under 2 minutes.

"Hi, I'm Rajesh, a Senior DevOps Engineer with 4+ years of hands-on experience. I've worked at Accenture and L&T Technology Services, where I built and owned end-to-end CI/CD pipelines, cloud infrastructure on AWS, and Kubernetes-based container platforms.

My biggest achievement was reducing deployment time by 83% — from 30 minutes to under 5 minutes — by building Jenkins pipelines integrated with Docker, EKS, and ArgoCD GitOps.

On the infrastructure side, I've automated AWS provisioning using Terraform modules, cutting environment setup from 3+ hours to under 10 minutes. I also own the full observability stack — Prometheus, Grafana, and Alertmanager.

I'm comfortable owning things end-to-end: from writing Terraform code, managing Kubernetes clusters, to mentoring developers on CI/CD workflows. I'm looking for a role where I can keep growing in cloud-native and DevOps engineering."

---

## ☁️ AWS Questions

---

### 1. Why do we use RDS Multi-AZ?

**Simple Answer:**
RDS Multi-AZ is used for **high availability**. If your primary database goes down, AWS automatically switches to a standby copy — with no data loss and minimal downtime.

**Practical Explanation:**
Imagine your app is running and your primary RDS instance crashes. Without Multi-AZ, your app is down until you manually recover. With Multi-AZ, AWS keeps a **standby replica** in a different Availability Zone (like a backup data center). The failover is automatic and takes 1-2 minutes.

**Key Points:**
- The standby is **not used for reads** — it's only a failover target (unlike Read Replicas)
- Data is **synchronously replicated** — so the standby always has the latest data
- Used when your application **cannot afford downtime** (production databases)
- Covers AZ failures, hardware failures, patching/maintenance

**Interview Tip:** Say — *"In my project, we enabled Multi-AZ on our production RDS to ensure zero data loss during failover. It also helped during maintenance windows because AWS handles patching with automatic failover."*
- "We use RDS Multi-AZ for high availability. If primary DB fails, the standby takes over automatically. I used this in my AWS infra to ensure our production DB never becomes a single point of failure."

---

### 2. Can we add tables and rows in RDS?

**Simple Answer:**
Yes! RDS is just a **managed relational database**. You interact with it exactly like a regular MySQL/PostgreSQL/Oracle database.

**Practical Explanation:**
- RDS manages the underlying server, backups, patching, and HA
- But **the database itself** works the same way — you connect using standard SQL clients
- You can run `CREATE TABLE`, `INSERT INTO`, `SELECT`, etc. normally

```sql
-- Connect to RDS endpoint and run normal SQL
CREATE TABLE users (id INT, name VARCHAR(100));
INSERT INTO users VALUES (1, 'Rajesh');
SELECT * FROM users;
```

**What RDS manages vs. what you manage:**
| AWS Manages | You Manage |
|---|---|
| OS patching | Tables, schemas, rows |
| Backups | Queries and indexes |
| HA/failover | Application connections |
| Storage scaling | Users and permissions |

---

### 3. Types of S3 Storage Classes

**Simple Answer:**
S3 has different storage tiers based on **how often you access data** and **how much you want to pay**.

| Storage Class | Use Case | Cost |
|---|---|---|
| **S3 Standard** | Frequently accessed data (apps, websites) | Highest |
| **S3 Intelligent-Tiering** | Unknown or changing access patterns | Auto-adjusts |
| **S3 Standard-IA** (Infrequent Access) | Data accessed once a month | Lower storage cost, retrieval fee |
| **S3 One Zone-IA** | Non-critical infrequent data (one AZ only) | Cheaper than Standard-IA |
| **S3 Glacier Instant Retrieval** | Archive, retrieved in milliseconds | Very low |
| **S3 Glacier Flexible Retrieval** | Archive, retrieved in minutes-hours | Lower |
| **S3 Glacier Deep Archive** | Long-term archive (7-10 years) | Lowest |

**Practical Example:**
- Application logs → **S3 Standard** (accessed daily)
- Old logs after 30 days → **S3 Standard-IA** (accessed rarely)
- Compliance records → **S3 Glacier Deep Archive** (keep for years, rarely accessed)

**Interview Tip:** *"In my projects, I used S3 Standard for Terraform state files and ECR image layers. I also configured lifecycle policies to move older logs to S3-IA after 30 days to reduce costs."*

---

### 4. What is IAM and How is it Used?

**Simple Answer:**
IAM (Identity and Access Management) controls **who can do what** in your AWS account. It's the security gatekeeper.

**Three main components:**

| Component | What it is | Example |
|---|---|---|
| **Users** | Individual people | Rajesh, Jenkins bot |
| **Groups** | Collection of users | Developers, DevOps team |
| **Roles** | Permissions for AWS services | EC2 role to access S3 |

**Policies** define the permissions (what actions are allowed/denied on which resources).

**Practical Example:**
```json
{
  "Effect": "Allow",
  "Action": ["s3:GetObject", "s3:PutObject"],
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```
This policy allows reading and writing to a specific S3 bucket.

**How I used it:**
*"In my Terraform projects, I created IAM roles for EC2 instances so they could access S3 and DynamoDB without storing access keys. I also created IAM policies with least-privilege access for Jenkins to push images to ECR."*

---

### 5. Can We Restrict S3 Access Using IAM?

**Simple Answer:**
Yes! You can restrict S3 access in two ways — **IAM policies** (user/role level) and **S3 Bucket Policies** (bucket level).

**IAM Policy Approach (deny everyone except specific role):**
```json
{
  "Effect": "Deny",
  "Action": "s3:*",
  "Resource": "arn:aws:s3:::my-secret-bucket/*",
  "Condition": {
    "StringNotEquals": {
      "aws:PrincipalArn": "arn:aws:iam::123456789:role/AllowedRole"
    }
  }
}
```

**Common Restriction Scenarios:**
- Block public access (enable "Block Public Access" setting)
- Allow only specific IAM roles to read/write
- Restrict access to specific VPC only (using VPC endpoint condition)
- Allow only encrypted uploads (`s3:x-amz-server-side-encryption` condition)

**Interview Tip:** *"In my setup, I restricted the Terraform state S3 bucket so only the Jenkins IAM role could access it. I combined both a bucket policy and IAM policy for defense-in-depth."*

---

## 🐳 Docker Questions

---

### 6. Difference Between COPY and ADD in Docker

**Simple Answer:**
Both copy files into a Docker image. But `ADD` has extra superpowers — and that's usually why `COPY` is preferred.

| Feature | COPY | ADD |
|---|---|---|
| Copy local files | ✅ Yes | ✅ Yes |
| Auto-extract tar files | ❌ No | ✅ Yes |
| Download from URL | ❌ No | ✅ Yes |
| Predictable behavior | ✅ Yes | ⚠️ Can be surprising |
| Recommended for | Most use cases | Only when tar/URL needed |

**Practical Example:**
```dockerfile
# Use COPY for simple file copying
COPY ./app /app
COPY requirements.txt /app/

# Use ADD only when you need auto-extraction
ADD archive.tar.gz /opt/  # automatically extracts the tar
```

**Best Practice:** Always use `COPY` unless you specifically need `ADD`'s tar extraction. It makes your Dockerfile more readable and predictable.

---

## 🏗️ Terraform Questions

---

### 7. What Module Would You Change in Terraform to Modify AWS Cloud?

**Simple Answer:**
It depends on **what you want to change**. Terraform uses **modules** as reusable blocks. Each module manages a specific AWS resource or group of resources.

**Common Modules and What They Control:**

| Module | What it modifies in AWS |
|---|---|
| `vpc` module | VPC, subnets, route tables, internet gateway |
| `ec2` module | EC2 instances, AMIs, key pairs |
| `eks` module | Kubernetes cluster on AWS |
| `rds` module | Database instances |
| `iam` module | Roles, policies, users |
| `s3` module | Buckets, policies, versioning |

**Example Module Call:**
```hcl
# In main.tf — calling a VPC module
module "vpc" {
  source = "./modules/vpc"
  cidr_block = "10.0.0.0/16"
  environment = "production"
}
```

To change something in AWS, you update the **variables inside the relevant module**, run `terraform plan` to preview, then `terraform apply`.

**Interview Tip:** *"In my project, I had separate modules for VPC, EC2, IAM, and EKS. To modify the number of worker nodes in EKS, I updated the `desired_size` variable in the EKS module and applied the change through the Jenkins pipeline."*

---

### 8. Resources Provisioned by Terraform (in your project)

**What I provisioned:**
- **VPC** — custom Virtual Private Cloud with public/private subnets
- **Subnets** — across multiple Availability Zones
- **Route Tables** — for traffic routing between subnets
- **NAT Gateway** — for private subnet internet access
- **Internet Gateway** — for public subnet access
- **Security Groups** — firewall rules for EC2 and EKS
- **EC2 Instances** — application servers
- **IAM Roles & Policies** — least-privilege access for services
- **S3 Bucket** — for Terraform remote state storage
- **DynamoDB Table** — for Terraform state locking
- **EKS Cluster** — managed Kubernetes cluster

---

### 9. What is Terraform Lint?

**Simple Answer:**
`tflint` is a static analysis tool that **checks your Terraform code for errors and bad practices before you run it**.

**What it catches:**
- Invalid resource types (e.g., typo in `aws_instanc` instead of `aws_instance`)
- Deprecated syntax or arguments
- Missing required variables
- Naming convention violations
- Provider-specific rule violations (like invalid AWS instance types)

**How to use:**
```bash
# Install and run
tflint --init
tflint

# Example output
Warning: aws_instance.web - "t1.micro" is previous generation instance type.
Error: aws_s3_bucket.my_bucket - "region" argument is not allowed.
```

**Why it matters:** It catches mistakes early — before `terraform plan` or `terraform apply` — saving time and preventing broken infrastructure.

---

### 10. Explain the Terraform Update Workflow

**Step-by-step practical process:**

```
1. terraform init       # Initialize — download providers and modules
2. terraform validate   # Check syntax is valid
3. tflint               # Lint for best practices
4. terraform plan       # Preview what will change (NEVER skip this!)
5. terraform apply      # Apply the changes
6. terraform state list # Verify resources were created
```

**For updates specifically:**
1. Update the `.tf` file (change variable value, add a resource, etc.)
2. Run `terraform plan` — review the diff carefully
3. Look for `~` (update), `+` (create), `-` (destroy)
4. If it looks correct → `terraform apply`
5. State is updated automatically in remote backend (S3 + DynamoDB lock)

**Interview Tip:** *"In my pipeline, Terraform plan runs automatically on PR merge for peer review. The apply step requires manual approval in Jenkins before it runs in production."*

---

## ☸️ Kubernetes Questions

---

### 11. Kubernetes Architecture

**Simple Explanation (Master + Worker):**

```
┌─────────────────────────────────────┐
│           CONTROL PLANE (Master)    │
│  API Server → etcd                  │
│  Scheduler → Controller Manager     │
└─────────────────────────────────────┘
              ↕ communicates
┌─────────────────────────────────────┐
│         WORKER NODES                │
│  kubelet → kube-proxy → Pods        │
└─────────────────────────────────────┘
```

**Control Plane components:**
| Component | Job |
|---|---|
| **API Server** | Entry point for all kubectl commands |
| **etcd** | Key-value store — stores all cluster state |
| **Scheduler** | Decides which node to place a pod on |
| **Controller Manager** | Watches cluster state, fixes drift (like restarting failed pods) |

**Worker Node components:**
| Component | Job |
|---|---|
| **kubelet** | Runs on each node, ensures pods are running |
| **kube-proxy** | Handles network routing between pods and services |
| **Container Runtime** | Docker/containerd — actually runs containers |

---

### 12. Types of Services in Kubernetes

| Service Type | What it does | Use Case |
|---|---|---|
| **ClusterIP** | Internal IP, only accessible inside cluster | Pod-to-pod communication |
| **NodePort** | Exposes service on a static port on each node | Dev/testing, direct node access |
| **LoadBalancer** | Creates a cloud load balancer (AWS ELB) | Production external traffic |
| **ExternalName** | Maps service to an external DNS name | Connect to external services |

**Practical Example:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  type: LoadBalancer       # Creates an AWS ELB
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 8080
```

---

### 13. Kubernetes Headless Service

**Simple Answer:**
A headless service has **no ClusterIP** (`clusterIP: None`). Instead of load balancing, it returns the **individual Pod IPs** directly via DNS.

**When to use it:**
- StatefulSets (databases like MySQL, Cassandra, MongoDB)
- When each pod needs to be addressed individually
- Service discovery where you need direct pod access

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-headless-svc
spec:
  clusterIP: None    # This makes it headless
  selector:
    app: my-db
  ports:
    - port: 3306
```

**Normal Service vs Headless:**
- Normal: `my-svc.default.svc.cluster.local` → single LoadBalancer IP
- Headless: `my-svc.default.svc.cluster.local` → returns all Pod IPs

---

### 14. Pod-to-Pod Communication

**How it works:**
Every pod in Kubernetes gets its own **unique IP address**. Pods can communicate directly using these IPs — no NAT needed.

**Types of communication:**
1. **Same node:** Pods talk via a virtual bridge (cbr0)
2. **Different nodes:** Traffic goes through the CNI plugin (Calico, Flannel, etc.)
3. **Using Services:** Best practice — use Service DNS instead of pod IPs (pods are ephemeral)

**Practical flow:**
```
Pod A (10.0.1.5) → calls http://my-service → 
  kube-proxy routes to → Pod B (10.0.2.8)
```

**Why use Services instead of direct pod IPs?**
Pod IPs change when pods restart. Services provide a stable DNS name.

---

### 15. Difference Between ReplicaSet and Deployment

| Feature | ReplicaSet | Deployment |
|---|---|---|
| Ensures N pods running | ✅ Yes | ✅ Yes (wraps ReplicaSet) |
| Rolling updates | ❌ No | ✅ Yes |
| Rollback | ❌ No | ✅ Yes |
| Pause/Resume updates | ❌ No | ✅ Yes |
| Use in production | Rarely directly | ✅ Always use Deployment |

**Simple Rule:** Always use **Deployment** in production. It manages ReplicaSets under the hood and gives you rolling updates + rollback.

```yaml
# Deployment manages ReplicaSets automatically
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
```

---

### 16. One Pod Goes Down — How Do You Resolve It?

**First, don't panic — Kubernetes usually fixes it automatically!**

**Step 1: Investigate**
```bash
kubectl get pods -n <namespace>               # See pod status
kubectl describe pod <pod-name>               # See events and errors
kubectl logs <pod-name>                       # Check application logs
kubectl logs <pod-name> --previous            # Logs from crashed container
```

**Step 2: Identify the cause**

| Status | Likely Cause |
|---|---|
| `CrashLoopBackOff` | App is crashing — check logs |
| `OOMKilled` | Out of memory — increase limits |
| `Pending` | Not enough resources on nodes |
| `ImagePullBackOff` | Wrong image name or no registry access |
| `Error` | Container start failure |

**Step 3: Fix based on cause**
- App crash → Fix the application bug, redeploy
- OOM → Increase memory limits in the manifest
- Image issue → Fix image tag, check ECR permissions
- Node full → Scale the node group or add a node

**The Deployment controller automatically restarts crashed pods** — but if it keeps crashing, it enters `CrashLoopBackOff`. That's your signal to look at logs.

---

### 17. How to Roll Back to the Previous Deployment

**Simple steps:**
```bash
# Check rollout history
kubectl rollout history deployment/my-app

# Roll back to previous version (one step back)
kubectl rollout undo deployment/my-app

# Roll back to a specific version
kubectl rollout undo deployment/my-app --to-revision=2

# Check rollout status
kubectl rollout status deployment/my-app
```

**How it works:**
Kubernetes keeps a history of ReplicaSets. When you roll back, it just points traffic back to the previous ReplicaSet.

**With ArgoCD (GitOps way):**
Since my setup uses ArgoCD, rollback means reverting the Git commit:
```bash
git revert <commit-hash>
git push
# ArgoCD detects the change and automatically rolls back
```

**Interview Tip:** *"In my projects with ArgoCD, rollback was a Git revert — which gave us a full audit trail. For non-GitOps environments, I used kubectl rollout undo."*

---

### 18. How to Patch Multiple VMs

**Using Ansible (my preferred approach):**
```bash
# inventory.ini — list all VMs
[web_servers]
vm1.example.com
vm2.example.com
vm3.example.com

# Patch all at once
ansible all -i inventory.ini -m apt -a "upgrade=yes update_cache=yes" --become

# Or using a playbook
ansible-playbook -i inventory.ini patch_servers.yml
```

```yaml
# patch_servers.yml
- name: Patch all VMs
  hosts: all
  become: yes
  tasks:
    - name: Update and upgrade packages
      apt:
        upgrade: dist
        update_cache: yes
    - name: Reboot if needed
      reboot:
        when: ansible_facts['os_family'] == 'Debian'
```

**AWS SSM Patch Manager approach:**
- Define a Patch Baseline (which patches to apply)
- Create a Maintenance Window
- AWS SSM automatically patches all tagged EC2 instances

**Interview Tip:** *"In my project, I used Ansible playbooks for bulk patching. I ran them from Jenkins pipelines with a specific inventory file targeting dev, staging, or production servers separately."*

---

## ☁️ Azure Questions

---

### 19. What is Azure Policy and Azure RBAC?

**Azure Policy:**
- Enforces **rules and compliance** across your Azure resources
- Example: "All resources must have a tag called 'Environment'"
- Example: "VMs can only be created in East US region"
- It audits or **auto-remediates** non-compliant resources

**Azure RBAC (Role-Based Access Control):**
- Controls **who can do what** on Azure resources
- You assign built-in or custom **roles** to users/groups

| Role | What they can do |
|---|---|
| **Owner** | Full access including managing permissions |
| **Contributor** | Create/manage resources, no permission management |
| **Reader** | View resources only |
| **Custom Role** | Specific permissions you define |

**Key Difference:**
- **RBAC** → Controls access (who can act)
- **Policy** → Enforces rules (what is allowed to exist)

**Example:** RBAC lets a developer create VMs. Azure Policy enforces that those VMs must use approved SKUs and have tags.

---

### 20. How Do You Update the AKS Master Version?

**Simple Answer:**
AKS handles master/control plane upgrades for you. You initiate the upgrade via Azure CLI or portal.

```bash
# Check available upgrade versions
az aks get-upgrades --resource-group my-rg --name my-aks-cluster

# Upgrade the control plane first
az aks upgrade \
  --resource-group my-rg \
  --name my-aks-cluster \
  --kubernetes-version 1.29.0 \
  --control-plane-only   # Upgrade master only first

# Then upgrade node pools separately
az aks nodepool upgrade \
  --resource-group my-rg \
  --cluster-name my-aks-cluster \
  --name agentpool \
  --kubernetes-version 1.29.0
```

**Best Practice:**
1. Always upgrade control plane first, then node pools
2. Test in dev/staging before production
3. Only upgrade one minor version at a time (1.27 → 1.28, not 1.27 → 1.29)
4. Check for deprecated APIs before upgrading

---

### 21. Have We Downgraded an AKS Cluster?

**Honest Answer:** AKS **does not support downgrading**. Once you upgrade Kubernetes version, you cannot go back.

**How to handle a bad upgrade:**
1. Restore from a backup (Velero snapshots)
2. Create a new cluster with the old version
3. Restore workloads to the new cluster

**Prevention (better than cure):**
- Always test upgrades in non-production first
- Keep etcd backups before upgrades
- Use Blue-Green cluster strategy for zero-risk upgrades

**Interview Tip:** *"In my experience, we never downgraded — it's not supported. That's why we always tested upgrades in a staging AKS cluster first before applying to production. We also kept Velero backups for disaster recovery."*

---

## 🔄 CI/CD & GitOps Questions

---

### Additional: Explain ArgoCD GitOps Workflow

**How it works:**
1. Developer pushes code to GitHub
2. Jenkins builds Docker image, pushes to ECR
3. Jenkins updates the Kubernetes manifest in the GitOps repo (new image tag)
4. ArgoCD detects the Git change
5. ArgoCD automatically syncs the cluster to match Git state
6. New pods roll out with the new image

**Why GitOps?**
- Git is the single source of truth
- Every change is auditable (Git history)
- Self-healing: if someone manually changes a pod, ArgoCD reverts it
- Easy rollback = just revert the Git commit

---

## 📋 Quick Reference Cheat Sheet

| Topic | Key Command |
|---|---|
| Check pods | `kubectl get pods -A` |
| Describe pod | `kubectl describe pod <name>` |
| Check logs | `kubectl logs <pod> --previous` |
| Rollback | `kubectl rollout undo deployment/<name>` |
| Terraform preview | `terraform plan` |
| Terraform apply | `terraform apply -auto-approve` |
| Ansible ping all | `ansible all -m ping -i inventory.ini` |
| ArgoCD sync | `argocd app sync <app-name>` |
| AKS upgrade | `az aks upgrade --kubernetes-version X.X.X` |

---

## 💡 Interview Tips

1. **Always connect answers to your real experience** — mention Accenture or L&T projects
2. **Use the STAR format** for behavioral questions (Situation, Task, Action, Result)
3. **Mention your 83% deployment time reduction** — it's impressive and concrete
4. **Know your numbers:** 3+ hours → 10 minutes for infra provisioning with Terraform
5. **If unsure, be honest** — say "I haven't done this specifically, but I would approach it by..."
6. **Ask clarifying questions** — shows seniority and thoughtfulness

---

*Prepared based on resume of Chilukuri Rajesh — Senior DevOps Engineer*
