# Pi-hole Ansible Playbook

## Description
This playbook deploys Pi-hole using Docker.

## Playbook
```yaml
- name: Deploy Pi-hole
  hosts: pihole_hosts
  become: true
  tasks:
    - name: Create Pi-hole directory
      file:
        path: /opt/pihole
        state: directory
        mode: '0755'

    - name: Deploy Pi-hole container
      docker_container:
        name: pihole
        image: "pihole/pihole:latest"
        ports:
          - "80:80"
          - "53:53/udp"
        env:
          TZ: "America/New_York"
          WEBPASSWORD: "your_password"
        volumes:
          - "/opt/pihole:/etc/pihole"
          - "/opt/pihole/dnsmasq.d:/etc/dnsmasq.d"
        restart_policy: always
```