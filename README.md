# Guacamole Docker Ansible Role

This repo provisions an Apache Guacamole stack (Guacamole, guacd, PostgreSQL, nginx) using Docker Compose. You can view the container topology [here](./container_diagram.md).

## What it does

- Installs **Docker CE** + **docker compose plugin** from Docker's official apt repo
- Creates directory layout under `/opt/guacamole-docker`
- Generates `guac_initdb/initdb.sql` using the official Guacamole image in order to properly initialize the Postgres database
- Renders:
  - `compose.yaml` (Docker Compose v2 file)
  - `nginx/templates/default.conf.template` for the nginx container
- Optionally generates a **self-signed TLS certificate** for nginx

## Requirements

- Ubuntu 22 (tested with 22.04.3)
- Ansible 2.13+ (tested with 2.19.4)
- `sudo` on the target

## Inventory

Example `inventory` for remote host:

```ini
[guacamole_hosts]
guac01 ansible_host=10.135.5.135 ansible_user=ubuntu

[guacamole_hosts:vars]
ansible_ssh_pass='myLinuxPass'
ansible_become_pass='mySudoPass'
ansible_python_interpreter=/usr/bin/python3.12
```

## How to deploy

First run the playbook:

```
ansible-playbook -i inventory guacamole.yml
```

There are two ways to run Guacamole:
1. Manually with the `docker compose` command
1. Create a systemd service using an optional variable in this playbook

### Run manually

On the Linux host run the following commands:

```
cd /opt/guacamole-docker
docker compose up -d
```

Guacamole will come up automatically, even after a reboot. But this is not the most reliable method because Guacamole will not restart automatically if you run `docker compose down`. The most reliable method is to create a systemd service.

### Set up as a service (optional)

The following variable in `roles/guacamole/defaults/main.yml` controls whether or not a systemd service is created to gracefully handle starting and stopping guacamole when the host reboots.

```yaml
guac_enable_systemd_service: true
```

This is not necessary for lab environments, but is recommended for a production environment.

## Change the `guacadmin` password immediately

Log in to the web interface as with username/password of `guacadmin` and update the password from that default.
