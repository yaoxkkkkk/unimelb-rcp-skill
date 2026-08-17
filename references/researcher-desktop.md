# RCP managed compute

Source snapshot: 2026-08-17. Verify volatile details at the [RCP learning portal](https://rcp.research.unimelb.edu.au/learn/) and the current RCS Knowledge Hub.

## Select the managed product

- Use Researcher Desktop for one person's managed Windows/Linux interactive VM.
- Use Research Server when a managed VM must be shared or host a team workload.
- Use a managed RStudio/Shiny application when the supported portal product matches the workload.
- Use MRC Unmanaged Cloud when the team needs full infrastructure control and can own system administration.
- Use Spartan for batch/parallel/high-memory/HPC GPU work.
- Treat every managed VM and attached working volume as compute space, not the only persistent research-data copy.

## Provision in RCP

1. Open RCP, using the University VPN when off campus if required.
2. For Researcher Desktop, use **My Personal Resources → Add Resource**; do not create an Activity solely for it.
3. For Activity-scoped products such as Research Server or data resources, select/create the correct Activity and verify the RCAO and user privileges.
4. Submit only when the user asked. Wait for required RCAO approval and provisioning before expecting **Create** or **Open**.
5. Inspect the current product page for standard size, attached-volume limits, boost/premium options, GPU reservation duration, and per-user/per-Activity caps.

## Connect

### Windows

1. Use the RCP **Open** action to download the `.rdp` file.
2. Open it with Microsoft Remote Desktop/Windows App or another supported RDP client.
3. Let the user enter University credentials in the official prompt.

Closing the RDP window disconnects the view; it does not normally stop the VM or applications.

### Linux

- For the browser desktop, authenticate through the RCP launch path and select the supported UoM desktop session.
- For SSH, obtain the displayed IP from RCP, verify the host, then use `ssh <uom-username>@<researcher-desktop-ip>`.
- If off campus, verify VPN before changing SSH or firewall settings.

## Work with software and data

- On managed Windows, prefer Software Centre and allow for initial catalogue population.
- On a personal managed Linux VM, use the supported graphical installer or documented `sudo apt` workflow; this differs from Spartan, where `sudo`/`apt` are prohibited.
- Obtain required UoM cybersecurity approval before installing unmanaged third-party software.
- Use OneDrive/SharePoint or an approved RCS data service for persistent copies. Linux desktops may provide ExpanDrive; Mediaflux/SCP/shared drives are alternatives.
- For NVivo, distinguish installing the desktop application from importing a project into NVivo Collaboration Server; verify the current licence workflow.

## Manage lifecycle

- **Resize/boost/GPU change:** verify entitlement and duration; warn that a reboot stops processes and transfers.
- **Soft reboot:** prefer an operating-system restart after saving work.
- **Hard reboot:** use only for an unresponsive VM; warn about filesystem damage/data loss.
- **Shelve:** releases compute while preserving the VM state/disk; verify the current inactivity policy.
- **Delete:** resolve the exact VM and attached volumes, verify persistent copies, explain whether volumes can be retained, then confirm immediately before deletion.
- **OS end of life:** inspect the OS with `lsb_release -a` or `cat /etc/*release*`; verify current vendor/UoM support before recommending migration or in-place upgrade.
- The local snapshot says Windows 10 Research Server/Desktop instances must be replaced or deleted by 2026-10-01 and that Windows 10 → 11 in-place upgrade is unsupported. Reopen the [current migration guide](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/1050181633) before acting.

## Troubleshoot

- Missing **Create**: check pending RCAO approval, Activity privileges, personal-resource limits, and resource state.
- Cannot connect: verify VPN, VM state/IP, RDP/SSH client, security software, and whether a reboot/resize is in progress.
- Unexpected stop: refresh RCP and inspect available actions; warn that processes ended and local-only files may be damaged.
- Software Centre empty: allow the documented first-provisioning interval, then escalate with VM/resource details.
- Before migration: inventory software, licences, paths, shares, attached volumes, and persistent backups; test the replacement before deleting the old VM.
