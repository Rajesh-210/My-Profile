cat > /mnt/user-data/outputs/interview_preparation.md << 'ENDOFFILE'
# 🎯 Interview Preparation Guide — Chilukuri Rajesh
### Senior DevOps Engineer | AWS | Kubernetes | Terraform | CI/CD

---

## 👋 Introduction — "Tell Me About Yourself"

*How to say it in the interview:*

> "Hi, I'm Rajesh. I'm a DevOps Engineer with around 4 years of experience. I've worked at Accenture and L&T Technology Services, where I built CI/CD pipelines, managed cloud infrastructure on AWS, and handled Kubernetes deployments.
> 
>My biggest achievement was reducing deployment time by 83% — from 30 minutes to under 5 minutes — using Jenkins, Docker, Kubernetes, and ArgoCD.
> 
>I've also automated AWS infrastructure using Terraform, cutting environment setup from 3+ hours to under 10 minutes.
> 
>I handle everything from writing Terraform code, managing Kubernetes clusters, setting up monitoring with Prometheus and Grafana, to helping developers with deployment issues.
> 
>I'm looking for a role where I can continue growing in cloud-native and DevOps engineering."

---

## ☁️ AWS Questions

---

### 1. Why Do We Use RDS Multi-AZ?

*What it means (simple):*
Multi-AZ means your database runs in two data centers at the same time. If one goes down, the other takes over automatically.

*How to say it in the interview:*

> "RDS Multi-AZ is used for high availability. AWS keeps a standby copy of your database in a different Availability Zone. If the primary database fails — due to hardware issue, network problem, or even during maintenance — AWS automatically switches to the standby. The failover happens in 1 to 2 minutes without any manual action.
> 
>The important thing is that the standby is not used for reading queries — it's only there as a backup. Data is always in sync between primary and standby.
> 
>In my project, we enabled Multi-AZ on production RDS so that even during AWS maintenance windows, our application had zero downtime because the failover happened automatically."

---

### 2. Can We Add Tables and Rows in RDS?

*What it means (simple):*
Yes. RDS is just a managed database. AWS handles the server — you handle the data.

*How to say it in the interview:*

> "Yes, absolutely. RDS is a managed database service, which means AWS takes care of the underlying server — things like OS patching, backups, storage, and high availability. But the database itself works exactly like a normal MySQL or PostgreSQL database.
> 
>You connect to the RDS endpoint using any SQL client, and then you can create tables, insert rows, run queries — everything works the same way.
> 
>The only difference from a self-managed database is that you don't have access to the underlying OS. But from a database operations perspective, it's completely normal SQL."

---

### 3. Types of S3 Storage Classes

*What it means (simple):*
S3 has different tiers based on how often you access the data. More access = higher cost. Rare access = lower cost.

*How to say it in the interview:*

> "S3 has multiple storage classes, and you choose based on how frequently you access the data.
> 
>S3 Standard is for data you access frequently — like application files or active logs. It's the most expensive but has instant access.
> 
>S3 Standard-IA, which stands for Infrequent Access, is for data you access maybe once a month. Storage cost is lower but you pay a small fee when you retrieve it.
> 
>S3 Glacier is for long-term archival — like compliance records you keep for years but rarely open. It's very cheap but retrieval can take minutes to hours.
> 
>S3 Intelligent-Tiering is useful when you don't know the access pattern — AWS automatically moves data between tiers based on usage.
> 
>In my projects, I used S3 Standard for Terraform state files. I also set up lifecycle policies to automatically move old application logs to Standard-IA after 30 days to save costs."

---

### 4. What is IAM and How is it Used?

*What it means (simple):*
IAM controls who can access what in your AWS account. Think of it as the security guard.

*How to say it in the interview:*

