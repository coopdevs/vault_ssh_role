# Vault SSH Setup Role

The `vault_ssh_setup` role is designed to configure SSH servers to use SSH keys signed by HashiCorp Vault. This role ensures that your SSH configuration is secure and manageable, especially useful for environments where SSH key management and server access need to be tightly controlled.

## Role Tasks

This role performs the following tasks:

1. **Install Dependencies:** Installs necessary packages like `curl` and `openssh-server`.

2. **Ensure SSH Service is Running:** Makes sure that the SSH daemon (`sshd`) is running and enabled on the system.

3. **Download Vault's Public Key:** Retrieves the public key from a specified URL and saves it to the server for later use in validating signed SSH keys.

4. **Configure `sshd_config`:** Updates the SSH daemon configuration to use the trusted CA keys provided by Vault.

5. **Enable `PubkeyAuthentication`:** Ensures that public key authentication is enabled in the SSH configuration.

6. **Configure `AuthorizedPrincipalsFile`:** If hardening is enabled, sets up and configures an `AuthorizedPrincipalsFile` for additional security measures.

7. **Create `authorized_principals_file` for Users:** For each specified user, ensures that an `authorized_principals_file` exists.

## Role Variables

- `vault_public_key_url`: URL of the Vault server's public key. Default is set to the provided `vault_public_key`.
- `ssh_service_name`: The name of the SSH service. Default is `"ssh"`.
- `dependencies`: A list of packages required by the role. Defaults to `["curl", "openssh-server"]`.
- `authorized_principals_file`: The path template for the authorized principals file, with `%u` replaced by each username. Default is `"/etc/ssh/auth_principals/%u"`.
- `deploy_user`: The default user for deployment operations. Default is `"coopbot"`.
- `users_list`: A list of users for whom the `authorized_principals_file` will be configured. Default includes the `deploy_user`.
- `hardening`: Boolean value to enable additional security measures. Default is `True`.

## Hardening Option in Vault SSH Setup Role

The `vault_ssh_setup` role includes an optional hardening feature, designed to enhance the security of the SSH service on your servers. This option is controlled by the `hardening` variable in the role. When enabled, it activates additional security measures, particularly around SSH key management and user authentication.

### What Does Hardening Do?

When the `hardening` feature is enabled, the role performs the following additional tasks:

1. **Configure `AuthorizedPrincipalsFile`:** The role sets up the `AuthorizedPrincipalsFile` in the SSH configuration (`sshd_config`). This file specifies a list of principals (users) who are authorized to access each account, providing an extra layer of control over SSH access.

2. **Create `authorized_principals_file` for Each User:** If the `authorized_principals_file` does not exist for a user listed in `users_list`, the role will create it. This ensures that each user has a designated file that can be used to specify their authorized keys.

3. **Restrict Access Based on Principals:** Only users with their principals defined in the `authorized_principals_file` are allowed to access the server, tightening security by limiting SSH access to a controlled list of users and their corresponding keys.

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
