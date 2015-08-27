.. flowr documentation master file, created by
   sphinx-quickstart on Mon Mar 23 11:44:18 2015.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

|Build Status| |cran| |downloads| |docs|

.. |DOI|

flowr: Streamline your workflows
=================================

This framework allows you to design and implement complex pipelines, and deploy them on your institution's computing cluster. This has been built keeping in mind the needs of bioinformatics workflows. However, it is easily extendable to any field where a series of steps (shell commands) are to be executed in a (work)flow.

Highlights
-------------
- Effectively process a pipeline multi-step pipeline, spawning it across the computing cluster
- Example: 
	- A typical case with next-generation sequencing, a sample with tens of `fastq <http://en.wikipedia.org/wiki/FASTQ_format>`_ files)
	- Each file can be processed (`aligned <http://en.wikipedia.org/wiki/Sequence_alignment>`_) individually, each using multiple cores
	- Say 50 files using 10 cores each, totalling 500 cores across several machines, for one sample
	- flowr further supports processing multiple samples in parrellel, spawning thousands of cores.
- Reproducible, with cleanly structured execution logs
- Track and re-run flows
- Lean and Portable, with easy installation
- Supports multiple platforms (torque, lsf, sge, slurm ...)
	
.. Say the next step needs to wait for all these 10 jobs to complete, or say 

.. There are a few built-in pipelines which start from fastq files and do alignment and variant calling.


A few lines, to get started:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: r

	## From the official R repository (may be a few versions behind)
	install.packages("flowr")

	## OR

	install.packages(devtools)
	devtools::install_github("sahilseth/flowr")
	
	library(flowr) ## load the library
	setup() ## copy flowr bash script; and create a folder flowr under home.
	run('sleep', execute=TRUE,  platform='moab') ## submit a simple example

- Here is a shiny app, `flow_creator <https://sseth.shinyapps.io/flow_creator>`_ which helps you build a flow.
- A few `slides <http://sahilseth.github.io/slides/flowrintro>`_ providing a quick overview.




.. The process of submitting jobs uses the `dependency` feature of submitting jobs to a computing cluster.
.. This lets the user concentrate more on the type of analysis than its implmentation. Also the pipeline becomes really portable across platforms and computing clusters.




Contents:
-----------------------

.. toctree::
	:caption: Table of Contents
	:name: mastertoc
	:maxdepth: -1
	
	rd/vignettes/build-pipes
	faqs
	rd/topics/complete-help
	


Indices and tables
---------------

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

Aknowledgements
---------------

- Jianhua Zhang
- Samir Amin
- Kadir Akdemir
- Ethan Mao
- Henry Song
- An excellent resource for writing your own R packages: r-pkgs.had.co.nz



.. links in this page

.. _flow_creator: https://sseth.shinyapps.io/flow_creator

.. |Build Status| image:: https://travis-ci.org/sahilseth/flowr.svg?branch=master
   :target: https://travis-ci.org/sahilseth/flowr

.. |DOI| image:: https://zenodo.org/badge/11075/sahilseth/flowr.svg
   :target: http://dx.doi.org/10.5281/zenodo.16170

.. |cran| image:: http://www.r-pkg.org/badges/version/flowr
	:target: http://cran.rstudio.com/web/packages/flowr/index.html
	
.. |downloads| image:: http://cranlogs.r-pkg.org/badges/grand-total/flowr

.. |docs| image:: https://readthedocs.org/projects/flowr/badge/?version=latest
	:target: https://readthedocs.org/projects/flowr/?badge=latest
	:alt: Documentation Status

