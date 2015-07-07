Let us load some example data
-----------------------------

.. code:: r

    exdata = file.path(system.file(package = "flowr"), "extdata")
    flow_mat = read_sheet(file.path(exdata, "example1_flow_mat.txt"))

::

    ## Using 'samplename'' as id_column

.. code:: r

    flow_def = read_sheet(file.path(exdata, "example1_flow_def.txt"))

::

    ## Using 'jobname'' as id_column

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
    ## Using flow_base_path default: ~/flowr
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
    ## Test Successful!
    ## You may check this folder for consistency. Also you may re-run submit with execute=TRUE
    ##  ~/flowr/flowname-sample1-20150706-21-32-27-2gI9jqbn
    ## input x is flow
    ## 
    ## 
    ## Working on... sample2
    ## input x is list
    ## ....input x is flow
    ## Test Successful!
    ## You may check this folder for consistency. Also you may re-run submit with execute=TRUE
    ##  ~/flowr/flowname-sample2-20150706-21-32-27-FceZzY4f
    ## input x is flow
    ## 
    ## 
    ## Working on... sample3
    ## input x is list
    ## ....input x is flow
    ## Test Successful!
    ## You may check this folder for consistency. Also you may re-run submit with execute=TRUE
    ##  ~/flowr/flowname-sample3-20150706-21-32-27-zENXBYhx
    ## input x is flow

--- do the following for tests
------------------------------

.. code:: r

    fobj <- to_flow(x = flow_mat, def = flow_def, platform = "local", 
                                    execute = TRUE)
    fobj <- to_flow(x = flow_mat, def = flow_def, platform = "moab")
    fobj <- to_flow(x = flow_mat, def = flow_def, platform = "lsf")

