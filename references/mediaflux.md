# Mediaflux

Source snapshot: 2026-08-17. Verify current behavior at [Getting started with Mediaflux](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/5472333/Mediaflux) and the linked client pages.

## Provision and identify the target

- Request a Mediaflux Activity Resource/allocation in RCP or obtain access from the project owner/administrator.
- Resolve the exact project ID/path, requested quota, RCAO, collaborators, data classification, and whether restricted folders are involved.
- In Mediaflux, a **namespace** is a directory-like container and an **asset** combines file content, metadata, checksum, and versions.

## Choose an interface

| Need | Preferred starting point |
| --- | --- |
| Beginner GUI upload/download/search/share | Mediaflux Explorer over HTTPS |
| Metadata definitions, project structure, permissions, tape recall | Mediaflux Desktop in a browser |
| Large/restartable scripted upload/download plus verification | UniMelb command-line clients over HTTPS |
| Shareable-link transfer or instrument workflow | Data Mover |
| Administration/service scripting | aterm; advanced users only |
| Mounted drive | SMB inside the UoM network/VPN; NFS only when provisioned |
| Delta synchronization/cloud-to-cloud workflow | rsync or rclone after checking delete/overwrite semantics |

Prefer HTTPS clients when integrity and restartability matter. Treat the unofficial Python client as best-effort and lacking some pooling/retry/cluster-I/O behavior.

## Authenticate safely

- Staff commonly use the `unimelb` domain and students `student`; local-account behavior differs. Verify the current login page.
- Never place a password or MFA code in a script, committed config, support request, or chat.
- Use a secure identity token for supported automation/non-interactive access. Scope it to the minimum project role, path, protocol/application, and duration.
- MFA rollout/client behavior in the local documents contains 2024-era text; reopen the current [MFA page](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/21397506/Multifactor+Authentication+MFA) before troubleshooting.

## Manage users and roles

- Use the UAM Portal when the RCAO or a user with RCP **manage resources** privilege is authorized to manage access.
- Add a user to the project before granting a restricted-folder ACL.
- Snapshot role meanings from the source docs; verify before applying:
  - `participant-a`: read-only.
  - `participant-acm`: access/create/modify without delete; suited to many automated uploads.
  - `participant-acmd-n`: full read/write including delete for assets and child namespaces.
  - `administrator`: full data access plus project metadata/query and role-management capability.
- Removing a user, role, restricted-folder access, or token can interrupt workflows. Resolve the user/project/path and confirm immediately before removal.

## Protect and recover data

- Mediaflux versions overwritten assets and maintains database/content replication, but replication is not the same as a user-controlled point-in-time backup.
- For overwrite, deletion, or directory recovery, collect project/path, asset/version if known, time/timezone, and scope; do not create conflicting replacements before support assesses recovery.
- File names may work over one protocol and fail over another. Avoid `/`, Windows-reserved characters/names, case-only collisions, excessive paths, and platform-specific trailing characters.

## Handle tape-tiered data

- `ONLINE` means content is on disk; `OFFLINE` means it is on tape, not deleted.
- Check status in Desktop or Explorer. Recall is performed at collection/folder level in Mediaflux Desktop using **Request offline content restoration**.
- Wait for completion notification before retrying. SMB I/O errors, SFTP `EOFFLINE`, rsync exit 23, Explorer `asset.get` failures, or CLI failed assets can all indicate offline content.
- Do not repeatedly retry or overwrite when tape status explains the error.

## Troubleshoot

- Access denied: verify identity domain, project membership, role, restricted-folder ACL, MFA/session state, and exact path.
- Missing file through SMB but visible elsewhere: check invalid cross-platform name and tape status.
- Transfer incomplete: capture client/version, source/destination, worker count, failed-assets log, overwrite settings, token role, and checksum/verification output.
- Large old dataset: check/recall tiered content before starting a bulk transfer.
