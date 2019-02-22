---
title: "Optimising for Parallel Processing"
author: "Colin Sauze, Ed Bennett"
teaching: 15
exercises: 0
questions:
 - "How can I run several tasks from a single Slurm job."
objectives:
 - "Understand how to use GNU Parallel to run multiple programs from one job"
keypoints:
 - "GNU Parallel lets a single Slurm job start multiple subprocesses"
 - "This helps to use all the CPUs on a node effectively."
---


# Optimising for Parallel Processing


## Running a job on multiple cores

By default most programs will only run one job per node, but all SCW/HPCW nodes have multiple CPU cores and are capable of running multiple processes at once without (much) loss of performance.

A crude way to achieve this is to have our job submission script just run multiple processes and background each one with the `&` operator.

~~~
#!/bin/bash --login
###
#job name
#SBATCH --job-name=test
#SBATCH --output=test.out.%J
#SBATCH --error=test.err.%J
#SBATCH --time=0-00:01
#SBATCH --ntasks=3
###

command1 &
command2 &
command3
~~~
{: .bash}

This will run command1,2 and 3 simultaneously. It also requests 3 cores with the ntasks option.

When command3 finishes the job will end as backgrounded jobs won't keep the job running. An alternative to this is to put a long sleep command in as the last statement, but we need to get the timing accurate for this. If all the commands finish and the sleep is still running we'll be reserving resources we aren't using.

This method has its limits if we want to run multiple tasks after the first ones have completed. Its possible, but scaling it will be harder.


## GNU Parallel

GNU Parallel is a utility specially designed to run multiple parallel jobs. It can execute a set number of tasks at a time and when they are complete run more tasks.

GNU Parallel can be loaded a module called "parallel". Its syntax is a bit complex, but its very powerful.


### A simple GNU Parallel example

For this example we'll just run on a quick test on the head node. First we have to load the module for parallel.

~~~
module load parallel
~~~
{: .bash}

> ## Citing Software
> 
> Each time you run GNU parallel it will remind that you academic tradition requires you to cite the software you used. You can stop this message by running `parallel --citation` once and parallel will then remember not to show this message anymore. Running this will also show you the Bibtex code for citing parallel in your papers.
{: .callout}

The command below will run ls to list all the files in the current directory and it will send the list of files to parallel. Parallel will in turn run the echo command on each input it was given. The `{1}` means to use the first argument (and in this case its the only one) as the parameter to the echo command.

~~~
ls | parallel echo {1}
~~~
{: .bash}

As a short hand we could have also run the command

~~~
ls | parallel echo
~~~
{: .bash}

and it would produce the same output.

An alternate syntax for the same command is:

~~~
parallel echo {1} ::: $(ls)
~~~
{: .bash}

Here we specify what command to run first and the put the data to process second, after the `:::`.


### A more complex example

As an example we're going to use the example data from the Software Carpentry [Unix Shell lesson](http://swcarpentry.github.io/shell-novice/). This features some data from a researcher named Nelle who is studying the North Pacfici Gyre. She has 1520 data files, each of which measure the relative abundnace of 300 different proteins. Each file is named NENE followed by a 5 digit number identifying the sample and finally an A or a B to identify which of two machines analysed the sample.

#### Downloading the Data

First we need to download Nelle's data from the Software Carpentry website. This can be downloaded with the wget command, the files then need to be extracted from the zip archive with the unzip command.

~~~
wget http://swcarpentry.github.io/shell-novice/data/data-shell.zip
unzip data-shell.zip
~~~
{: .bash}

The data we'll be using is now extracted into the directory data-shell/north-pacific-gyre/2012-07-03

~~~
cd data-shell/north-pacific-gyre/2012-07-03/
~~~
{: .bash}

Nelle needs to run a program called `goostats` on each file to process it. During the Unix Shell lesson this data was processed in series by the following set of commands:

~~~
# Calculate stats for data files.
for datafile in $(ls NENE*[AB].txt)
do
    echo $datafile
    bash goostats $datafile stats-$datafile
done
~~~
{: .bash}

The `ls NENE*[AB].txt` command lists all the files which start with "NENE" and end either A.txt or B.txt. The for loop will work through the list of files produced by ls one by one and runs goostats on each one.

