---
title: "Best practices"
teaching: 15
exercises: 15
questions:
- "Dos, don'ts and general advice"
objectives:
- "Become familiar with best practices in SCW systems"
- "Understand quota allocation"
- "Understand partitions purpose"
keypoints:
- "Do NOT run long jobs in the login nodes"
- "Check your quota regularly"
- "Submit jobs to appropriate partitions"
---
This section introduces best practices when working on SCW systems. You can find more
information in the [SCW portal](https://portal.supercomputing.wales/index.php/best-practice/).

## Login nodes

**Do NOT run long jobs in the login nodes**. Remember that login nodes (cl1, cl2,
cla1) are shared nodes used by all users. Short testing, compilation, file transfer, 
light debugging are typically ok. Large MPI jobs are definitely not. If we detect 
heavy jobs running on the system the application will be terminated without notice.

> ## Who is at home?
> To help you realize the shared nature of the login nodes, try running this command:
>
> <pre style="color: silver; background: black;">$ who</pre>
>
> What is this showing you?
{: .challenge}

> ## Consequences of breaking the rules
>
> <img src="{{ page.root }}/fig/judge.jpg" alt="The Judge" width="250px"/>
>
> By default each user is limited to the concurrent use of at most 8 processor cores
> and 200GB of memory. However, should **heavy usage** (defined as more than 75% of 
> the allocation for continuous time 3 minutes) be made of this allocation then the
> user’s access will be reduced for a penalty period of time. There are three penalty
> levels that are reached if the user mantains the offending behaviour. The user’s 
> access reduced by a fraction of the default quota by a defined period of time, both
> increase as the user moves through penalty levels. You can find more details in the
> [SCW portal](https://portal.supercomputing.wales/index.php/best-practice/).
>
> The user will receive an email notification if a *violation of the usage policy* 
> has happened providing details about the offending application and current penalty
> level.
>
> If you are not sure about why you are receiving this notifications or need further 
> help do not hesitate to contact us.
>
>
{: .callout}

## Your .bashrc
Linux allows you to configure your work environment in detail. This is done by 
reading text files on login. Typically a user would edit *.bashrc* to finetune module
loading, adding directories to *PATH*, etc. This is ok in personal systems but on SCW
*.bashrc* is managed centrally. What this means is that in principle you can edit 
*.bashrc* but your changes might get lost without warning in case there is a need to 
reset this file. In turn we encourage users to edit *.myenv* which is also read on 
login and should have the same effect as editing *.bashrc*.

> ## My application insist on editing *.bashrc*
>
> Some applications include instructions on how to add changes to *.bashrc*. If this
> is your case, try doing the changes on *.myenv*, if this doesn't work please get 
> in contact with us and we will help you find the best solution.
{: .callout}

## Running jobs

> ## Whenever possible:
>
> - Estimate the time and resources that your job needs. This will reduce the time
>   necessary to grant resources.
> - Try to avoid submitting a massive number of small jobs since this creates an 
>   overhead in resource provision. Whenever possible, stack small jobs in single 
>   bigger jobs.
> - Avoid creating thousands of small files. This has a negative impact on the global
>   filesystem. Better to have a smaller number or larger files.
> - Run a small test case (dev partition) before submitting a large job, to make sure
>   it works as expected.
> - Use *--exclusive*, only if you are certain that you require the resources (cpus, 
>   memory) of a full node.
>   * Disadvantages: takes longer for your job to be allocated resources, potentially
>     inefficient if you are using less cpus/memory than provided.
> - Use checkpoints if your application allows it. This will let you restart your job
>   from the last successful state rather that rerunning from the beginning.
{: .checklist}

## Partitions

Partitions in the context of SCW systems refer to groups of nodes with certain capabilities/features in common. You can list the partitions available to you with the following command:

Hawk:
<pre style="color: silver; background: black;">$ sinfo
PARTITION   AVAIL  TIMELIMIT  NODES  STATE NODELIST
compute\*       up 3-00:00:00    133  alloc ccs[0001-0009,0011-0134]
compute\*       up 3-00:00:00      1   idle ccs0010
compute_amd    up 3-00:00:00     52  alloc cca[0001-0002,0005-0016,0018-0019,0021-0025,0027-0031,0036,0039-0041,0043-0064]
compute_amd    up 3-00:00:00     12   idle cca[0003-0004,0017,0020,0026,0032-0035,0037-0038,0042]
highmem        up 3-00:00:00      5   comp ccs[1001-1003,1009,1014]
highmem        up 3-00:00:00      2    mix ccs[1023,1025]
highmem        up 3-00:00:00     19  alloc ccs[1004-1008,1010-1013,1015-1022,1024,1026]
gpu            up 2-00:00:00     10    mix ccs[2002-2007,2009,2011-2013]
gpu            up 2-00:00:00      3  alloc ccs[2001,2008,2010]
gpu_v100       up 2-00:00:00      9    mix ccs[2101-2103,2105,2108-2110,2112,2115]
gpu_v100       up 2-00:00:00      6  alloc ccs[2104,2106-2107,2111,2113-2114]
htc            up 3-00:00:00      1  down\* ccs3004
htc            up 3-00:00:00      2   comp ccs[1009,1014]
htc            up 3-00:00:00     17    mix ccs[1023,1025,2009,2011-2013,2103,2105,2108-2110,2112,2115,3010,3012,3019,3024]
htc            up 3-00:00:00     43  alloc ccs[1010-1013,1015-1022,1024,1026,2008,2010,2104,2106-2107,2111,2113-2114,3001-3003,3005-3009,3011,3013-3018,3020-3023,3025-3026]
dev            up    1:00:00      2   idle ccs[0135-0136]
</pre>

In the output above the user has access to several partitions and should submit jobs 
depending on the application requirements since each partition is ideally designed
for different kind of jobs.

<table style="width:100%">
 <tr>
  <th> Partition </th>
  <th> Meant for </th>
  <th> Avoid </th>
 </tr>
 <tr>
  <td> compute </td>
  <td> Parallel and MPI jobs </td>
  <td> Serial (non-MPI) jobs </td>
 </tr>
 <tr>
  <td> compute_amd </td>
  <td> Parallel and MPI jobs using AMD EPYC 7502 </td>
  <td> Serial (non-MPI) jobs </td>
 </tr>
 <tr>
  <td> highmem </td>
  <td> Large memory (384GB) jobs </td>
  <td> Jobs with low or standard memory requirements </td>
 </tr>
 <tr>
  <td> gpu </td>
  <td> GPU (CUDA) jobs - P100 </td>
  <td> Non-GPU jobs </td>
 </tr>
 <tr>
  <td> gpu_v100 </td>
  <td> GPU (CUDA) jobs - V100 </td>
  <td> Non-GPU jobs </td>
 </tr>
 <tr>
  <td> htc </td>
  <td> High Throughput Serial jobs </td>
  <td> MPI/parallel jobs </td>
 </tr>
 <tr>
  <td> dev </td>
  <td> Testing and development </td>
  <td> Production jobs </td>
 </tr>
</table>

Sunbird:
<pre style="color: silver; background: black;">$ sinfo
PARTITION   AVAIL  TIMELIMIT  NODES  STATE NODELIST
PARTITION          AVAIL  TIMELIMIT  NODES  STATE NODELIST
compute*              up 3-00:00:00      2  fail* scs[0050,0097]
compute*              up 3-00:00:00      2 drain* scs[0041,0056]
compute*              up 3-00:00:00     17  down* scs[0011,0020,0023,0034,0048-0049,0055,0064,0086,0094,0110,0113,0115-0118,0121]
compute*              up 3-00:00:00     13  drain scs[0013,0025,0028,0030-0031,0039,0043,0047,0060,0067,0071,0080,0104]
compute*              up 3-00:00:00      2   resv scs[0111,0120]
compute*              up 3-00:00:00     55    mix scs[0001,0003-0006,0008,0014-0019,0021-0022,0026,0029,0032,0037,0040,0042,0045-0046,0051-0053,0057,0059,0063,0065,0069-0070,0073-0079,0081-0082,0085,0087-0091,0093,0096,0098,0103,0106-0108,0112,0122]
compute*              up 3-00:00:00     20  alloc scs[0007,0009,0012,0024,0027,0035-0036,0038,0044,0054,0066,0083-0084,0092,0095,0100,0102,0109,0114,0123]
compute*              up 3-00:00:00     12   down scs[0002,0010,0033,0058,0061-0062,0068,0072,0099,0101,0105,0119]
gpu                   up 2-00:00:00      1    mix scs2001
gpu                   up 2-00:00:00      3   idle scs[2002-2004]
development           up      30:00      2  fail* scs[0050,0097]
development           up      30:00      2 drain* scs[0041,0056]
development           up      30:00     17  down* scs[0011,0020,0023,0034,0048-0049,0055,0064,0086,0094,0110,0113,0115-0118,0121]
development           up      30:00     13  drain scs[0013,0025,0028,0030-0031,0039,0043,0047,0060,0067,0071,0080,0104]
development           up      30:00      2   resv scs[0111,0120]
development           up      30:00     55    mix scs[0001,0003-0006,0008,0014-0019,0021-0022,0026,0029,0032,0037,0040,0042,0045-0046,0051-0053,0057,0059,0063,0065,0069-0070,0073-0079,0081-0082,0085,0087-0091,0093,0096,0098,0103,0106-0108,0112,0122]
development           up      30:00     20  alloc scs[0007,0009,0012,0024,0027,0035-0036,0038,0044,0054,0066,0083-0084,0092,0095,0100,0102,0109,0114,0123]
development           up      30:00     12   down scs[0002,0010,0033,0058,0061-0062,0068,0072,0099,0101,0105,0119]
accel_ai              up 2-00:00:00      1    mix scs2041
accel_ai              up 2-00:00:00      4   idle scs[2042-2045]
xaccel_ai             up 2-00:00:00      1    mix scs2041
xaccel_ai             up 2-00:00:00      5   idle scs[2042-2046]
accel_ai_dev          up    2:00:00      1    mix scs2041
accel_ai_dev          up    2:00:00      4   idle scs[2042-2045]
accel_ai_mig          up   12:00:00      1   idle scs2046
s_compute_chem        up 3-00:00:00      2    mix scs[3001,3003]
s_compute_chem_rse    up    1:00:00      2    mix scs[3001,3003]
s_gpu_eng             up 2-00:00:00      1   idle scs2021
s_highmem_opt         up 3-00:00:00      1   idle scs0160
</pre>

In the output above the user has access to several partitions and should submit jobs 
depending on the application requirements since each partition is ideally designed
for different kind of jobs.

<table style="width:100%">
 <tr>
  <th> Partition </th>
  <th> Meant for </th>
  <th> Avoid </th>
 </tr>
 <tr>
  <td> compute </td>
  <td> Parallel and MPI jobs </td>
  <td> Serial (non-MPI) jobs </td>
 </tr>
 <tr>
  <td> gpu </td>
  <td> GPU (CUDA) jobs - V100 </td>
  <td> Non-GPU jobs </td>
 </tr>
 <tr>
  <td> accel_ai </td>
  <td> GPU (CUDA) jobs - A100 </td>
  <td> Non-GPU jobs </td>
 </tr>
 <tr>
  <td> dev </td>
  <td> Testing and development </td>
  <td> Production jobs </td>
 </tr>
</table>

NOTE: Any additional nodes not mentioned are not open to the public as they are research specific.
> ## Testing your script
>
> Notice the *dev* entry in the *sinfo* output above? This is the development 
> partition and is meant to perform short application tests. Runtime is limited to 
> 1 h and you can use up to 2 nodes. This is typically enough to test most parallel 
> applications. Please avoid submitting production jobs to this queue since this 
> impact negatively on users looking to quickly test their applications.
>
{: .callout}

## Scratch

If your compute jobs on the cluster produce intermediate results, using your scratch
directory can be beneficial:

- The scratch filesystem has a faster I/O speed than home.
- It has a higher default quota (5 Tb) so you can store bigger input files if 
  necessary. 

Remember to instruct your scripts to clean after themselves by removing unnecessary 
data, this prevents filling up your quota. Remember that unused files on scratch 
might be removed without previous notice. 

## Your quota

On SCW systems there are two types of quotas: storage and files. Storage quota is 
measured in bytes (kb, Mb, Tb) while file quota is measured in individual files 
(independent of their size). You can check your current quota with the command 
**myquota**:

<pre style="color: silver; background: black;">[new_user@cl1 ~]$ myquota
HOME DIRECTORY c.medib
     Filesystem   space   quota   limit   grace   files   quota   limit   grace
chnfs-ib:/nfshome/store01
                    32K  51200M  53248M               8    100k    105k

SCRATCH DIRECTORY c.medib
     Filesystem    used   quota   limit   grace   files   quota   limit   grace
       /scratch      4k      0k      5T       -       1       0 3000000       -

 </pre>

On account approval all users are allocated a default home quota of 50 Gb and 100 K 
files. In the example above, the user has 32 Kb of data currently on home, if the 
user were to go beyond 51200 Mb, it would enter a grace period of 6 days to reduce 
the storage footprint. After this grace period or if hitting 53248 Mb, the user 
wouldn't be able to create any more files. Something similar applies to file quota.

On scratch, new users are allocated a default quota of 5 Tb and 3M files. The main 
difference with home quota is that in scratch there is no grace period (that is what 
the *0* under quota tries to signify) but for all intents and purposes behaves in the
same manner as home.

> ## My application used to work ...
> 
> As you can imagine, several errors arise from programs not being able to create 
> files and the error messages quite often are unrelated to this fact. A common case
> is an application that used to work perfectly well and suddenly throws an error 
> even if running the same job. As a first step in troubleshooting, it is worth 
> checking that your quota hasn't being reached. 
>
{: .callout}

> ## Checking your usage
> 
> If you are worried about hitting your quota, and want to do some clean-up but are
> wondering which might be the problematic directories, Linux has an utility that can
> help you. 
>
> Try running this command in your home directory:
> 
> <pre style="color: silver; background: black;">[new_user@cl1 ~]$ du -h --max-depth=1  </pre>
> 
> What does it shows you? Now try this command?
>
> <pre style="color: silver; background: black;">[new_user@cl1 ~]$ du --inodes --max-depth=1  </pre>
>
> What is it showing you now? Is it useful?
>
> Tip: you can always find out more about *du* in its man page (*man du*).
{: .challenge}

> ## Backups
>
> Please note that although we try to keep your data as safe as possible, at the 
> moment **we do not offer backups**. So please make sure to have a backup of 
> critical files and transfer important data to a more permanent storage.
>
> Did you know that Cardiff University offer a **Research Data Store**? Cardiff 
> researchers can apply for 1Tb of storage (more storage can be provided depending on
> certain criteria). Find out more on the <a href="https://intranet.cardiff.ac.uk/staff/supporting-your-work/research-support/equipment-and-resources/research-data-storage-service" target="_blank">intranet</a>.
>
{: .callout}

{% include links.md %}
