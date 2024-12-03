# Homelab Setup Plan

## Overview
This plan outlines the step-by-step process for setting up a homelab stack using Proxmox, Ansible, Docker, and several self-hosted services. GitHub will be used for initial configuration management, and GitLab will be deployed later for version control, CI/CD, and backup purposes.

---

## Step-by-Step Plan

### 1. Set Up Proxmox Environment
1. **Install Proxmox** on your hardware.
2. **Configure Storage Pools** for VMs and containers.
3. **Set Up Networking**:
   - Configure virtual networks, bridges, or VLANs.
   - Ensure Proxmox host has internet and local network access.

---

### 2. Prepare the Ansible Control Node
1. **Create a Control Node** (dedicated VM or local machine).
2. **Install Ansible**:
   - On Linux:
     ```bash
     sudo apt update
     sudo apt install ansible
     ```
   - On Windows, use WSL and follow similar steps.
3. **Set Up SSH Access**:
   - Generate SSH keys:
     ```bash
     ssh-keygen -t rsa -b 4096
     ```
   - Copy keys to managed VMs:
     ```bash
     ssh-copy-id user@vm-ip-address
     ```

---

### 3. Use GitHub for Initial Configuration Storage
1. **Create a GitHub Repository**:
   - Name it something like `homelab-ansible`.
2. **Organize the Repository**:
   - Structure:
     ```
     homelab-ansible/
     ├── inventory/
     ├── playbooks/
     ├── roles/
     ├── group_vars/
     ├── host_vars/
     └── README.md
     ```
3. **Push Initial Configurations** to GitHub.

---

### 4. Set Up Base VM with Docker
1. **Create a Docker Host VM** in Proxmox.
2. **Install Docker Using Ansible**:
   ```bash
   ansible-playbook -i inventory/hosts.yml playbooks/docker.yml
   ```
3. **Verify Installation**:
   ```bash
   docker run hello-world
   ```

---

### 5. Deploy Traefik Using Ansible
1. **Configure Traefik Playbook**.
2. **Run the Playbook**:
   ```bash
   ansible-playbook -i inventory/hosts.yml playbooks/traefik.yml
   ```
3. **Set Up Domain** DNS for Traefik routing.

---

### 6. Deploy Authentik
1. **Update Authentik Playbook**.
2. **Run the Playbook**:
   ```bash
   ansible-playbook -i inventory/hosts.yml playbooks/authentik.yml
   ```
3. **Integrate with Traefik for SSO**.

---

### 7. Deploy Pi-hole
1. **Prepare Pi-hole Configuration** in GitHub.
2. **Run the Playbook**:
   ```bash
   ansible-playbook -i inventory/hosts.yml playbooks/pihole.yml
   ```
3. **Set Pi-hole as Network DNS Server**.

---

### 8. Bootstrap GitLab
1. **Manually Deploy GitLab** using `docker-compose.yml`.
2. **Start GitLab**:
   ```bash
   docker-compose up -d
   ```
3. **Verify Installation** by accessing GitLab in your browser.

---

### 9. Migrate Repositories from GitHub to GitLab
1. **Create New Projects** in GitLab.
2. **Mirror Repositories**:
   - Set up GitLab to pull from GitHub.

---

### 10. Set Up Sync from GitLab to GitHub for Backups
1. **Configure GitLab CI/CD** to sync to GitHub.
2. **Use `.gitlab-ci.yml`**:
   ```yaml
   stages:
     - backup

   backup_to_github:
     stage: backup
     script:
       - git config --global user.name "GitLab Backup Bot"
       - git config --global user.email "backup@yourdomain.com"
       - git remote add github $GITHUB_URL
       - git push --mirror https://$GITHUB_TOKEN@github.com/yourusername/your-repo.git
     only:
       - main
   ```

---

### 11. Deploy Home Assistant
1. **Add Configurations** to GitLab.
2. **Run the Playbook**:
   ```bash
   ansible-playbook -i inventory/hosts.yml playbooks/home_assistant.yml
   ```

---

### 12. Deploy Wazuh
1. **Prepare Configurations** in GitLab.
2. **Run the Playbook**:
   ```bash
   ansible-playbook -i inventory/hosts.yml playbooks/wazuh.yml
   ```

---

### 13. Enhance Ansible for Maintenance
1. **Create Playbooks for Updates and Backups**.
2. **Schedule Regular Runs** via cron jobs or AWX/Tower.

---

### 14. Document and Monitor
1. **Document the Setup** in GitLab README files.
2. **Implement Monitoring Tools**:
   - Use Wazuh, Prometheus, or Grafana.

---

## Summary
This setup plan ensures:
- **Efficient Bootstrapping** with GitHub as a temporary host.
- **Automation** with Ansible for deployments and updates.
- **Resilience** with GitLab as the main repository and GitHub as a backup.

Follow this plan step-by-step, and your homelab stack will be ready to manage, monitor, and grow seamlessly.

---
