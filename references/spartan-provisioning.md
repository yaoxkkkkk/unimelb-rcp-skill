# Spartan provisioning and access

Source snapshot: 2026-08-17. Verify current details at the [RCP Spartan guide](https://rcp.research.unimelb.edu.au/learn/314114052/Spartan%20HPC/) and [Getting started with Spartan](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/958070789/Getting%2Bstarted%2Bwith%2BSpartan).

## Prerequisites

Require both:

1. A personal **HPC Account**.
2. Membership in an **HPC Project**, either newly requested or shared by a project lead.

Spartan is a RHEL9-based Linux cluster. Confirm that proposed software supports Linux/RHEL9 before promising suitability.

Confirm current UoM/RCS eligibility, a valid institutional email, supervisor/department details, and RCAO responsibility. External collaborators must complete the official external-access process before a Spartan account/project membership can be granted.

## Request an HPC Account

1. Open RCP Home.
2. Under **My Personal Resources**, choose **Add Resource**.
3. Add **HPC Account** and choose **Request an HPC Account**.
4. Complete the official form, including its requested password, phone, preferred shell, and special access needs.

Let the user enter credentials directly into RCP. Do not collect them in chat or automation logs. Do not promise an approval time; quote the current page if timing matters.

## Request an HPC Project

1. Create or select the RCP Activity that represents the research.
2. Wait for required Activity/RCAO and HPC Account approvals.
3. Under that Activity, add **HPC Project**.
4. Request the project with a meaningful title and a substantive description.

Include in the description:

- Research goal and project/thesis summary.
- Software and workflow.
- Compute, GPU, and current storage requirements.
- Estimated duration.
- Named project participants.
- Valid RCAO identity.

Minimal one-line descriptions are declined. Request storage based on current need, not an unsupported lifetime maximum. The current guide describes a typical 500 GB project quota; verify before relying on it.

## Add collaborators

1. Open the Activity containing the HPC Project.
2. Choose **Manage Activity** then **Manage Users**.
3. Add the collaborator's UoM email and assign only necessary privileges.
4. Apply changes.
5. Verify that every collaborator also has an active HPC Account.

Sharing occurs at Activity scope and can expose every resource in that Activity. If only the HPC Project should be shared, place it in a separate Activity. Confirm before removing a user because access loss may interrupt work.

Keep account contact information current. The source snapshot says bounced email can lock an account and that projects with no valid unlocked users can expire; verify the current retention/deletion policy before quoting a deadline.

## Log in and reset access

Use the Spartan account username and the password created during HPC signup; it is distinct from the user's University password.

```bash
ssh <spartan-username>@spartan.hpc.unimelb.edu.au
```

For Windows, use a current OpenSSH client, PuTTY, or MobaXterm. Obsolete SSH clients can fail algorithm negotiation.

Reset the Spartan password through RCP Home > HPC Account **Manage** > **Change Password**. Never ask the user to reveal it.

For SSH keys:

- Generate and retain the private key locally with a passphrase.
- Add only the public key to Spartan.
- Never store an unencrypted private key on Spartan.
- Use agent forwarding only when necessary and after considering its security implications.

Do not upload a private key to Spartan. Do not put a Spartan password into a script, scheduler directive, support ticket, or automation log.

## Manage or retire resources

- Use HPC Account **Manage** for account details, password, software access, or cancellation.
- Use HPC Project **Manage** for project details, quota requests, or cancellation.
- Treat account/project cancellation and collaborator removal as destructive. Resolve the target, check jobs and persistent data, explain access consequences, and confirm immediately before execution.
