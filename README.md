# Homelab Ansible

Playbooks to rebuild my homelab server (ServerChan) from a bare Linux install, so I'm not screwed if it ever dies. Goes with my [homelab-stack](.) repo, which has the actual Docker Compose files these playbooks deploy.

## What's in here

- `playbooks/base-setup.yml` - Docker Engine, Compose plugin, and the base packages (git, sqlite3, curl) I need before anything else can run
- `playbooks/directories.yml` - sets up the folder structure the app configs and media mounts expect
- `playbooks/cifs_mounts.yml` - mounts the NAS shares over CIFS/SMB, using the fstab options I actually got working reliably (`hard,actimeo=5,noserverino`)
- `playbooks/deploy_stack.yml` - clones homelab-stack, templates the secrets into a `.env`, and brings up the full stack (Mealie included, as its own Compose project)

## Requirements

- Ubuntu or Debian-based box (I run Linux Mint 22 / Ubuntu 24.04 "noble")
- Ansible installed on whatever machine runs the playbook
- SSH access, or local connection if you're running it against the same box
- The Vault password, for anything touching secrets (`cifs_mounts.yml`, `deploy_stack.yml`)

## Usage

Run these in order for a full rebuild:

```
1. Edit inventory/hosts.ini to point at your target host
2. ansible-playbook -i inventory/hosts.ini playbooks/base-setup.yml --ask-become-pass
3. ansible-playbook -i inventory/hosts.ini playbooks/directories.yml --ask-become-pass
4. ansible-playbook -i inventory/hosts.ini playbooks/cifs_mounts.yml --ask-vault-pass --ask-become-pass
5. ansible-playbook -i inventory/hosts.ini playbooks/deploy_stack.yml --ask-vault-pass
```

You can also run any playbook on its own against a server that's already up - the copy/mount tasks won't stomp on stuff that's already there.

## Status

Base setup, directories, CIFS mounts, and stack deploy are all done and tested on ServerChan. Full bare-metal-to-running-stack path works.

**Known gap:** Mealie's `BASE_URL` in the deployed compose file is just a placeholder domain - I edit that by hand after deploy since this is a public repo and I'm not baking my real domain into it.

## Notes

I built and tested this against a server that was already running, not a truly clean install, so I hit (and fixed) a few Docker repo/GPG key edge cases along the way. Should still run fine on a fresh machine, just haven't proven it yet.

Mealie runs as its own Compose project (`-p mealie`) because it was originally deployed from a differently-named folder. Skip that flag and Compose gets confused and treats it as some orphaned container instead of recognizing it's already running.
