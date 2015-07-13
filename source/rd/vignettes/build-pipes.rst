An easy and quick way to build a workflow is create two separate files.
First is a table with commands to run the other has details regarding
how the modules are stitched together. In the rest of this document we
would refer to them as flow\_mat and flow\_def respectively.

Both these files have a ``jobname`` column which is used as a ID to
connects them both.

Ingredients
-----------

.. code:: r

    exdata = file.path(system.file(package = "flowr"), "extdata")
    flow_mat = read_sheet(file.path(exdata, "example1_flow_mat.txt"))
    flow_def = read_sheet(file.path(exdata, "example1_flow_def.txt"))

Flow Definition
~~~~~~~~~~~~~~~

Each row in this table refers to one step of the pipeline. It describes
the resources used by this step and also its relationship with other steps.
Especially the step immediately prior to it.

This is a tab separated file, with a minimum of 4 columns.

-  ``jobname``: Name of the step
-  ``sub_type``: Submission type, how should multiple commands of this
   step be submission. Can take values ``serial`` or ``scatter``.
-  ``prev_job``: Short for previous job. This can be NA/./none if this
   is a independent step, and not previous step is required for this to
   start.
-  ``dep_type``: Dependency type, refers to the relationship of this job
   with the that defined in previous job. This can take values ``none``,
   ``gather``, ``serial`` or ``burst``.

Apart from these, there are several other variables which define the
resource requirements of each step. This gives great amount of
flexibility to the user in choosing CPU, wall time, memory and queue for
each step (These are all passed along to the HPCC platform).

-  ``cpu_reserved``
-  ``memory_reserved``
-  ``nodes``
-  ``walltime``
-  ``queue``

Most cluster platform accept these arguments. These are used to fill up
the variables defined in curly braces. Example ``{{{CPU}}}`` in this
file for
``torque <https://github.com/sahilseth/flowr/blob/master/inst/conf/torque.sh>``\ \_\_

If these are not included in the flow_def, their values should be explicitly defined in the submission template. 

Here is a example
``flow_def file <https://raw.githubusercontent.com/sahilseth/flowr/master/inst/extdata/example1_flow_def2.txt>``\ \_\_

