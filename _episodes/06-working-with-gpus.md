---
title: "Working with GPUs"
teaching: 10
exercises: 10
questions:
- "What type of GPU devices are available on SCW systems?"
- "How to modify a job script to request GPUs?"
objectives:
- "Identify types of GPUs available on SCW systems"
- "Obtain knowledge on how to modify job scripts to request GPU devices"
- "Running a GPU simple job script"
keypoints:
- "SCW systems offer P100 and V100 generation GPU devices"
- "GPUs are treated as a consumable resource and need to be requested explicitly"
- "CUDA libraries are only available on GPU partitions, dev and login nodes"
---


## Available GPU devices

### Hawk
There are currently two types of GPU devices available to our users on two partitions:
<table>
 <thead>
  <tr>
   <th>Partition name</th>
   <th>Number of nodes</th>
   <th>Device model</th>
   <th>Memory (GB)</th>
   <th>Tensor cores</th>
   <th>Cuda cores</th>
   <th>Time limit (h)</th>
  </tr>
 </thead>
 <tbody>
  <tr>
   <td>gpu</td>
   <td>13</td>
   <td>P100</td>
   <td>16</td>
   <td>0</td>
   <td>3584</td>
   <td>72</td>
  </tr>
  <tr>
   <td>gpu_v100</td>
   <td>15</td>
   <td>V100</td>
   <td>16</td>
   <td>640</td>
   <td>5120</td>
   <td>72</td>
  </tr>
 </tbody>
  <tfoot style="text-align:right">
   <tr>
     <td colspan="7"> All GPU nodes have 2 GPU devices </td>
   </tr>
  </tfoot>
</table>

The main difference between these devices is on the availability of Tensor cores on the *V100*.
Tensor cores are a new type of programmable core exclusive to GPUs based on the Volta architecture
that run alongside standard CUDA cores. Tensor cores can accelerate mixed-precision matrix multiply
and accumulate calculations in a single operation. This capability is specially significant for
AI/DL/ML applications that rely on large matrix operations.

### Sunbird
All GPU nodes are V100
<table>
 <thead>
  <tr>
   <th>Partition name</th>
   <th>Number of nodes</th>
   <th>Device model</th>
   <th>Memory (GB)</th>
   <th>Tensor cores</th>
   <th>Cuda cores</th>
   <th>Time limit (h)</th>
  </tr>
 </thead>
 <tbody>
  <tr>
   <td>gpu</td>
   <td>4</td>
   <td>V100</td>
   <td>16</td>
   <td>640</td>
   <td>5120</td>
   <td>48</td>
  </tr>
 </tbody>
  <tfoot style="text-align:right">
   <tr>
     <td colspan="7"> All GPU nodes have 2 GPU devices </td>
   </tr>
  </tfoot>
</table>

## Requesting GPUs
Slurm controls access to the GPUs on a node such that access is only granted when the resource is
requested specifically (i.e. is not implicit with processor/node count), so that in principle it
would be possible to request a GPU node without GPU devices but this would bad practice. Slurm
models GPUs as a Generic Resource (GRES), which is requested at job submission time via the
following additional directive:

~~~
#SBATCH --gres=gpu:2
~~~
{: .language-bash}

This directive instructs Slurm to allocate two GPUs per allocated node, to not use nodes without
GPUs and to grant access.

On your job script you should also point to the desired GPU enabled partition:
~~~ 
#SBATCH -p gpu  # to request P100 GPUs
# Or
#SBATCH -p gpu_v100 # to request V100 GPUs
~~~
{: .language-bash}

It is then possible to use CUDA enabled applications or the CUDA toolkit modules themselves,
modular environment examples being:

~~~
module load CUDA/11.7
module load gromacs/2018.2-single-gpu
~~~
{: .language-bash}

### GPU compute modes (not included on sunbird)
NVIDIA GPU cards can be operated in a number of Compute Modes. In short the difference is whether
multiple processes (and, theoretically, users) can access (share) a GPU or if a GPU is exclusively
bound to a single process. It is typically application-specific whether one or the other mode is
needed, so please pay particular attention to example job scripts. GPUs on SCW systems default to
‘shared’ mode.

Users are able to set the Compute Mode of GPUs allocated to their job through a pair of helper
scripts that should be called in a job script in the following manner:

To set exclusive mode:
~~~
clush -w $SLURM_NODELIST "sudo /apps/slurm/gpuset_3_exclusive"
~~~
{: .language-bash}
And to set shared mode (although this is the default at the start of any job):
~~~
clush -w $SLURM_NODELIST "sudo /apps/slurm/gpuset_0_shared"
~~~
{: .language-bash}
To query the Compute Mode:
~~~
clush -w $SLURM_NODELIST "nvidia-smi -q|grep Compute"
~~~
{: .language-bash}

