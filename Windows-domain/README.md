# Teleport

This playbook prepares an Active Directory domain for use with Teleport. This
is a requirement for Desktop Access (RDP).

## Prerequisites

You need an Active Directory domain and Certificate Services available on a
member server. See the Linux-server playbook's README for instructions on
making your Windows root CA's certificate available to the Teleport server.

Your AD environment also needs to be configured for secure LDAP (LDAPS).
[This article](https://pdhewaju.com.np/2017/03/02/configuring-secure-ldap-connection-server-2016/)
shows how to set that up.

## Usage

Install the requirements on the control node:

    ansible-galaxy collection install -r requirements.yml

Edit `inventory.template` and rename to `inventory`. Edit 
`group_vars\targets.yml` to match your AD domain.

Enter your required credentials into `vars\secrets.yml.template` and rename to
`vars\secrets.yml`. Encrypt this with Ansible Vault. Then to run the playbook,
assuming the Vault password file is at ~/vault-password:

    ansible-playbook --vault-id ~/vault-password main.yml

The user this playbook runs as must be a domain administrator.

## Remote Desktop permissions

This playbook currently applies a GPO to the root of Active Directory, to enable
inbound RDP connections. You may wish to reduce the scope of this permission to
an OU. Search for "New-GPLink" in the tasks and amend the target as preferred.
Alternatively, remove the links and manually add targeting based on security
groups, or whatever you need.

## Certificates

You need the user CA from your Teleport server. Export this on the server as
follows:

    tctl auth export --type=windows > user-ca.cer

Transfer that file to roles\teleport\files in this playbook.

## Manual steps

TODO: convert to Ansible playbook tasks, if possible

Having run the playbook:

- As a Domain Admin, edit the Teleport Access Policy GPO on a domain controller.
- Browse to Computer Configuration > Policies > Windows Settings > Security.
  Settings > Public Key Policies > Trusted Root Certification Authorities
- Right-click the right-hand pane and click Import.
- Click Next, then enter the path `C:\Windows\Temp\user-ca.cer` (next, etc.).