Lets convert this process to run in parallel by using GNU Parallel instead. By running

~~~
ls NENE*[AB].txt | parallel goostats {1} stats-{1}
~~~
{: .bash}

We'll run the same program in parallel. GNU parallel will automatically run on every core on the system, if there are more files to process than there are cores it will run a task on each core and then move on to the next once those finish. If we run the time command before both the serial and parallel versions of this process we should see the parallel version runs several times faster.

### Using a list stored in a file

Frequently it is more convenient to specify a list of files, or other arguments, to process by listing them in a file rather than typing them out at the command line. For example, in Nelle's workflow, she may want to ensure that the analysis always tries exactly the same file list, even if one is missing, so she will be alerted to its absence.

To create the file list, recall that we can use `>` to redirect output.

~~~
ls NENE*[AB].txt > files_to_process.txt
~~~
{: .bash}

Now, to tell parallel to use this file as a list of arguments, we can use `::::` instead of `:::`.

~~~
parallel goostats {1} stats-{1} :::: files_to_process.txt
~~~
{: .bash}

### Running Parallel under Slurm

To process using Parallel on a single node, we can use a job submission script very similar to the ones we have been using so far.
Since each compute node on Sunbird and Hawk has 40 cores, Parallel will automatically run 40 different parameters at once. We
need to be careful to request the whole node so that we don't use cores that aren't allocated to us.

First lets create a job submission script and call it `parallel_1node.sh`.

~~~
#!/bin/bash --login
###
#SBATCH --nodes 1                      # request everything runs on the same node
#SBATCH --exclusive                    # request that we get exclusive use of this node
#SBATCH -o output.%J                   # Job output
#SBATCH -t 00:01:00                    # Max wall time for entire job
###

# Ensure that parallel is available to us
module load parallel

# Run the tasks:
parallel bash ./goostats {1} stats-{1} :::: files_to_process.txt
~~~
{: .bash}

We can then run this by using `sbatch` to submit `parallel_1node.sh`.

If we need to process a very large number of files or parameters, we might want to request
to run on more than one node. One way of doing this is splitting the parameter set into
subsets, and running multiple jobs. If the machine is very busy, this has the advantage that
Slurm can fit a single-node job in whenever one node is free, rather than having to wait for
a large number of nodes to become free at the same time. However, the downside is that you
end up with many different submission scripts and parameter sets, so it gets easier to miss one.

To submit a single submission script that runs on many nodes using Parallel, we need to tell Parallel
how to run programs on other nodes than the current one. Slurm provides a built-in program to do this
called `srun`, which we used earlier to run on our interactive allocation. We also need to tell Parallel
to run enough programs to fill all of the nodes that we have allocated. Let's create a file
`parallel_multinode.sh`.

~~~
#!/bin/bash --login
###
#SBATCH --ntasks 80               # Number of processors we will use - 80 will fill two nodes
#SBATCH -o output.%J              # Job output
#SBATCH -t 00:01:00               # Max wall time for entire job
###

# Ensure that parallel is available to us
module load parallel

# Define srun arguments:
srun="srun -n1 -N1"
# -N1 -n1         allocates a single core to each task

# Define parallel arguments:
parallel="parallel -j $SLURM_NTASKS --joblog parallel_joblog"
# -j $SLURM_NTASKS  is the number of concurrent tasks parallel runs, so number of CPUs allocated
# --joblog name     parallel's log file of tasks it has run

# Run the tasks:
$parallel "$srun bash ./goostats {1} stats-{1}" :::: files_to_process.txt
~~~
{: .bash}

This script looks a bit more complicated than the ones we've used so far; the comments explain what each step does.
To use this script for your own workloads, you can ignore the middle section that sets up things for Parallel;
only the last line that calls your code, and the first few lines to control the job parameters, need to be adjusted.

Let's go ahead and run the job by using `sbatch` to submit `parallel_multinode.sh`.

~~~
sbatch parallel.sh
~~~
{: .bash}

This will take a minute or so to run. If we watch the output of `sacct` we should see 15 subjobs being created.

