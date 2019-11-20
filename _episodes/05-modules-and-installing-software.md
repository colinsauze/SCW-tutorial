---
title: "Working with Modules and Installing Software"
author: "Colin Sauze"
teaching: 15
exercises: 30
questions:
 - "How do I get access to additional software?"
objectives:
 - "Understand how to load a module"
 - "Understand how to install pip packages for Python"
keypoints:
 - "A lot of software is only available by loading extra modules. This helps prevent problems where two packages are incompatible."
 - "Python 3 is one such package."
 - "If you want to install pip packages use the `--user` option to store the packages in your home directory."
---

# Using & installing software

On most cluster systems, all the software installed is not immediately available for use;
instead, it is loaded into your environment incrementally using a module system.

The `module` command controls this.
You can get a list of available software with the `module avail` command. This should return a long list of available software.

One common piece of software that isn't installed on the Supercomputing Wales hubs (without a module) is Python version 3. If we attempt to run `python3` from the command line it will respond with an error:

~~~
[s.jane.doe@sl1 ~]$ python3
~~~
{: .bash}

~~~
-bash: python3: command not found
~~~
{: .output}

**Note that the login nodes and the compute nodes have identical
configurations in terms of what software is available, so you can
discover if your program will run from the login nodes**

From the output of the `module avail` command there should have been
an entry `anaconda/2019.03` in the `/app/local/modules/langauges` section
near the bottom. Anaconda is a distribution including a variety of
Python packages, and tools for managing them. Let's go ahead and load it.

~~~
[s.jane.doe@sl1 ~]$ module load anaconda/2019.03
[s.jane.doe@sl1 ~]$ source activate
[s.jane.doe@sl1 ~]$ python3
Python 3.7.0 (default, Jul 13 2018, 10:08:05)
[GCC Intel(R) C++ gcc 4.8.5 mode] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> exit()
[s.jane.doe@sl1 ~]$
~~~
{: .bash}

> ## Legacy HPC Wales and Raven Modules
> All of the old modules which were available on HPC Wales and the old Cardiff Raven system
> are available by running either `module load hpcw` or `module load raven`. (Raven modules are only available on Hawk.)
> Please note that much of this software is outdated and may by suboptimal as its not compiled
> to take advantage of the newer CPU architectures on Supercomputing Wales.
{: .callout}


## Installing Python modules

Python is a popular language for researchers to use and has modules
(aka libraries) that do all kinds of useful things, but sometimes the
module you need won't be installed. Many modules can be installed
using the `pip` (or `pip3` with Python 3) command. Since we don't have
administrative rights to the supercomputer, we can't install these
into the shared base Anaconda environment; instead, we need to create
a local one.

~~~
$ conda create -n scw_test python=3.7
~~~
{: .bash}

This tells the `conda` command to `create` a new environment, to
give it the name `scw_test`, and to install Python 3.7 into it.
Conda will take a little time to work out what it needs to install,
and once you confirm by typing `y`, then place it in a new directory
in `~/.conda/envs`.

Once your environment is created, you can activate it so you can
use it to work in with the command

~~~
$ conda activate scw_test
~~~
{: .bash}

The prefix at the start of your prompt will now change from `base`
to `scw_test`, to indicate the environment that you have active.
Similarly, `which python` now returns
`~/.conda/envs/scw_test/bin/python`, indicating that this is now
where Python will run from if you run `python`.

So far we have created a relatively bare environemnt, but we can
install packages into the new environment just as we can on our own
machines. Let's now install Matplotlib into this environment:

~~~
$ conda install matplotlib
~~~
{: .bash}

Conda automatically works out which extra packages need to be present
for Matplotlib to work, and then prompts to install them.

You could alternatively have specified `matplotlib` directly to the
`conda create` command, and it would have been installed when the
environment was created.

If you wanted to create a full Anaconda Python installation to base
your environment on, you can do this with
`conda create -n [name] anaconda`; we don't do this here because
it creates a very large number of files and takes a substantial
time to download and install. In general, it's better to only
install packages that you need, since they are less likely to
conflict with each other.

