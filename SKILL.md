---
name: unimelb-rcp-skill
description: Operate, explain, and troubleshoot University of Melbourne Research Computing Services (RCS), including the Research Computing Portal (RCP), Researcher Desktop and Research Server, Melbourne Research Cloud (MRC/OpenStack), Spartan HPC and Slurm, Mediaflux, DaRIS, NetApp storage, Omeka, NVivo, access, data transfer, and support escalation. Use when a UoM/UniMelb request concerns research compute, storage, SSH/RDP, GPUs, quotas, collaborators, or transfers; or when platform-specific names such as RCP, RCAO, Researcher Desktop/Server, MRC, Spartan, Open OnDemand, Mediaflux, DaRIS, or RCS support appear.
---

# UniMelb Research Computing

Use official UoM procedures to select, provision, operate, and troubleshoot RCS compute and data services.

## Route the request

1. Identify the service, resource owner, lifecycle stage, operating system, data source/destination, and whether the user wants guidance, diagnosis, or an authorized mutation.
2. Read only the smallest relevant reference set:
   - Service selection, eligibility, RCAO/Activity concepts, external collaborators, Omeka, or support: [references/rcs-services.md](references/rcs-services.md).
   - Researcher Desktop/Server, browser/RDP access, software, attached storage, GPU resizing, OS migration, boost, shelving, reboot, or deletion: [references/researcher-desktop.md](references/researcher-desktop.md).
   - SSH clients, host keys, passwords/keys, SSH config, Researcher Desktop or Spartan login, agent forwarding, or connection failures: [references/ssh-access.md](references/ssh-access.md).
   - MRC Unmanaged Cloud, OpenStack Dashboard, allocations, projects, instances, images, flavors, volumes, security groups, public/private networking, or Nectar migration: [references/mrc-openstack.md](references/mrc-openstack.md).
   - Spartan account/project requests, collaborators, RCAO, password reset, or initial SSH access: [references/spartan-provisioning.md](references/spartan-provisioning.md).
   - Slurm scripts, partitions, jobs, modules, GPUs, monitoring, login-node policy, containers, data paths, or runtime failures: [references/spartan-operations.md](references/spartan-operations.md).
   - Mediaflux provisioning, clients, MFA/tokens, project roles/ACLs, metadata, versioning, recovery, or tape-tiered data: [references/mediaflux.md](references/mediaflux.md).
   - DaRIS access, object model, roles, portal, DICOM/NIfTI, shopping carts, command-line clients, or servlets: [references/daris.md](references/daris.md).
   - Upload/download workflows, cross-system transfer, protocol/client choice, `scp`/SFTP/rsync, Mediaflux clients, integrity checks, Spartan staging, SMB/NFS/rclone/Data Mover, or NetApp storage: [references/data-transfer-storage.md](references/data-transfer-storage.md).
3. For cross-service work, read both endpoint references plus the access/transfer reference. Example: local computer → Spartan needs `ssh-access.md`, `spartan-operations.md`, and `data-transfer-storage.md`; Spartan ↔ Mediaflux needs `spartan-operations.md`, `mediaflux.md`, and `data-transfer-storage.md`.
4. Distinguish control planes from runtime paths:
   - Use RCP to request and manage managed resources, Activities, allocations, and access.
   - Use the MRC/OpenStack Dashboard to manage unmanaged cloud projects and infrastructure.
   - Use SSH or Open OnDemand to operate Spartan; never treat RCP as a shell.
   - Use Mediaflux/DaRIS/NetApp as data services; do not treat Researcher Desktop, MRC instance disks, or Spartan working filesystems as the only persistent copy.

## Verify volatile facts

Treat UI labels, URLs, eligibility, quotas, approval/expiry timing, resource limits, flavor/image names, network names, GPU/node counts, Slurm partitions/QoS/wall times, modules, shared datasets, client versions, MFA behavior, tiering policy, outages, and support routes as volatile.

- Browse the nearest official UoM page before answering a current-state question; local reference values are routing context, not an entitlement guarantee.
- For an authorized Spartan session, prefer live read-only checks such as `sinfo -s`, `squeue --me`, `sacct`, `scontrol show job`, `module spider`, `df`, and the current quota helpers.
- For MRC/RCP, inspect the authenticated portal or dashboard before describing resources available to that user or project.
- Report the check time and separate observed state, documented policy, and inference.
- Do not rely on legacy `dashboard.hpc.unimelb.edu.au` pages or old `dataservices` links when they redirect; reopen the current RCP or RCS Knowledge Hub page.

## Operate safely

- Never request, expose, store, or echo passwords, MFA codes, private keys, session IDs, or access tokens. Let the user enter secrets into the official UI or local credential prompt.
- Start with read-only inspection. Resolve the exact Activity, project, allocation, VM, volume, job, user, role, token, DaRIS CID, Mediaflux path, and transfer endpoints before mutation.
- Require explicit authorization for submissions and changes. Confirm immediately before deletion, cancellation, overwrite, formatting, access removal, token deletion, public exposure, or a transfer command with delete semantics.
- Warn before any action that reboots/stops a VM, detaches an interface/volume, changes a network, cancels jobs, or interrupts transfers.
- Never run compute-heavy work or externally exposed services on Spartan login nodes. Prefer batch jobs or Open OnDemand; secure any permitted job-local TCP service.
- Do not use `sudo`, `apt`, or `yum` on Spartan. Use modules, supported user environments, containers, or an RCS software request.
- Require Cyber Security approval before placing an MRC instance on a public network; minimize security-group rules and keep public systems patched.
- Keep a verified persistent copy of research data outside compute working storage. Use checksums or a supported verification client after important transfers.
- Treat actions performed by autonomous agents under a user's credentials as that user's responsibility; do not broaden authorization merely because automation is possible.

## Build actionable answers

- Lead with the next safe action or verified status.
- Use the user's real service, project, paths, software, job ID, and operating system. Label every placeholder.
- For Slurm, map the application's serial/threaded/MPI/GPU/array model to resources before choosing nodes, tasks, CPUs, memory, GPUs, partition, and wall time.
- For transfers, state source, destination, direction, protocol/client, authentication method, overwrite/delete behavior, restartability, and verification method.
- For failures, collect the exact action/command, full error, time/timezone, resource identifiers, relevant logs, permissions, and current status before diagnosing.
- Link the official page nearest to each policy or time-sensitive claim and say when a value still needs live confirmation.
