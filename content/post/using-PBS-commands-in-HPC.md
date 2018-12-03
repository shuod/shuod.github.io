+++
Categories = ["Linux", "Server"]
Description = " Some useful commands to manage jobs on HPC"
Tags = ["Linux", "HPC"]
date = "2016-05-13T13:32:24-05:00"
title = "Using PBS commands in HPC"
+++

PBS, or Portable Batch System is one of the popular job scheduler used in HPC (High Performance Computer) cluster system. It provides useful commands to help with job management. Here I just list some of them for note.


Some useful PBS commands:

* qsub
* showq
* qstat
* qdel
* qpeek
* pbsnode
* qalter

<!--more-->

Those commands with some most used parameters:

## qsub 
#### qsub script

The script is used to put in some control in HPC jobs when submitted them. Like how much time/processors/memory you would like to assign to your job.

A basic script template looks like this
```
#! /bin/bash

#  The compute node will inherited the enviromental variables defined in user's account.
#PBS -V


#  Each node on cowboy cluster has 12 processors and 32GB RAM.  If your job will
#  need all 32 GB RAM, request ppn=12. if you need more than 1 node, then set 
#  nodes=n and still use ppn=12
#PBS -l nodes=1:ppn=12

#  Specify the queue type of your jobs; batch/express/bigmem; batch is the
#  regular type; express is for testing purpos which will have high priority but
#  will be confined to use 1 node and 1h walltime
#PBS -q batch


#  To join output and error log so can be viewed together.
#PBS -j oe

#  Your job will be killed if it goes over its walltime.  
#  However, requesting a shorter walltime will get your job running sooner.
#PBS -l walltime=48:00:00

#  Sending email when dones
#  PBS -m bae email sent when
#  e when end, a when aborted, b when begin
#PBS -m a email 


# Job start
date
module load gaussian/g09
cd $PBS_O_WORKDIR
echo "Output from the application begin"




```

###### Some variables in script

|  Variable			|  Meaning											|
|:-----------------------|:--------------------------------------------------|
|  $PBS_HOME 		|  The home dirrectory Where you ran qsub command 	|
|  $PBS_O_LOGNAME 	|  Your UID 										|
|  $PBS_O_HOST 		|  Your host or node name from where you ran qsub 	|
|  $PBS_O_QUEUE  	|  The queue name from which you submitted your job |
|  $PBS_QUEUE 		|  The queue name where your job is running 		|
|  $PBS_JOBID 	 	|  The job ID 										|
|  $PBS_JOBNAME  	| The job name  									|
|  $PBS_ENVIRONMENT |  The PBS enviromental variables 					|
|  $PBS_O_WORKDIR 	|   The working directory from where you ran qsub	|



#### qsub script
Also we could use qsub in interactive jobs:
qsub -I

#### useful qsub script snipplet

##### PBS -W: job dependency

`#PBS -W depend = Logic:JobID`

This is used when a later job depends on succussful run of a previous one or similiar situations given by the following logic:

|  Keyword			|	Logic																	|
|:-----------------------|:--------------------------------------------------------------------------|
|  after	 		|  this job is scheduled after JobID have started 							|
|  afterok		 	|  this job is scheduled after JobID have completed without errors			|
|  afternotok 		|  this job is scheduled after JobID have completed with errors 			|
|  afterany		  	|  this job is scheduled after JobID have completed (ignore error if any)	|
|  before	 		|  After this job begins, JobID will schduled						 		|
|  beforeok 	 	|  After this job completed without errors, JobID will be scheduled			|
|  beforenotok  	|  After this job completed with errors, JobID will be scheduled			|
|  beforeany		|  After this job completed (ignore error if any), JobID will be scheduled 	|

Example:
```
#!/bin/bash
JOBONE=`qsub program1.pbs`
JOBTWO=`qsub -W depend=afterok:$JOBONE program2.pbs`
JOBTHREE=`qsub -W depend=afterok:$JOBTWO program3.pbs`

exit 0
```

##### PBS -t : job arrays

