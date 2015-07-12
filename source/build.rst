Building Pipelines
==========================

.. toctree::
	:maxdepth: 2

	rd/vignettes/build-pipes
	
	
## HPCC submission formats
Here is an example submission template: https://github.com/sahilseth/flowr/blob/master/inst/conf/torque.sh

Other submission templates are also in the same folder.

Add a new platform is streamlined here are a few details: https://github.com/sahilseth/flowr/issues/7


## Relationship between flow_def and final submission

Some columns of flow definition are passed along to the final submisstion script.

Here is an example for submission template. https://github.com/sahilseth/flowr/blob/master/inst/conf/moab.sh

Variables are defined in curly braces, example `{{{CPU}}}`, these variables are gathered from the flow definition file.

===============  ===================
flow_def_column  hpc_script_variable
===============  ===================
nodes            NODES              
cpu_reserved     CPU                
memory_reserved  MEMORY             
email            EMAIL              
walltime         WALLTIME           
extra_opts       EXTRA_OPTS         
*                JOBNAME            
*                STDOUT             
*                CWD                
*                DEPENDENCY         
*                TRIGGER            
**               CMD                
===============  ===================




Here is a shiny app, `flow_creator`_ which builds your `shiny` flow.
	
.. _flow_creator: https://sseth.shinyapps.io/flow_creator