Using `pip` works very similarly to Python; provided the Anaconda
module is loaded and your Conda environment is activated, then `pip`
will automatically install into your Conda environment and not try to
install at the system level.

For more detail on using Python with Supercomputing Wales, see the
training on [High Performance
Python](https://edbennett.github.io/high-performance-python).


## How to install other software yourself

*For Perl modules and R packages*, we encourage you to set up
directories in your home and/or lab folders for installing your own
copies locally. Instructions on how to install these are available
from the [How
To](https://portal.supercomputing.wales/index.php/index/how-to-guides-archive/)
pages on the Supercomputing Wales portal.

## Requesting additional software

*If software you need is not installed*, we encourage you to do local
installs in your home or project shared directory for bleeding-edge
releases, software you are testing, or software used only by your
project. For programs that are commonly used by your domain, field,
or department, please submit a
[software install request](email:support@supercomputingwales.ac.uk).
Note that due to demand and the complex nature of software installs,
it may take a while for us to complete these requests.

Commercial software will require the appropriate licenses.


# Exercises

> ## Running a Python script
>
> 1. Create a new file using `nano` and call it `plot.py`. Give it the
>    following contents:
>    ~~~
>    import matplotlib as mpl
>    mpl.use('Agg') #set the backend to Agg to make a png file instead of displaying on screen
>    import matplotlib.pyplot as plt
>    plt.plot(range(10))
>    plt.savefig('temp.png')
>    ~~~
>    {: .python}
> 2. Create a new job submission script called `plot.sh` containing
>    the following:
>    ~~~
>    #!/bin/bash --login
>    ###
>    # job name
>    #SBATCH --job-name=plotgraph
>    # job stdout file
>    #SBATCH --output=plotgraph.out.%J
>    # job stderr file
>    #SBATCH --error=plotgraph.err.%J
>    # maximum job time in D-HH:MM
>    #SBATCH --time=0-00:01
>    # number of tasks you are requesting
>    #SBATCH --ntasks=1
>    # memory per process in MB
>    #SBATCH --mem=2
>    # number of nodes needed
>    #SBATCH --nodes=1
>    # specify our current project
>    # change this for your own work
>    #SBATCH --account=scw1389
>    # specify the reservation we have for the training workshop
>    # remove this for your own work
>    # replace XX with the code provided by your instructor
>    #SBATCH --reservation=scw1389_XX
>    ###
>    module load anaconda/2019.03
>    source activate scw_test
>    python3 plot.py
>    ~~~
>    {: .bash}
> 3. Run the job with `sbatch plot.sh`. Did the job complete
>    successfully? Are there any errors in the error file?
> 4. Fix the cause of the errors by adjusting (or removing) the appropriate parameter.
> 5. Copy back the resulting file, `temp.png` using SCP or SFTP and
>    view it on your computer.
> 6. Add the following lines to the Python script (between the
>    `plt.plot` line and the `plt.savefig` line) to make it embed the job
>    ID into the title of the image:
>    ~~~
>    import os
>    jobid = str(os.environ.get('SLURM_JOBID'))
>    plt.title(f'Job id {jobid}')
>    ~~~
>    {: .python}
>    Run the job again and look at the output image. Try using some of
>    the other Slurm environment variables from [this
>    list](https://www.glue.umd.edu/hpcc/help/slurmenv.html), too.
>
> > ## Solution
> > 1. You will get errors about the amount of memory being used. These might show up as a segmentation fault rather than a memory error. For some reason this script can be very slow to run sometimes, so increasing the timeout can help too---only do this if you are getting timeout errors. Around 50MB of memory and 5 minutes should be enough.
> > 2. Your Python code should now read:
> >
> > ~~~
> > import matplotlib as mpl
> > mpl.use('Agg') #set the backend to Agg to make a png file instead of displaying on screen
> > import os
> > import matplotlib.pyplot as plt
> > plt.plot(range(10))
> > jobid=str(os.environ.get('SLURM_JOBID'))
> > plt.title(f'Job id {jobid}')
> > plt.savefig('temp.png')
> > ~~~
> > {: .bash}
> >
> {: .solution}
{: .challenge}

