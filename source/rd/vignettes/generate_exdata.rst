Example1
========

Generate 100 commands each for sleep make div

.. code:: r

    ## create a vector of sample names


    example1 <- function(samp, n, i){
        ## sleep for a few seconds (100 times)
        cmd_sleep = sprintf("sleep %s", abs(round(rnorm(n)*10, 0)))
        
        ## Create 100 temporary files
        tmp10 = sprintf("tmp%s_%s", i, 1:n)
        cmd_tmp = sprintf("head -c 100000 /dev/urandom > %s", tmp10)
        
        ## Merge them according to samples, 10 each
        cmd_merge <- sprintf("cat %s > merge%s", paste(tmp10, collapse = " "), i)

        ## get the size of merged files
        cmd_size = sprintf("du -sh merge%s", i)
        
        cmd <- c(cmd_sleep, cmd_tmp, cmd_merge, cmd_size)
        jobname <- c(rep("sleep", length(cmd_sleep)),
            rep("tmp", length(cmd_tmp)),
            rep("merge", length(cmd_merge)),
            rep("size", length(cmd_size)))
        ## create a DF
        df = data.frame(samplename = samp[i],
            jobname = jobname, 
            cmd = cmd, stringsAsFactors = FALSE)
    }

    i=1;n=3;samps = sprintf("sample%s", 1:n)
    ## we want to do this for 3 samples
    ## call the above function for each
    lst <- lapply(samps, function(samp){
        flow_mat = example1(samp, 3, 1)
    })

    ## combing each element of the list, by row bind
    flow_mat = do.call(rbind, lst)
    kable(head(flow_mat))

+--------------+-----------+-----------------------------------------+
| samplename   | jobname   | cmd                                     |
+==============+===========+=========================================+
| sample1      | sleep     | sleep 2                                 |
+--------------+-----------+-----------------------------------------+
| sample1      | sleep     | sleep 1                                 |
+--------------+-----------+-----------------------------------------+
| sample1      | sleep     | sleep 1                                 |
+--------------+-----------+-----------------------------------------+
| sample1      | tmp       | head -c 100000 /dev/urandom > tmp1\_1   |
+--------------+-----------+-----------------------------------------+
| sample1      | tmp       | head -c 100000 /dev/urandom > tmp1\_2   |
+--------------+-----------+-----------------------------------------+
| sample1      | tmp       | head -c 100000 /dev/urandom > tmp1\_3   |
+--------------+-----------+-----------------------------------------+

Make the flow definition
------------------------

Generate a skeleton flow definition
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: r

    def = sample_flow_def(jobnames = unique(flow_mat$jobname))

::

    #> Creating a skeleton flow_def

.. code:: r

    kable(def)

+-----------+--------------+-------------+-------------+----------+--------------------+------------+-----------------+
| jobname   | prev\_jobs   | dep\_type   | sub\_type   | queue    | memory\_reserved   | walltime   | cpu\_reserved   |
+===========+==============+=============+=============+==========+====================+============+=================+
| sleep     | none         | none        | scatter     | medium   | 163185             | 23:00      | 1               |
+-----------+--------------+-------------+-------------+----------+--------------------+------------+-----------------+
| tmp       | sleep        | serial      | scatter     | medium   | 163185             | 23:00      | 1               |
+-----------+--------------+-------------+-------------+----------+--------------------+------------+-----------------+
| merge     | tmp          | serial      | scatter     | medium   | 163185             | 23:00      | 1               |
+-----------+--------------+-------------+-------------+----------+--------------------+------------+-----------------+
| size      | merge        | serial      | scatter     | medium   | 163185             | 23:00      | 1               |
+-----------+--------------+-------------+-------------+----------+--------------------+------------+-----------------+

Change the dependency type for merge step into gather
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

It might be easier to do such, by hand

.. code:: r

    def[def[, 'jobname'] == "merge","dep_type"] = "gather"
    def[def[, 'jobname'] == "merge","sub_type"] = "serial"
    def[def[, 'jobname'] == "size","sub_type"] = "serial"
    kable(def)

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

