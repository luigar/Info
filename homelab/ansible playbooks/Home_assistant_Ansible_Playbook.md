# Home Assistant Ansible Playbook

## Description
This playbook deploys Home Assistant using Docker.

## Playbook
```yaml
- name: Deploy Home Assistant
  hosts: home_assistant_hosts
  become: true
  tasks:
    - name: Create Home Assistant directory
      file:
        path: /opt/homeassistant
        state: directory
        mode: '0755'

    - name: Deploy Home Assistant container
      docker_container:
        name: home_assistant
        image: "homeassistant/home-assistant:latest"
        ports:
          - "8123:8123"
        volumes:
          - "/opt/homeassistant:/config"
        restart_policy: always
```