## Compiling with CUDA libraries
CUDA libraries are not accessible from general compute nodes (*compute*, *htc*, *highmem*) but
they are on *dev* and login nodes for the purpose of testing and compilation. Trying to load
CUDA libraries on these partitions would result in the following error:
 <pre style="color: silver; background: black;">
ERROR: CUDA is not available. GPU detected is unknown
</pre>
What this means is that you are capable of building your CUDA application on the login nodes and
test basic functionality on *dev*, but to test actual GPU work you will need to submit your job
to *gpu* or *gpu_v100*.

Current CUDA versions available on our systems are:
 <pre style="color: silver; background: black;">
$ module avail CUDA
-------------------------- /apps/modules/libraries --------------------------
CUDA/10.0 CUDA/10.2 CUDA/11.2 CUDA/11.6 CUDA/12.0 CUDA/9.0  CUDA/9.2
CUDA/10.1 CUDA/11   CUDA/11.5 CUDA/11.7 CUDA/8.0  CUDA/9.1
</pre>

CUDA/12.0 is not currently available on Sunbird.

The latest NVIDIA driver version on the GPU nodes is 525.60.13 for CUDA 12.0 which is
backwards compatible with prior versions of CUDA. 

## Running a GPU job
Here is a job script to help you submit a "hello-world" GPU job:

{::options parse_block_html="true" /}
<div>
  <ul class="nav nav-tabs" role="tablist">
   <li role="presentation" class="active"><a data-os="hawk" href="#hawk-gpu-example" aria-controls="hawk-gpu-example" role="tab" data-toggle="tab">Hawk</a></li>
   <li role="presentation"><a data-os="sunbird" href="#sunbird-gpu-example" aria-controls="sunbird-gpu-example" role="tab" data-toggle="tab">Sunbird</a></li>
  </ul>

 <div class="tab-content">
 <article role="tabpanel" class="tab-pane active" id="hawk-gpu-example">
~~~
#!/bin/bash --login
#SBATCH --job-name=gpu.example
#SBATCH --error=%x.e.%j
#SBATCH --output=%x.o.%j
#SBATCH --partition=gpu
#SBATCH --time=00:10:00
#SBATCH --ntasks=20
#SBATCH --ntasks-per-node=20
#SBATCH --gres=gpu:1
#SBATCH --account=scwXXXX

clush -w $SLURM_NODELIST "sudo /apps/slurm/gpuset_3_exclusive"

set -eu

module purge
module load CUDA/10.1
module load keras/2.3.1
module list

test=imdb
input_dir=$SLURM_SUBMIT_DIR
WDPATH=/scratch/$USER/gpu.example.${test}.$SLURM_JOBID
rm -rf ${WDPATH}
mkdir -p ${WDPATH}
cd ${WDPATH}
cp ${input_dir}/${test}.py ${WDPATH}

start="$(date +%s)"
time python -u -i ${test}.py
stop="$(date +%s)"
finish=$(( $stop-$start ))
echo gpu test ${test} $SLURM_JOBID Job-Time $finish seconds
echo Keras End Time is `date`
~~~
{: .language-bash}
  </article>

  <article role="tabpanel" class="tab-pane" id="sunbird-gpu-example">
~~~
#!/bin/bash --login
#SBATCH --job-name=gpu.example
#SBATCH --error=%x.e.%j
#SBATCH --output=%x.o.%j
#SBATCH --partition=gpu
#SBATCH --time=00:10:00
#SBATCH --ntasks=20
#SBATCH --ntasks-per-node=20
#SBATCH --gres=gpu:1
#SBATCH --account=scw2196

set -eu

module purge
module load CUDA/10.1
module load tensorflow/1.11-gpu
module list

test=imdb
input_dir=$SLURM_SUBMIT_DIR
WDPATH=/scratch/$USER/gpu.example.${test}.$SLURM_JOBID
rm -rf ${WDPATH}
mkdir -p ${WDPATH}
cd ${WDPATH}
cp ${input_dir}/${test}.py ${WDPATH}

start="$(date +%s)"
time python -u -i ${test}.py
stop="$(date +%s)"
finish=$(( $stop-$start ))
echo gpu test ${test} $SLURM_JOBID Job-Time $finish seconds
echo Keras End Time is `date`
~~~
{: .language-bash}
  </article>
 </div>
</div>

The above example is taken from "Deep Learning with Python" by Francois Chollet and explores a
Deep Learning Two-class classification problem. In specific, the purpose of the model is to
classify movie review as positive or negative based on the context of the reviews.

> ## Running a GPU job...
>
> Try running the above example.
> <pre style="color: silver; background: black;">
> $ sbatch gpu.example.q
> </pre>
> Does it work? Try submitting it to a non-GPU node. Is there a big difference in timing? Take
> into account that this is a very model. However, in real research problems, using GPU enabled
> applications have the potential to reduce significantly computational time.
{: .challenge}
{% include links.md %}
