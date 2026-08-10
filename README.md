# Homelab Ansible


Infrastructure-as-code for rebuilding my homelab server (ServerChan) from a bare Linux install. Pairs with the homelab-stack repo, which holds the actual Docker Compose files this playbook deploys onto.


## What's here

- playbooks/base-setup.yml - installs Docker Engine, Docker Compose plugin, and base packages (git, sqlite3, curl) needed to run the stack


## Requirements

- Target machine running Ubuntu or a Debian-based distro (tested on Linux Mint 22 / Ubuntu 24.04 "noble")
- Ansible installed on whichever machine runs the playbook
- SSH access (or local connection, if running against the same machine)


## Usage

1. Edit inventory/hosts.ini to point at your target host
2. Run: ansible-playbook -i inventory/hosts.ini playbooks/base-setup.yml --ask-become-pass


## Status

Work in progress. Currently covers base system + Docker setup. Planned next: CIFS/NAS mount configuration, directory structure, and stack deployment.


## Notes

Built and tested against an already-running server, not a clean install, so some Docker repo/GPG key edge cases got worked out along the way. Should run cleanly on a genuinely fresh machine too.