> "IAM stands for Identity and Access Management. It's basically the security layer for your entire AWS account — it controls who can access which AWS services and what actions they can perform.
> 
>IAM has three main things: Users, which are individual people or bots. Groups, which are collections of users with shared permissions. And Roles, which are permissions assigned to AWS services — for example, giving an EC2 instance permission to read from S3.
> 
>Permissions are defined through Policies — JSON documents that say 'allow this action on this resource' or 'deny this action.'
> 
>In my work, I created IAM roles for EC2 instances so they could access S3 and DynamoDB without storing any access keys in the code. I also created IAM roles with least-privilege access for Jenkins, so it could only push images to ECR and nothing else. This follows the security principle of giving minimum required permissions."

---

### 5. Can We Restrict S3 Access Using IAM?

*What it means (simple):*
Yes. You can lock down an S3 bucket so only specific users or services can access it.

*How to say it in the interview:*

> "Yes, you can restrict S3 access using IAM in two ways.
> 
>First, through IAM policies — you attach a policy to a user or role that either allows or denies access to a specific S3 bucket.
> 
>Second, through S3 Bucket Policies — these are policies you attach directly to the bucket itself, controlling who can access it from outside.
> 
>For example, you can write a bucket policy that says 'deny all access except from this specific IAM role.' You can also enable Block Public Access at the bucket level to prevent accidental public exposure.
> 
>In my project, the S3 bucket storing Terraform state was restricted so only the Jenkins IAM role could read and write to it. No other user or service had access. I used both an IAM policy and a bucket policy together for stronger security."

---

## 🐳 Docker Questions

---

### 6. Difference Between COPY and ADD in Docker

*What it means (simple):*
Both put files into a Docker image. COPY is simple and safe. ADD does extra things like extracting zip files.

*How to say it in the interview:*

> "Both COPY and ADD are used to copy files from your local machine into the Docker image during build time. But there are key differences.
> 
>COPY is straightforward — it just copies files or folders as-is. That's it. It's simple and predictable.
> 
>ADD does the same thing but has two extra features. One, if you give it a tar file, it automatically extracts it. Two, it can also download files from a URL directly into the image.
> 
>The best practice is to always use COPY unless you specifically need those extra features of ADD. The reason is that COPY is more transparent — when someone reads your Dockerfile, they know exactly what's happening. ADD can cause confusion because people might not expect automatic extraction.
> 
>In my Dockerfiles, I always use COPY for copying application code and config files. I only use ADD if I need to extract a compressed archive."

---

## 🏗️ Terraform Questions

---

### 7. What Module Would You Change in Terraform to Modify AWS Cloud?

*What it means (simple):*
In Terraform, each module manages a specific AWS resource. To change something, you find the right module and update it.

*How to say it in the interview:*

> "In Terraform, we organize code into modules — each module is responsible for a specific AWS resource or group of resources.
>> terraform/
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
> 
>So the answer depends on what I want to modify. If I want to change the VPC settings — like adding a subnet or changing a CIDR block — I go to the VPC module. If I want to change EC2 instance type, I go to the EC2 module. For IAM roles and permissions, I go to the IAM module.
> 
>For example, in my project I had separate modules for VPC, EC2, EKS, IAM, and S3. When we needed to increase the number of Kubernetes worker nodes, I updated the desired_size variable in the EKS module, ran terraform plan to preview the changes, and then applied it through our Jenkins pipeline.
> 
>The key point is that you never touch the underlying AWS console directly — all changes go through Terraform so everything is tracked in code and version-controlled in Git."

---

### 8. Resources Provisioned by Terraform in My Project

*How to say it in the interview:*

> "In my projects, I used Terraform to provision complete AWS environments from scratch.
> 
>The main resources were: VPC with public and private subnets across multiple availability zones. Internet Gateway for public subnet access. NAT Gateway so private subnets could reach the internet. Route Tables for directing traffic. Security Groups acting as firewalls for EC2 and EKS. EC2 instances for application servers. IAM roles and policies for service permissions. S3 bucket for storing Terraform remote state. DynamoDB table for state locking so two people can't apply at the same time. And finally the EKS cluster for running Kubernetes workloads.
> 
>The whole environment that used to take 3+ hours to set up manually was reduced to under 10 minutes with Terraform."

