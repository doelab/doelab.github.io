---
title: SERVER USER GUIDE
date: 2024-07-31 19:03:00 +0700
categories: [Guide, Server]
tags: [slurm,guide,faq,server]     # TAG names should always be lowercase
author: haicao
toc: true
comments: false
description: This guide serves as a comprehensive manual for accessing and utilizing the centralized server infrastructure provided by the DOE lab for academic purposes.
---
# SERVER USER GUIDE

| Author      | Email                     | Version |
|-------------|---------------------------|:-------:|
| Hai Cao     | cxhai.sdh221@hcmut.edu.vn | 1.0     |
| Hung Nguyen | ngthung@hcmut.edu.vn      | 2.0     |

## Overview

This guide serves as a comprehensive manual for accessing and utilizing the centralized server infrastructure provided by the DOE lab for academic purposes. The server centralizes all necessary software tools, significantly enhancing the educational experience for students and teaching assistants (TAs). By housing these tools on a dedicated server, we eliminate the need for students to install large, potentially incompatible software on their laptops. This setup not only resolves compatibility issues but also streamlines the monitoring and supervision processes for TAs to effectively assist and oversee student activities. To ensure optimal performance and equitable access to server resources, both students and TAs have to follow the workflow provided in this guide. This document outlines the procedures for accessing the server, submitting jobs using the SLURM workload manager, and utilizing various server resources.

## Remote Access

Accessing the DOE server requires a secure connection facilitated by a VPN client (Wireguard). At the beginning of each semester, TAs will send an email providing students with the necessary VPN configuration and user account credentials. Students must meticulously follow the provided configuration guide to establish a stable connection.

The necassary configuration file is a *.conf file. Do not under any circumstance public this file outside of the class usage, as it contains sensitive information.

