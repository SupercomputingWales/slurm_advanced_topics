---
title: "Efficient job scripts"
teaching: 15
exercises: 15
questions:
- "How can we parallelize multiple tasks with SLURM and GNU Parallel?"
- "What is Quality of Service"
objectives:
- "Understand what are array jobs"
- "Understand what are dependency jobs"
- "Understand how to use GNU Parallel to run multiple tasks in parallel"
keypoints:
- "SLURM array jobs can be used to parallelize multiple tasks in a single script"
- "GNU Parallel can be used similarly to array jobs but has additional features like resubmission capabilities"
- "SLURM allow us to create jobs pipelines with its *--dependency* option"
- "Hawk has a Quality of Service feature on partitions that sets limits to users on how many jobs can queued, run at the same time, maximum number of CPUs and nodes"
---

## Aberystwyth Users
For this section make sure to use your partition is *compute*, account is *scw2196* and reservation is *scw2196_201* (monday) or *scw2196_201* (tuesday).



## Job Arrays

SLURM and other job schedulers have a convenient feature known as *Job arrays* that allow repetitive tasks to be run lots of times. The structure of an array job script is very similar to a regular one with the addition of *--array* and the possibility of use new replacement patterns and environment variables. Take a look to the script below:

~~~
#!/bin/bash
#SBATCH --job-name=array-example
#SBATCH -o %x.o.%A.%a
#SBATCH -e %x.e.%A.%a
#SBATCH --partition=c_compute_mdi1 (hawk) compute (sunbird)
#SBATCH --ntasks=1
#SBATCH --time=00:05:00
#SBATCH --array=1-4
#SBATCH --reservation=training
#SBATCH --account=scw1148

# some commands specific to your job
# for example:
module purge
module load python
module list

env | grep SLURM

sleep 30

echo "This is task $SLURM_ARRAY_TASK_ID of $SLURM_ARRAY_TASK_COUNT"
./example.py ${SLURM_ARRAY_TASK_ID}

OUTDIR=output_array
mkdir -p $OUTDIR
mv fig${SLURM_ARRAY_TASK_ID}.png $OUTDIR
~~~
{: .language-bash}