---

### 9. What is Terraform Lint (tflint)?

*What it means (simple):*
tflint checks your Terraform code for mistakes and bad practices before you run it — like a spell-checker for infrastructure code.

*How to say it in the interview:*

> "Terraform lint, or tflint, is a static analysis tool for Terraform code. It scans your .tf files before you run terraform plan or apply, and catches issues early.
> 
>For example, it will warn you if you're using a deprecated resource argument, if you've made a typo in a resource type name, if you're using an invalid AWS instance type that doesn't exist, or if you're missing required variables.
> 
>It's like a code quality check that runs before the actual infrastructure changes. We integrated tflint into our Jenkins pipeline so every Terraform change gets linted automatically before anyone can apply it. This saved us from pushing broken infrastructure code to production."

---

### 10. Explain the Terraform Update Workflow

*What it means (simple):*
There's a standard set of steps you follow every time you make a Terraform change.

*How to say it in the interview:*

> "Whenever we need to make a change to infrastructure using Terraform, we follow a standard workflow.
> 
>First, terraform init — this initializes the working directory, downloads the required providers and modules.
> 
>Second, terraform validate — this checks if the syntax of the Terraform files is correct.
> 
>Third, tflint — this lints the code for best practices and catches issues before planning.
> 
>Fourth, terraform plan — this is the most important step. It shows you exactly what will be created, modified, or destroyed before making any actual changes. We always review this output carefully.
> 
>Fifth, terraform apply — this actually applies the changes to AWS.
> 
>In my Jenkins pipeline, the plan step ran automatically on every pull request so the team could review infrastructure changes before approval. The apply step required a manual approval in Jenkins before it ran in production. This gave us full control and audit trail for every infrastructure change."

---

## ☸️ Kubernetes Questions

---

### 11. Kubernetes Architecture

*What it means (simple):*
Kubernetes has a Master (brain) that controls everything, and Worker Nodes (hands) that actually run the applications.

*How to say it in the interview:*

> "Kubernetes has two main parts — the Control Plane, which is the brain, and the Worker Nodes, which actually run the applications.
> 
>In the Control Plane, we have four key components. The API Server is the entry point — every kubectl command you run goes through it. etcd is like the database of Kubernetes — it stores the entire cluster state. The Scheduler decides which worker node a new pod should run on based on available resources. The Controller Manager watches the cluster and makes sure everything matches the desired state — for example, if a pod dies, it creates a new one.
> 
>On each Worker Node, we have three components. The kubelet is the agent that runs on every node — it receives instructions from the control plane and makes sure containers are running. kube-proxy handles the networking — it routes traffic to the correct pods. And the Container Runtime, like Docker or containerd, actually runs the containers.
> 
>In simple terms — you tell Kubernetes what you want through kubectl, the Control Plane figures out how to make it happen, and the Worker Nodes do the actual work."

---

### 12. Types of Services in Kubernetes

*What it means (simple):*
Services expose your pods to traffic. Different types serve different purposes — internal, external, or cloud load balancers.

*How to say it in the interview:*

> "Kubernetes has four types of services.
> 
>ClusterIP is the default. It gives a pod a stable internal IP address that only other pods inside the cluster can reach. We use this for internal communication — like a backend service talking to a database.
> 
>NodePort exposes the service on a specific port on every node in the cluster. It's accessible from outside the cluster using the node's IP and port. We mostly use this for testing, not production.
> 
>LoadBalancer is what we use in production on AWS. It automatically creates an AWS Elastic Load Balancer and routes external internet traffic to the pods. This is what makes your application reachable from the internet.
> 
>ExternalName is a special type that maps a Kubernetes service to an external DNS name — useful when your app inside the cluster needs to talk to an external service.
> 
>In my EKS projects, I used ClusterIP for internal services like Prometheus, and LoadBalancer type for frontend services that needed to be accessed by end users."

---

