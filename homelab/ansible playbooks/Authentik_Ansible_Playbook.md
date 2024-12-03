# Authentik Ansible Playbook

## Description
This playbook deploys Authentik using Docker.

## Playbook
```yaml
- name: Deploy Authentik
  hosts: authentik_hosts
  become: true
  tasks:
    - name: Create Authentik directory
      file:
        path: /opt/authentik
        state: directory
        mode: '0755'

    - name: Deploy Authentik container
      docker_container:
        name: authentik
        image: "goauthentik/server:latest"
        ports:
          - "9000:9000"
        volumes:
          - "/opt/authentik:/config"
        restart_policy: always
```