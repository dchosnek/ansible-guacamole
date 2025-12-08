# Instructions for manual deploy

These are the instructions for deploying Guacamole without Ansible.

## Prepare the repo

```
sudo apt update
sudo apt install -y ca-certificates curl gnupg

sudo install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
```

## Install and verify Docker

```
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
docker compose version
```

It should return something like: `Docker Compose version v2.40.3`

## Create self-signed certs for nginx

```
mkdir -p nginx/ssl
openssl req -x509 -newkey rsa:4096 -nodes \
  -keyout nginx/ssl/self-ssl.key \
  -out nginx/ssl/self.cert \
  -days 365 \
  -subj "/CN=localhost"
```

create this directory structure

```
nginx/
  templates/
    default.conf.template
  ssl/
    self.cert
    self-ssl.key
```

Copy [default.conf.template](./default.conf.template) into that structure.

## Create init file for Posgres database

This is a one-time operation required to create the init file for the database.

```
mkdir -p guac_initdb

docker run --rm guacamole/guacamole:1.6.0 \
  /opt/guacamole/bin/initdb.sh --postgresql > guac_initdb/initdb.sql
```

## Run the compose file

```
docker compose up -d
```