Plot flow
~~~~~~~~~

.. code:: r

    fobj <- to_flow(x = flow_mat, def = def)

::

    #> input x is data.frame
    #> 
    #> 
    #> ##--- Getting default values for missing parameters...
    #> Using `samplename` as the grouping column
    #> Using `jobname` as the jobname column
    #> Using `cmd` as the cmd column
    #> Using flowname default: flowname
    #> Using flow_run_path default: ~/flowr/runs
    #> 
    #> 
    #> ##--- Checking flow definition and flow matrix for consistency...
    #> 
    #> 
    #> ##--- Detecting platform...
    #> 
    #> 
    #> ##--- flowr submission...
    #> 
    #> 
    #> Detected 3 samples/groups in flow_mat.
    #> flow_mat would be split and each would be submitted seperately...
    #> 
    #> 
    #> Working on... sample1
    #> input x is list
    #> ....
    #> 
    #> Working on... sample2
    #> input x is list
    #> ....
    #> 
    #> Working on... sample3
    #> input x is list
    #> ....

.. code:: r

    plot_flow(fobj[[1]])

::

    #> input x is flow

.. figure:: figure/make_flow_plot-1.pdf
   :alt: plot of chunk make\_flow\_plot

   plot of chunk make\_flow\_plot

Write both into example data
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: r

    write.table(flow_mat, file = "inst/extdata/example1_flow_mat.txt", 
        row.names = FALSE, quote = FALSE, sep = "\t")
    write.table(def, file = "inst/extdata/example1_flow_def.txt", 
        row.names = FALSE, quote = FALSE, sep = "\t")

Example2
--------

.. code:: r

    example2 <- function(n, i, samp){
        cmd_sleep = sprintf("sleep %s", abs(round(rnorm(1)*10, 0)))
        
        ## Create 100 temporary files
        tmpn = sprintf("tmp%s_%s", i, 1:n)
        cmd_tmp = sprintf("head -c 100000 /dev/urandom > %s", tmpn)
        
        ## Merge them according to samples, 10 each
        cmd_merge <- sprintf("cat %s > merge%s", paste(tmpn, collapse = " "), i)
        
        ## get the size of merged files
        cmd_size = sprintf("du -sh merge%s", i)
        
        cmd <- c(cmd_sleep, cmd_tmp, cmd_merge, cmd_size)
        jobname <- c(rep("sleep", length(cmd_sleep)),
                                 sprintf("tmp%s", 1:length(cmd_tmp)),
                                 rep("merge", length(cmd_merge)),
                                 rep("size", length(cmd_size)))
        ## create a DF
        df = data.frame(samplename = samp,
                                        jobname = jobname, 
                                        cmd = cmd, stringsAsFactors = FALSE)
        return(df)
    }

    i=1;n=3
    flow_mat = example2(3, 1, "samp1")

    ## Make sample skeleton
    def = sample_flow_def(jobnames = unique(flow_mat$jobname))

::

    #> Creating a skeleton flow_def

.. code:: r

    fobj=to_flow(flow_mat, def)

::

    #> input x is data.frame
    #> 
    #> 
    #> ##--- Getting default values for missing parameters...
    #> Using `samplename` as the grouping column
    #> Using `jobname` as the jobname column
    #> Using `cmd` as the cmd column
    #> Using flowname default: flowname
    #> Using flow_run_path default: ~/flowr/runs
    #> 
    #> 
    #> ##--- Checking flow definition and flow matrix for consistency...
    #> 
    #> 
    #> ##--- Detecting platform...
    #> 
    #> 
    #> ##--- flowr submission...
    #> 
    #> 
    #> Working on... samp1
    #> input x is list
    #> ......

.. code:: r

    plot_flow(to_flow(flow_mat, def))

