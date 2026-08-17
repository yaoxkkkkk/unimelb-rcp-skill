# Data transfer and storage

Source snapshot: 2026-08-17. Reopen the service-specific pages before using hostnames, ports, module names, thread limits, share paths, or token roles.

## Define the transfer first

Record:

- Source and destination service/host/path, direction, data size/file count, sensitivity, and owner.
- One-time versus repeat/sync workflow, restartability, metadata needs, and acceptable downtime.
- Authentication method without collecting the secret.
- Overwrite/delete behavior, expected destination layout, quota/free space, and verification method.

Prefer direct server-to-server transfer for large data. Do not route through a laptop unless an endpoint cannot initiate/receive the transfer.

## Choose the method

| Scenario | Starting method | Notes |
| --- | --- | --- |
| Small ad hoc SSH transfer | `scp`/SFTP | Simple; limited restart/sync behavior |
| Repeated SSH filesystem sync | `rsync` | Use dry-run; delete flags are destructive |
| Mediaflux beginner workflow | Explorer | GUI, HTTPS, sharing |
| Mediaflux large scripted transfer | UniMelb CLI | Restartable; use `unimelb-mf-check`/checksums |
| Mediaflux accountless/shareable workflow | Data Mover | Upload/download shareables; robust parallel transfer |
| Mounted Mediaflux project | SMB or provisioned NFS | SMB is UoM network/VPN scoped; filesystem semantics vary |
| Cloud-to-Mediaflux/remote sync | rclone | Protect config tokens; verify provider flags and checksums |
| DaRIS recursive/transcoded export | Shopping cart or `daris-download` | Confirm recursive scope and metadata behavior |
| Spartan active data | `scp`, `rsync`, Mediaflux module/client | Run transfer appropriately; do not compute on login nodes |
| Conventional network file share | NetApp SMB/CIFS or NFS | Use the exact provisioned service/path, not Mediaflux SMB paths |

## Place Spartan data correctly

- Home: small personal/configuration data; not shared project storage.
- `/data/gpfs/projects/<project>`: shared active project data; check group permissions and current quota/backup policy.
- `/data/scratch/projects/<project>`: high-speed temporary data; no backup and subject to current purge policy.
- Job-local `/tmp`: fastest/node-local and cleaned after the job; request it with the current Slurm `--tmp` syntax.
- Persistent/inactive data belongs in Mediaflux or another approved data service.

## Use Mediaflux protocols safely

- Prefer HTTPS clients for strong integrity/restart behavior.
- Keep parallel workers within the current documented service limit; the local snapshot warns Spartan Mediaflux users not to exceed four threads, but recheck before transfer.
- Use least-privilege token roles and application/path restrictions. Never paste a token into a command that will be logged or committed.
- SMB password changes can leave stale sessions causing account lockouts; disconnect and reconnect mounts after a credential change.
- Check tape status before bulk access to old data; recall OFFLINE collections through Mediaflux Desktop.

## Use SMB/NFS mounts safely

- Distinguish Mediaflux SMB, NetApp SMB/CIFS, and NetApp NFS; their servers, export paths, identity domains, ACLs, and support owners differ.
- On Linux, use a root-readable credentials file rather than embedding a password in `/etc/fstab`.
- Resolve UID/GID, mount point, protocol version/options, and network/VPN reachability before mounting.
- Test a manual mount before making it persistent. Validate `fstab` with `mount -a` before reboot.
- NFS is normally provisioned for specific hosts/networks; do not assume a share is reachable merely because SMB access exists.

## Verify and recover

1. Compare source/destination file counts, total bytes, and expected directory layout.
2. Use checksums or the supported verification client for important data; record failures and skipped/offline files.
3. For sync/delete workflows, run a dry-run and inspect every delete/overwrite before authorization.
4. Preserve logs and resumable state until the owner confirms the destination is usable.
5. Do not delete the source merely because the transfer command exited zero; verify at the application/data level when formats or archives were transformed.
