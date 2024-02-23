---
title: "Requesting resources"
teaching: 15
exercises: 15
questions:
- "How to use SLURM environment variables?"
- "How to obtain information about jobs and partitions?"
objectives:
- "To learn how to use SLURM environment variables"
- "Familiarize with some SLURM commands to extract information from jobs and partitions"
keypoints:
- "`srun` is used to run an interactive job."
- "`sbatch` is used to queue a job script for later execution."
- "SLURM defines several environment variables that can be used within job scripts"
- "Use replacement patterns to uniquely identify your log files"
- "SLURM has several commands to help you interact with jobs and partitions"
---
## Aberystwyth Users
For this section make sure to use your partition is *compute*, account is *scw2196* and
reservation is *scw2196_201* (monday) or *scw2196_201* (tuesday).

## How to request resources
Setting the correct resource requirements will help to ensure a faster turnaround time on the
supercomputer. For this, users need to interact with SLURM, the supercomputer's job scheduler,
that is in charge of allocating resources to jobs from all users.

There are two ways to request resources:
- Interactive access
- Prepare and submit a job script

### Interactive jobs
Sometimes is useful to request a node for an interactive session that allows you to use it as if
you would be in a login node. The `srun` command allows you to do this, it will add your resource
request to the queue and once allocated it will start a new bash session on the granted nodes.

> ## Requesting an interactive job (1)
> Try the following command
>
> Hawk:
> <pre style="color: silver; background: black;">$ srun -n 1 -p c_compute_mdi1 --account=scw1148 --reservation=training --pty bash </pre>
>
> Sunbird:
> <pre style="color: silver; background: black;">$ srun -n 1 -p compute --account=scw2196 --reservation=scwXXXX --pty bash </pre>
>
> What happened? Since the command is using a special reservation for this training session,
> the time to allocate resources should have been small and you should have been taken to a
> new node (from cl1 to ccs0076, for example). Once inside the work node, you can run your
> applications as if you were in the login node but without the restrictions. 
> While we are here try this command and put attention to the output:
> <pre style="color: silver; background: black;">$ env | grep SLURM  </pre>
>
> This should show you all the environment variables that match "SLURM". These are automatically
> set based on the resources requested. How many cpus are you using? What is the directory where
> you executed the command? How would this output change if you use *-n 2* when running `srun`?
>
> To exit the node type *exit*.
>
> **Please note, do not use the above --reservation option for your regular jobs, this only
> works during this training session. You also will also need to change scw1148 or scw2196 to
> your own project code**
{: .challenge}

> ## Requesting an interactive job (2)
> Now, try the following command:
> <pre style="color: silver; background: black;">$ srun -n 1 -p compute --account=scwXXXX --pty bash </pre>
>
> Don't forget to replace **scwXXXX** with your own scw project code. What did you notice? If
> you were lucky, you might have landed in a new node as in the previous case, most likely you
> received a message like:
> <pre style="color: silver; background: black;">$ job 12345678 queued and waiting for resources </pre>
> which indicates that your request has been put in the queue and you can sit and wait for it
> to be allocated. Since we don't want to do that right now, go ahead and press
> <kbd>Ctrl</kbd>+<kbd>C</kbd> to cancel the request.
{: .challenge}


### Job scripts

~~~
#!/bin/bash --login
#SBATCH -J my.job
#SBATCH -o %x.o.%j
#SBATCH -e %x.e.%j
#SBATCH --ntasks=5
#SBATCH --ntasks-per-node=5
#SBATCH -p dev
#SBATCH --time=00:05:00

# some commands specific to your job
# for example:
env | grep SLURM
sleep 120
echo “Hello World!”
~~~
{: .language-bash}

In the above Bash script pay attention to the *#SBATCH* entries, these are calls to `sbatch`,
the program in charge to submit batch jobs to SLURM. This is where users specify how much
resource to request. In this example the script is requesting: 5 tasks, 5 tasks to be run in
each node (hence only 1 node), resources to be granted in the *dev* partition and maximum
runtime of 5 minutes. 

The table below show some common `sbatch` commands (also shared with `srun` except for *--array*):
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
  <td>Request a specific partition (or partitions) for resource allocation</td>
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
  <td>Specify minimum memory required per cpu. Mutually exclusive with --mem and --mem-per-gpu</td>
 </tr>
 <tr>
  <td>--mem-per-gpu=</td>
  <td></td>
  <td>Specify minimum memory required per allocated GPU. Mutually exclusive with --mem and --mem-per-cpu</td>
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

> ## Partition selection
> 
> Partition selection usually takes one option, e.g. `#SBATCH --partition=gpu` but it can also
> take multiple values such as accessing all the GPU partitions,
> `#SBATCH --partition=gpu,gpu_v100`, where if you do not care which type of GPU to use (P100
> vs V100) then it will find the next available GPU node. This can also be used with dedicated
> researcher partitions such as `c_gpu_comsc1` to use either your dedicated partitions or the
> shared partitions.
{: .callout}