This is useful when you try to run the same program but different input data. You can name the input file in such a way that the data file names are paired with the ID of jobs in the job array. So it would be controllable in a shell script loop.

` #PBS -t numberID`

Example:

```
#!/bin/sh
#PBS -V
#PBS -l nodes=1:ppn=12
#PBS -q batch
#PBS -t 1-10
#PBS -j oe
#PBS -l walltime=00:05:00

cd ~/program
./myprogram -input=file-${PBS_ARRAYID}

```

So myprogram will use file-1 as the input file at its first time running. Then it will use file-2 as the input file at its second time running and so on.




## showq 
#### showq -u Username

  Tell you information of jobs submited by username in commond fields.

` showq -u Prometheus `

| Job Name   |  Username | Status  |  Processors | Remaining  |  Start Time |
|:----------:|:---------:|:-------:|:-----------:|:----------:|-------------|
| 575279  | Prometheus  | Running | 12  | 4:23:28:09 | Fri May 13 12:29:17|
| 575280  | Prometheus  | Running | 12  | 4:23:32:16 | Fri May 13 12:33:24|

###### Some explainations:
* The Job Name is also the Job ID that could be used later with "qdel" command to terminate unwanted job.
* The Remaining time is how much time left for your job. when time is up the job will be terminated.



## qstat
#### qstat -q

Tell you  the information of queue system in this HPC facility, like how much is the walltime for each type of queue, how many jobs of each types are runding/avaliable/queueing.


#### qstat -u Username

Tell you information of jobs submited by username in commond fields.

`qstat -u Prometheus`

| Job ID   |  Username | Queue  |Jobname   | SessID  | NDS | TSK| Req Memory| Req Time| Status| Elap Time|
|:--------:|:---------:|:----------:|:---------:|:----------:|:--------:|:---------:|:--------:|:--------:|:--------:|:--------:|
| 575279.mgmt1  | Prometheus  | batch | submit_146275036  | 26450  | 1 | 12 | -- | 120:0 | R | 00:42 |
| 575280.mgmt1  | Prometheus  | batch | submit_146275036  | 26450  | 1 | 12 | -- | 120:0 | R | 00:38 |

###### Some explainations:
* Some different fields than "showq" and sometime are convinient if you want those information.
* Elapse Time is just opposite to remaining time that is given with showq.

#### qstat -f Jobname

Tell you the detailed information of the JobID.

`qstat -f 575269`

###### Some explainations:

* The Job Name could be accquired from showq or qstat command.

* There will be lots of information on this job, just mention a few useful ones: the starting directory, the bash environment variables, the path of "INCLUDE", login host, etc

## qdel

#### qdel Jobname

To kill an unwanted job.

`qdel 575279`


###### Some explainations:

* Don't worry if you accidentally give a wrong jobname (maybe happened to be other username's jobname). It simply will not work, because you don't have the privillage to kill other user's job.

## qpeek

#### qpeek jobname

`qpeek 575279`

To check the log file for the job. if you chose to combine the output and error log, this will show both or you could specify which one you want to see:

```
#  This will show the error logfile only
qpeek -e 575279

#  This will show the end of logfile, keep listening until Ctrl+C is pressed
qpeek -f 575279
```


## pbsnodes

#### pbsnodes [-a]

To show the information of all nodes in the HPC.

`pbsnodes -a`

After get the nodes information (e.g. nodename), you could then use command from linux to check out the information.
```
ssh n232  # switch to "n232" node
free -mo  # checkout the memory usage
top -b -n 1 -u Promotheus
```



## qalter

#### qalter [options] Jobname

To change the property of a job (like the job type is batch or express) without retract it so you will still maintain the position in the queue

`qalter -l walltime=4:00:00:00`
Change walltime to 4 days. Depending on your privillage, as a user you actually could only decrease you walltime but couldn't set it to longer.



## Reference
1. https://sites.google.com/a/umich.edu/engin-cac/resources/systems/nyx/pbs
2. http://linuxinfo.physik.hu-berlin.de/pbs.html
3. https://albertsk.files.wordpress.com/2011/12/pbs.pdf





