---
title: "flowr simple examples"
date: "2015-07-14"
output: rmarkdown::html_document
vignette: >
  %\VignetteIndexEntry{flowr simple example}
  %\VignetteEngine{knitr::rmarkdown}
  \usepackage[utf8]{inputenc}
---

## Let us load some example data

```r
exdata = file.path(system.file(package = "flowr"), "extdata")
flow_mat = read_sheet(file.path(exdata, "example1_flow_mat.txt"))
```

```
## Reading file, using 'samplename' as id_column to remove empty rows.
```

```r
flow_def = read_sheet(file.path(exdata, "example1_flow_def.txt"))
```

```
## Reading file, using 'jobname' as id_column to remove empty rows.
```


```r
fobj <- to_flow(x = flow_mat, def = flow_def)
```

```
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
## ....
## 
## Working on... sample2
## input x is list
## ....
## 
## Working on... sample3
## input x is list
## ....
```

## --- do the following for tests

```r
fobj <- to_flow(x = flow_mat, def = flow_def, platform = "local", 
								execute = TRUE)
fobj <- to_flow(x = flow_mat, def = flow_def, platform = "moab")
fobj <- to_flow(x = flow_mat, def = flow_def, platform = "lsf")
```

