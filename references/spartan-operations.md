# Spartan operations

Source snapshot: 2026-08-17. Use the current [Spartan Knowledge Hub](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/880345089/Spartan+HPC), especially [submitting jobs](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/957775887/Submitting%2Bjobs), [monitoring jobs](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/958431233/Monitoring%2Bjobs), [scheduling](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/958398465/Understanding%2Bscheduling), [data management](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/957284357/Managing%2Bdata), and [usage policies](https://rcs-knowledge-hub.atlassian.net/wiki/spaces/KB/pages/958595073/Complying%2Bwith%2BSpartan%2Busage%2Bpolicies).

## Contents

- Operating rules
- Inspect before acting
- Build a Slurm script
- Map parallelism to Slurm
- Submit and monitor
- Diagnose pending or failed jobs
- Software, containers, and GPUs
- Data placement and transfer

## Operating rules

- Use login nodes only for editing, lightweight inspection, data movement, supported package installation, and Slurm submission/monitoring.
- Run computation with `sbatch`, `sinteractive`, or Open OnDemand. Administrators may kill compute-heavy login-node processes.
- Do not use `sudo`, `apt`, or `yum`. Use modules, supported user environments, containers, or a software request.
- Read the Message of the Day after login for outages and changes.
- Do not expose unauthenticated TCP services. Prefer Open OnDemand for Jupyter/RStudio and secure any permitted job-local service.
- Remember that the credential owner remains responsible for autonomous actions performed under their account.
- No login-node or compute-node ports are directly exposed externally. For an allowed job-local service, choose a non-privileged available port, authenticate it, and use the documented tunnel/Open OnDemand path.
- Do not use Spartan for an always-on web application; route that requirement to MRC or another supported hosting service.
- Keep account contact information current. Treat account locking, project expiry, and home/project/scratch deletion timing as volatile policy and verify before quoting it.

## Inspect before acting

Use read-only commands first:

```bash
hostname
id
sinfo -s
squeue --me
sacct -X --starttime today --format=JobID,JobName,Partition,State,ExitCode,Elapsed,ReqMem,AllocCPUS
module spider <software>
```

Check current partitions and limits live; do not hardcode a partition merely because it appeared in an older example.

## Build a Slurm script

Place scheduler directives before executable shell commands:

```bash
#!/bin/bash
#SBATCH --job-name=<name>
#SBATCH --partition=<verified-partition>
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=<threads>
#SBATCH --mem=<memory>
#SBATCH --time=<days-hh:mm:ss>
#SBATCH --output=slurm-%j.out
#SBATCH --error=slurm-%j.err

set -euo pipefail
module purge
module load <verified-module>
export OMP_NUM_THREADS="${SLURM_CPUS_PER_TASK}"
<command>
```

Do not invent resource values. Derive them from a small representative run, application documentation, and observed utilization.

## Map parallelism to Slurm

- Serial: `--nodes=1`, `--ntasks=1`, `--cpus-per-task=1`.
- Shared-memory/multithreaded: one task and `--cpus-per-task=N`; set the application's thread count to `SLURM_CPUS_PER_TASK`.
- MPI/distributed: request the appropriate task and node counts and launch with the software's supported `srun`/MPI pattern.
- Independent repeated inputs: use a Slurm array and map `SLURM_ARRAY_TASK_ID` to inputs; limit concurrency when I/O or licences are constrained.
- GPU: verify the current GPU partition, account access, module/toolchain, GPU count syntax, CPU, RAM, and wall-time limits before composing directives.

Adding cores does not accelerate software that lacks corresponding parallelism.

## Submit and monitor

```bash
sbatch <job.slurm>
squeue -j <job-id>
squeue --me
scontrol show job <job-id>
my-job-stats -j <job-id> -a
```

Use `my-job-stats` to compare CPU/GPU/RAM use with the request. For short jobs or automatic end-of-job capture, consult its current `-h` output and Knowledge Hub instructions.

Cancel only with explicit authorization:

```bash
scancel <job-id>
```

Resolve array IDs and job ownership before cancellation.

## Diagnose pending or failed jobs

For pending jobs, inspect `squeue` reason, `scontrol show job`, project/partition access, requested resources, fair-share, and current capacity. Common quota reasons include per-user or per-account CPU/memory limits, but verify the exact Slurm reason.

For failed jobs, collect:

- `State`, `ExitCode`, and elapsed time from `sacct`.
- `slurm-<job-id>.out` and any separate error log.
- Submission script, working directory, inputs, loaded modules, and software version.
- Requested versus observed CPU, GPU, and memory.

`Killed` often indicates memory exhaustion, but confirm with job accounting before increasing `--mem` or `--mem-per-cpu`. Avoid blindly enlarging requests because over-requesting delays scheduling and wastes shared capacity.

## Software, containers, and GPUs

- Search with `module spider`; load an exact available module in the job script.
- Check `/apps/examples/` and the current Spartan examples before inventing module combinations.
- Install supported language packages in user/project environments without monopolizing the login node.
- Follow the current Spartan container instructions; do not assume Docker or root access.
- Treat GPU models, partitions, limits, and CUDA modules as volatile. Check live system status and the current GPU guide.
- Match CUDA/cuDNN and application builds to the assigned GPU architecture. Do not assume a module built for an older GPU supports A100/H100/L40S.

## Data placement and transfer

- Use home for configuration and small personal files, `/data/gpfs/projects/<project>` for shared active inputs/results, `/data/scratch/projects/<project>` for temporary high-I/O data, and job-local `/tmp` only when explicitly requested in Slurm.
- Check current quotas and backup/purge policies with the documented helper commands rather than relying on snapshot values. Scratch and job-local `/tmp` are not backed up.
- Run `check_project_usage` in the user's Spartan session for their accessible projects; do not append a project ID unless the installed command's help explicitly supports one. Use `check_home_usage` for home usage when available.
- Request job-local space with the current `#SBATCH --tmp=<size>` syntax and stage data in/out inside the job; `/tmp` is node-local and removed when the job ends.
- Treat all Spartan storage as computational/working storage, not the only persistent copy.
- Move critical or inactive data to Mediaflux or another approved persistent platform.
- Prefer direct server-to-server transfer instead of routing large data through a laptop.
- On Spartan, verify the current modules for `unimelb-mf-clients`, Mediaflux Data Mover, or Mediaflux Explorer. Use the Mediaflux verification client after important transfers.
- The Knowledge Hub asks users of parallel Mediaflux clients not to exceed four threads; recheck the current page before large transfers.
