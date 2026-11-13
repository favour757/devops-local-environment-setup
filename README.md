# DevOps Engineer Local Environment Setup

## 1. Project Overview & Architecture

This repository contains the complete specification, automated provisioning scripts, shell configuration profiles, and verification audits for a **production-grade DevOps local development workstation**. 

The environment is designed to eliminate the *"works on my machine"* syndrome by leveraging reproducible package managers, containerized workflows, infrastructure as code (IaC), Kubernetes orchestration, and AI-assisted engineering practices.

```
+---------------------------------------------------------------------------------------+
|                              HOST OPERATING SYSTEM                                    |
|                       (Windows 11 / macOS / Ubuntu Linux)                             |
+---------------------------------------------------------------------------------------+
|  Package Managers: Winget (Win) | Homebrew (macOS) | APT (Linux) | Dev Containers     |
+-------------------------------------------+-------------------------------------------+
|             DEV & RUNTIME TOOLS           |              CLOUD & IAC                  |
|  - Git 2.45+ (VCS & Hooks)                |  - Terraform (IaC engine)                 |
|  - VS Code & Dev Containers               |  - Ansible (Config Management via WSL2)   |
|  - Node.js LTS & npm                      |  - AWS CLI v2 & Azure CLI                 |
|  - jq (JSON parsing)                      |  - AI Assistants: GitHub Copilot / Q      |
+-------------------------------------------+-------------------------------------------+
|                           CONTAINERS & KUBERNETES LAYER                               |
|  - Docker Engine & Docker Compose (v2)                                                |
|  - Minikube (Local Single-Node Cluster via Docker Driver)                             |
|  - kubectl (Cluster Interactivity) & Helm (Package Management)                        |
+---------------------------------------------------------------------------------------+
```

---

## 2. System Requirements & Allocation

| Resource | Minimum Requirement | Recommended (DevOps Workstation) | Purpose |
| :--- | :--- | :--- | :--- |
| **CPU** | 2 Physical Cores (x86_64 / ARM64) | 4+ Cores (VT-x / AMD-V enabled) | Minikube VM/container virtualization |
| **RAM** | 4.0 GB | 16.0 GB (Allocate 4GB to Minikube, 4GB to Docker) | Docker daemon, local etcd, apiserver, and IDE |
| **Disk** | 10.0 GB free space | 50+ GB SSD free space | Base container images, tool binaries, cache |
| **OS** | Windows 10/11 (WSL2), Ubuntu 22.04+, macOS 13+ | Windows 11 with WSL2 Ubuntu 24.04 or Linux | Native POSIX compliance for Ansible & K8s |

---

## 3. Package Manager Rationale

| Platform | Primary Package Manager | Rationale |
| :--- | :--- | :--- |
| **Windows Host** | `winget` (Windows Package Manager) | Microsoft's official, cryptographically verified CLI repository. Automates silent installs of Docker Desktop, VS Code, Minikube, and cloud CLIs without manual web downloads. |
| **Linux / WSL2** | `apt` (Advanced Package Tool) | Standard Debian/Ubuntu package manager. Backed by official upstream vendor repositories (HashiCorp, Docker, Kubernetes) for reliable updates. |
| **macOS Host** | `brew` (Homebrew) | Defacto standard package manager across Unix environments on Darwin. |
| **Portable / Team** | `Dev Containers` (`.devcontainer.json`) | Encapsulates the entire toolchain into an OCI container image. Eliminates host configuration variance across mixed developer OS fleets. |

---

## 4. Step-by-Step Task Implementation

### Task 1: Prepare Host Environment
- **Hardware Audit:** Verify RAM with `free -m` (Linux) or `Get-CimInstance Win32_OperatingSystem` (Windows).
- **Virtualization:** Ensure CPU virtualization (VT-x / AMD-V) is enabled in BIOS/UEFI.
- **Subsystem:** On Windows, provision WSL2 with:
  ```powershell
  wsl --install -d Ubuntu-24.04
  wsl --set-default-version 2
  ```

### Task 2: Establish Version Control & Editor
- **Git Installation & Baseline Configuration:**
  ```bash
  git config --global user.name "DevOps Engineer"
  git config --global user.email "devops@enterprise.internal"
  git config --global init.defaultBranch main
  git config --global core.editor "code --wait"
  git config --global pull.rebase true
  ```
- **Visual Studio Code:** Install VS Code with essential extensions:
  - `Docker` (`ms-azuretools.vscode-docker`)
  - `Kubernetes` (`ms-kubernetes-tools.vscode-kubernetes-tools`)
  - `HashiCorp Terraform` (`HashiCorp.terraform`)
  - `Ansible` (`redhat.ansible`)

