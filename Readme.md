# Secure Elasticsearch Cluster with Ansible

This repository contains an **Ansible playbook** to automate the installation and secure configuration of a multi-node **Elasticsearch cluster** using **self-signed certificates**.

---

## Features

- Deploys Elasticsearch (version configurable, default: 9.0.3)
- Configures a secure 3-node cluster (transport + HTTP TLS)
- Uses PKCS#12 format certificates signed by a local CA
- Adds all certificate passwords to the Elasticsearch keystore securely
- Configures node roles, ports, and cluster discovery settings
- Automatically sets up the required directory structure and file permissions

---

## Prerequisites

Before running the playbook, prepare the certificates using the **`elasticsearch-certutil`** tool on the **Ansible control node**:

> **Note:** The `elasticsearch-certutil` binary is bundled with Elasticsearch. You can extract it from the `.tar.gz` or `.deb` package without installing Elasticsearch itself.

### 1. Generate a Certificate Authority (CA):

```bash
elasticsearch-certutil ca -out path_to/certs/elastic-stack-ca.p12
```

This creates:
```bash
certs
└── elastic-stack-ca.p12
```

### 2. Create an instances.yml file for all nodes:
```yaml
instances:
  - name: es-node1
    ip:
      - <IP>
    dns:
      - <dns>
  - name: es-node2
    ip:
      - <IP>
    dns:
      - <dns>
  - name: es-node3
    ip:
      - <IP>
    dns:
      - <dns>
```

### 3. Generate certificates for all nodes using your CA:
```bash
elasticsearch-certutil cert --silent \
  --ca certs/ca/elastic-stack-ca.p12 \
  --in instances.yml \
  --out node-certs.zip \
  --pass <yourpassword-here>
```

unzip node-certs.zip -d certs
This will produce a structure like:
```bash
certs/
├── es-node1/
│   ├── es-node1.p12
├── es-node2/
│   └── ...
```

## ⚠️ SSH Access Required
Before running any playbooks, ensure the Ansible control node has SSH access to all Elasticsearch nodes.

You must either:
- Set up SSH key-based authentication
- Or use ansible_user and ansible_ssh_pass in your inventory file
