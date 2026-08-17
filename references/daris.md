# DaRIS

Source snapshot: 2026-08-17. Verify current access and tooling at [Getting Started with DaRIS](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/5474892/DaRIS) and linked pages.

## Understand the object model

Use the hierarchy **Project → Subject → ExMethod → Study → DataSet**.

- Project contains team/roles and project metadata.
- Subject represents the research subject and can expose public/private metadata by role.
- ExMethod instantiates a Method/workflow; its graph contains steps.
- Study groups data produced for a step.
- DataSet contains file content and format-specific metadata such as indexed DICOM series fields.
- Use the citable ID (CID) to identify an object for clients, imports, downloads, and support.

## Provision and access

1. For a new allocation, create/select the correct RCP Activity, add DaRIS, then request the allocation name/quota.
2. Wait for RCAO approval and **Allocation ready to use** before expecting access.
3. For an existing project, ask its owner/administrator to request access with user identity, project ID, and role.
4. External collaborators need the current University external-access process before DaRIS access is granted.

Role hierarchy from the snapshot:

- **Project Administrator:** full project and user administration; includes Subject Administrator.
- **Subject Administrator:** public/private metadata; includes Member.
- **Member:** public metadata and associated content; includes Guest.
- **Guest:** read-only public metadata/content.

Treat role assignment/removal as an authorized access mutation.

## Use the portal

- Tree view is convenient for smaller hierarchies; list view is paginated and better for large projects.
- The portal can browse metadata, view DICOM/NIfTI, import small datasets, download, search, tag, copy studies, and use shopping carts.
- Browser upload is not recommended for large DICOM datasets.
- Study copy includes descendant DataSets; confirm source and destination Subject/ExMethod before execution.

## Import data

- **DICOM transfer:** the instrument Application Entity and source IP must be registered/allowed; TLS is optional when supported. Coordinate new instrument paths with RCS.
- **CLI HTTP ingest:** install `daris-clients`, configure host/domain/user without storing a password, then use `daris-dicom-ingest --cid <project-or-subject-cid> <path>`.
- **Generic portal upload:** create a primary DataSet under the intended Study in tree/list view.
- For Bruker/ParaVision, distinguish proprietary raw data from DICOM output and verify the destination CID; consult DaRIS support before establishing a new workflow.

## Download and export

- Direct portal download suits one DataSet or a simple recursive archive.
- Shopping cart recursively gathers descendant content and can transcode, create browser archives, or send to a preconfigured SFTP/SCP sink. Metadata is not necessarily included; verify the current behavior.
- `daris-download` supports CIDs, output directory, unarchive policy, content/metadata selection, transcode, AQL filter, workers, overwrite, and DataSet-only modes. Run `daris-download -h` for the installed version.
- `daris-dicom-export-nifti` supports CID/filter/output/name pattern/gzip/JSON/overwrite. Inspect output naming and existing files before `--overwrite`.
- Prefer direct server-to-server transfer for large data; verify completion and destination permissions.

## Configure clients and servlets

Default client configuration is commonly `~/.Arcitecta/mflux.cfg` on Linux/macOS or `%USERPROFILE%\.Arcitecta\mflux.cfg` on Windows, with DaRIS host, HTTPS/443, domain, and user. Omit passwords/tokens from committed files.

DaRIS exposes `object.mfjp`, `main.mfjp`, `shoppingcart.mfjp`, and `dicom.mfjp` for describe/list/download, logon/logoff, cart lifecycle, and DICOM file/image/view/metadata operations. Read the installed/current servlet description before integrating; do not copy stale argument lists blindly.

## Troubleshoot safely

- Capture user/domain, project/CID, role, portal view or client/version, exact operation, time/timezone, full error, and destination path.
- For DICOM ingest, check AE registration, source IP/firewall, destination CID, format, and size.
- For shopping carts, check cart state, recursive scope, transcode, sink, remote path, credentials entered only in the official UI, and destination quota/permissions.
- Treat patient identifiers, private Subject metadata, DICOM headers, deletion, role changes, and cross-project copies as sensitive/high-impact.