### Task 3: Integrate AI Capabilities
- **GitHub Copilot CLI:** Provides AI terminal generation for complex shell pipes and commands:
  ```bash
  gh extension install github/gh-copilot
  gh copilot suggest "find large files older than 30 days"
  ```
- **IDE AI Assistance:** Enable GitHub Copilot or Amazon Q extensions in VS Code for automated IaC scaffolding and playbook generation.

### Task 4: Containerization (Docker & Compose)
- **Engine Setup:** Install Docker Engine and Docker Compose v2.
- **Permissions:** Prevent running Docker with `sudo` by adding the user to the `docker` security group:
  ```bash
  sudo usermod -aG docker $USER
  newgrp docker
  ```
- **Verification:** Run `docker run --rm hello-world` to confirm daemon responsiveness.

### Task 5: Kubernetes Tooling & Local Cluster
- **CLI Tools:** Install `kubectl` and `helm`.
- **Minikube Initialization:** Start cluster using the lightweight `docker` driver:
  ```bash
  minikube start --driver=docker --cpus=2 --memory=4096mb
  ```
- **Verification:** Confirm nodes and pods:
  ```bash
  kubectl get nodes -o wide
  kubectl get pods -A
  ```

### Task 6: Cloud CLIs & Language Runtimes
- **AWS CLI v2 & Azure CLI:**
  - AWS: Install official v2 bundle; verify with `aws --version`.
  - Azure: Install via Microsoft repository; verify with `az version`.
- **Node.js & jq:**
  - Install Node.js LTS (v20+) for tooling and CI/CD scripts.
  - Install `jq` for parsing JSON payloads from cloud CLIs and Kubernetes manifests.

### Task 7: Infrastructure as Code & Configuration
- **HashiCorp Terraform:** Install Terraform CLI; configure local plugin caching in `.bashrc`:
  ```bash
  export TF_PLUGIN_CACHE_DIR="$HOME/.terraform.d/plugin-cache"
  ```
- **Ansible:** Install Ansible core via `apt` or `pipx`. *(Note: Ansible control nodes require a POSIX environment; on Windows, run inside WSL2 or Dev Containers).*

### Task 8: Verification & Security
- Run the automated test suite:
  ```bash
  ./scripts/verify-tools.sh
  ```
- On Windows:
  ```powershell
  .\scripts\verify-tools.ps1
  ```

---

## 5. Troubleshooting & Remediation Guide

| Issue Encountered | Root Cause | Remediation Step |
| :--- | :--- | :--- |
| **`docker: Got permission denied while trying to connect to the Docker daemon socket`** | Current user lacks membership in the `docker` Unix group. | Run `sudo usermod -aG docker $USER` and reload shell session with `newgrp docker`. |
| **`minikube start fails with driver error`** | Docker service is not running or nested virtualization is disabled. | Start Docker (`sudo systemctl start docker` or launch Docker Desktop) and pass `--driver=docker` explicitly. |
| **`ansible: command not found` on Windows** | Ansible does not support native Windows as a control node. | Provision WSL2 (`wsl --install`) and execute Ansible playbooks inside the Ubuntu distribution. |
| **`wsl: Virtual machine could not be started`** | Hyper-V or Hardware Virtualization is disabled in host BIOS. | Enable `Intel VT-x` or `AMD SVM` in host BIOS, and enable Windows Hyper-V platform via `dism.exe`. |
| **Terraform provider download timeouts** | High latency or rate-limiting against Terraform Registry. | Configure `TF_PLUGIN_CACHE_DIR="$HOME/.terraform.d/plugin-cache"` in `.bashrc` to reuse cached providers. |

---

## 6. Deliverable Directory Structure

```
devops-environment-setup/
├── .devcontainer/
│   └── devcontainer.json                   # Submission 1: Complete Dev Container definition
├── configs/
│   ├── .bashrc                             # Submission 1: DevOps Linux/WSL2 profile
│   ├── .zshrc                              # Submission 1: DevOps Zsh profile
│   └── Microsoft.PowerShell_profile.ps1    # Submission 1: DevOps Windows profile
├── scripts/
│   ├── setup-devops-env.sh                 # Linux/WSL2 automated setup script
│   ├── setup-devops-env.ps1                # Windows Winget automated setup script
│   ├── verify-tools.sh                     # POSIX tooling verification script
│   └── verify-tools.ps1                    # PowerShell tooling verification script
├── reports/
│   ├── tooling-verification-report.txt     # Submission 2: Full CLI version output report
│   └── local-cluster-proof.txt             # Submission 3: Running Minikube cluster proof
└── README.md                               # Submission 4: Setup logic & documentation
```
