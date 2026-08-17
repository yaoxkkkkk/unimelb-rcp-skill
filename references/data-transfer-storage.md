# Upload, download, data transfer, and storage

Source snapshot: 2026-08-17. Reopen service-specific pages before using hostnames, ports, modules, worker limits, share paths, token roles, or retention policy.

## Contents

- Define and preflight the transfer
- Choose the method
- Transfer to or from a Researcher Desktop
- Transfer to or from Spartan
- Transfer between Spartan and Mediaflux
- Use Mediaflux SFTP and rsync
- Use SMB/NFS and cloud tools
- Verify and troubleshoot

## Define and preflight the transfer

Record before composing a command:

- Source/destination service, host, absolute path, direction, data owner, size, file count, and sensitivity.
- One-time copy versus repeated synchronization; metadata, symlink, timestamp, permissions, and empty-directory requirements.
- Authentication method without collecting the secret.
- Existing destination content, overwrite/delete behavior, quota/free space, restartability, log location, and verification method.

Run read-only checks first:

```bash
du -sh <source>
find <source> -type f | wc -l
df -h <destination-parent>
```

Do not assume those commands are suitable for a huge or remote tree; use service quota helpers or client-side inventory when a recursive scan would be expensive. Prefer direct server-to-server transfer for large data and retain the source until verification and user acceptance.

## Choose the method

| Scenario | Starting method | Properties |
| --- | --- | --- |
| One/few files over SSH | `scp` | Simple; weak restart behavior |
| Directory or repeat SSH copy | `rsync` over SSH | Resumable/delta transfer; trailing slash matters |
| Windows/macOS GUI to SSH host | WinSCP, FileZilla, Cyberduck | Drag/drop, queue, reconnect; verify protocol and host key |
| Researcher Desktop persistent user files | OneDrive/ExpanDrive or approved share | VM itself is not backed up |
| Mediaflux beginner workflow | Explorer | HTTPS GUI, upload/download/share |
| Mediaflux large scripted workflow | UniMelb clients | Restartable, parallel, checksum-aware |
| Mediaflux accountless/shareable workflow | Data Mover | Shareable links, pause/resume, GUI/CLI |
| Mediaflux mounted project | SMB or provisioned NFS | Convenient, but filesystem/client semantics vary |
| DaRIS recursive/transcoded export | Shopping cart or `daris-download` | Confirm recursive scope and metadata behavior |
| Cloud-provider synchronization | rclone | Protect OAuth/token config; inspect copy/sync/delete semantics |

## Transfer to or from a Researcher Desktop

Connect to VPN off campus and obtain the current hostname from RCP. Run SSH transfer commands on the local computer, not inside the Researcher Desktop, unless the actual source is reachable only from the desktop.

Upload one file:

```bash
scp <local-file> <uom-username>@<desktop-hostname>:<remote-directory>/
```

Download one file:

```bash
scp <uom-username>@<desktop-hostname>:<remote-file> <local-directory>/
```

Copy a directory recursively:

```bash
scp -r <local-directory> <uom-username>@<desktop-hostname>:<remote-parent>/
```

For restartable/repeated copies, preview with rsync, then remove `--dry-run` only after reviewing the destination:

```bash
rsync -avh --partial --progress --dry-run \
  <local-directory>/ \
  <uom-username>@<desktop-hostname>:<remote-directory>/
```

`source/` copies the contents; `source` copies the directory itself. Do not add `--delete` unless exact mirroring and destination deletions were explicitly authorized.

Other Researcher Desktop routes:

- OneDrive through the supported ExpanDrive workflow.
- Departmental shares under `~/Shares` when mounted for the user.
- Provisioned Research Data Storage paths, after confirming whether the allocation is SMB/CIFS or NFS.
- Mediaflux using a supported GUI/CLI.

Researcher Desktop local/attached storage is not the only copy. After download/upload, verify the persistent copy and required file permissions.

## Transfer to or from Spartan

Run local↔Spartan commands on the local computer. Place shared active data in the correct project path, not another user's home:

```bash
scp <local-file> \
  <spartan-username>@spartan.hpc.unimelb.edu.au:/data/gpfs/projects/<project>/

scp \
  <spartan-username>@spartan.hpc.unimelb.edu.au:/data/gpfs/projects/<project>/<remote-file> \
  <local-directory>/
```

For a directory or retryable transfer:

```bash
rsync -avh --partial --progress --dry-run \
  <local-directory>/ \
  <spartan-username>@spartan.hpc.unimelb.edu.au:/data/gpfs/projects/<project>/<directory>/
```

Before removing `--dry-run`, inspect every path and confirm source/destination direction. Use `--update` only when preserving newer destination files matches the intended policy. Never use `--delete` as a generic cleanup shortcut.

Spartan placement:

- Home: small personal/configuration data.
- `/data/gpfs/projects/<project>`: shared active inputs/results; check group ownership/permissions and current quota.
- `/data/scratch/projects/<project>`: temporary high-I/O data; no backup and subject to purge.
- Job-local `/tmp`: node-local, requested through Slurm and cleaned after the job.
- Mediaflux/approved storage: persistent or inactive data.

Login nodes permit data movement but are shared. Avoid excessive concurrency, CPU-heavy checksum storms, decompression, or transformations there; submit compute-intensive preparation as a job.

## Transfer between Spartan and Mediaflux

