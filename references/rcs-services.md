# RCS service selection, eligibility, and support

Source snapshot: 2026-08-17. Reopen the [RCS Knowledge Hub](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/overview?homepageId=98403) before quoting current eligibility, forms, limits, or contacts.

## Select a service

| Need | Primary service | Boundary |
| --- | --- | --- |
| Personal managed Windows/Linux interactive VM | Researcher Desktop | Personal resource; compute/working space, not the only data copy |
| Shared managed VM or managed application | Research Server / managed RCP product | Activity-scoped; RCS manages the base environment |
| Batch, parallel, high-memory, or cluster GPU compute | Spartan | Linux/Slurm; working storage only |
| Flexible self-managed infrastructure or long-running service | MRC Unmanaged Cloud | Team owns OS, patching, network, backups, and application security |
| Persistent research files plus metadata/versioning/sharing | Mediaflux | Multiple HTTPS and filesystem-style access methods |
| Imaging-oriented data/metadata workflows | DaRIS | Project → Subject → ExMethod → Study → DataSet model; DICOM/NIfTI support |
| Conventional departmental/research file share | NetApp SMB/CIFS or NFS | Confirm provisioned share and protocol before giving a mount path |
| Public-facing scholarly collections/exhibits | Omeka Classic | Web publishing, not general compute or bulk working storage |

Ask about data volume, sensitivity/classification, integrity, collaborators, platform/OS, interactive versus batch work, performance, retention, backup/versioning, network exposure, and budget before recommending a service.

## Apply RCP concepts correctly

- An **Activity** represents the research activity and contains Activity Resources such as Mediaflux, DaRIS, Research Server, or HPC Project.
- An **allocation** is the provisioned instance/quota/path under a resource.
- A **Research Computing Activity Owner (RCAO)** accepts responsibility for the Activity and its data/resources.
- A Researcher Desktop is a Personal Resource; do not create an Activity solely for it.
- RCP manages provisioning and access. It does not replace the MRC OpenStack Dashboard, Spartan shell/Open OnDemand, or Mediaflux/DaRIS clients.

## Check eligibility and collaborators

- Confirm a valid University identity and an appropriate RCAO before promising access.
- For an external collaborator, the RCAO must use the official external-access process and ensure acceptance of RCS Terms of Service and University IT/security obligations.
- Do not collect identity evidence, passwords, or MFA codes in chat. Let the collaborator and RCAO complete official forms.
- Sharing an RCP Activity may expose every resource in that Activity. If only one resource should be shared, recommend a separate Activity or the service-specific least-privilege mechanism.

## Protect research data

- Use the University's current data-classification process before selecting storage for sensitive data.
- Keep a persistent copy outside Researcher Desktop, MRC instance disks, Spartan project/scratch, or job-local `/tmp`.
- Confirm sharing scope and permissions before adding users or generating links.
- Treat deletion, overwrite, migration, quota reduction, token revocation, and access removal as destructive.

## Escalate effectively

Use the current [Getting help](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/957710338/Getting%2Bhelp) page for Spartan and the current RCS Hub contact section for general services. Do not reuse an old short link without reopening it.

Prepare:

- Service, username, RCAO/contact role, Activity/project/resource/allocation name; omit secrets.
- Time and timezone, exact action/command, complete error, expected and observed result.
- Spartan job ID/state/partition/script/modules/output logs when applicable.
- Source/destination paths, protocol/client, direction, and verification result for transfer issues.
- VM/volume/network/security-group identifiers for MRC issues; DaRIS CID or Mediaflux project/path for data-service issues.
- Reproduction steps and troubleshooting already attempted. Use separate requests for unrelated problems.
