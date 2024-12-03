# GitLab Ansible Playbook

## Description
This playbook deploys GitLab using Docker.

## Playbook
```yaml
- name: Deploy GitLab
  hosts: gitlab_hosts
  become: true
  tasks:
    - name: Create GitLab directory
      file:
        path: /opt/gitlab
        state: directory
        mode: '0755'

    - name: Deploy GitLab container
      docker_container:
        name: gitlab
        image: "gitlab/gitlab-ce:latest"
        ports:
          - "80:80"
          - "443:443"
          - "22:22"
        volumes:
          - "/opt/gitlab/config:/etc/gitlab"
          - "/opt/gitlab/logs:/var/log/gitlab"
          - "/opt/gitlab/data:/var/opt/gitlab"
        restart_policy: always
```