# Ansible Role: Vault SSH Role

## Overview

This role configures a host to use SSH keys signed by HashiCorp Vault, streamlining SSH key management.

Initially, it sets up the Vault client and configures SSH to use Vault-signed keys.

When the `hardening` variable is enabled, the role additionally applies enhanced security measures.

## Requirements

- Ansible 2.9 or higher.
- Operational HashiCorp Vault server.
- A Vault token with the `create` and `update` capabilities on the `ssh-client-signer` endpoint.

## Role Variables

Below are the variables you can override, along with their default values:

### `main.yml` Variables

The most relevant variables are:

- `deploy_user`: User for assigning SSH keys. Default: `"coopbot"`
- `users_list`: Users to configure for SSH access. Default: `["{{ deploy_user }}"]`
- `hardening`: Apply additional security measures (True/False). Default: `False`
- `vault_address`: URL of the Vault server. Default: `"http://127.0.0.1:8200"`
- `vault_public_key`: URL or path for the public key of the Vault. Default: `""`

### `secrets.yml` Variables

Ensure to update this file with your own secrets and encrypt them with Ansible Vault.

```yaml
---
vault_address: "https://vault.example.com"
vault_token: "token"
```

## Role Behavior

### Without hardening

Sets up SSH to use keys signed by HashiCorp Vault (main.yml).

- Download Vault public key.
- Configure SSH to use Vault-signed keys.

### With hardening Enabled

Performs the above tasks and applies additional security configurations (`hardening.yml`).

- Install `vault` client.
- Sign `HostCertificate` with Vault and set up it to be used by SSHD. This allows the host to be authenticated by Vault.
- Enable `AuthorizedPrincipalsFile`. This file specifies a list of principals (users) who are authorized to access each account, providing an extra layer of control over SSH access.

## Example Playbook

Remember to replace the values in `vars` with ones appropriate for your environment.
Here's an example playbook on how to use this role:

```yaml
- hosts: servers
  roles:
    - vault_ssh_setup
  vars:
    vault_public_key: "https://example.com/vault-public-key.pem"
    users_list: ["deployuser", "anotheruser"]
    hardening: True
```