::

    #> input x is data.frame
    #> 
    #> 
    #> ##--- Getting default values for missing parameters...
    #> Using `samplename` as the grouping column
    #> Using `jobname` as the jobname column
    #> Using `cmd` as the cmd column
    #> Using flowname default: flowname
    #> Using flow_run_path default: ~/flowr/runs
    #> 
    #> 
    #> ##--- Checking flow definition and flow matrix for consistency...
    #> 
    #> 
    #> ##--- Detecting platform...
    #> 
    #> 
    #> ##--- flowr submission...
    #> 
    #> 
    #> Working on... samp1
    #> input x is list
    #> ......input x is flow

.. figure:: figure/unnamed-chunk-5-1.pdf
   :alt: plot of chunk unnamed-chunk-5

   plot of chunk unnamed-chunk-5

.. code:: r

    ## change a few things
    def$sub_type = "serial"
    plot_flow(to_flow(flow_mat, def))

::

    #> input x is data.frame
    #> 
    #> 
    #> ##--- Getting default values for missing parameters...
    #> Using `samplename` as the grouping column
    #> Using `jobname` as the jobname column
    #> Using `cmd` as the cmd column
    #> Using flowname default: flowname
    #> Using flow_run_path default: ~/flowr/runs
    #> 
    #> 
    #> ##--- Checking flow definition and flow matrix for consistency...
    #> 
    #> 
    #> ##--- Detecting platform...
    #> 
    #> 
    #> ##--- flowr submission...
    #> 
    #> 
    #> Working on... samp1
    #> input x is list
    #> ......input x is flow

.. figure:: figure/unnamed-chunk-5-2.pdf
   :alt: plot of chunk unnamed-chunk-5

   plot of chunk unnamed-chunk-5

.. code:: r

    ## change a few more
    def[def[, 'jobname'] == "tmp2","prev_jobs"] = "sleep"
    def[def[, 'jobname'] == "tmp3","prev_jobs"] = "sleep"
    plot_flow(to_flow(flow_mat, def))

::

    #> input x is data.frame
    #> 
    #> 
    #> ##--- Getting default values for missing parameters...
    #> Using `samplename` as the grouping column
    #> Using `jobname` as the jobname column
    #> Using `cmd` as the cmd column
    #> Using flowname default: flowname
    #> Using flow_run_path default: ~/flowr/runs
    #> 
    #> 
    #> ##--- Checking flow definition and flow matrix for consistency...
    #> 
    #> 
    #> ##--- Detecting platform...
    #> 
    #> 
    #> ##--- flowr submission...
    #> 
    #> 
    #> Working on... samp1
    #> input x is list
    #> ......input x is flow

.. figure:: figure/unnamed-chunk-5-3.pdf
   :alt: plot of chunk unnamed-chunk-5

   plot of chunk unnamed-chunk-5

.. code:: r

    ## we would like all three to complete
    def[def[, 'jobname'] == "merge","prev_jobs"] = "tmp1,tmp2,tmp3"
    plot_flow(to_flow(flow_mat, def))

::

    #> input x is data.frame
    #> 
    #> 
    #> ##--- Getting default values for missing parameters...
    #> Using `samplename` as the grouping column
    #> Using `jobname` as the jobname column
    #> Using `cmd` as the cmd column
    #> Using flowname default: flowname
    #> Using flow_run_path default: ~/flowr/runs
    #> 
    #> 
    #> ##--- Checking flow definition and flow matrix for consistency...
    #> 
    #> 
    #> ##--- Detecting platform...
    #> 
    #> 
    #> ##--- flowr submission...
    #> 
    #> 
    #> Working on... samp1
    #> input x is list
    #> ......input x is flow

.. figure:: figure/unnamed-chunk-5-4.pdf
   :alt: plot of chunk unnamed-chunk-5

   plot of chunk unnamed-chunk-5

Write both into example data
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: r

    write.table(flow_mat, file = "inst/extdata/example2_flow_mat.txt", 
        row.names = FALSE, quote = FALSE, sep = "\t")
    write.table(def, file = "inst/extdata/example2_flow_def.txt", 
        row.names = FALSE, quote = FALSE, sep = "\t")
