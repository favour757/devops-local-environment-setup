# DevOps Engineer Local Environment Setup

## 1. Project Overview & Architecture

This repository contains the complete specification, automated provisioning scripts, shell configuration profiles, visual screenshots, and verification audits for a **production-grade DevOps local development workstation**. 

The environment is designed to eliminate the *"works on my machine"* syndrome by leveraging reproducible package managers, containerized workflows, infrastructure as code (IaC), Kubernetes orchestration, and AI-assisted engineering practices.

```
+---------------------------------------------------------------------------------------+
|                              HOST OPERATING SYSTEM                                    |
|                       (Windows 11 with WSL2 Ubuntu Linux)                             |
+---------------------------------------------------------------------------------------+
|  Package Managers: APT (Linux) | Official Vendor Upstream Repos | Winget (Windows)    |
+-------------------------------------------+-------------------------------------------+
|             DEV & RUNTIME TOOLS           |              CLOUD & IAC                  |
|  - Git 2.45+ (VCS & Identity Config)      |  - Terraform v1.17 (IaC engine)           |
|  - VS Code & VS Code Server Integration   |  - Ansible Core v2.20 (Configuration Mgmt)|
|  - Node.js LTS v22 & npm 9.2              |  - AWS CLI v2.40 & Azure CLI v2.92        |
|  - jq v1.8 (JSON processing)              |  - GitHub CLI (gh v2.46 Authenticated)    |
+-------------------------------------------+-------------------------------------------+
|                           CONTAINERS & KUBERNETES LAYER                               |
|  - Docker Engine v29.9 & Docker Compose v5.7 (non-root execution via docker group)    |
|  - Minikube v1.39 (Single-Node Local Cluster via Docker Driver)                       |
|  - kubectl v1.37 (Cluster Controller) & Helm v3.22 (Package Distribution)             |
+---------------------------------------------------------------------------------------+
```

---

## 2. Visual Proof & Verification Screenshots

All visual evidence required by the evaluation rubric is cataloged in the [`screenshots/`](./screenshots/) directory:

| Screenshot Description | Preview | File Link |
| :--- | :--- | :--- |
| **Local Tools (Git & VS Code)** | Git identity configuration, user credentials, and VS Code CLI integration. | [`screenshots/01-git-and-vscode.png`](./screenshots/01-git-and-vscode.png) |
| **Container Engine Verification** | Docker daemon running with unprivileged group permissions and successful `hello-world` execution. | [`screenshots/02-docker-hello-world.png`](./screenshots/02-docker-hello-world.png) |
| **Local Kubernetes Cluster (Ready)** | Running local Kubernetes control plane via Minikube showing `STATUS: Ready`. | [`screenshots/03-kubernetes-minikube-ready.png`](./screenshots/03-kubernetes-minikube-ready.png) |
| **Complete CLI Version Audit** | Full terminal audit showing `git`, `docker`, `kubectl`, `minikube`, `helm`, `aws`, `az`, `node`, `npm`, `jq`, `terraform`, and `ansible`. | [`screenshots/04-cli-tools-verification.png`](./screenshots/04-cli-tools-verification.png) |
| **GitHub Account Authentication** | Verified GitHub cloud account login (`favour757`) and device authorization. | [`screenshots/05-github-account-login.jpg`](./screenshots/05-github-account-login.jpg) |
| **Published GitHub Repository** | Complete project codebase and deliverables published live on GitHub. | [`screenshots/06-github-repository-live.jpg`](./screenshots/06-github-repository-live.jpg) |

---

## 3. System Requirements & Resource Allocation

| Resource | Minimum Requirement | Workstation Provisioned | Purpose |
| :--- | :--- | :--- | :--- |
| **CPU** | 2 Physical Cores (x86_64 / ARM64) | 4+ Virtual Cores (VT-x Enabled) | Minikube VM/container virtualization |
| **RAM** | 4.0 GB | 7.8 GB Allocated (WSL2) | Docker daemon, local etcd, apiserver, and runtimes |
| **Disk** | 10.0 GB free space | 919 GB Available SSD space | Container layers, tool binaries, cache |
| **OS** | Windows 10/11 (WSL2), Linux, macOS | Windows 11 with WSL2 Ubuntu | POSIX compliance for Ansible & K8s |

---

## 4. Package Manager & Tooling Rationale

- **Ubuntu `apt` (Advanced Package Tool):** Primary package manager for core OS dependencies (`curl`, `wget`, `unzip`, `jq`, `nodejs`, `npm`, `ansible`, `gh`).
- **Official Upstream Repositories:**
  - **Docker Engine:** Installed directly from `download.docker.com` with official GPG keying to ensure v29+ container runtime.
  - **HashiCorp Terraform:** Installed via HashiCorp's signed Debian repository for immediate access to current provider APIs.
  - **Kubernetes (`kubectl`, `minikube`, `helm`):** Installed from Google Cloud Storage and Kubernetes release binaries.
  - **AWS CLI v2 & Azure CLI:** Installed via vendor distribution packages (`awscli-exe-linux-x86_64.zip` and Microsoft Azure repository).

---

## 5. Troubleshooting & Remediation Log

1. **WSL2 OOBE Initialization Lock:**
   - *Problem:* Initial Ubuntu 24.04/26.04 setup hung waiting for a graphical Out-Of-Box Experience (OOBE) helper.
   - *Remediation:* Bypassed via explicit root shell invocation and direct user provisioning (`wsl --install -d Ubuntu`).
2. **Docker Daemon Group Permissions:**
   - *Problem:* Running Docker commands initially prompted for root/sudo access.
   - *Remediation:* Enforced security best practice by adding the user to the `docker` group (`sudo usermod -aG docker $USER`) and recycling the login shell (`exec su -l $USER`).
3. **Minikube Containerized Driver:**
   - *Problem:* Selecting virtual machine drivers under nested virtualization can create Hyper-V conflicts.
   - *Remediation:* Explicitly designated the container-based driver (`minikube start --driver=docker`), achieving full single-node cluster readiness within 2 minutes.
4. **Shell Productivity & Context Switching:**
   - *Problem:* Manual repetition of verbose CLI commands (`kubectl`, `docker compose`, `terraform`) slows development.
   - *Remediation:* Enhanced `~/.bashrc` with production aliases (`k`, `kgp`, `kgn`, `dps`, `dco`, `tf`, `mks`) and enabled bash autocompletions for `kubectl` and `helm`.

---

## 6. Directory Structure & Deliverables

```
devops-local-environment-setup/
├── configs/
│   └── .bashrc                             # Submission 1: DevOps shell profile & aliases
├── reports/
│   ├── tooling-verification-report.txt     # Submission 2: Raw CLI version audit output
│   └── local-cluster-proof.txt             # Submission 3: Running Minikube cluster proof
├── screenshots/
│   ├── 01-git-and-vscode.png               # Local tools proof (Git & VS Code)
│   ├── 02-docker-hello-world.png           # Container engine proof
│   ├── 03-kubernetes-minikube-ready.png    # Live Kubernetes cluster proof
│   ├── 04-cli-tools-verification.png       # All CLI tools version check
│   ├── 05-github-account-login.jpg         # Cloud account proof (GitHub favour757)
│   └── 06-github-repository-live.jpg       # Live published repository proof
└── README.md                               # Submission 4: Complete setup documentation
```
