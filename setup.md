---
title: Setup
---
Access to the system is necessary to undertake this course. It is assumed that attendees have a user account or have received a guest training account.

Please follow the instructions below to obtain some example scripts:

1. Download [arc_slurm_adv_topics.zip][zip-file] and extract somewhere on Hawk. 
2. Extract the zip file and check the extracted directory

For example:

~~~
$ wget {{ site.url }}{{ site.baseurl }}/data/arc_slurm_adv_topics.zip
$ unzip arc_slurm_adv_topics.zip
$ ls arc_slurm_adv_topics
~~~
{: .language-bash}

A node reservation is created in partition c_compute_mdi1. To access it users need to specify in their job scripts:
~~~
#SBATCH --reservation=training
#SBATCH --account=scw1148
~~~
{: .language-bash}

[zip-file]: {{ page.root }}/data/arc_slurm_adv_topics.zip

{% include links.md %}
