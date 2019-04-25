---
title: "Logging in to Supercomputing Wales"
author: "Colin Sauze"
teaching: 20
exercises: 15
questions:
 - "How can do I log in to Supercomputing Wales?"
objectives:
 - "Understand how to log in to the Supercomputing Wales hubs"
 - "Understand the difference between the login node and each cluster's head node."
keypoints:
 - "`ssh sunbird.swansea.ac.uk` or `ssh hawklogin.cf.ac.uk` to log in to the system"
 - "`sinfo` shows partitions and how busy they are."
 - "`slurmtop` shows another view of how busy the system is."
---



# Logging in

Your username is your institutional ID prefixed by `a.` for
Aberystwyth users, `b.` for Bangor users, `c.` for Cardiff users and `s.` for Swansea users. External collaborators will have a username beginning with `x.`.

Aberystwyth and Swansea users (and their external collaborators) should log in to the Swansea Sunbird system by typing:

~~~
$ ssh username@sunbird.swansea.ac.uk
~~~
{: .bash}

Bangor and Cardiff Users (and their external collaborators) should log in to the Cardiff Hawk system by typing:

~~~
$ ssh username@hawklogin.cf.ac.uk
~~~
{: .bash}

If you use Windows and haven't installed the Git bash shell, you can instead use [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
and enter either `sunbird.swansea.ac.uk` or `hawklogin.cf.ac.uk` in the hostname box.


## What's available?

### Supercomputing Wales

These figures may still be subject to some change and might have been sourced from out of date documents.

|Partition|Number of Nodes|Cores per node|RAM|Other|
|-------|----|----|------|----|------|
|Swansea Compute|122|40|382GB||
|Swansea GPU|4|40|382GB|2x Nvidia V100 (5120 core, 16GB RAM)|
|Swansea Data Lake|1|72|1500GB|Installed with Swansea system, and intended for e.g. Hadoop and Elastic Stack users. Not integrated with the main Sunbird system; contact Support or your RSE team for access details.|


|Cluster|Number of Nodes|Cores per node|RAM|Other|
|-------|----|----|------|----|------|
|Cardiff Compute|136|40|190GB||
|Cardiff HTC|26|40|190GB||
|Cardiff High Memory|26|40|382GB||
|Cardiff GPU|13|40|382GB|2x Nvidia P100 (3584 core, 16GB RAM)|
|Cardiff Dev|2|40|190GB||
|Cardiff Data Lake|2|?|?|Will be installed later. Intended for Hadoop and Elastic Stack users.|

Aberystwyth and Swansea users are expected to use the Swansea system and will need to make a case for why they would need to use the Cardiff system. Bangor and Cardiff users are expected to use Cardiff, and external users are expected to use the same system as the owner of the project of which they are a member.


### Slurm

Slurm is the management software used on Supercomputing Wales. It lets
you submit (and monitor or cancel) jobs to the cluster, and chooses
when and where to run them.

Other clusters might run different job management software such as LSF, Sun Grid Engine or Condor, although they all operate along similar principles.


### How busy is the cluster?

The ```sinfo``` command tells us the state of the cluster. It lets us know what nodes are available, how busy they are and what state they are in.

Clusters are sometimes divided up into partitions. This might separate some nodes which are different to the others (e.g. they have more memory, GPUs or different processors).

~~~
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
compute*     up 3-00:00:00      1   fail scs0042
compute*     up 3-00:00:00      1 drain* scs0004
compute*     up 3-00:00:00      2    mix scs[0018,0065]
compute*     up 3-00:00:00     86  alloc scs[0001-0003,0005-0017,0019-0035,0043-0046,0049-0064,0066-0072,0097-0122]
compute*     up 3-00:00:00     32   idle scs[0036-0041,0047-0048,0073-0096]
gpu          up 2-00:00:00      4   idle scs[2001-2004]
~~~
{: .output}

 * The `*` after `compute` means that this is the default partition.
 * `AVAIL` tells us if the partition is available.
 * `TIMELIMIT` tells us if there's a time limit for jobs
 * `NODES` is the number of nodes in this partition in this particular
   state.
 * `STATE` describes what these nodes are doing:
     * `drain` means that the node will become unavailable once the
       jobs currently running on it complete.
	 * `down` means that the node is is powered off or otherwise
       unavailable for use.
	 * `alloc` means that the node is fully allocated; all CPU cores
       are in use running users' software.
	 * `mix` means that some of the CPU cores on a node are allocated
       to a user, and others are available for use.
	 * `idle` means that the node is not currently allocated, and is
       available for use.



# Exercises

> ## Logging into Supercomputing Wales
> 1. In your web browser go to [https://my.supercomputing.wales](https://my.supercomputing.wales) and log in with your university username and password.
> 2. Click on "Reset SCW Password" and choose a new password for logging into the HPC. Your username is displayed in the "Account summary" box on the main page. Its usually a/b/c/s. and your normal university login details.
> 3. Log in to sunbird.swansea.ac.uk or hawklogin.cf.ac.uk using your SSH client.
> 4. Run the `sinfo` command to see how busy things are.
> 5. Try `sinfo --long`, what extra information does this give?
{: .challenge}

