

# HPCC Platforms
	
As of now we have tested the on the following clusters:


|platform |command |status     |short_name |
|:--------|:-------|:----------|:----------|
|LSF 7    |bsub    |testers?   |lsf        |
|LSF 9.1  |bsub    |yes        |lsf        |
|Torque   |qsub    |yes        |torque     |
|SGE      |qsub    |beta       |sge        |
|moab	  |qsub	    |yes        |moab        | 

The short_name column, is the identifier used rest of the framework, for example in:
to_flow() and flow_definition

\*queue short-name used in [flow](https://github.com/sahilseth/flow)

There are several [job scheduling](http://en.wikipedia.org/wiki/Job_scheduler) systems
available and we try to support the major players. Adding support is
quite easy if we have access to them. Your favourite not in the list?
Send a [message](mailto:sahil.seth@me.com)

- PBS: [wiki](http://en.wikipedia.org/wiki/Portable_Batch_System)
- Torque: [wiki](http://en.wikipedia.org/wiki/TORQUE_Resource_Manager)
	- MD Anderson
	- [University of Houston](http://www.rcc.uh.edu/hpc-docs/49-using-torque-to-submit-and-monitor-jobs.html)
- LSF [wiki](http://en.wikipedia.org/wiki/Platform_LSF):
	- Harvard Medicla School uses: [LSF HPC 7](https://wiki.med.harvard.edu/Orchestra/IntroductionToLSF)
	- Also Used at [Broad](https://www.broadinstitute.org/gatk/guide/article?id=1311)
- SGE [wiki](http://en.wikipedia.org/wiki/Sun_Grid_Engine)
	- A tutorial for [Sun Grid Engine](https://sites.google.com/site/anshulkundaje/inotes/programming/clustersubmit/sun-grid-engine)
	- Another from [JHSPH](http://www.biostat.jhsph.edu/bit/cluster-usage.html)
	- Dependecy info [here](https://wiki.duke.edu/display/SCSC/SGE+Job+Dependencies)

http://en.wikipedia.org/wiki/Comparison_of_cluster_software


<pandoc -t rst -f md hpc-support.md>