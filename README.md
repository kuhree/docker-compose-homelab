# Multi-file Docker Compose Homelab

This repo contains a number of services defined in docker-compose.yml files.

I used this for a while to manage/deploy services on my home network and now serves as reference.

## Features

- Multi-file Docker Compose w/ the [include](https://docs.docker.com/compose/how-tos/multiple-compose-files/include/) property.
- Use of [profiles](https://docs.docker.com/reference/compose-file/profiles/) to separate services
- Services
    - Traefik Reverse Proxy with automatic service discovery
    - Gitea for code hosting and Github Actions compatible runners
    - Owncloud for file hosting/sync
    - Jellyfin + ARR stack
    - Grafana and friends for monitoring
    - A few other misc services

## Usage

- Clone the repo - `git clone <repo_url>`
- Create .env files
    - There should be a shared `.env` file at the root of the repo containing the following:
        ```env
        STACKS_DIR="/opt/stacks" # location of stacks
        LOG_LEVEL=DEBUG
        NAME=homelab
        DOMAIN=example.com
        TZ="America/New_York" # Your timezone, https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
        PUID="1000" # UNIX PUID and PGID, find with: id $USER
        PGID="1000"

        # Media Storage
        MEDIA="/mnt/media" 
        MEDIA_LIBRARY="${MEDIA:?}/library"

        # Admin
        ADMIN_USER=admin
        ADMIN_EMAIL=${ADMIN_USER:?}@${DOMAIN:?}
        ADMIN_PASSWORD=<random-string>
        ADMIN_API_PASSWORD=${ADMIN_PASSWORD:?}
        PASSWORD_SALT=<random-string>
        ```
    - Each folder should have its own `.env` file, for example traefik:
        ```env
        # Cloudflare
        CF_DNS_API_TOKEN=<cloudflare-api-token>
        CF_API_KEY=<cloudflare-api-key>

        # GIT
        GIT_SSH_PORT=2221
        ```
- Use the `./vibe` script at the root of the repo to launch services by mode/profile
    - `./vibe <profile> <command>` 
        - where `profile` is one of "debug,internal,service,arr,all" and `command` is your regular docker compose commands

## Lessons Learned

- This doesn't scale once you start adding multiple machines. -> Docker Swarm/Kubernetes
- You need to have a machine with [docker/docker-compose installed already](https://docs.docker.com/engine/install/). -> Ansible
- The use of volumes made a backup strategy a bit difficult. -> Network storage & custom scripts
- The use of `.env` files caused the variables to be stored insecurely -> Docker Secrets

