# Teleport

These playbooks are for setting up infrastructure to run Teleport services.
Teleport is a "Certificate authority and access plane for SSH, Kubernetes, 
web apps, databases and desktops". Think "private BeyondTrust/TeamViewer/etc."
Basicallty Teleport facilitates secure remote access to systems. Amongst other
features, it provides:

- Workflow for access requests & approval (access flow is a commercial feature)
- Single sign-on integration (limited to GitHub in the community edition)
- Multifactor authentication
- Web-based RDP access
- SSH access
- Session recording
- Role-based access control

References:

- [Teleport's GitHub repository](https://github.com/gravitational/teleport)
- [Teleport's web site](https://goteleport.com/)

## Playbooks

This repository contains the following playbooks:

- Linux-server: set up a RHEL 8 compatible machine as a Teleport server/proxy.
- Windows-domain: prepare a Windows domain for the use of Teleport's Desktop
  Access (RDP) service.

See each playbook's own README for important usage notes.