### 13. Kubernetes Headless Service

*What it means (simple):*
A normal service gives you one IP and load balances behind it. A headless service gives you the actual IP of each individual pod directly.

*How to say it in the interview:*

> "A headless service is a special kind of service where you set clusterIP to None. Instead of giving a single load-balanced IP, it returns the individual IP addresses of each pod directly through DNS.
> 
>The reason you'd use this is when you need to connect to a specific pod directly — not just any pod in the group. The most common use case is databases like MySQL, Cassandra, or MongoDB running as StatefulSets, where each pod has a unique identity and you might need to talk to the primary node specifically.
> 
>With a normal service, if you have 3 database pods, the service randomly picks one. With a headless service, you can target pod-0, pod-1, or pod-2 by name.
> 
>So in simple terms — use a normal service when any pod can serve the request, use a headless service when you need to reach a specific pod."

---

### 14. Pod-to-Pod Communication

*What it means (simple):*
Every pod gets its own IP. Pods can talk to each other directly using these IPs, but using Services is the better approach.

*How to say it in the interview:*

> "In Kubernetes, every pod gets its own unique IP address, and pods can communicate with each other directly using those IPs — there's no NAT in between.
> 
>If two pods are on the same node, they communicate through a virtual network bridge on that node. If they're on different nodes, the traffic goes through the CNI plugin — we used Calico in our setup — which handles routing between nodes.
> 
>However, in practice we don't use pod IPs directly because pods are temporary. When a pod restarts, it gets a new IP. So instead, we use Services. A Service provides a stable DNS name and IP that always points to the healthy pods behind it, even if the pods restart and get new IPs.
> 
>So the best practice is: pods talk to each other through Services, not directly through pod IPs."

---

### 15. Difference Between ReplicaSet and Deployment

*What it means (simple):*
ReplicaSet just keeps pods running. Deployment does everything ReplicaSet does, plus gives you rolling updates and rollback.

*How to say it in the interview:*

> "A ReplicaSet's only job is to make sure a certain number of identical pods are always running. If you say 3 replicas, it will always maintain 3 pods. If one goes down, it creates a new one.
> 
>A Deployment does the same thing but adds more features on top. It manages ReplicaSets under the hood and gives you rolling updates — meaning when you deploy a new version, it gradually replaces old pods with new ones, so there's no downtime. It also gives you rollback — if something goes wrong, you can go back to the previous version with one command.
> 
>In practice, you never create a ReplicaSet directly. You always create a Deployment, and Kubernetes automatically creates and manages the ReplicaSet for you.
> 
>Think of it this way — ReplicaSet is the engine, Deployment is the car. You always drive the car, not the engine directly."

---

### 16. One Pod Goes Down — How Do You Resolve It?

*What it means (simple):*
First Kubernetes tries to fix it automatically. If it can't, you investigate the logs and fix the root cause.

*How to say it in the interview:*

> "The first thing to know is that if a pod goes down and there's a Deployment managing it, Kubernetes will automatically try to restart it. So in many cases, it self-heals.
> 
>But if it keeps restarting and going into CrashLoopBackOff, that's when I step in.
> 
>My first step is to check the pod status with kubectl get pods to see what state it's in. Then I run kubectl describe pod with the pod name to see the events — it usually tells me why it failed, like an image pull error or a resource issue.
> 
>Then I check the logs with kubectl logs and also kubectl logs with the --previous flag to see logs from the crashed container before it restarted.

> Step 1: Check pod status
# kubectl get pods

> Step 2: Describe the pod to see events
# kubectl describe pod <pod-name>

> Step 3: Check the logs of the crashed container
# kubectl logs <pod-name> --previous

># Common causes:
  - Application error at startup
  - Wrong environment variable or missing config
  - Image pull error
  - Resource limits too low (OOMKilled)

