---
title: Setup
---
Access to the system is necessary to undertake this course. It is assumed that attendees have a user account or have received a guest training account.

Some exercises are used in this course and are located in the login node cl1 in /tmp/slurm_adv_topics_exercises.tar.gz. To copy and extract this file:

<pre style="color: silver; background: black;">
user@cl1: ~:$ cp /tmp/arc_2020_slurm_adv_topics.tar.gz /home/$USER
user@cl1: ~:$ tar -xzvf arc_2020_slurm_adv_topics.tar.gz </pre>

A node reservation is created in partition c_compute_mdi1. To access it users need to specify in their job scripts:
~~~
#SBATCH --reservation=training
#SBATCH --account=scw1148
~~~
{: .language-bash}

{% include links.md %}
