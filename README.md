# Homelab Ansible

Infrastructure-as-code for rebuilding my homelab server (ServerChan) from a bare Linux install. Pairs with the homelab-stack repo, which holds the actual Docker Compose files this playbook deploys onto.

## What's here

- playbooks/base-setup.yml - installs Docker Engine, Docker Compose plugin, and base packages (git, sqlite3, curl) needed to run the stack
- playbooks/directories.yml - creates the directory structure app configs and media mounts expect
- playbooks/cifs_mounts.yml - mounts the NAS shares over CIFS/SMB with the fstab options that actually work reliably (hard, actimeo=5, noserverino)
- playbooks/deploy_stack.yml - clones homelab-stack, templates secrets into a .env file, and brings up the full Docker stack (including Mealie as a separate Compose project)

## Requirements

- Target machine running Ubuntu or a Debian-based distro (tested on Linux Mint 22 / Ubuntu 24.04 "noble")
- Ansible installed on whichever machine runs the playbook
- SSH access (or local connection, if running against the same machine)
- Ansible Vault password for anything touching secrets (cifs_mounts.yml, deploy_stack.yml)

## Usage

Run in order for a full rebuild:

1. Edit inventory/hosts.ini to point at your target host
2. `ansible-playbook -i inventory/hosts.ini playbooks/base-setup.yml --ask-become-pass`
3. `ansible-playbook -i inventory/hosts.ini playbooks/directories.yml --ask-become-pass`
4. `ansible-playbook -i inventory/hosts.ini playbooks/cifs_mounts.yml --ask-vault-pass --ask-become-pass`
5. `ansible-playbook -i inventory/hosts.ini playbooks/deploy_stack.yml --ask-vault-pass`

Each playbook can also be run independently against an already-running server; the copy/mount tasks won't clobber existing files.

## Status

Base system, directory structure, CIFS mounts, and stack deployment are all done and tested against ServerChan. This repo now covers a full rebuild path from bare OS to running stack.

Known gap: Mealie's BASE_URL in the deployed compose file is a placeholder domain and needs a manual edit post-deploy (not automated, since it's a public-facing sanitized repo).

## Notes

Built and tested against an already-running server, not a clean install, so some Docker repo/GPG key edge cases got worked out along the way. Should run cleanly on a genuinely fresh machine too.

Mealie runs as its own Docker Compose project (explicit `-p mealie` flag) since it was originally deployed from a differently-named directory — without this, Compose treats it as an orphaned/conflicting container instead of recognizing it as already running.