>Based on what I find — if the app is crashing, I fix the application code and redeploy. If it's out of memory, I increase the memory limits in the pod spec. If it's an image pull error, I fix the image tag or check ECR permissions. If the node doesn't have enough resources, I scale the node group.
> 
>In my project, I also had Prometheus and Grafana alerting set up, so I would get notified about pod failures before users even noticed."

---

### 17. How to Roll Back to the Previous Deployment

*What it means (simple):*
Kubernetes keeps history of old deployments. One command takes you back to the previous version.

*How to say it in the interview:*

> "Kubernetes keeps a history of all deployment versions through the ReplicaSets it manages. Rolling back is very straightforward.
> 
>I run kubectl rollout undo deployment followed by the deployment name. This immediately switches back to the previous version. If I need to go to a specific older version, I first check the history with kubectl rollout history, find the revision number I want, and then run kubectl rollout undo with the --to-revision flag and the version number.
> 
>I can then check the progress with kubectl rollout status to confirm the rollback completed successfully.
> 
>In my setup with ArgoCD, rollback was even simpler — since Git is the source of truth, I would just do a git revert on the commit that caused the issue and push it. ArgoCD would detect the change and automatically roll back the cluster to match. This also gave us a complete audit trail of what changed and who reverted it."

---

### 18. How to Patch Multiple VMs

*What it means (simple):*
Use Ansible to run patching commands across many servers at once with a single command.

*How to say it in the interview:*

> "To patch multiple VMs at the same time, I use Ansible. It's perfect for this because you can define all your servers in an inventory file and run a single playbook across all of them simultaneously.
> 
>I create an inventory file listing all the VMs grouped by environment — like web servers, database servers, production, staging. Then I write an Ansible playbook that updates the package cache and applies all available patches. I can also add a task to reboot the servers if a kernel update requires it.
> 
>To run it, I simply execute ansible-playbook with the inventory file and playbook name. Ansible connects to all servers in parallel via SSH and applies the patches.
> 
>In my project, I ran these patching playbooks from Jenkins pipelines. We had separate runs for dev, staging, and production to avoid patching everything at once. For AWS environments, you can also use AWS Systems Manager Patch Manager, which lets you define patch baselines and maintenance windows, and AWS automatically patches all tagged EC2 instances."

---

## ☁️ Azure Questions

---

### 19. What is Azure Policy and Azure RBAC?

"Azure RBAC and Azure Policy are two different but complementary security features in Azure.

>Azure RBAC stands for Role-Based Access Control. It controls who can do what on Azure resources. For example, in my projects I assign the Contributor role to developers so they can create and manage resources, but not manage permissions. Junior team members get the Reader role so they can only view resources without making any changes. The Owner role is reserved for leads or admins who need full control.

>Azure Policy is different — it enforces compliance rules across the entire Azure environment. For example, I've used policies to ensure all resources must have specific tags for cost tracking, virtual machines can only be created in certain regions, and storage accounts must always have encryption enabled. If someone tries to create a resource that violates the policy, Azure either blocks it automatically or flags it as non-compliant.

>The key difference I always mention is — RBAC controls access, meaning who can perform actions. Azure Policy controls compliance, meaning what kind of resources are allowed to exist.

>A simple way to think about it — RBAC is the lock on the door, Azure Policy is the building code that says what you're allowed to build inside."
---

### 20. How Do You Update the AKS Master Version?

*What it means (simple):*
You use the Azure CLI to trigger a Kubernetes version upgrade. Always upgrade the control plane first, then the nodes.

*How to say it in the interview:*

> "Upgrading the AKS master version is done through the Azure CLI. The process has two steps — first upgrade the control plane, then upgrade the node pools.
> 
>Before upgrading, I run az aks get-upgrades to check which versions are available for the cluster. Then I upgrade the control plane first using az aks upgrade with the --control-plane-only flag and specifying the new Kubernetes version. After the control plane upgrade is complete, I upgrade each node pool separately using az aks nodepool upgrade.

># Step 1: Check what versions are available
az aks get-upgrades --name myCluster --resource-group myRG

