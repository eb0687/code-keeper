# Code-Keeper GitLab Platform

This repository provisions a local GitLab CE instance and Docker-executor GitLab
Runner inside an Ubuntu 22.04 VirtualBox VM. Vagrant creates the VM; Ansible
installs Docker and configures the GitLab platform.

## Requirements

- VirtualBox
- Vagrant
- Ansible on the laptop
- At least 2 CPU cores, 6 GB RAM, and 30 GB disk available for the VM

Install the Ansible collection before provisioning:

```sh
ansible-galaxy collection install -r ansible/requirements.yml
```

## Initial Deployment

1. Create an encrypted secrets file from the template.

```sh
cp ansible/inventories/vagrant/vault.example.yml ansible/inventories/vagrant/group_vars/gitlab_hosts/vault.yml
ansible-vault encrypt ansible/inventories/vagrant/group_vars/gitlab_hosts/vault.yml
printf '%s' 'your-vault-password' > .vault-password
chmod 600 .vault-password
```

Set `gitlab_initial_root_password_vault` to a password of at least 8
characters. Leave the API and runner tokens empty for the first run. This is
the root login password; it is different from the Ansible Vault encryption
password stored in `.vault-password`.

2. Create and provision the VM.

```sh
vagrant up
```

GitLab will be available at `http://192.168.57.10:8929`. The VM uses a VirtualBox
host-only network, so this address works from the laptop regardless of the
network to which the laptop is connected. It is not exposed to other devices.

Alternatively, provision directly after the VM exists:

```sh
ansible-playbook -i ansible/inventories/vagrant/hosts.yml ansible/playbooks/gitlab.yml --ask-vault-pass
```

## Complete The Bootstrap

1. Sign in at GitLab as `root` with `gitlab_initial_root_password_vault`.
2. Create an administrator personal access token with the `api` scope.
3. Add the administrator token to `vault.yml` as `gitlab_api_token_vault` and
   run `vagrant provision` again. This configures policies and creates the group.
4. In GitLab, create a group runner for `code-keeper`. Configure it as
   protected, with the `docker-local` tag, and copy its authentication token.
5. Add that token to `vault.yml` as `gitlab_runner_token_vault`, then run
   `vagrant provision` once more.

The second provision disables public sign-up, enables local password
authentication, applies the password policy, makes defaults private, creates the
`code-keeper` group and its four projects, protects their `main` branches, and
registers the Docker runner. It deliberately does not create individual users.

## Operations

```sh
vagrant ssh
sudo docker ps
sudo docker logs gitlab
sudo docker exec gitlab gitlab-rake gitlab:check SANITIZE=true
sudo docker exec gitlab-runner gitlab-runner verify
```

Show the auditable Ansible task list with:

```sh
ansible-playbook -i ansible/inventories/vagrant/hosts.yml ansible/playbooks/gitlab.yml --list-tasks
```

GitLab data persists in `/srv/code-keeper/gitlab` and runner configuration in
`/srv/code-keeper/gitlab-runner` within the VM. Back up these directories before
destroying or recreating the VM.

## Security Notes

- Never commit `vault.yml`, API tokens, runner tokens, or passwords.
- GitLab runs over HTTP only on the private Vagrant network. Do not expose this
  VM to untrusted networks.
- The runner mounts Docker's socket. Trusted projects and protected branches are
  required because CI jobs can control Docker on the VM.
