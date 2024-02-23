---
title: "Accessing webtools"
teaching: 15
exercises: 15
questions:
- "How do I run a webtool for framwork/language X?"
objectives:
- "Run webtools via OpenOndemand service."
- "Run webtools software directly via Slurm"
- "Access those webtools from a browser."
keypoints:
- "OpenOndemand is a powerful way to access GUIs."
- "Accessing Hawk compute nodes directly requires some care but can be powerful in running some tools."
---

# OpenOndemand

[OpenOndemand.org](https://openondemand.org) is a community that supports the use of the OpenOndemand software and on Hawk this software is available (currently only on-campus and via VPN) at [ARCOndemand](https://arcondemand.cardiff.ac.uk).  This service provides a web interface to common functionality on the cluster. Introductory video is available [Introduction to ARCOndemand](https://cardiff.cloud.panopto.eu/Panopto/Pages/Viewer.aspx?id=61664718-88c3-4e96-ae6c-ae6e00af7dea)

The aim of OpenOndemand is to make software easy to use.  Most popular tools are Rstudio, Jupyter, Matlab and Linux Desktops that are available all via the browser running on a compute node with the resources you need to run the job. You can also perform 3D visualisation via use of VirtualGL to access the GPU on the compute nodes.

Below are some points about specific application in OpenOndemand

## Rstudio (not included on sunbird)

[Rstudio](https://rstudio.com) is a useful tool to program and access R.  Whilst we recommend running the GUI via ARCOndemand it is possible to load the module within a Slurm job to get access to the same version of R as used via ARCOndemand.

~~~
$ module load rstudio-server
~~~
{: .language-bash}

> ## Rstudio versions
>
> There are a number of versions such as Rstudio and Rstudio Server, Rstudio is
> a desktop app whilst Rstudio Server is a web browser.
{: .callout}

To run R as found inside Rstudio we can use (this wraps the required code to set it up).  
~~~
$ rstudio-server-R
~~~
{: .language-bash}

When this runs we see:
~~~
R version 4.1.2 (2021-11-01) -- "Bird Hippie"
Copyright (C) 2021 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

  Natural language support but running in an English locale

R is a collaborative project with many contributors.
Type 'contributors()' for more information and
'citation()' on how to cite R or R packages in publications.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

>
~~~
{: .output}

Rstudio keeps its R environments in a different place `$HOME/R-Rstudio` which is bound to `$HOME/R` when run inside its environment (using containerisation).

Therefore you can develop using ARCOndemand but also run R as expected inside a Slurm when needed (e.g. scaling up to a larger job).

## Jupyter

[Jupyter](https://jupyter.org/) is a common Python web tool to aid in development.

To install packages which are available inside Jupyter please use:

~~~
$ module load anaconda
$ source activate
$ conda create -n my_env
$ conda activate my_env
$ conda install jupyterlab
~~~
{: .language-bash}

This sets up a base environment which Jupyter will see when run inside ARCOndemand service.

Long running jobs inside Jupyter do not work well (once disconnected from server the calculation will stop.  This can be used with

~~~
%%capture stored_output
import time
time.sleep(30)
print("Hi")
~~~
{: .language-python}

Then accessed later using:

~~~
stored_output.show()
# Hi
~~~
{: .language-python}

## Desktops

Desktops are easily setup within ARCOndemand.  If using a GPU node to run 3D visualisations then `vglrun` can be used.  Please get in contact with ARCCA to get further help with this feature.

