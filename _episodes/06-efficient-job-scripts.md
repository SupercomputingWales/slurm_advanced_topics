---
title: "Efficient job scripts"
teaching: 0
exercises: 0
questions:
- "TO BE ADDED"
objectives:
- "First learning objective. (fixed)"
keypoints:
- "First key point. Brief Answer to questions. (fixed)"
---
This section introduces three 

## Job Arrays

SLURM and other job schedulers have a convenient feature known as *Job arrays* that allow repetitive tasks to be run lots of times. The structure of an array job script is very similar to a regular one with the addition of *--array* and the possibility of use new replacement patterns and environment variables. Take a look to the script below:

~~~
#!/bin/bash
#SBATCH --job-name=array-example
#SBATCH -o %x.o.%A.%a
#SBATCH -e %x.e.%A.%a
#SBATCH --partition=c_compute_mdi1
#SBATCH --ntasks=1
#SBATCH --time=00:05:00
#SBATCH --array=1-5
#SBATCH --reservation=training
#SBATCH --account=scw1148

# some commands specific to your job
# for example:
module purge
module load python
module list

env | grep SLURM

echo "This is task $SLURM_ARRAY_TASK_ID of $SLURM_ARRAY_TASK_COUNT"
./example.py ${SLURM_ARRAY_TASK_ID}
~~~
{: .language-bash}

This script submits 5 independent jobs using 1 cpu each that will run for up to 5 minutes. The output and error filenames use new replacement patters *%A* (the job array's master allocation ID) and *%a* (job array index number) that allow us to uniquely idetify our jobs log files. Each of these jobs will perform exactly the same instructions (execute a python script *example.py*) but with a varying argument defined by our job task ID. Our python script is a simple program to create a statiscal distribution, plot it and save it as an image file using the job ID number as identifier.

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
`GNU parallel` is a shell tool that allows to run programs in parallel in one or more computers. It takes a list of items (files, lines, commands) and split them to be run in parallel. A feature of GNU parallel is that the output from the commands is the same as you would have get from running the commmands sequentially. Combined with `srun`, `GNU parallel` becomes a powerful method of distributing a large amount of jobs in a limited number or workers.

Might be easier to explain it with an example. Consider the following script:

~~~
#!/bin/bash
#SBATCH -J parallel-example  
#SBATCH -o %x.o.%J
#SBATCH -e %x.e.%J
#SBATCH --ntasks=4
#SBATCH -p c_compute_mdi1
#SBATCH --time=00:05:00
#SBATCH --reservation=training
#SBATCH --account=scw1148

module purge
module load python
module load parallel
module list

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

This job script create only job that runs for up to 5 minutes with 4 cpus. Within the script we define two shortcut bash variables *parallel* and *srun* with instructions and arguments for `GNU parallel` and `srun`. The meaning of the different options is documented in the script. Put special attention to the argument {1..4} in *parallel*, this defines the set of arguments passed to our python script.

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
> `GNU parallel` keeps track of what jobs have been submitted in a joblog file. This alllows us to resubmit (*--resume* option) a job in case there was an interruption (error, time limit,etc). 
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



## Chain Jobs
Another useful SLURM tool is the possibility of creating dependency jobs that allow us to build pipelines. 


Building pipelines using slurm dependencies

Useful to create dependencies between jobs. 
For example, if a job relies on the result of one or more preceding jobs. 
Can also be used if the runtime limit of the batch queues is not sufficient for your job.
Use SLURM option -d or "--dependency" to specify that a job is only allowed to start if another job finished. 

## Quality of Service
On Hawk, partitions have an associated *Quality of Service* (QoS) feature that assignes a set of limits to try to keep things fair for everyone. This defines the maximum number of queued and running jobs a user can have and the maximum number of nodes and cpus that can be requested at the same time.

For example, on *htc* users can queue up to 40 jobs and run 10 at the same time and cannot request more that 10 nodes or 400 cpus at the same time.

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

