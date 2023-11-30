# Vault SSH Setup Role

This Ansible role configures hosts to use SSH key signing with a Vault server.

## Requirements

- Ansible 2.9 or higher
- SSH server access

## Role Variables

- `vault_public_key`: The path or URL to the Vault's public key.
- `ssh_service_name`: The name of the SSH service.

## Dependencies

None.

## Example Playbook

```yaml
- hosts: all
  roles:
    - vault_ssh_setup
```

## License

MIT / BSD
