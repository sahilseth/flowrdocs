Let us load some example data
-----------------------------

.. code:: r

    exdata = file.path(system.file(package = "flowr"), "extdata")
    flow_mat = read_sheet(file.path(exdata, "example1_flow_mat.txt"))

::

    ## Reading file, using 'samplename' as id_column to remove empty rows.

.. code:: r

    flow_def = read_sheet(file.path(exdata, "example1_flow_def.txt"))

::

    ## Reading file, using 'jobname' as id_column to remove empty rows.

.. code:: r

    fobj <- to_flow(x = flow_mat, def = flow_def)

::

    ## input x is data.frame
    ## 
    ## 
    ## ##--- Getting default values for missing parameters...
    ## Using `samplename` as the grouping column
    ## Using `jobname` as the jobname column
    ## Using `cmd` as the cmd column
    ## Using flowname default: flowname
    ## Using flow_run_path default: ~/flowr/runs
    ## 
    ## 
    ## ##--- Checking flow definition and flow matrix for consistency...
    ## 
    ## 
    ## ##--- Detecting platform...
    ## 
    ## 
    ## ##--- flowr submission...
    ## 
    ## 
    ## Detected 3 samples/groups in flow_mat.
    ## flow_mat would be split and each would be submitted seperately...
    ## 
    ## 
    ## Working on... sample1
    ## input x is list
    ## ....input x is flow
    ## 
    ## Working on: sleep
    ## 
    ## Working on: tmp
    ## 
    ## Working on: merge
    ## 
    ## Working on: size
    ## Test Successful!
    ## You may check this folder for consistency. Also you may re-run submit with execute=TRUE
    ##  ~/flowr/runs/flowname-sample1-20150710-10-58-20-mdwWvnO1
    ## input x is flow
    ## 
    ## 
    ## Working on... sample2
    ## input x is list
    ## ....input x is flow
    ## 
    ## Working on: sleep
    ## 
    ## Working on: tmp
    ## 
    ## Working on: merge
    ## 
    ## Working on: size
    ## Test Successful!
    ## You may check this folder for consistency. Also you may re-run submit with execute=TRUE
    ##  ~/flowr/runs/flowname-sample2-20150710-10-58-20-pmjl2n7K
    ## input x is flow
    ## 
    ## 
    ## Working on... sample3
    ## input x is list
    ## ....input x is flow
    ## 
    ## Working on: sleep
    ## 
    ## Working on: tmp
    ## 
    ## Working on: merge
    ## 
    ## Working on: size
    ## Test Successful!
    ## You may check this folder for consistency. Also you may re-run submit with execute=TRUE
    ##  ~/flowr/runs/flowname-sample3-20150710-10-58-20-3LvNU27P
    ## input x is flow

--- do the following for tests
------------------------------

.. code:: r

    fobj <- to_flow(x = flow_mat, def = flow_def, platform = "local", 
                                    execute = TRUE)
    fobj <- to_flow(x = flow_mat, def = flow_def, platform = "moab")
    fobj <- to_flow(x = flow_mat, def = flow_def, platform = "lsf")
