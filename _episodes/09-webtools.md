---
title: "Accessing webtools"
teaching: 15
exercises: 15
questions:
- "How do I run a webtool for framwork/language X?"
objectives:
- "Run webtools in Slurm"
- "Access those webtools from a browser."
keypoints:
- "Accessing Hawk compute nodes requires some care but can be powerful in running some tools."
---

# Rstudio

[Rstudio](https://rstudio.com) is a useful tool to program and access R.  There are a number of methods available but to
use it efficeintly on Slurm we can use a Rstudio server to run on the compute nodes.

~~~
$ module load rstudio-server
~~~
{: .language-bash}

> ## Rstudio versions
>
> There are a number of versions such as Rstudio and Rstudio Server, Rstudio is
> a desktop app whilst Rstudio Server is a web browser.
{: .callout}

To run Rstudio server we can use `srun`. 
~~~
$ srun -n 1 -p htc --account=scwXXXX rstudio-server 
~~~
{: .language-bash}

When this runs we see:
~~~
INFO: Server address is: ccs2011:8787
INFO: Password to access site is: RshEFvNoiPEK4
~~~
{: .output}

An optional argument can be given to `rstudio-server` to override the default port 
number. If multiple `rstudio-server` processes are run on the same node the ports 
would only work for one process.

Next, open a new terminal and create an ssh tunnel using the node and port obtained 
in the previous step (e.g. ccs2011:8787):
~~~
$ ssh -L8787:ccs2011:8787 hawk-username@hawklogin.cf.ac.uk
~~~
{: .language-bash}

You should be able to login to Hawk as usual but additioanly you should be able to 
navigate to http://localhost:8787 in your web browser. If everything went well you 
should see something like:
<img src="{{ page.root }}/fig/Rstudio-login.png" alt="Rstudio login" width="50%" height="50%" />

Where you should use your Hawk username and the password obtained before (e.g. RshEFvNoiPEK4)

# Jupyter

[Jupyter](https://jupyter.org/) is a common Python web tool to aid in development.

It can easily be installed with:

~~~
$ module load python/3.7.7-intel2020u1
$ pip install --user jupyterlab
~~~
{: .language-bash}

Then run on Slurm with (this requests 1h of runtime).

~~~
$ srun -n 1 -p htc --account=scwXXXX -t 1:00:00 jupyter-lab --ip=0.0.0.0
~~~
{: .language-bash}

~~~
http://ccs1015:8888/?token=77777add13ab93a0c408c287a630249c2dba93efdd3fae06
 or http://127.0.0.1:8888/?token=77777add13ab93a0c408c287a630249c2dba93efdd3fae06
~~~
{: .output}

Next, open a new terminal and create an ssh tunnel using the node and port obtained 
in the previous step (e.g. ccs1015:8888):
~~~
$ ssh -L8888:ccs1015:8888 hawk-username@hawklogin.cf.ac.uk
~~~
{: .language-bash}

You should be able to navigate to http://localhost:8888 in your web browser. If 
everything went well you should see something like:
<img src="{{ page.root }}/fig/jupyter-lab-home.png" alt="Jupyter Lab Home" width="50%" height="50%" />

Where you should be able to access the files stored your Hawk user account.

{% include links.md %}