~~~
35590.batch       batch               scw1000          4  COMPLETED      0:0
35590.extern     extern               scw1000          4  COMPLETED      0:0
35590.0            bash               scw1000          1  COMPLETED      0:0
35590.1            bash               scw1000          1  COMPLETED      0:0
35590.2            bash               scw1000          1  COMPLETED      0:0
35590.3            bash               scw1000          1  COMPLETED      0:0
35590.4            bash               scw1000          1  COMPLETED      0:0
35590.5            bash               scw1000          1  COMPLETED      0:0
35590.6            bash               scw1000          1  COMPLETED      0:0
35590.7            bash               scw1000          1  COMPLETED      0:0
35590.8            bash               scw1000          1  COMPLETED      0:0
35590.9            bash               scw1000          1  COMPLETED      0:0
35590.10           bash               scw1000          1  COMPLETED      0:0
35590.11           bash               scw1000          1  COMPLETED      0:0
35590.12           bash               scw1000          1  COMPLETED      0:0
35590.13           bash               scw1000          1  COMPLETED      0:0
35590.14           bash               scw1000          1  COMPLETED      0:0
~~~
{: .output}

The file `parallel_joblog` will contain a list of when each job ran and how long it took.

~~~
Seq     Host    Starttime       JobRuntime      Send    Receive Exitval Signal  Command
1       :       1542803199.833       2.205      0       0       0       0       srun -n1 -N1 bash ./goostats NENE01729A.txt stats-NENE01729A.txt
2       :       1542803199.835       2.250      0       0       0       0       srun -n1 -N1 bash ./goostats NENE01729B.txt stats-NENE01729B.txt
3       :       1542803199.837       2.251      0       0       0       0       srun -n1 -N1 bash ./goostats NENE01736A.txt stats-NENE01736A.txt
4       :       1542803199.839       2.282      0       0       0       0       srun -n1 -N1 bash ./goostats NENE01751A.txt stats-NENE01751A.txt
5       :       1542803202.040       2.213      0       0       0       0       srun -n1 -N1 bash ./goostats NENE01751B.txt stats-NENE01751B.txt
6       :       1542803202.088       2.207      0       0       0       0       srun -n1 -N1 bash ./goostats NENE01812A.txt stats-NENE01812A.txt
7       :       1542803202.091       2.207      0       0       0       0       srun -n1 -N1 bash ./goostats NENE01843A.txt stats-NENE01843A.txt
8       :       1542803202.124       2.208      0       0       0       0       srun -n1 -N1 bash ./goostats NENE01843B.txt stats-NENE01843B.txt
9       :       1542803204.257       2.210      0       0       0       0       srun -n1 -N1 bash ./goostats NENE01978A.txt stats-NENE01978A.txt
10      :       1542803204.297       2.173      0       0       0       0       srun -n1 -N1 bash ./goostats NENE01978B.txt stats-NENE01978B.txt
11      :       1542803204.300       2.223      0       0       0       0       srun -n1 -N1 bash ./goostats NENE02018B.txt stats-NENE02018B.txt
12      :       1542803204.336       2.230      0       0       0       0       srun -n1 -N1 bash ./goostats NENE02040A.txt stats-NENE02040A.txt
13      :       1542803206.470       2.216      0       0       0       0       srun -n1 -N1 bash ./goostats NENE02040B.txt stats-NENE02040B.txt
14      :       1542803206.472       2.276      0       0       0       0       srun -n1 -N1 bash ./goostats NENE02043A.txt stats-NENE02043A.txt
15      :       1542803206.526       2.270      0       0       0       0       srun -n1 -N1 bash ./goostats NENE02043B.txt stats-NENE02043B.txt
~~~
{: .output}


### More complex command handling with Parallel

We can run parallel with multiple arguments. For example `parallel echo "hello {1} {2}" ::: 1 2 3 ::: a b c`

Which runs each possible combination of the first and second arguments to give:

~~~
hello 1 a
hello 1 b
hello 1 c
hello 2 a
hello 2 b
hello 2 c
hello 3 a
hello 3 b
hello 3 c
~~~
{: .output}

If we only want to run each argument as a pair, so there are only three outputs adding the `+` symbol to the end of the second `:::` will achieve this:

~~~
parallel echo "hello {1} {2}" ::: 1 2 3 :::+ a b c

hello world 1 a
hello world 2 b
hello world 3 c
~~~

These options also work when using `::::` to take the list from a file instead.

{: .output}

