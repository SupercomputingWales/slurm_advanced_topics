---
title: "Requesting resources"
teaching: 0
exercises: 0
questions:
- "Key question (fixed)"
objectives:
- "First learning objective. (fixed)"
keypoints:
- "First key point. Brief Answer to questions. (fixed)"
---

## How to request resources
Setting the correct resource requirements will help to ensure a faster turnaround time on the supercomputer. For this users need to interact with SLURM, the supercomputer job scheduler, that is in charge of allocating resources to jobs from all users.

There are two ways to request resources:
- Interactive access
- Prepare and submit a job script

### Interactive jobs
*srun* can be used to request nodes for interactive use.
> 
>
>
>
Run example 3 and follow the instructions. Pay attention to the effect that srun flags have on SLURM environment variables.


### Job scrips

~~~
#!/bin/bash --login
#SBATCH -J my.job
#SBATCH -o %x.o.%J
#SBATCH -e %x.e.%J
#SBATCH --ntasks=5
#SBATCH --ntasks-per-node=5
#SBATCH -p dev
#SBATCH --time=00:01:00
#SBATCH --account=scwXXXX

# some commands specific to your job
# for example:
env
echo “Hello World!”
~~~
{: .language-bash}

In the above Bash script pay attention to the `#SBATCH` entries, these are calls to *sbatch*, the program in charge to submit batch jobs to SLURM. This is were users specify how much resource to request. In this example the script is requesting: 5 tasks, 5 tasks to be run in each node (hence only 1 node), resources to be granted in the *dev* partition and maximum runtime of 1 minute. 

The table below show some common *sbatch* commands:
<table style="width:100%">
 <tr>
  <th>Command name</th>
  <th>Command short name</th>
  <th>Description</th>
 </tr>
 <tr>
  <td>--job-name=</td>
  <td>-J</td>
  <td>Job name</td>
 </tr>
 <tr>
  <td>--output=</td>
  <td>-o</td>
  <td>Output filename</td>
 </tr>
 <tr>
  <td>--error=</td>
  <td>-e</td>
  <td>Error filename</td>
 </tr>
 <tr>
  <td>--ntasks=</td>
  <td>-n</td>
  <td>How many task to be invoked in total</td>
 </tr>
 <tr>
  <td>--ntasks-per-node=</td>
  <td></td>
  <td>How many tasks to be invoked in each node</td>
 </tr>
 <tr>
  <td>--partition=</td>
  <td>-p</td>
  <td>Request a specific partition for resource allocation</td>
 </tr>
 <tr>
  <td>--time=</td>
  <td>-t</td>
  <td>Set time limit. Format: DD-HH:MM:SS </td>
 </tr>
 <tr>
  <td>--account=</td>
  <td>-A</td>
  <td>Your SCW project account to charge the resources used by this job</td>
 </tr>
 <tr>
  <td>--mem=</td>
  <td></td>
  <td>Specify memory required per node.</td>
 </tr>
 <tr>
  <td>--mem-per-cpu=</td>
  <td></td>
  <td>Specify minimum memory required per cpu. Mutually exlusive with --mem and --mem-per-gpu</td>
 </tr>
 <tr>
  <td>--mem-per-gpu=</td>
  <td></td>
  <td>Specify minimum memory required per allocated GPU. Mutually exlusive with --mem and --mem-per-cpu</td>
 </tr>
 <tr>
  <td>--array=</td>
  <td>-a</td>
  <td>Submit a job array</td>
 </tr>
 <tr>
  <td>--mail-type=</td>
  <td></td>
  <td>Notify user by email when certain event types occur. Valid type values are NONE, BEGIN, END, FAIL, REQUEUE, ALL</td>
 </tr>
 <tr>
  <td>--mail-user=</td>
  <td></td>
  <td>User email account</td>
 </tr>
 <tr>
  <td>--exclusive=</td>
  <td></td>
  <td>Request exclusive access to resources</td>
 </tr>
 <tr>
  <td></td>
  <td></td>
  <td></td>
 </tr>
</table>


## Filename pattern

## Environment variables

## Managing jobs

Request node interactive access

{% include links.md %}

