# MRC Unmanaged Cloud and OpenStack

Source snapshot: 2026-08-17. Verify all UI labels, quota, lifecycle timing, image/flavor names, and networks at the [MRC Dashboard](https://dashboard.cloud.unimelb.edu.au/) and current [MRC documentation](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/209420293/MRC+Unmanaged+Cloud+MRC+IaaS).

## Distinguish MRC from RCP

- MRC Unmanaged Cloud is OpenStack infrastructure: users manage projects, instances, OS patching, applications, security groups, networking, data, and backups.
- RCP provides managed products and RCS-maintained base environments. Do not apply RCP lifecycle instructions to an unmanaged MRC instance.

## Manage an allocation/project

- Every instance, volume, object container, database, and advanced-networking resource belongs to the currently selected OpenStack project.
- Only the allocation requester/project manager may see the request under **Allocations → My Requests**; verify the selected project before diagnosing missing quota/resources.
- Use a new allocation for a new project. Use amend/extend for quota, metadata, or duration changes.
- Treat approval time, expiry notices, quota reduction, archive, and deletion timing as volatile. Reopen the allocation page before stating them.
- Request compute, volume, object storage, database, or advanced-networking quota from actual current requirements; do not copy static flavor tables from old examples.

## Launch a Linux instance safely

1. Verify the correct project, available quota, purpose, data plan, and network exposure.
2. Create/select an SSH keypair. Store the private key locally with restrictive permissions and a passphrase; never upload or reveal it.
3. Select a currently supported image and compatible flavor.
4. Prefer `qh2-uom-internal`/the current private UoM network unless public access is necessary and approved.
5. Attach only the security groups required for the service; SSH normally needs TCP 22 from an appropriate source range.
6. Launch, verify state and IP, confirm the expected SSH username from the image documentation, and verify the host key.
7. Patch the OS and configure monitoring/backups before placing a service into use.

## Handle networking

- The snapshot describes `Classic Provider` as equivalent to `qh2-uom-internal`, and `qh2-uom` as public. Verify current names before changing anything.
- Public exposure requires Cyber Security approval. Minimize inbound CIDRs/ports and do not use `0.0.0.0/0` unless the approved service truly requires it.
- Advanced private networks require quota for networks, subnets, routers, load balancers, and/or floating IPs.
- A fixed IP belongs to the instance/network lifecycle. A floating IP can be reassociated; prefer DNS when the need is a stable name rather than an allow-listed IP.
- Detaching/attaching interfaces changes IPs and can remove non-default security groups. Record the old configuration, warn about disconnection, and re-check security groups afterward.

## Manage instances, flavors, and volumes

- Resizing changes flavor and may reboot or fail if quota/capacity is unavailable. Verify backups and application state first.
- A volume persists independently of an instance, but it is not automatically mounted or protected from formatting/deletion.
- Before formatting, resolve the device with `lsblk`, confirm it is the new empty volume, choose the required filesystem, create a mount point, and use UUID in `/etc/fstab` where practical.
- Test `mount -a` before reboot. Never copy an old device name blindly into `fstab`.
- For Windows, use Disk Management to initialize/format only the intended new disk.
- Stop or delete idle resources when appropriate, but distinguish **stop**, **shelve**, **snapshot**, **detach**, and **delete**; a snapshot is not a complete backup strategy.

## Migrate or retire

- For Nectar/MRC or OS migration: inventory dependencies, snapshot/export where supported, create the target, attach/copy data, update network/DNS, test, then delete the old resource only after explicit confirmation.
- For Windows instances, verify the current image, VirtIO drivers, activation, and RDP procedure; old Server 2016 examples are not a default recommendation.
- For an end-of-life OS, prefer a new supported instance plus application/data migration when an in-place upgrade is risky.

## Troubleshoot

- Missing resource: check selected project, quota, allocation status, availability zone, image/flavor compatibility, and capacity.
- SSH/RDP failure: check instance state, IP/network, security groups, VPN/public reachability, username/key, host firewall, and service status.
- Volume issue: check OpenStack attachment, guest device discovery, filesystem, mount point, permissions, and `/etc/fstab` syntax.
- Public-service issue: collect instance/port/security-group/floating-IP/DNS details without changing exposure until the approved design is understood.