.. raw:: html

   <!-- Each row of this table translates to a call to ([job](http://docs.flowr.space/build/html/rd/topics/job.html) or) [queue](http://docs.flowr.space/build/html/rd/topics/queue.html) function. -->

.. raw:: html

   <!-- 
   - jobname: is passed as `name` argument to job().
   - prev_jobs: passed as `previous_job` argument  to job().
   - dep_type: passed as `dependency_type` argument  to job(). Possible values: gather, serial
   - sub_type: passed as `submission_type` argument  to job().
   - queue: name of the queue to be used for this particular job. 
       Since each jobs can be submitted to a different queue, this makes your flow very flexible
   - memory_reserved: Refer to your system admin guide on what values should go here. 
       Some examples: 160000, 16g etc representing a 16GB reservation of RAM
   - walltime: How long would this job run. Again refer to your HPCC guide. Example: 24:00, 24:00:00
   - cpu_reserved: Amount of CPU reserved.

   Its best to have this as a tab seperated file (with no row.names). -->

.. code:: r

    kable(head(flow_def))

+-----------+--------------+-------------+-------------+----------+--------------------+------------+-----------------+
| jobname   | prev\_jobs   | dep\_type   | sub\_type   | queue    | memory\_reserved   | walltime   | cpu\_reserved   |
+===========+==============+=============+=============+==========+====================+============+=================+
| sleep     | none         | none        | scatter     | medium   | 163185             | 23:00      | 1               |
+-----------+--------------+-------------+-------------+----------+--------------------+------------+-----------------+
| tmp       | sleep        | serial      | scatter     | medium   | 163185             | 23:00      | 1               |
+-----------+--------------+-------------+-------------+----------+--------------------+------------+-----------------+
| merge     | tmp          | gather      | serial      | medium   | 163185             | 23:00      | 1               |
+-----------+--------------+-------------+-------------+----------+--------------------+------------+-----------------+
| size      | merge        | serial      | serial      | medium   | 163185             | 23:00      | 1               |
+-----------+--------------+-------------+-------------+----------+--------------------+------------+-----------------+

flow\_mat: A table with all the commands to run
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This is also a tab separated table, with a minimum of three columns
names as defined below:

-  samplename: A grouping column. The table is split using this column
   and each subset is treated as a individual flow. This makes it very
   easy to process multiple samples using a single submission command.

   -  If all the commands are for a single sample, one can just repeat a
      dummy name like sample1 all throughout.

-  jobname: This corresponds to the name of the step. This should match
   perfectly with the jobname column in flow\_def table defined above.
-  cmd: A shell command to run. One can get quite creative here. These
   could be multiple shell commands separated by a ``;`` or ``&&``, more
   on this
   ``here <http://stackoverflow.com/questions/3573742/difference-between-echo-hello-ls-vs-echo-hello-ls>``\ \_\_.

Here is a example
``flow_mat <https://raw.githubusercontent.com/sahilseth/flowr/master/inst/extdata/example1_flow_mat.txt>``\ \_\_

.. code:: r

    kable(subset(flow_mat, samplename == "sample1"))

+--------------+-----------+-----------------------------------------+
| samplename   | jobname   | cmd                                     |
+==============+===========+=========================================+
| sample1      | sleep     | sleep 2 && sleep 5;echo hello           |
+--------------+-----------+-----------------------------------------+
| sample1      | sleep     | sleep 13 && sleep 7;echo hello          |
+--------------+-----------+-----------------------------------------+
| sample1      | sleep     | sleep 23 && sleep 7;echo hello          |
+--------------+-----------+-----------------------------------------+
| sample1      | tmp       | head -c 100000 /dev/urandom > tmp1\_1   |
+--------------+-----------+-----------------------------------------+
| sample1      | tmp       | head -c 100000 /dev/urandom > tmp1\_2   |
+--------------+-----------+-----------------------------------------+
| sample1      | tmp       | head -c 100000 /dev/urandom > tmp1\_3   |
+--------------+-----------+-----------------------------------------+
| sample1      | merge     | cat tmp1\_1 tmp1\_2 tmp1\_3 > merge1    |
+--------------+-----------+-----------------------------------------+
| sample1      | size      | du -sh merge1; echo MY shell: $SHELL    |
+--------------+-----------+-----------------------------------------+

.. raw:: html

   <!---
   ### Style 2

   This style may be more suited for people who like to explore more advanced usage and like to code in R. Also this one find this much faster if jobs and their relationships changes a lot.

   Here instead of seperating cmds and definitions one defines them step by step incrementally.

   - Use: queue(), to define the computing cluster being used
   - Use: multiple calls job()
   - Use: flow() to stich the jobs into a flow.


   Currently we support LSF, Torque and SGE. Let us use LSF for this example.


   ```r
   qobj <- queue(platform = "lsf", queue = "normal", verbose = FALSE)
   ```

   Let us stitch a simple flow with three jobs, which are submitted one after the other.


   ```r
   job1 <- job(name = "myjob1", cmds = "sleep1", q_obj = qobj)
   job2 <- job(name = "myjob2", cmds = "sleep2", q_obj = qobj, previous_job = "myjob1", dependency_type = "serial")
   job3 <- job(name = "myjob3", cmds = "sleep3", q_obj = qobj, previous_job = "myjob1", dependency_type = "serial")
   fobj <- flow(name = "myflow", jobs = list(job1, job2, job3), desc="description")
   plot_flow(fobj)
   ```

   ```
   #> input x is flow
   ```

   ![](figure/plot_simpleflow-1.pdf) 

   The above translates to a flow definition which looks like this:


   ```r
   dat <- flowr:::create_jobs_mat(fobj)
   knitr:::kable(dat)
   ```



   |       |jobname |prev_jobs |dep_type |sub_type |cpu_reserved |nodes | jobid| prev_jobid|
   |:------|:-------|:---------|:--------|:--------|:------------|:-----|-----:|----------:|
   |myjob1 |myjob1  |          |none     |scatter  |1            |1     |     1|         NA|
   |myjob2 |myjob2  |myjob1    |serial   |scatter  |1            |1     |     2|          1|
   |myjob3 |myjob3  |myjob1    |serial   |scatter  |1            |1     |     3|          1|
   --->

Example:
--------

A ----> B -----> C -----> D

Consider a example with three steps A, B and C. A has 10 commands from
A1 to A10, similarly B has 10 commands B1 to B10 and C has a single
command, C1.

Consider another step D (with D1-D3), which comes after C.

Submission types
----------------

    This refers to the sub\_type column in flow definition.

-  ``scatter``: submit all commands as parallel independent jobs.
   *Submit all A1 through A10 as independent jobs*
-  ``serial``: run these commands sequentially one after the other.
   *Wrap A1 through A10, into a single job.*

Dependency types
----------------

    This refers to the dep\_type column in flow definition.

-  ``none``: independent job. *Initial step A has no dependency*
-  ``serial``: *one to one* relationship with previous job. *B1 can
   start as soon as A1 completes.*
-  ``gather``: *many to one*, wait for **all** commands in previous job
   to finish then start the current step. *All jobs of B (1-10), need to
   complete before C is started*
-  ``burst``: *one to many* wait for the previous step which has one job
   and start processing all in the current step. *D1 to D3 are started
   as soon as C finishes.*

Relationships
-------------

Using the above submission and dependency types one can create several
types of relationships between former and later jobs. Here are a few
examples of relationships one may typically use.

Serial: one to one relationship
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

A is submitted as scatter A1 through A10. Similarly B1 and B10 can be
processed independently of each other. Further B1, require A1 to
complete; B2 requires A2 and so on.

To set this up, A and B would have ``sub_type`` ``scatter`` and B would
have ``dep_type`` as ``serial``. Since A is a initial step its
``dep_type`` and ``prev_job`` would defined as be ``none``.

Gather: many to one relationship
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Since C is a single command which requires all steps of B to complete,
intuitively it would ``gather`` pieces of data generated by B. In this
case ``dep_type`` would be gather and ``sub_type`` type would be
``serial`` since its a single command.

.. raw:: html

   <!---
   - makes sense when previous job had many commands running in parallel and current job would wait for all
   - so previous job submission: `scatter`, and current job's dependency type `gather`

   --->

Burst: one to many relationship
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Further, D is a set of three commands (D1-D3), which need for a single
process. They would be submitted as ``scatter`` after waiting on C in a
``burst`` type relationship.

.. raw:: html

   <!---
   - makes sense when previous job had one command current job would split and submit several jobs in parallel
   - so previous job submission_type: `serial`, and current job's dependency type `burst`, with a submission type: `scatter`

   --->

In essence and example flow\_def would look like as follows (with
additional resource requirements, not shown for brevity).

.. code:: r

    ex2def = read_sheet(file.path(exdata, "example2_flow_def.txt"))
    ex2mat = read_sheet(file.path(exdata, "example2_flow_mat.txt"))
    fobj = to_flow(x = ex2mat, def = ex2def)
    kable(ex2def[, 1:4])

+-----------+-------------+--------------+-------------+
| jobname   | sub\_type   | prev\_jobs   | dep\_type   |
+===========+=============+==============+=============+
| A         | scatter     | none         | none        |
+-----------+-------------+--------------+-------------+
| B         | scatter     | A            | serial      |
+-----------+-------------+--------------+-------------+
| C         | serial      | B            | gather      |
+-----------+-------------+--------------+-------------+
| D         | scatter     | C            | burst       |
+-----------+-------------+--------------+-------------+

.. code:: r

    plot_flow(fobj)

.. figure:: figure/ex2def-1.pdf
   :alt: 

    There is a darker more prominent shadow to indicate scatter steps.

Here is the
``full flow definition <https://raw.githubusercontent.com/sahilseth/flowr/master/inst/extdata/example1_flow_mat.txt>``\ \_\_
used in this example.

Cluster interface
-----------------

Here is an example submission template:
https://github.com/sahilseth/flowr/blob/master/inst/conf/torque.sh

Other submission templates are also in the same folder.

Add a new platform is streamlined here are a few details:
https://github.com/sahilseth/flowr/issues/7

flow\_def columns
-----------------

Some columns of flow definition are passed along to the final
submisstion script.

Here is an example for submission template.
https://github.com/sahilseth/flowr/blob/master/inst/conf/moab.sh

Variables are defined in curly braces, example ``{{{CPU}}}``, these
variables are gathered from the flow definition file.

.. code:: r

    mat = read_sheet(file.path(exdata, "flow_def_columns.txt"))

::

    #> Reading file, using 'flow_def_column' as id_column to remove empty rows.

.. code:: r

    kable(mat)

+---------------------+-------------------------+
| flow\_def\_column   | hpc\_script\_variable   |
+=====================+=========================+
| nodes               | NODES                   |
+---------------------+-------------------------+
| cpu\_reserved       | CPU                     |
+---------------------+-------------------------+
| memory\_reserved    | MEMORY                  |
+---------------------+-------------------------+
| email               | EMAIL                   |
+---------------------+-------------------------+
| walltime            | WALLTIME                |
+---------------------+-------------------------+
| extra\_opts         | EXTRA\_OPTS             |
+---------------------+-------------------------+
| \*                  | JOBNAME                 |
+---------------------+-------------------------+
| \*                  | STDOUT                  |
+---------------------+-------------------------+
| \*                  | CWD                     |
+---------------------+-------------------------+
| \*                  | DEPENDENCY              |
+---------------------+-------------------------+
| \*                  | TRIGGER                 |
+---------------------+-------------------------+
| \*\*                | CMD                     |
+---------------------+-------------------------+

| =============== =================== flow\_def\_column
  hpc\_script\_variable =============== =================== nodes NODES
| cpu\_reserved CPU
| memory\_reserved MEMORY
| email EMAIL
| walltime WALLTIME
| extra\_opts EXTRA\_OPTS
| \* JOBNAME
| \* STDOUT
| \* CWD
| \* DEPENDENCY
| \* TRIGGER
| \*\* CMD
| =============== ===================

\*: These are generated on the fly \*\*: This is gathered from flow\_mat

My HPCC is not supported, how to make it work? send a message to:
sahil.seth [at] me.com
