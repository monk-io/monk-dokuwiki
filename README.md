Dokuwiki meets Monk
===

This repository contains Monk.io template to deploy Dokuwiki system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

This template uses [Traefik](https://traefik.io/) as a reverse proxy to provide full HTTPS encryption.
Traefik will try to obtain full SSL certificate from Lets Encrypt(assuming valid hostname is provided in the `manifest.yaml`) and if not it will fallback to a default self generated SSL certificate.

If you wish to disable Traefik(for example you have your own reverse proxy) please look at [Variables](#variables) section.

- [Dokuwiki meets Monk](#dokuwiki-meets-monk)
  - [Prerequisites](#prerequisites)
    - [Make sure monkd is running.](#make-sure-monkd-is-running)
    - [Clone Repository](#clone-repository)
    - [Load Template](#load-template)
    - [Verify if it's loaded correctly](#verify-if-its-loaded-correctly)
  - [Deploy Stack](#deploy-stack)
    - [Deploy Dokuwiki Server](#deploy-dokuwiki-server)
  - [Variables](#variables)
  - [Stop, remove and clean up workloads and templates](#stop-remove-and-clean-up-workloads-and-templates)
  - [Persistency](#persistency)

## Prerequisites
- [Install Monk](https://docs.monk.io/docs/get-monk)
- [Register and Login Monk](https://docs.monk.io/docs/acc-and-auth)
- [Add Cloud Provider](https://docs.monk.io/docs/cloud-provider)
- [Add Instance](https://docs.monk.io/docs/multi-cloud)

### Make sure monkd is running.

```bash
foo@bar:~$ monk status
daemon: ready
auth: logged in
not connected to cluster
```

### Clone Repository

```bash
git clone git@github.com:monk-io/monk-dokuwiki.git
```

### Load Template

```bash
cd monk-dokuwiki
monk load manifest.yaml
```

### Verify if it's loaded correctly

```bash
$ monk list -l dokuwiki

✔ Got the list
Type      Template        Repository  Version  Tags
runnable  dokuwiki/server  local       -        -
```

## Deploy Stack

### Deploy Dokuwiki Server

```bash
$ monk run dokuwiki/server
? Select tag to run [local/dokuwiki/server] on: aws
✔ Starting the job: local/dokuwiki/server... DONE
✔ Preparing nodes DONE
✔ Checking/pulling images...
✔ [================================================] 100% traefik:v2.9.1 set1-2
✔ [================================================] 100% lscr.io/linuxserver/dokuwiki:latest set1-2
✔ Checking/pulling images DONE
✔ Started local/dokuwiki/server

🔩 templates/local/dokuwiki/server
 └─🧊 Peer set1-2
    ├─🔩 templates/local/dokuwiki/server
    │  └─📦 f725d3ff8b38a2f637e10aa0e8d2f688-local-dokuwiki-server-traefik
    │     ├─🧩 traefik:v2.9.1
    │     ├─💾 /var/lib/monkd/volumes/dokuwikiacme -> /acmestore
    │     ├─💾 /var/run/podman/podman.sock -> /var/run/docker.sock
    │     ├─🔌 open 44.195.66.170:80 -> 80
    │     └─🔌 open 44.195.66.170:443 -> 443
    └─🔩 templates/local/dokuwiki/server
       └─📦 3fca6c2394ea761f05b304cd9246115b-local-dokuwiki-server-dokuwiki
          ├─🧩 lscr.io/linuxserver/dokuwiki:latest
          └─💾 /var/lib/monkd/volumes/dokuwikistorage -> /config

💡 You can inspect and manage your above stack with these commands:
        monk logs (-f) local/dokuwiki/server - Inspect logs
        monk shell     local/dokuwiki/server - Connect to the container's shell
        monk do        local/dokuwiki/server/action_name - Run defined action (if exists)
💡 Check monk help for more!
```

## Variables

The variables are stored in `manifest.yaml` file.
You can quickly setup by editing the values there.

| Variable       | Description                                                                    | Default              |
| -------------- | ------------------------------------------------------------------------------ | -------------------- |
| hostname       | Your Jenkins master hostname                                                   | <- ip-address-public |
| enable-traefik | If disabled traffic will be routed only to Dokuwiki via non-ssl encrypted port | true                 |
| force-https    | If enabled traffic will always be routed only to ssl encrypted port            | true                 |
| http-port      | Port that Traefik/Jenkins will listen for non SSL traffic                      | 80                   |
| https-port     | Port that Traefik will listen for SSL traffic                                  | 443                  |

## Stop, remove and clean up workloads and templates

```bash
monk purge    dokuwiki/server
monk purge -x dokuwiki/server
```

## Persistency
If you're using any of the clouds available via Monk. You can use volume definition to spin a disk block device to make your Jenkins instance independent from the node it's running on.
To do simply uncomment the `volume` block in `manifest.yaml`
