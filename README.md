# UniMelb Research Computing Skill

`unimelb-rcp-skill` is a Codex skill for operating, explaining, and troubleshooting University of Melbourne Research Computing Services (RCS).

It covers the Research Computing Portal (RCP), Researcher Desktop and Research Server, Melbourne Research Cloud, Spartan HPC, Mediaflux, DaRIS, research storage, remote access, and data transfer workflows.

> This is an independent operational aid, not an official University of Melbourne service or policy source. Confirm time-sensitive limits, interfaces, entitlements, and procedures against the current RCP and RCS Knowledge Hub.

## Capabilities

- Select the appropriate UniMelb research computing or data service.
- Provision and manage RCP Activities, Researcher Desktop/Server, and MRC resources.
- Connect to Linux Researcher Desktop and Spartan with SSH safely.
- Configure SSH keys, host aliases, `ProxyJump`, and narrowly scoped agent forwarding.
- Upload and download data with `scp`, SFTP, `rsync`, Mediaflux clients, SMB/NFS, and supported data movers.
- Stage and verify transfers between laptops, Researcher Desktop, Spartan, Mediaflux, and shared storage.
- Prepare and diagnose Slurm jobs, including CPU, memory, GPU, array, module, and container workflows.
- Work with Mediaflux projects, roles, tokens, metadata, versioning, recovery, and tape-tiered data.
- Navigate DaRIS projects, roles, DICOM/NIfTI workflows, shopping carts, and command-line access.
- Troubleshoot access, quota, lifecycle, networking, storage, and transfer failures.

## Install

Clone the repository into the Codex skills directory:

```bash
mkdir -p ~/.codex/skills
git clone https://github.com/yaoxkkkkk/unimelb-rcp-skill.git \
  ~/.codex/skills/unimelb-rcp-skill
```

If the repository is private, authenticate with GitHub first or use GitHub CLI:

```bash
gh auth login
gh repo clone yaoxkkkkk/unimelb-rcp-skill \
  ~/.codex/skills/unimelb-rcp-skill
```

The installed directory name must remain `unimelb-rcp-skill`. Start a new Codex task after installation so skill discovery can refresh.

## Use

Invoke the skill explicitly with `$unimelb-rcp-skill`, or ask about a supported UniMelb service by name.

Example prompts:

```text
Use $unimelb-rcp-skill to explain how to connect to my Linux Researcher Desktop with SSH from macOS.

Use $unimelb-rcp-skill to create a restartable, checksum-verified upload plan from my laptop to Spartan project storage.

Use $unimelb-rcp-skill to diagnose why my Spartan Slurm job is pending.

Use $unimelb-rcp-skill to download a Mediaflux project to Spartan without overwriting existing files.

Use $unimelb-rcp-skill to compare Researcher Desktop, Research Server, MRC, and Spartan for my workload.
```

Replace every placeholder in generated commands with the actual username, hostname, project, path, job ID, or allocation. Review commands before execution.

## Reference map

| File | Scope |
| --- | --- |
| [`SKILL.md`](SKILL.md) | Routing, verification rules, safety boundaries, and answer requirements |
| [`references/rcs-services.md`](references/rcs-services.md) | Service selection, eligibility, Activities, collaborators, Omeka, and support |
| [`references/researcher-desktop.md`](references/researcher-desktop.md) | Researcher Desktop/Server lifecycle, browser/RDP access, software, storage, GPU, and recovery |
| [`references/ssh-access.md`](references/ssh-access.md) | Researcher Desktop and Spartan SSH, keys, config, host verification, forwarding, and diagnostics |
| [`references/mrc-openstack.md`](references/mrc-openstack.md) | MRC/OpenStack projects, instances, images, flavors, volumes, security groups, and networking |
| [`references/spartan-provisioning.md`](references/spartan-provisioning.md) | Spartan accounts, projects, access requests, collaborators, and password reset |
| [`references/spartan-operations.md`](references/spartan-operations.md) | Slurm, partitions, jobs, modules, GPUs, monitoring, containers, and runtime failures |
| [`references/mediaflux.md`](references/mediaflux.md) | Mediaflux access, clients, roles, metadata, versioning, recovery, and tiering |
| [`references/daris.md`](references/daris.md) | DaRIS object model, roles, portal, DICOM/NIfTI, carts, clients, and servlets |
| [`references/data-transfer-storage.md`](references/data-transfer-storage.md) | Upload/download decisions, `scp`, SFTP, `rsync`, Mediaflux transfers, integrity checks, and storage |

## Safety model

The skill is designed to:

- start with read-only inspection;
- distinguish documented policy, observed state, and inference;
- verify volatile facts against current official sources or live read-only commands;
- avoid collecting or exposing passwords, MFA codes, private keys, tokens, and session identifiers;
- require confirmation before deletion, cancellation, overwrite, public exposure, or delete-synchronization;
- avoid compute-heavy work and externally exposed services on Spartan login nodes;
- keep a verified persistent copy outside ephemeral or working compute storage;
- record transfer direction, endpoints, authentication, overwrite behavior, restartability, and verification.

## Update an installation

```bash
cd ~/.codex/skills/unimelb-rcp-skill
git pull --ff-only
```

If you installed the skill by copying files rather than cloning the repository, replace the installed directory with a verified release or fresh clone.

## Validation

The package is checked with the Codex `skill-creator` validator. Its Markdown references are also checked for missing links, orphaned reference files, unclosed code fences, and accidental credential-like content.
