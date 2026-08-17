# SSH access to UniMelb research computing

Source snapshot: 2026-08-17. Verify hostnames, login methods, VPN requirements, key-enrolment paths, and supported clients in the current [RCP](https://rcp.research.unimelb.edu.au/) and [Spartan getting-started guide](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/958070789/Getting%2Bstarted%2Bwith%2BSpartan).

## Contents

- Identify the target
- Prepare the client and credentials
- Connect to a Linux Researcher Desktop
- Connect to Spartan
- Configure repeatable access
- Use keys and agent forwarding safely
- Troubleshoot systematically

## Identify the target

Do not interchange credentials or hosts.

| Target | Host | Username | Normal authentication | Network |
| --- | --- | --- | --- | --- |
| Linux Researcher Desktop | Hostname shown after RCP **Open**, commonly `vm-...rc.cloud.unimelb.edu.au` | University username | University credentials in the snapshot | UoM network/VPN normally required |
| Spartan HPC | `spartan.hpc.unimelb.edu.au` | Spartan username | Spartan password created during signup, or enrolled public key | Internet-reachable SSH; verify current access policy |
| MRC Linux instance | IP/DNS from the selected OpenStack project | Image-specific account such as `ubuntu` | Private key injected at launch | Depends on private/public network and VPN/security groups |

Before connecting, resolve the exact target, expected username, authentication method, and whether the resource is running. Never guess a username from an email address.

## Prepare the client and credentials

- macOS/Linux: use current OpenSSH in Terminal.
- Current Windows: prefer built-in OpenSSH when available; PuTTY, MobaXterm, WinSCP, or FileZilla are alternatives for supported protocols.
- Upgrade obsolete clients when they fail algorithm negotiation; do not weaken server/client algorithms to accommodate legacy software.
- Keep private keys only on trusted local devices, protected by a passphrase. Never send a private key to Spartan, a Researcher Desktop, Git, chat, or a support ticket.
- Protect OpenSSH key material:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/<private-key>
chmod 644 ~/.ssh/<public-key>.pub
```

Generate a dedicated key with a current RCS-supported algorithm. A common OpenSSH example is:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_unimelb
```

If the target requires RSA, use the currently documented RSA size, protect it with a passphrase, and keep public/private filenames distinct. Upload or append only the `.pub` content.

## Connect to a Linux Researcher Desktop

1. Connect to the University VPN when off campus.
2. Open the Researcher Desktop tile in RCP.
3. Copy the hostname from the opened browser URL; do not copy the entire `https://` URL or path. On the desktop itself, `hostname -f` shows the fully qualified hostname.
4. Connect from the local computer:

```bash
ssh <uom-username>@<desktop-hostname>
```

5. Enter University credentials only in the SSH prompt. Do not put the password in the command, config file, shell history, or automation.

For verbose diagnosis without changing the server:

```bash
ssh -vvv <uom-username>@<desktop-hostname>
```

Closing SSH or the browser disconnects the view; it does not normally stop the VM. Before assuming an SSH fault, verify RCP shows the desktop as available and that browser/FastX access works.

## Connect to Spartan

1. Confirm the user has both an approved HPC Account and membership in an HPC Project.
2. Connect with the Spartan username, not automatically the central UoM username:

```bash
ssh <spartan-username>@spartan.hpc.unimelb.edu.au
```

3. Enter the Spartan password created during HPC signup, unless a public key is already enrolled. Reset that password through RCP; never ask the user to reveal it.
4. Read the Message of the Day and verify the login host. The SSH session lands on a login node.
5. Use the login node only for editing, lightweight inspection, data movement, package setup allowed by policy, and Slurm submission/monitoring. Use `sbatch`, `sinteractive`, or Open OnDemand for computation.

When the current RCS guide permits direct public-key enrolment, prefer `ssh-copy-id` if installed:

```bash
ssh-copy-id -i ~/.ssh/<public-key>.pub <spartan-username>@spartan.hpc.unimelb.edu.au
```

Otherwise follow the current RCP/Spartan public-key process. Confirm `~/.ssh` is mode `700` and `authorized_keys` is mode `600`; never copy the private key.

## Configure repeatable access

Create `~/.ssh/config` on the local computer and protect it with mode `600`:

```sshconfig
Host unimelb-rd
    HostName <desktop-hostname>
    User <uom-username>
    ServerAliveInterval 120

Host unimelb-spartan
    HostName spartan.hpc.unimelb.edu.au
    User <spartan-username>
    ServerAliveInterval 120
    IdentityFile ~/.ssh/<spartan-private-key>
    IdentitiesOnly yes
```

Then use `ssh unimelb-rd` or `ssh unimelb-spartan`. Omit `IdentityFile`/`IdentitiesOnly` when password authentication is intended. Update the Researcher Desktop hostname if RCP shows a replacement resource.

## Use keys and agent forwarding safely

- Add a passphrase-protected local key to the local agent with `ssh-add ~/.ssh/<private-key>`.
- Use agent forwarding only when the Spartan session must authenticate onward to another SSH host and the risk is understood:

```bash
ssh -A <spartan-username>@spartan.hpc.unimelb.edu.au
```

- Do not set `ForwardAgent yes` under `Host *`; scope it to the one trusted host and period where it is required.
- Agent forwarding does not copy the private key, but a compromised remote host may use the forwarded agent while the session is active. Prefer direct endpoint-to-endpoint transfer or a narrowly scoped deploy key/token when possible.

## Troubleshoot systematically

1. Reproduce with the exact hostname and `ssh -vvv`; redact usernames/paths only when necessary, and always redact tokens or private-key material.
2. Classify the failure:
   - **Could not resolve hostname:** wrong hostname, copied URL/path, DNS/VPN problem.
   - **Connection timed out / no route:** Researcher Desktop VPN/state, MRC network/security group, local firewall, or outage.
   - **Connection refused:** host reachable but SSH service/listener unavailable; verify resource state before rebooting.
   - **Permission denied:** wrong username, wrong password domain, locked/unapproved account, unenrolled public key, wrong key, or key permissions.
   - **Too many authentication failures:** too many agent keys; retry with `IdentitiesOnly=yes` and the intended `-i <key>`.
   - **Algorithm negotiation failed:** obsolete client; update it rather than enabling weak algorithms.
   - **REMOTE HOST IDENTIFICATION HAS CHANGED:** stop and verify whether the VM was rebuilt or the hostname/IP reassigned. Only after verification remove the stale entry with `ssh-keygen -R <hostname>`.
3. For Researcher Desktop, verify VPN, RCP state, current hostname, and browser access.
4. For Spartan, verify account/project approval, password reset state, current service status, and whether the failure affects all clients.
5. Escalate with timestamp/timezone, target hostname, client/version, network/VPN state, exact redacted error, and the relevant RCP resource or Spartan username; omit passwords, MFA codes, and keys.