Prefer direct Mediaflux↔Spartan transfer instead of routing through a laptop. Check the installed module and client help:

```bash
module spider unimelb-mf-clients
module load unimelb-mf-clients
unimelb-mf-upload --help
unimelb-mf-download --help
unimelb-mf-check --help
```

Use a configuration file containing host/transport/domain/user or a least-privilege token through the supported mechanism. Do not store a password in the file or repository.

Upload from Spartan to Mediaflux with checksum comparison:

```bash
unimelb-mf-upload \
  --mf.config ~/.Arcitecta/mflux.cfg \
  --csum-check \
  --nb-workers 4 \
  --log-dir <log-directory> \
  --dest /projects/<mediaflux-project>/<destination> \
  /data/gpfs/projects/<spartan-project>/<source>
```

Download from Mediaflux to Spartan without overwriting different local files:

```bash
unimelb-mf-download \
  --mf.config ~/.Arcitecta/mflux.cfg \
  --csum-check \
  --nb-workers 4 \
  --log-dir <log-directory> \
  --out /data/gpfs/projects/<spartan-project>/<destination> \
  /projects/<mediaflux-project>/<source>
```

The snapshot recommends no more than four workers even if a client accepts more; verify the current limit. Uploading a changed file at the same Mediaflux path may create a new asset version. Download `--overwrite`, upload `--sync-delete-assets`, download `--sync-delete-files`, and hard-delete flags are high-impact; omit them unless explicitly required and authorized.

Verify a download with Mediaflux as the authoritative side:

```bash
unimelb-mf-check \
  --mf.config ~/.Arcitecta/mflux.cfg \
  --direction down \
  --output <report.csv> \
  /data/gpfs/projects/<spartan-project>/<local-directory> \
  /projects/<mediaflux-project>/<remote-collection>
```

Do not add `--no-csum-check` when checksum verification is required. Review missing/invalid rows and the transfer log before declaring success.

For old Mediaflux data, check `Content → Status`. `OFFLINE` content is on tape; request folder-level restoration in Mediaflux Desktop and wait for completion before retrying. Rsync code 23, SFTP `EOFFLINE`, or failed assets may indicate unrecalled content rather than corruption.

## Use Mediaflux SFTP and rsync

Mediaflux SFTP snapshot details are host `mediaflux.researchsoftware.unimelb.edu.au`, port 22 or 9003, and remote projects under `/Volumes/<project>`. Verify them before use. Username formats differ by identity domain; enter the password/MFA response only at the prompt.

Interactive example:

```bash
sftp -o User=<domain>:<username> mediaflux.researchsoftware.unimelb.edu.au
```

Then use `cd /Volumes/<project>`, `put`, `put -r`, `get`, or `get -r`. Before accepting an unknown host key, verify its fingerprint through an official channel. Keep GUI parallel transfers conservative; the snapshot recommends starting with two and not exceeding four.

Mediaflux rsync uses a service-specific endpoint and is described as beta/use-at-own-risk in the source snapshot. Always dry-run and verify:

```bash
rsync -rvn \
  -e 'ssh -p 6600 -l <domain>:<username>' \
  <local-directory>/ \
  mediaflux.researchsoftware.unimelb.edu.au::projects/<project>/<destination>/
```

Remove `-n` only after checking the complete destination. Never omit the project after `::projects/`; never add `--delete` without explicit approval and a reviewed dry-run. Use `unimelb-mf-check` afterward.

## Use SMB/NFS and cloud tools

- Distinguish Mediaflux SMB, NetApp SMB/CIFS, and NetApp NFS. Their servers, paths, ACLs, identity domains, and support owners differ.
- On Linux, keep credentials in a root-readable file rather than `/etc/fstab`; test a manual mount and then `mount -a` before reboot.
- Disconnect/reconnect SMB sessions after a password change to avoid repeated stale-authentication lockouts.
- NFS usually requires host/network provisioning; SMB access does not imply NFS access.
- For rclone, prefer `copy` for a non-destructive first transfer. `sync`, `move`, and delete-capable flags can remove data. Protect the rclone configuration because it may contain OAuth refresh tokens or access credentials.

## Verify and troubleshoot

Require all applicable checks:

1. Transfer command exited successfully and its log contains no failed/skipped/offline assets.
2. Source/destination file counts, total bytes, and directory layout match expectations.
3. Checksums or the supported verification client report no missing/invalid files.
4. Representative files open, archives test successfully, and the consuming application accepts a small sample.
5. Permissions/group ownership at the destination support the intended collaborators and jobs.

Diagnose by the earliest specific error, not only the final summary:

- Permission denied: identity, role/ACL, directory execute permission, ownership, or read-only destination.
- No space/quota: service quota and filesystem free space; include output/log without secrets.
- rsync code 23: inspect preceding filenames/errors; check Mediaflux tape status, permissions, invalid names, vanished source files, and path length.
- Connection reset/timeout: VPN/network, client timeout, server status, worker count, or an interrupted session; use restartable tools.
- Host key warning: stop and verify; do not blindly replace the known-host entry.
- Checksum mismatch: preserve both copies and logs, retry the specific file to a new destination, then escalate if it repeats.

Keep logs and the source until the data owner confirms the destination is complete and usable. Escalate with timestamp/timezone, source/destination paths, client/version, redacted command, worker count, failed-files report, quota status, and verification output—never credentials or tokens.
