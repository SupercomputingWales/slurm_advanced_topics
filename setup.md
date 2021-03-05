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

You can confirm that you are part of the scw1148 training project running this command on Hawk:
~~~
$ id | grep scw1148
uid=635422(c.c1045890) gid=9635422(c.c1045890) groups=9635422(c.c1045890),20308(SCWales-user-sync),6000002(CardiffUniversity),7000006(scw1001),7000073(scw1057),7000295(scw1176),7000323(scw1148),7000705(scw1733),8000001(hawk_admin) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
~~~

Your output may vary from the above. If you don't see an output, most likely you are
not part of scw1148. You can request access from your [SCW dashboard](http://my.supercomputing.wales/),
just enter scw1148 in the correspoding section and click in *Join*, we will approve
the request as soon as possible:
<img src="{{ page.root }}/fig/join-scw-project.png" alt="Join SCW Project" width="50%" height="50%" />

[zip-file]: {{ page.root }}/data/arc_slurm_adv_topics.zip

{% include links.md %}
