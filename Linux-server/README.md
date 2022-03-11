# Teleport Linux server

This Ansible playbook sets up a single Linux server to run the central Teleport
services (proxy, certificate authority, audit log). The playbook is designed for
Red Hat-derived operating systems - in particular Oracle Linux (8).

This is built assuming PKI based around Smallstep. Other PKI can be used, but
you'll need to adapt the playbook accordingly. For security, Teleport is
highly dependent on certificates.

## Usage

Edit `inventory.template` and rename to `inventory`. Edit 
`group_vars\targets.yml` to match your environment.

Playbook runs:

    ansible-playbook main.yml

## Certificates

The `pki` role configures this server to trust the specified root certificate
authority. Certificates can be issued for the Teleport server manually on an
intermediate Smallstep CA, e.g.:

    step certificate create teleport.lan.pomeroy.me teleport-server.crt teleport-server.key \
        --profile leaf --not-after=8760h \
        --ca /etc/step-ca/certs/intermediate_ca.crt \
        --ca-key /etc/step-ca/secrets/intermediate_ca_key --bundle

## Active Directory CA

If you are deploying Teleport to a Windows environment, you'll likely be using
Active Directory Certificate Services. To trust that CA:

- Run the Certification Authority MMC
- Right click the CA computer > Properties
- General > View Certificate
- Details view > Copy to File
- In the Certificate Export Wizard, select DER encoded binary X.509
- Save as "AD_Domain_CA.cer"
- Convert this certificate to PEM format.

Windows:

    certutil -encode AD_Domain_CA.cer AD_Domain_CA.pem

Linux:  

    openssl x509 -inform der -in AD_Domain_CA.cer -out AD_Domain_CA.pem

The Linux approach is slightly superior, since it results in Linux-style line
endings.

- Copy this certificate to roles\pki\files\AD_Domain_CA.pem

## First user account

After running the playbook, on the Teleport server create the first user, e.g.:

    sudo tctl users add teleport-admin --roles=editor,access --logins=root,ansible

This command will provide a URL to complete the setup of the user (create a 
password). An authentication app will be required, to set up MFA.