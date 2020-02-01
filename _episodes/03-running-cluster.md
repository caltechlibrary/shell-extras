---
title: "Running Bash Scripts on an HPC Cluster"
teaching:30
exercises: 0
questions:
- "How do I run a bash script on a HPC cluster ?"
objectives:
- "Learn the organization of a HPC cluster"
- "Learn the SLURM scheduler"
---

Let's ssh back to the cluster, where we're copied Nell's goobash files and the
bash script we created to automate her analysis. Now we want to modify the
script such that it can run on the HPC cluster.

## Job scheduler
An HPC system might have thousands of nodes and thousands of users. How do we decide who gets what
and when? How do we ensure that a task is run with the resources it needs? This job is handled by a
special piece of software called the scheduler. On an HPC system, the scheduler manages which jobs
run where and when.

The scheduler for the clusters we'll use today us SLURM. We have to provide
SLURM details on how to run our job. Be do this by adding special comments at
the top of our bash script. 

First add 

~~~
#SBATCH --job-name="goostats"
#SBATCH --output="goostats.%j.%N.out"
~~~
{: .bash}

to the top of your script. This is the name for your job and the output file name
for anything that would normally show up on the command line. The '%j' variable
is the unique job id that SLURM will assign, and %N is the hostname where the
job ran. You can customize these names to be whatever your want.

Next we'll set information about what computing resources the job needs. These
include the number of individual tasks your job will generate (the number of
processors you need), the number of nodes (computers) yur job needs, and the
amount of memory your job requires. Choosing these values depends on the setup
of the cluster (how many cores are available per node) and how well your code
works when split up among many CPUs (paralellization). You can experiment with
these values to determine which combo gives you the best performance.

~~~
#SBATCH --ntasks=1   # number of processor cores (i.e. tasks)
#SBATCH --nodes=1   # number of nodes
#SBATCH --mem-per-cpu=1G   # memory per CPU core
~~~
{: .bash}

Next we add how long we expect the calculation to take. It's better to
overestimate this value, because if your calculation is still running after
this time the scheduler will kill it.

~~~
#SBATCH --time=00:30:00   # walltime
~~~
{: .bash}

The next setting will depend on what cluster you're using. For comet, you need
to provide the queue where the job will run. We'll use debug because we want
the job to run quickly and we don't need many resources

~~~
#SBATCH --partition=debug
~~~
{: .bash}

The Caltech HPC cluster doesn't have queues, and instead uses a quality of
service flag to indicate test jobs

~~~
#SBATCH --qos=debug
~~~
{: .bash}

If you have access to multiple accounts to charge usage, you may have to
specify which to use.

~~~
#SBATCH --account=
~~~
{: .bash}

~~~
salloc
~~~
{: .bash}

If you want, you can have the scheduler send you emails when the job starts,
finishes, or has a problem.

~~~
#SBATCH --mail-user=myemail@example.com   # email address
#SBATCH --mail-type=BEGIN
#SBATCH --mail-type=END
#SBATCH --mail-type=FAIL
~~~
{: .bash}

You can then send your script to the scheduler by typing

~~~
$ sbatch do-stats.sh 
~~~
{: .bash}

You can check how your job is doing by typing

~~~
$ squeue
~~~
{: .bash}

or use the -u option to filter out only the jobs for your username.

Your jobs will have a status indicated by the ST column, likely PD for something that is still in the
queue or R for something that is running. All statuses are listed at https://slurm.schedmd.com/squeue.html

~~~
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
          31263068     debug goostats tmorrell PD       0:00      1 (None)
~~~
{: .bash}

Once your job is completed, you should see that stats- files have been created,
as we expect for this bash script. There is also a new file named something
like 'goostats.31263068.comet-14-01.out'. Type

~~~
$ cat goostats.31263068.comet-14-01.out
~~~
{: .bash}

and you'll see the same output as you would if you ran the script on the
command line.


{% include links.md %}
