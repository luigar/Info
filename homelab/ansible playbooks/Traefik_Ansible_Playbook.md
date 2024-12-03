# Traefik Ansible Playbook

## Description
This playbook deploys and configures Traefik using Docker on the target host.

## Playbook
```yaml
- name: Deploy Traefik
  hosts: traefik_hosts
  become: true
  tasks:
    - name: Create Traefik directory
      file:
        path: /opt/traefik
        state: directory
        mode: '0755'

    - name: Copy Traefik configuration file
      copy:
        src: files/traefik.yml
        dest: /opt/traefik/traefik.yml

    - name: Deploy Traefik container
      docker_container:
        name: traefik
        image: "traefik:v2.9"
        ports:
          - "80:80"
          - "443:443"
        volumes:
          - "/opt/traefik/traefik.yml:/etc/traefik/traefik.yml"
        restart_policy: always
```