># Step 2: Upgrade control plane FIRST
az aks upgrade --name myCluster --resource-group myRG \
  --kubernetes-version 1.28 --control-plane-only

># Step 3: Then upgrade worker nodes
az aks nodepool upgrade --cluster-name myCluster \
  --resource-group myRG --name nodepool1 --kubernetes-version 1.28
> 
>A few important rules to follow: only upgrade one minor version at a time — you can't jump from 1.27 to 1.29 directly. Always test in a staging cluster before touching production. And always check if any of your workloads use deprecated Kubernetes APIs that might break after the upgrade.
> 
>The reason we separate control plane and node pool upgrades is so we can verify everything looks good after the control plane upgrade before rolling it out to the nodes."

---

### 21. Have We Downgraded an AKS Cluster?

*How to say it in the interview:*

> "No, and actually AKS does not support downgrading Kubernetes versions. Once you upgrade to a newer version, you cannot roll back to the older version on the same cluster.
> 
>This is exactly why we follow a strict upgrade process — we always test in a staging environment first. If the upgrade causes issues in staging, we fix them before touching production.
> 
>If a production upgrade ever went wrong and we needed to go back, the recovery path would be to create a new AKS cluster with the previous version and restore workloads from Velero backups. But in my experience, we never had to do this because of our thorough pre-upgrade testing process.
> 
>Some teams also use a Blue-Green cluster strategy for major upgrades — they spin up a completely new cluster with the new version, migrate traffic gradually, and only decommission the old cluster once everything is stable."

---

## 🔄 CI/CD & GitOps Questions

---

### 22. Explain ArgoCD GitOps Workflow

*How to say it in the interview:*

> "In our GitOps setup with ArgoCD, the entire deployment process is driven by Git.
> 
>The flow works like this: A developer pushes code to GitHub. Jenkins picks it up via a webhook, runs the tests, builds a Docker image, and pushes it to Amazon ECR. Jenkins then updates the Kubernetes manifest in a separate GitOps repository with the new image tag and commits that change.
> 
>ArgoCD is always watching the GitOps repository. As soon as it detects the new commit, it automatically syncs the Kubernetes cluster to match the new state — deploying the updated pods.
> 
>The big advantage of this approach is that Git becomes the single source of truth. Every deployment is a Git commit, so you have a full audit trail. If something breaks, rollback is just a git revert. And ArgoCD is self-healing — if someone manually changes something in the cluster, ArgoCD automatically reverts it to match what's in Git.
> 
>This is how we achieved near-zero human error in deployments in my project."

---

## 📋 Quick Reference — Key Commands

| What you need | Command |
|---|---|
| Check all pods | kubectl get pods -A |
| See why pod failed | kubectl describe pod <name> |
| Check pod logs | kubectl logs <pod-name> |
| Check crashed pod logs | kubectl logs <pod-name> --previous |
| Rollback deployment | kubectl rollout undo deployment/<name> |
| Check rollout history | kubectl rollout history deployment/<name> |
| Terraform preview | terraform plan |
| Terraform apply | terraform apply |
| Ansible patch all VMs | ansible-playbook -i inventory.ini patch.yml |
| AKS available upgrades | az aks get-upgrades --name <cluster> --resource-group <rg> |
| AKS upgrade | az aks upgrade --kubernetes-version X.X.X |

---

## 💡 Interview Tips

1. *Always end answers with your real experience* — "In my project at Accenture / L&T..."
2. *Mention your numbers* — 83% deployment time reduction, 3+ hours → 10 minutes with Terraform
3. *Keep answers to 1-2 minutes* — don't over-explain, let them ask follow-ups
4. *If you don't know something, say: *"I haven't worked on that specifically, but based on my experience I would approach it by..."
5. *For architecture questions*, draw it out — even verbally walk through components one by one
6. *Stay calm on tricky questions* — interviewers often want to see how you think, not just the perfect answer

---

Prepared based on resume of Chilukuri Rajesh — Senior DevOps Engineer
ENDOFFILE
