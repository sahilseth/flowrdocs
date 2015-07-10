HPCC Platforms
==============

As of now we have tested this on the following clusters:

+------------+-----------+--------------+--------------+
| Platform   | command   | status       | queue.type   |
+============+===========+==============+==============+
| LSF 7      | bsub      | Not tested   | lsf          |
+------------+-----------+--------------+--------------+
| LSF 9.1    | bsub      | Yes          | lsf          |
+------------+-----------+--------------+--------------+
| Torque     | qsub      | Yes          | torque       |
+------------+-----------+--------------+--------------+
| SGE        | qsub      | Beta         | sge          |
+------------+-----------+--------------+--------------+

\*queue short-name used in `flow <https://github.com/sahilseth/flow>`__

There are several `job
scheduling <http://en.wikipedia.org/wiki/Job_scheduler>`__ systems
available and we try to support the major players. Adding support is
quite easy if we have access to them. Your favourite not in the list?
Send a `message <mailto:sahil.seth@me.com>`__

-  PBS: `wiki <http://en.wikipedia.org/wiki/Portable_Batch_System>`__
-  Torque:
   `wiki <http://en.wikipedia.org/wiki/TORQUE_Resource_Manager>`__

   -  MD Anderson
   -  `University of
      Houston <http://www.rcc.uh.edu/hpc-docs/49-using-torque-to-submit-and-monitor-jobs.html>`__

-  LSF `wiki <http://en.wikipedia.org/wiki/Platform_LSF>`__:

   -  Harvard Medicla School uses: `LSF HPC
      7 <https://wiki.med.harvard.edu/Orchestra/IntroductionToLSF>`__
   -  Also Used at
      `Broad <https://www.broadinstitute.org/gatk/guide/article?id=1311>`__

-  SGE `wiki <http://en.wikipedia.org/wiki/Sun_Grid_Engine>`__

   -  A tutorial for `Sun Grid
      Engine <https://sites.google.com/site/anshulkundaje/inotes/programming/clustersubmit/sun-grid-engine>`__
   -  Another from
      `JHSPH <http://www.biostat.jhsph.edu/bit/cluster-usage.html>`__
   -  Dependecy info
      `here <https://wiki.duke.edu/display/SCSC/SGE+Job+Dependencies>`__

- SLURM 
   - A detailed `man page <http://slurm.schedmd.com>`__
   - Another good description on `submission <https://computing.llnl.gov/linux/slurm/sbatch.html>`__
   
`comparing platforms <http://en.wikipedia.org/wiki/Comparison_of_cluster_software>`__

Adding a new platform, is quite streamlined and should take minimal changes in the code, here is a discussion on the same:
https://github.com/sahilseth/flowr/issues/7