This script submits 4 independent jobs using 1 cpu each that will run for up to 5 minutes. Notice that  the output and error filenames use new replacement patters *%A* (the job array's master allocation ID) and *%a* (job array index number) that allow us to uniquely identify our jobs log files. Each of these jobs will perform exactly the same instructions (execute a python script *example.py*) but with a varying argument defined by our job task ID. Our python script is a simple program to create a probability distribution, plot it and save it as an image file using the job ID number as identifier. At the end, these image files are moved to a new output_array directory.

> ## Running an array job...
>
> Try running lesson_6/array_job.q
> <pre style="color: silver; background: black;">
> $ sbatch array_job.q
> </pre>
> Look at the output files and put attention to the environment variables defined by SLURM. Can you spot new ones?
> How many figures were created? 
> You can use this script as a template to fit more complicated cases.
{: .challenge}


## GNU Parallel jobs
`GNU parallel` is a shell tool that allows to run programs in parallel in one or more computers. It takes a list of items (files, lines, commands) and split them to be run in parallel. A feature of GNU parallel is that the output from the commands is the same as you would have get from running the commands sequentially. Combined with `srun`, `GNU parallel` becomes a powerful method of distributing a large amount of jobs in a limited number or workers.

Might be easier to explain it with an example. Consider the following script:

~~~
#!/bin/bash
#SBATCH -J parallel-example  
#SBATCH -o %x.o.%J
#SBATCH -e %x.e.%J
#SBATCH --ntasks=4
#SBATCH -p c_compute_mdi1 (hawk) compute (sunbird)
#SBATCH --time=00:05:00
#SBATCH --reservation=training
#SBATCH --account=scw1148

module purge
module load python
module load parallel
module list

sleep 30

# Define srun arguments:
srun="srun -n1 -N1 --exclusive"
# --exclusive     ensures srun uses distinct CPUs for each job step
# -n1 -N1         allocates a single core to each task

# Define parallel arguments:
parallel="parallel -N 1 --delay .2 -j $SLURM_NTASKS --joblog parallel_joblog --resume"
# -N 1              is number of arguments to pass to each job
# --delay .2        prevents overloading the controlling node on short jobs
# -j $SLURM_NTASKS  is the number of concurrent tasks parallel runs, so number of CPUs allocated
# --joblog name     parallel's log file of tasks it has run
# --resume          parallel can use a joblog and this to continue an interrupted run (job resubmitted)

# Run the tasks:
$parallel "$srun ./example.py {1}" ::: {1..4}
# in this case, we are running a script named example.py, and passing it a single argument
# {1} is the first argument
# parallel uses ::: to separate options. Here {1..4} is a shell expansion defining the values for
# the first argument, but could be any shell command so parallel will run the example.py script
# for the numbers 0 through 3
#
# as an example, the first job will be run like this:
#    srun -N1 -n1 --exclusive ./example.py arg1:0
~~~
{: .language-bash}

This job script creates only one job that runs for up to 5 minutes with 4 CPUs. Within the script we define two shortcut bash variables *parallel* and *srun* with instructions and arguments for `GNU parallel` and `srun`. The meaning of the different options is documented in the script. Put special attention to the argument {1..4} in *parallel*, this defines the set of arguments passed to our python script.

> ## Run a GNU parallel script (1)
>
> Try running the above script:
> <pre style="color: silver; background: black;">
> $ sbatch gnu_parallel_job.q
> </pre>
> Do you get the same output as before?
> Take a look to the parallel_joblog file just created. 
{: .challenge}

> ## Job history
>
> `GNU parallel` keeps track of what jobs have been submitted in a joblog file. This alllows us to resubmit (*--resume* option) a job in case there was an interruption (error, time limit, etc). 
{: .callout}

So far, it seems like we get the same output as we did with our array job script. So why bother with `GNU parallel` since it seems more complicated?

> ## Run a GNU parallel script (2)
>
> Open gnu_parallel_job.q and modify the sequence argument in the *parallel* variable from {1..4} to {1..80} and try running it again:
> <pre style="color: silver; background: black;">
> $ sbatch gnu_parallel_job.q
> </pre>
> Long list the files in your directory and pay attention to the time stamps the figures just created, what do you notice? Where the first 4 figures produced again?
> Try obtaining the same output with the array job script. 
{: .challenge}



## Dependency jobs
Another useful SLURM tool is the possibility of creating dependency jobs that allow us to build pipelines. The script below uses our previous scripts to submit an array job in a first instance and then a `GNU Parallel` job that will wait until the first job finishes.

In this example, our jobs are independent of each other, but provide with a template useful in several cases where a job rely on the output produce by another job, and while the jobs could be in principle combined in a single script, it is very often the case that the jobs have different requirements (CPUs, memory, GPUs, etc) one might be a heavy MPI job using several nodes, while the second could be an array job processing the output from the first, in this case it might be more efficient submitting the jobs independently as a pipeline.


~~~
#!/bin/bash
first=$(sbatch array_job.q | cut -d' ' -f4)
echo "submitted first job with id $first"
second=$(sbatch --dependency=afterok:$first --account=scw1148 gnu_parallel_job.q | cut -d' ' -f4)
echo "submitted second job with id $second"
#
# Potentially more jobs...
# third=$(sbatch --dependency=afterok:$second --account=scw1148 some-other-job.q | cut -d' ' -f4)
#
~~~
{: .language-bash}


In the above script, we use SLURM option -d or "--dependency" to specify that a job is only allowed to start if another job finished. Some of the possible values that this option accepts are:

<table>
 <tr>
  <th>Value</th>
  <th>Description</th>
 </tr>
 <tr>
  <td>after</td>
  <td>Begin execution after all jobs specified have finished or have been cancelled.</td>
 </tr>
 <tr>
  <td>afterany</td>
  <td>This job can begin execution after the specified jobs have terminated.</td>
 </tr>
 <tr>
  <td>afternotok</td>
  <td>This job can begin execution after the specified jobs have terminated in some failed state.</td>
 </tr>
 <tr>
  <td>afterok</td>
  <td>This job can begin execution after the specified jobs have successfully executed </td>
 </tr>
</table> 

> ## Running a dependency job...
>
> Go ahead and try running
> <pre style="color: silver; background: black;">
> ./submit_dependency_job.sh
> </pre>
> Does it work? Try changing *afterok* to *afternotok*, what is the effect?
{: .challenge}

## Quality of Service
On Hawk, partitions have an associated *Quality of Service* (QoS) feature that assigns a set of limits to try to keep things fair for everyone. This defines the maximum number of queued and running jobs a user can have and the maximum number of nodes and CPUs that can be requested at the same time.

For example, on *htc* users can queue up to 40 jobs and run 10 at the same time and cannot request more that 10 nodes or 400 CPUs at the same time.

If you submit more than 10 jobs to *htc*, some of them will have the following message *QOSMaxJobsPerUserLimit*

<pre style="color: silver; background: black;">
16720251       htc   my.job new-user PD       0:00      1 (QOSMaxJobsPerUserLimit)
16720252       htc   my.job new-user PD       0:00      1 (QOSMaxJobsPerUserLimit)
16720253       htc   my.job new-user PD       0:00      1 (QOSMaxJobsPerUserLimit)
16720218       htc   my.job new-user  R       0:44      1 ccs3020
16720219       htc   my.job new-user  R       0:44      1 ccs3020
16720220       htc   my.job new-user  R       0:44      1 ccs3020
</pre>

which indicates that you have 10 jobs running at the same time and that the remaining are waiting due to the partition's limits. If you continue submitting jobs, you will receive the following  message:

<pre style="color: silver; background: black;">
sbatch: error: Batch job submission failed: Job violates accounting/QOS policy (job submit limit, user's size and/or time limits)
</pre>

This is just SLURM letting you know that you have exceeded the number of jobs you are allowed to queue. As you can see, this error can also be triggered in other circumstances, like requesting a run time higher than the maximum allowed. If you need to queue or run more jobs at the same time, get in contact with us and we will try to help you. 



{% include links.md %}

