# 🚀 AAP GitOps Config Management

![Ansible](https://img.shields.io/badge/Ansible-Automation-red)
![AAP](https://img.shields.io/badge/RedHat-AAP%202.6-black)
![GitOps](https://img.shields.io/badge/Model-GitOps-blue)
![License](https://img.shields.io/badge/License-MIT-green)

> Manage **Red Hat Ansible Automation Platform (AAP)** configuration as code using a GitOps approach — executed directly from **AAP 2.6 Job Templates**.

---

## 🌟 Overview

This repository provides a **GitOps-based framework** for managing AAP configuration declaratively using Ansible.

It enables platform teams to **define, version, and deploy AAP configuration through AAP 2.6 job templates**.

Supported resources:

* Organizations
* Teams
* Projects
* Job Templates
* Credentials

Across environments:

* `dev`
* `qa`
* `prod`

---

## 🧭 Architecture

```mermaid
flowchart LR
    A[Git Repository] --> B[Env Config dev qa prod]
    B --> C[AAP Project Sync]
    C --> D[AAP Job Template]
    D --> E[Execution Environment]

    E --> F[manage aap config role]

    F --> G[ansible platform]
    F --> H[ansible controller]

    G --> I[AAP Gateway]
    H --> J[Automation Controller]

    I --> K[Organizations and Teams]
    J --> L[Projects Job Templates Credentials]
```

---

## 🏗️ Repository Structure

```text
.
├── dev/
│   ├── playbooks/
│   │   └── manage-aap-config.yml
│   └── var_files/
│       ├── credential.yml
│       ├── job_templates.yml
│       ├── orgs.yml
│       ├── platform-info.yml
│       ├── projects.yml
│       └── teams.yml
├── qa/
├── prod/
└── roles/
    ├── manage-aap-config/
    └── delete-aap-config/
```

---

## ⚙️ How It Works

1. Configuration is stored in Git (this repository)
2. AAP project sync pulls the latest configuration
3. A Job Template executes the playbook
4. Execution Environment runs the roles
5. AAP resources are created/updated

### Collections Used (inside Execution Environment)

* `ansible.platform` → Organizations, Teams
* `ansible.controller` → Projects, Job Templates, Credentials

---

## 📦 Prerequisites

* Access to **AAP 2.6**
* AAP Project connected to this repository
* Execution Environment with:

  * `ansible.platform`
  * `ansible.controller`
* AAP credentials with sufficient permissions

> ⚠️ No manual installation of collections or CLI execution is required.

---

## 🔧 Configuration

Each environment contains:

```text
<env>/var_files/
```

### Key Files

| File                | Purpose                |
| ------------------- | ---------------------- |
| `platform-info.yml` | AAP connection details |
| `orgs.yml`          | Organizations          |
| `teams.yml`         | Teams                  |
| `projects.yml`      | Projects               |
| `job_templates.yml` | Job templates          |
| `credential.yml`    | Credentials            |

---

## 🧪 Example Configuration

### platform-info.yml

```yaml
aap_hostname: "https://aap.example.com"
aap_username: "admin"
aap_password: "{{ vault_aap_password }}"
validate_certs: false
```

### orgs.yml

```yaml
organizations:
  - name: Demo Org
    description: Demo organization for GitOps
    state: present
```

### teams.yml

```yaml
teams:
  - name: Platform Team
    organization: Demo Org
    description: Platform engineers
    state: present
```

### projects.yml

```yaml
projects:
  - name: Demo Project
    organization: Demo Org
    scm_type: git
    scm_url: https://github.com/example/demo-repo.git
    scm_branch: main
    state: present
```

### credential.yml

```yaml
credentials:
  - name: Demo Machine Credential
    organization: Demo Org
    credential_type: Machine
    inputs:
      username: ec2-user
      password: "{{ vault_machine_password }}"
    state: present
```

### job_templates.yml

```yaml
job_templates:
  - name: Demo Job Template
    project: Demo Project
    inventory: Demo Inventory
    playbook: site.yml
    credential: Demo Machine Credential
    state: present
```

---

## ▶️ Usage (AAP 2.6)

### Setup in AAP

1. Create a **Project** pointing to this repository:

```
https://github.com/taiseerhussein/import-config
```

2. Create a **Job Template**:

* Project: this repo
* Playbook:

  * `dev/playbooks/manage-aap-config.yml`
  * `qa/playbooks/manage-aap-config.yml`
  * `prod/playbooks/manage-aap-config.yml`
* Execution Environment: includes required collections
* Credentials: AAP admin or automation user

3. Launch the Job Template from AAP UI.

---

## 🔄 Resource Lifecycle

### Create / Update

```yaml
state: present
```

### Delete

```yaml
state: absent
```

Deletion order:

1. Job Templates
2. Projects
3. Teams
4. Organizations

---

## 🧪 Demo Workflow

```mermaid
sequenceDiagram
    participant User
    participant AAP
    participant Git
    participant EE as Execution Environment

    User->>AAP: Launch Job Template
    AAP->>Git: Sync Project
    AAP->>EE: Run playbook
    EE->>AAP: Apply configuration
    AAP-->>User: Resources updated
```

---

## ⚡ Quick Start (AAP)

1. Import repository as a Project
2. Create Job Template
3. Select environment playbook
4. Attach credentials
5. Launch

---

## 🔐 Security Best Practices

* Use **Ansible Vault** for secrets
* Do not store:

  * Passwords
  * Tokens
  * Private keys
* Use environment separation (`dev`, `qa`, `prod`)

---

## 🎯 Use Cases

* GitOps for AAP configuration
* Environment standardization
* AAP 2.4 → 2.6 migration
* Disaster recovery

---

## 💡 Business Value

* Consistent environments
* Faster onboarding
* Reduced manual errors
* Full audit trail via Git
* Scalable automation

---

## 👤 Author

**Taiseer Hussein**
https://github.com/taiseerhussein

---