For Windows: [Wireguard Installation Guide](https://www.wireguard.com/install/)
For MacOS: install brew then run `brew install wireguard-tools` to install Wireguard. Then use wg-quick up/down <path-to-config-file> to connect/disconnect.
For Linux: install Wireguard using the package manager of your distribution. Then use wg-quick up/down <path-to-config-file> to connect/disconnect.

> _Note:_ Some routers may be configured to block VPN connections, which can prevent students from connecting to the DOE server. If this occurs, try using a cellular network or a different internet connection. Please note that HCMUT01 and HCMUT02 is known to block VPN connections.

After establishing a VPN connection, students test ping their corresponding login server. E.g `ping anthony.doelab.site` for the Anthony login server.

You can then use Remote Desktop Protocol (RDP) to connect to the server. For Windows, you can use the built-in Remote Desktop Connection application. For MacOS, you can use the Microsoft Windows application available on the App Store. For Linux, you can use Remmina or any other RDP client. Then connecto to the login server domain name (e.g anthony.doelab.site) with your provided username and password. You are encourage to change your password after the first login.

You can also use SSH to connect to the server. For Windows, you can use the built-in OpenSSH client. For MacOS and Linux, you can use the built-in SSH client. Then connect to the login server domain name (e.g anthony.doelab.site) with your provided username and password. E.g `ssh <username>@anthony.doelab.site`

In addition, it is possible to use a SFTP File Explorer to ease file exchange between your local machine and the server. For Windows, MacOS, Linux, you can use FileZilla. Then connect to the login server domain name (e.g sftp://anthony.doelab.site) with your provided username and password.

Do not change your home structure/bash library without permission. If you need to install software, please contact your advisor.

## DOE Resources

The server includes multiple compute nodes, each equipped with varying computational resources to support a wide range of academic applications. 

Below is a summary of the available nodes and their resource allocations (At this moment, please contact your advisor to know the name of the compute node to connect): // TODO: Update the table

| Node/Partition | Max CPU | Max RAM | Max Time | Running Jobs/User | Submit Jobs/User |
|----------------|:-------:|:-------:|:--------:|:-----------------:|:----------------:|
| `list updating`     | 3       | 15G     | 7 days   | 2                 | 5                |


Each node's specifies the maximum number of CPUs, the maximum amount of RAM, and the maximum allowable runtime for a job. Additionally, there are also the limits on concurrent running jobs and job submissions per user to thwart resource monopolization and ensure fair distribution among all users.

## Simple connection

After login to the login server, you can use a simple slurm command to instant the compute node. E.g:

``srun --x11 --partition=triton --account=comp --time=0:10:00 --mem-per-cpu=1G --cpus-per-task=1 --pty bash``
This command will allocate a compute node in the `triton` partition for 10 minutes with 1GB of memory per CPU and 1 CPU.

For the CPU flags, most can be compute with 1 core. Use ``-c 2`` for 2 cores, ``-c 1`` for 1 cores, etc. (in replace for the --cpu-per-task flag).

## Batch Jobs and Scripting

The main way to run applications on the server is sending a batch job while at the login node, which executes on a compute node with minimal interaction. Batch jobs are suitable for computationally intensive tasks that take a long time to complete, require little to no user input, and don't need extensive monitoring.

The workflow for Batch Jobs is:
1. Create a Job Script
2. Submit the Job Script
3. Monitor the Job using SLURM commands
4. Check the Job output

### Create a job script

Even though it's possible to run jobs directly from the command line, it can be tedious and disorganized. Therefore, students are encouraged to use job scripts for batch jobs. A job script contains Linux commands and resource requirements, which are passed to the SLURM job scheduler. SLURM then executes the job according to these specifications, running all commands included in the script.

A job script may look something like this:

```bash
#!/bin/bash --login

#SBATCH --job-name=example
#SBATCH --account=comp
#SBATCH --cpus-per-task=1
#SBATCH --time=00:02:00
#SBATCH --mem-per-cpu=10M
#SBATCH --partition=triton
#SBATCH --out=./out/example-%j.out

# Purge all modules and load the neccessary ones
module purge
module load tmux

# Beginning of scripts

echo "== Begining of the example =="

echo "[1] Print the tmux version then sleep for 10 second"
tmux -V
sleep 10

echo "[2] Print the hostname on which this script is running"
hostname

echo "== End of the example =="
```

After submitting the job script, an output file named `example-xxx.out` is created in the directory `out` with the content:

```bash
== Begining of the example ==
[1] Print the tmux version then sleep for 10 second
tmux 3.2a
[2] Print the hostname on which this script is running
triton.doelab.site
== End of the example ==
```

#### Directives
The beginning of this job script indicates that it uses `bash` with the flag `--login` to access the compute node `triton`.

The subsequent comments provide SLURM with information to allocate resources for this job. Each comment contains the indicator `#SBATCH` and a SLURM flag. Students must customize these flags according to their specific needs for proper allocation.

| Flag| Description |
| --- | --- |
| `--job-name=job_name`      | Specifies the name of the job. It must not contain spaces or special characters. |
| `--account=username`       | Indicates the student's username.|
| `--cpus-per-task=N`        | Allocates N CPU core per task. |
| `--time=D-HH:MM:SS`        | Defines the maximum job run time. |
| `--mem-per-cpu=10MB`       | Specifies the amount of memory allocated per CPU core (e.g., 10MB). |
| `--partition=compute_node` | Selects the partition (compute node) where the job will be executed. |
| `--output=output_file`     | Defines the output file path and name for job logs. |

> _Note:_ In the example, the output file is put into `out` directory, this directory has to be existed.

#### Software loading

Before running a job, the environment should be cleaned, and necessary software loaded. This is done using `module purge` to clear existing settings, followed by `module load <software>` to load required modules.

#### User scripting

This section contains the commands to execute the job. These can range from basic commands to complex scripts.

To use SLURM environment variables in the job scripts (such as `%j`), refer to [the documentation](https://slurm.schedmd.com/sbatch.html)


### Submit a job script

To submit a job script to the SLURM scheduler, use the `sbatch` command followed by the script file's name:

```bash
sbatch example.submit
```

This command queues the job for execution.
SLURM will schedule it based on resource availability and queue policies, ensuring efficient utilization of the server's capabilities.

### Monitor a job script

Refer to the section **Useful Commands**.

### Practice

You may run this command to copy that example to your home directory and try to run it.

```bash
cp -rf /earth/commom/job-script-example ~/
cd ~/job-script-example
```

## Interactive Jobs

### Interactive mode

While batch jobs are ideal for non-interactive tasks, interactive jobs are necessary when real-time interaction is required, such as during debugging or designing schematic.
Interactive jobs allow users to directly interact with the compute environment.
However, due to resource limitations and fair usage policies, users are encouraged to minimize the use of interactive sessions.

#### First workflow

**1. Allocate resources**

To allocate resources in a compute node and submit that request to the SLURM scheduler, use the `salloc` command followed by several flags:

```bash
salloc --x11 --partition=triton --account=comp --time=0:10:00 --mem-per-cpu=1G --cpus-per-task=1
```

This command queues the allocation request for execution.
The flags are the same as in Batch Jobs, but the `--x11` flag indicates that students can use GUI while in the allocated compute node.
If this flag is omitted, any attempts to open GUI will be denied.

**2. Access the shell in the allocated compute node**

To access the shell in the allocated partition, use the `srun` command followed by the shell:

```bash
srun --pty bash
or
srun --pty zsh
```

#### Second workflow

**1. Directly access the shell**

To access the shell without `salloc`, `srun` has to contain the flags similar to `salloc` in addtion to `--pty bash`.

```bash
srun --x11 --partition=triton --account=comp --time=0:10:00 --mem-per-cpu=1G --cpus-per-task=1 --pty bash
```

### `tmux`

To avoid allocating a new interactive job just to open another terminal, students are encouraged to use `tmux`.
The `tmux` utility can be employed to manage multiple terminal sessions within a single interactive job, conserving resources.

#### Key concepts of `tmux`
1. **Sessions**: tmux sessions can contain multiple windows, each running independent shell instances.
2. **Windows**: Similar to browser tabs, windows can host multiple panes.
3. **Panes**: Panes are subdivisions within a window, allowing for multiple terminal views in a single window.

#### Basic tmux Commands

- **Sessions**:
  - Start a new session: `tmux`
  - Detach from a session: `[Ctrl-B] + [D]`
  - Reattach to a session: `tmux attach`

- **Panes**:
  - Split vertically: `[Ctrl-B] + [%]`
  - Split horizontally: `[Ctrl-B] + ["]`
  - Navigate between panes: `[Ctrl-B] + arrow keys`
  - Close a pane: `[Ctrl-B] + [X]`

- **Windows**:
  - Create a new window: `[Ctrl-B] + [C]`
  - Rename a window: `[Ctrl-B] + [,]`
  - Navigate between windows: `[Ctrl-B] + [N]/[P]`
  - Close a window: `[Ctrl-B] + [&]`

For advanced functionality, refer to [the tmux cheat sheet](https://tmuxcheatsheet.com/).

## Useful Commands

### View queue information with `squeue`

The `squeue` command retrieves information about jobs in the queue. By default, it displays the job ID, partition, username, job status, number of nodes, and node names for all jobs queued or running in SLURM.

```bash
squeue
```

To only see your queue jobs, use the flag `--me`.
```bash
squeue --me
```

You can also format the output using its flags, for example:
```bash
squeue --format="%.18i %.9P %.30j %.8u %.8T %.10M %.9l %.6D %R" --me
```

The squeue command details a variety of information on an active job’s status with state and reason codes. 

#### STATE codes 
Job state codes describe a job’s current state in queue (e.g. pending, completed). 

| Status | Code | Explaination |
| --- | --- | --- |
| `COMPLETED`  | `CD` | The job has completed successfully. |
| `COMPLETING` | `CG` | The job is finishing but some processes are still active |
| `FAILED`     | `F`  | The job terminated with a non-zero exit code and failed to execute. |
| `PENDING`    | `PD` | The job is waiting for resource allocation. It will eventually run. |
| `PREEMPTED`  | `PR` | The job was terminated because of preemption by another job. |
| `RUNNING`    | `R`  | The job currently is allocated to a node and is running. |
| `SUSPENDED`  | `S`  | A running job has been stopped with its cores released to other jobs. |
| `STOPPED`    | `ST` | A running job has been stopped with its cores retained. |

#### REASON codes
Job reason codes describe the reason why the job is in its current state.

| Reason code | Explaination
| --- | --- |
| `Priority`                | One or more higher priority jobs is in queue for running. Your job will eventually run. |
| `Dependency`              | This job is waiting for a dependent job to complete and will run afterwards. |
| `Resources`               | The job is waiting for resources to become available and will eventually run. |
| `InvalidAccount`          | The job’s account is invalid. Cancel the job and rerun with correct account. |
| `InvaldQoS`               | The job’s QoS is invalid. Cancel the job and rerun with correct account. |
| `QOSGrpCpuLimit`          | All CPUs assigned to your job’s specified QoS are in use; job will run eventually. |
| `QOSGrpMaxJobsLimit`      | Maximum number of jobs for your job’s QoS have been met; job will run eventually. |
| `QOSGrpNodeLimit`         | All nodes assigned to your job’s specified QoS are in use; job will run eventually. |
| `PartitionCpuLimit`       | All CPUs assigned to your job’s specified partition are in use; job will run eventually. |
| `PartitionMaxJobsLimit`   | Maximum number of jobs for your job’s partition have been met; job will run eventually. |
| `PartitionNodeLimit`      | All nodes assigned to your job’s specified partition are in use; job will run eventually. |
| `AssociationCpuLimit`     | All CPUs assigned to your job’s specified association are in use; job will run eventually. |
| `AssociationMaxJobsLimit` | Maximum number of jobs for your job’s association have been met; job will run eventually. |
| `AssociationNodeLimit`    | All nodes assigned to your job’s specified association are in use; job will run eventually. |

For more information, refer to [the SLURM documentation](https://slurm.schedmd.com/squeue.html).


### Cancel jobs with `scancel`

To cancel a job that is currently queued or running, use the `scancel` command followed by the job ID:
```bash
scancel job_id
```

To cancel multiple jobs, list the job IDs separated by commas:
```bash
scancel job_id_1,job_id_2,job_id_3
```

### Analyze past jobs `sacct`

The `sacct` command provides detailed accounting information for completed jobs, which can be useful for auditing resource usage and identifying bottlenecks.
```bash
sacct
```

By default, `sacct` only shows jobs from the current day. Use the `--starttime` flag to look up older jobs:
```bash
sacct --starttime=YYYY-MM-DD
```

You can even format the output like `squeue`, for example:
```bash
sacct --format JobID,JobName,Elapsed,NCPUs,TotalCPU,CPUTime,ReqMem,MaxRSS,MaxDiskRead,MaxDiskWrite,State,ExitCode,User,Account
```

For more information, refer to [the SLURM documentation](https://slurm.schedmd.com/sacct.html).

### View the overview of compute nodes with `sinfo`

The `sinfo` command provides an overview of the SLURM nodes and partitions, including their availability, current load, and status.


```bash
sinfo
```

### Control jobs with `scontrol`

`scontrol` is a versatile command that allows for detailed control over job and node configurations within the SLURM environment. It is typically used by advanced users and administrators for the advanced control over jobs it offers, such as suspending, holding, or releasing jobs, and retrieving detailed status information.

```bash
scontrol suspend job_id
```

To resume a suspended job:
```bash
scontrol resume job_id
```

To hold a queued job:
```bash
scontrol hold job_id
```

To release a held job:
```bash
scontrol release job_id
```

To view detailed job information:
```bash
scontrol show job job_id
or
scontrol show job job_id > outputfile.txt
```

For more information on these commands, consult [the SLURM documentation](https://slurm.schedmd.com/scontrol.html).

### Check the disk usage with `quota`

The `quota` command is essential for monitoring disk usage and ensuring that users do not exceed their allocated storage limits. Exceeding these limits can prevent further job submissions and disrupt workflows.

```bash
quota -s
```
The `-s` flag formats the output in a human-readable way.

A typical output might look like this:

```bash
Disk quotas for user comp (uid 1100): 
     Filesystem   space   quota   limit   grace   files   quota   limit   grace
   earth:/earth   5440K   9216M  10240M             268       0       0        
```

1. `quota`: This is the soft limit. If usage exceeds this limit, users are warned to delete files to free up space, but they can still write data.
2. `limit`: This is the hard limit. Users cannot write any more data once this limit is reached.
3. `grace`: This shows the time remaining before the excess usage must be reduced to comply with the quota.

## FAQ

Question 1. **Why do I receive this error?**
```bash
sbatch: error: AssocMaxSubmitJobLimit
sbatch: error: Batch job submission failed: Job violates accounting/QOS policy (job submit limit, user's size and/or time limits)
```
This error indicates that you have reached the maximum number of jobs you are allowed to submit.
Please wait until some of your current jobs are completed.
Refer to the DOE Server Resources section to review the job submission limits applicable to your account.

Question 2. **Why can I access the server but my group member received this error?**
```bash
Could not acquire name on session bus
```
Only one session can be acquired at once. You must discuss with your friends to resolve this conflict.

Question 3. **My session is hung/frozen, and I can't connect to it anymore. What should I do?**

In this situation, you have to kill the program(s) leading to the problem. If you can't find the root cause, just kill all your works.\
First, use Terminal or Powershell to connect to the server
```bash
ssh <your username>@<your login>
```
Then, run the following command
```bash
killall -u `whoami`
```
All of your running programs on the server would be terminated, and you're supposed to have a fresh session.

Question 4. **I'm getting tired of typing out these lengthy commands. Is there a shortcut or easier way to do this?**

Typing long commands can be time-consuming and prone to errors. To streamline your workflow, consider using aliases and Makefiles.
Research online to learn how to create these tools.
Here's an example of an alias to simplify interactive job submissions: \
Assuming the username is `comp`, the target compute node is `triton`, the job will run for 2 hours, requires 1GB of memory per CPU, and uses 1 CPU, you can add the following alias to your `.bashrc` file:
```bash
cat "alias srun_triton=srun --x11 --partition=triton --account=comp --time=0:10:00 --mem-per-cpu=1G --cpus-per-task=1 --pty bash" >> ~/.bashrc
```
Now, instead of typing the entire srun command, you can simply use `srun_triton` to access the triton compute node with the specified parameters.
```bash
srun_triton
```
For more advanced users, customize your shell configuration files to suit your specific needs.

Question 5. **I use a GUI tool that say "24-bit color is not supported, please use 16-bit", I changed my RDP to 16-bit but now it doesn't let me login/close the RDP suddenly**

This is a known issue with the RDP client. It happens because the previous RDP session is still running in the background with 24-bit color.

To resolve this, you can use the following command to kill the RDP session:
```bash
ssh <your username>@<your login> pkill -f xrdp
```

or you can use the following command to kill the RDP session:
```bash
ssh <your username>@<your login> 
htop 
```
Then, find the xrdp process and kill it.

Then, you can reconnect to the server.

Question 6. **Where are the tools? I cannot find any.**

You need to RDP/SSH to the login node and `srun` to the compute node. The tools are installed in the compute node, not the login node.

You can see available tools by running the following command:
```bash
module avail
```
and load the tools you need by running the following command:
```bash
module load <tool_name>
```

Question 7. **I cannot connect to the server.**

Please ensure you read the Remote Access section carefully. If you still cannot connect, please contact your advisor.

