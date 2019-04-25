---
title: "What next?"
author: "Colin Sauze"
questions:
 - "What are the best practices for using an HPC system?"
 - "How can I take this forward and put it into practice?"
objectives:
 - "Understand HPC best practice"
 - "Know what steps are needed to use Supercomputing Wales for research"
---


## HPC Best Practice

When you start using the machines in your own research, bear the
following points in mind:

* Don't run jobs on login/head nodes
* Don't run too many jobs at once. The Supercomputing Wales hubs have a limit of 26 nodes/1040 cores
* Don't use all the disk space on scratch. Don't leave old files on there.
* Don't create/leave excessive numbers of files on scratch. Large file counts (100s of thousansd or millions) can cause problems for the filesystem.
* Try to use all the cores on a node, especially if you take the node exclusively. Sometimes with large memory jobs this isn't possible.
* Make jobs that last at least a few minutes, lots of small jobs creates excess load on the scheduler. Use something like GNU Parallel to make each Slurm job do several things.

Again, working on a cluster is working in a big sandbox, with people of all ages and skills. So it is
important to work carefully and be considerate. Please visit our list of Common Pitfalls and
Fair Use/Responsibilities pages so that you'll be a good member of the community...

[Common Pitfalls](https://rc.fas.harvard.edu/resources/documentation/common-odyssey-pitfalls/)
[Fair Use/Responsibilities:](https://rc.fas.harvard.edu/resources/responsibilities/)


## Getting access to Supercomputing Wales for your own research

In this training you have joined a project dedicated to training new
users. You will be able to run test jobs on this project for the next
few days, after which point your access will be revoked. (You will
receive an email informing you of this.)

To use the system in production in your research, you will need to
either create or join a project. If you are working on a project that
is already making use of the Supercomputing Wales resources, then you
can ask them for the project identifier, and request to join it in the
same way as you did for the training project, via [My Supercomputing
Wales](https://my.supercomputing.wales).

If you project is new to Supercomputing Wales, you will need to apply
for a new project. You can do this via [My Supercomputing
Wales](https://my.supercomputing.wales); if you have yet to log in,
then you do so by selecting your institution from the home page, and
then logging in with your institutional email and password. The first
time you log in, you will be asked to agree to the terms and
conditions, and your account will need to be approved by the technical
team before you will be able to access the machine. While you are
waiting for approval, you will still be able to submit a project
application, however.

The "Create Project
Application" button in the lower-left pane will take you to the
application form. This requests certain details about the nature of
the work you will be doing---this includes both the sorts of
computations that will be performed, and also the wider nature of the
research that the computations will support. It also asks for some
detail around the resources that you will need, including the number
of core hours, the amount of memory, and the amount of disk space that
your work will need. This allows us to check that your project will
fit on the system, and if necessary work with you to either make it
work more efficiently, or to gain access to more powerful resources
outside of Swansea. If you are not a permanent staff member, you will
need to specify a "project owner" (also known as a PI) who will
authorise your access to the system; this will typically be your
supervisor if you are a research student, or the academic you are
collaborating with if you are a postdoctoral researcher.

Projects are assessed by Supercomputing Wales staff who are looking for two key targets:

  * Grant income that can be attributed to Supercomputing Wales.
  * Science Outputs (e.g. journal papers)

At this stage you do NOT need to pay any money to Supercomputing
Wales, simply attribute that the grant funding required access to the
system. Funding which attributes other projects funded by the Welsh
European Funding Office (WEFO) cannot be counted towards
Supercomputing Wales.

If you are writing a grant application and intend to use
Supercomputing Wales please mention it in the grant and let us
know. The Supercomputing Wales project as a whole has a target to
bring in approx £8 million of research funding from sources other than
WEFO.


## Supercomputing Wales Research Software Engineers

While this training course is aimed at giving you enough experience
and knowledge to get started, it can't cover all possible use cases.
The Research Software Engineers who have written and delivered today's
training also work with individual researchers and research groups to
advise and assist on making optimal use of the available
facilities. Things that they can provide assistance with include:

* Converting existing software to run on the HPC system
* Optimising code to run more efficiently on HPC systems
* Writing new software
* Helping with training, on-boarding and project development

If you feel you'd benefit from more bespoke support from your local
RSE team, then speak to one of them before you leave and they will let
you know the best way to proceed.
