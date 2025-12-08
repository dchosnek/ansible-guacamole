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

- Ubuntu (22.04+ recommended)
- Ansible 2.13+ (or similar)
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

Then, on the Linux host run:

```
cd /opt/guamacole-docker
docker compose up -d
```
