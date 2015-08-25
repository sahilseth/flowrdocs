---
title: "Quick Start Example"
date: "2015-08-25"
output: rmarkdown::html_vignette
vignette: >
  %\VignetteIndexEntry{Quick Start Example}
  %\VignetteEngine{knitr::rmarkdown}
  \usepackage[utf8]{inputenc}
---



Get started
-------------



```r
install.packages('devtools')
devtools::install_github("sahilseth/flowr")
```

Run a setup function which copies 'flowr' helper script to enable using flow from shell terminal itself.
A few examples [here](https://github.com/sahilseth/rfun).


```r
library(flowr)
```


```r
setup()
```


# Toy example




![](imgs/toy.png)




A simple example where we have three instances of sleep (wait for few seconds), after completion three tmp jobs are started which create files with some random data. After all these are complete, a merge step follows, which combines them into one big file. Next we use `du` to calculate the size of the resulting file. 

.. note:: 
This is quite similar in structure to a typical workflow from where a series of alignment and sorting steps may take place on the raw fastq files. Followed by merging of the resulting bam files into one large file per-sample and further downstream processing.

The table below is referred to as [flow_mat](http://docs.flowr.space/en/latest/rd/vignettes/build-pipes.html#flow-mat-a-table-with-shell-commands-to-run).


|samplename |jobname    |cmd                                                            |
|:----------|:----------|:--------------------------------------------------------------|
|sample1    |sleep      |sleep 10 && sleep 2;echo hello                                 |
|sample1    |sleep      |sleep 11 && sleep 8;echo hello                                 |
|sample1    |sleep      |sleep 11 && sleep 17;echo hello                                |
|sample1    |create_tmp |head -c 100000 /dev/urandom > sample1_tmp_1                    |
|sample1    |create_tmp |head -c 100000 /dev/urandom > sample1_tmp_2                    |
|sample1    |create_tmp |head -c 100000 /dev/urandom > sample1_tmp_3                    |
|sample1    |merge      |cat sample1_tmp_1 sample1_tmp_2 sample1_tmp_3 > sample1_merged |
|sample1    |size       |du -sh sample1_merged; echo MY shell: $SHELL                   |

We use an additional file specifying relationship between the steps, and also other resource requirements [flow_def](http://docs.flowr.space/en/latest/rd/vignettes/build-pipes.html#flow-definition).


|jobname    |sub_type |prev_jobs  |dep_type |queue | memory_reserved|walltime | cpu_reserved|platform | jobid|
|:----------|:--------|:----------|:--------|:-----|---------------:|:--------|------------:|:--------|-----:|
|sleep      |scatter  |none       |none     |short |            2000|1:00     |            1|torque   |     1|
|create_tmp |scatter  |sleep      |serial   |short |            2000|1:00     |            1|torque   |     2|
|merge      |serial   |create_tmp |gather   |short |            2000|1:00     |            1|torque   |     3|
|size       |serial   |merge      |serial   |short |            2000|1:00     |            1|torque   |     4|


# Stitch


```r
fobj <- to_flow(x = flow_mat, def = as.flowdef(flow_def), 
	flowname = "example1", platform = "lsf")
```

# Plot

```r
plot_flow(fobj)
```

![Flow chart describing process for example 1](figure/plot_example1-1.png) 


# Test it
> Dry run (submit)


```r
submit_flow(fobj)
```

```
Test Successful!
You may check this folder for consistency. Also you may re-run submit with execute=TRUE
 ~/flowr/type1-20150520-15-18-27-5mSd32G0
```

# Submit it !

> Submit to the cluster


```r
submit_flow(fobj, execute = TRUE)
```

```
Flow has been submitted. Track it from terminal using:
flowr::status(x="~/flowr/type1-20150520-15-18-46-sySOzZnE")
OR
flowr status x=~/flowr/type1-20150520-15-18-46-sySOzZnE
```


# Check the status

```
flowr status x=~/flowr/type1-20150520-15-18-46-sySOzZnE
```

```
Loading required package: shape
Flowr: streamlining workflows
Showing status of: /rsrch2/iacs/iacs_dep/sseth/flowr/type1-20150520-15-18-46-sySOzZnE


|          | total| started| completed| exit_status|
|:---------|-----:|-------:|---------:|-----------:|
|001.sleep |    10|      10|        10|           0|
|002.tmp   |    10|      10|        10|           0|
|003.merge |     1|       1|         1|           0|
|004.size  |     1|       1|         1|           0|
```

.. note::
Interested? Here are some details on [building pipelines](http://docs.flowr.space/en/latest/rd/vignettes/build-pipes.html)