> ## Submitting a job script (1)
>
> Try running lesson_6/job_script_1.sh  
> <pre style="color: silver; background: black;">$ sbatch job_script_1.sh </pre>
> Now look at the output files. Are the SLURM variables the same as in the interactive case?
{: .challenge}

## Filename pattern
Did you notice the *%x* and *%J* in the previous batch script? 
~~~
#SBATCH -o %x.o.%J
#SBATCH -e %x.e.%J
~~~
{: .language-bash}

These are replacement symbols that help you tag your output scripts. Some of the symbols
available include:
<table style="width=100%">
 <tr>
  <th>Symbol</th>
  <th>Description</th>
 </tr>
 <tr>
  <td>%x</td>
  <td>Job name given by --job-name</td>
 </tr>
 <tr>
  <td>%J</td>
  <td>Job ID</td>
 </tr>
 <tr>
  <td>%u</td>
  <td>username</td>
 </tr>
 <tr>
  <td>%A</td>
  <td>Job array's master allocation number</td>
 </tr>
 <tr>
  <td>%a</td>
  <td>Job array ID (index) number.</td>
 </tr>
</table>

In our simple example script this has the effect of uniquely tagging the output and error files
as (for example) *my.job.o.16717342* and *my.job.e.16717342* avoiding rewriting the file in case
we were to submit the same job without saving our logs.

## Environment variables
As seen previously, SLURM defines several environment variables when we submit a job. These can
be used within a job to tag files, directories, make decisions based on resources available and
more. The following script shows how to query these variables:

~~~
#!/bin/bash --login
#SBATCH -J my.job
#SBATCH -o %x.o.%j
#SBATCH -e %x.e.%j
#SBATCH --ntasks=5
#SBATCH --ntasks-per-node=5
#SBATCH -p dev
#SBATCH --time=00:05:00

# some commands specific to your job
# for example:
env | grep SLURM

sleep 120

WORKDIR=/scratch/$USER/training.${SLURM_JOBID}
rm -rf $WORKDIR
mkdir $WORKDIR
cd $WORKDIR

OUTFILE=output.txt
touch $OUTFILE

echo “Hello World!” >> ${OUTFILE}
echo Submit dir is: $SLURM_SUBMIT_DIR >> ${OUTFILE}
echo Nodelist is: $SLURM_JOB_NODELIST >> ${OUTFILE}
echo Job id is: $SLURM_JOBID >> ${OUTFILE}

echo Number of nodes: $SLURM_JOB_NUM_NODES >> ${OUTFILE}
echo Number of tasks per node: $SLURM_NTASKS_PER_NODE >> ${OUTFILE}
echo Number of cpus: $SLURM_NPROCS >> ${OUTFILE}
~~~
{: .language-bash}

> ## Submitting a job script (2)
>
> Try submitting lesson_6/job_script_2.sh.
> <pre style="color: silver; background: black;">$ sbatch job_script_2.sh </pre>
> How are we tagging our work directory? Would it be useful to prevent deleting any output
> files created within this directory when running the script again? 
{: .challenge}

## Managing jobs
SLURM has several commands to help you interact with the cluster partitions and with your
running jobs. Some of these are:
<table>
 <tr>
  <th>Command</th>
  <th>Description</th>
  <th>Example</th>
 </tr>
 <tr>
  <td>`squeue`</td>
  <td>view information about jobs located in the Slurm scheduling queue</td>
  <td>squeue -u $USER</td>
 </tr>
 <tr>
  <td>`sinfo` </td>
  <td>view information about Slurm nodes and partitions</td>
  <td>sinfo -p compute</td>
 </tr>
 <tr>
  <td>`scontrol`</td>
  <td>view or modify Slurm configuration and state</td>
  <td>(for a running job) scontrol show job JOBID </td>
 </tr>
 <tr>
  <td>`sacct`</td>
  <td>displays accounting data for all jobs and job steps in the Slurm job accounting log or Slurm database</td>
  <td>(for a completed job) sacct -j JOBID --format=jobid,elapsed,ncpus,ntasks,state</td>
 </tr>
 <tr>
  <td>`scancel`</td>
  <td>Used to signal jobs or job steps that are under the control of Slurm.</td>
  <td>(for a running job) scancel JOBID</td>
 </tr>
</table>

> ## Looking for free nodes?
>
> `sinfo` gives us information about the cluster partitions. We can parse its output to look
> for *idle* nodes:
> <pre style="color: silver; background: black;">$ sinfo | grep idle </pre>
> And with a bit of luck we will find some. Would this information be useful if trying to submit
> an interactive job?
{: .challenge}

> ## When is my job going to start?
>
> You can use `scontrol` to find out many details about a submitted job, including an estimated
> starting time. Try submitting lesson_6/job_script_3.sh take a note of the JOBID and use this
> command to get job information:
> <pre style="color: silver; background: black;">$ scontrol show job JOBID </pre>
> Look for *StartTime* (it might take a few minutes for SLURM to assign a place in the queue to
> the job). What do you notice? Are the resources requested reasonable for this job? 
{: .challenge}

{% include links.md %}

