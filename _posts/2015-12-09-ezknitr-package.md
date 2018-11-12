---
layout: post
title: "ezknitr: R package to avoid the typical working directory pain when using knitr"
date: 2015-12-09 14:30:00 -0700
tags: [professional, rstats, r-bloggers, packages, knitr]
gh-repo: daattali/ezknitr
gh-badge: [star, watch, follow]
---

A new package has hit the CRAN shelves this week.  While `knitr` is one of the most useful R packages in existence, `ezknitr` is a simple extension to it that adds flexibility in several ways.

One common source of frustration with `knitr` is that it assumes the directory where the source file lives should be the working directory, which is often not true. `ezknitr` addresses this problem by giving you complete control over where all the inputs and outputs are, and adds several other convenient features. The two main functions are `ezknit()` and `ezspin()`, which are wrappers around `knitr`'s `knit()` and `spin()`, used to make rendering markdown/HTML documents easier. 

> You can see Jenny Bryan's way of dealing with this problem [in this gist](https://gist.github.com/jennybc/362f52446fe1ebc4c49f) or simply browse the [knitr GitHub issues](https://github.com/yihui/knitr/issues) to see people discussing the issue surrounding directories.

## Table of contents

- [Availability](#availability)
- [Overview](#overview)
- [Motivation & simple use case](#motivation-simple-usecase)
- [Use case: using one script to analyze multiple datasets](#usecase-advanced)
- [Experiment with ezknitr](#experiment)
- [spin() vs knit()](#spin-vs-knit)
- [Using rmarkdown::render()](#using-render)

## Availability {#availability}

`ezknitr` is available through both [CRAN](https://cran.r-project.org/package=ezknitr) (`install.packages("ezknitr")`) and [GitHub](https://github.com/daattali/ezknitr) (`devtools::install_github("daattali/ezknitr")`).

## Overview {#overview}

If you have a very simple project with a flat directory structure, then `knitr` works great. But even something as simple as trying to knit a document that reads a file from a different directory or placing the output rendered files in a different folder cannot be easily done with `knitr`.

`ezknitr` improves basic `knitr` functionality in a few ways. You get to decide:

- What the working directory of the source file is
    - Default is your current working directory, which often makes more sense than the `knitr` assumption that the working directory is wherever the input file is
- Where the output files will go
    - With `knitr`, all the rendered output files will be generated in the folder you're currently in
- Where the figures generated in the markdown document will be stored
    - `knitr` makes it cumbersome to change this directory
- Any parameters to pass to the source file
    - Useful if you want to run an identical source file multiple times with different parameters

## Motivation & simple use case {#motivation-simple-usecase}

Assume you have an Rmarkdown file that reads a data file and produces a short report while also generating a figure. Native `knit()` (or `spin()` if you're starting with an R script instead of an Rmd file) works great if you have a flat directory structure like this:

~~~
- project/
  |- input.csv
  |- report.Rmd
~~~


### Problem

But what happens if you have a slightly more complex structure? In a real project, you rarely have everything just lying around in the same folder.  Here is an example of a more realistic initial directory structure (assume the working directory is set to `project/`):

~~~
- project/
  |- analysis/
     |- report.Rmd
  |- data/
     |- input.csv
~~~

Now if you want `knitr` to work, you'd have to ensure the path to `input.csv` is relative to the `analysis/` directory. This is counter-intuitive because most people expect to create paths relative to the *working directory* (`project/` in this case), but `knitr` will use the `analysis/` folder as the working directory. Any code reading the input file needs to use `../data/input.csv` instead of `data/input.csv`.

Other than being confusing, it also means that you cannot naively run the Rmd code chunks manually because when you run the code in the console, your working directory is not set to what `knitr` will use as the working directory. More specifically, if you try to run the command that reads the input file, your console will look in `project/../data/input.csv` (which doesn't exist).

A similar problem arises when you want to create files in your report: `knitr` will create the files relative to where the Rmd file is, rather than relative to the project root.

Another problem with the flat directory structure is that you may want to control where the resulting reports get generated. `knitr` will create all the outputs in your working directory, and as far as I know there is no way to control that. 

### Solution

`ezknitr` addresses these issues, and more. It provides wrappers to `knit()` and `spin()` that allow you to set the working directory for the input file, and also uses a more sensible default working directory: the current working directory. `ezknitr` also lets you decide where the output files and output figures will be generated, and uses a better default path for the output files: the directory containing the input file.

Assuming your working directory is currently set to the `project/` directory, you could use the following `ezknitr` command to do what you want:

```r
library(ezknitr)
ezknit(file = "analysis/report.Rmd", out_dir = "reports", fig_dir = "myfigs")
```


~~~
- project/
  |- analysis/
     |- report.Rmd
  |- data/
     |- input.csv
  |- reports/
     |- myfigs/
        |- fig1.png
     |- report.md
     |- report.HTML
~~~

We didn't explicitly have to set the working direcory, but you can use the `wd` argument if you do require a different directory. After running `ezknit()`, you can run `open_output_dir()` to open the output directory in your file browser if you want to easily see the resulting report. Getting a similar directory structure with `knitr` is not simple, but with `ezknitr` it's trivial.

Note that `ezknitr` produces both a markdown and an HTML file for each report (you can choose to discard them with the `keep_md` and `keep_html` arguments).

## Use case: using one script to analyze multiple datasets {#usecase-advanced}

As an example of a more complex realistic scenario where `ezknitr` would be useful, imagine having multiple analysis scripts, with each one needing to be run on multiple datasets. Being the organizer scientist that you are, you want to be able to run each analysis on each dataset, and keep the results neatly organized. I personally was involved in a few projects requiring exactly this, and `ezknitr` was in fact born for solving this exact issue. Assume you have the following files in your project:

~~~
- project/
  |- analysis/
     |- calculate.Rmd
     |- explore.Rmd
  |- data/
     |- human.dat
     |- mouse.dat
~~~

We can easily use `ezknitr` to run any of the analysis Rmarkdowns on any of the datasets and assign the results to a unique output. Let's assume that each analysis script expects there to be a variable named `DATASET_NAME` that tells the script what data to operate on. The following `ezknitr` code illustrates how to achieve the desired output.

```r
library(ezknitr)
ezknit(file = "analysis/explore.Rmd", out_dir = "reports/human",
        params = list("DATASET_NAME" = "human.dat"), keep_html = FALSE)
ezknit(file = "analysis/explore.Rmd", out_dir = "reports/mouse",
        params = list("DATASET_NAME" = "mouse.dat"), keep_html = FALSE)
ezknit(file = "analysis/calculate.Rmd", out_dir = "reports/mouse",
        params = list("DATASET_NAME" = "mouse.dat"), keep_html = FALSE)
```

~~~
- project/
  |- analysis/
     |- calculate.Rmd
     |- explore.Rmd
  |- data/
     |- human.dat
     |- mouse.dat
  |- reports/
     |- human/
        |- explore.md
     |- mouse/
        |- calculate.md
        |- explore.md
~~~

Note that this example uses the `params = list()` argument, which lets you pass variables to the input Rmarkdown.  In this case, I use it to tell the Rmarkdown what dataset to use, and the Rmarkdown assumes a `DATASET_NAME` variable exists. This of course means that the analysis script has an external dependency by having a variable that is not defined inside of it. You can use the `set_default_params()` function inside the Rmarkdown to ensure the variable uses a default value if none was provided.

Also note that differentiating the species in the output could also have been done using the `out_suffix` argument instead of the `out_dir` argument. For example, using `out_suffix = "human"` would have resulted in an ouput file named `explore-human.md`.

<h2 id="experiment">Experiment with ezknitr</h2>

After installing the package, you can experiment with `ezknitr` using the `setup_ezknit_test()` or `setup_ezspin_test()` functions to see their benefits. See `?setup_ezknit_test` for more information.

## spin() vs knit() {#spin-vs-knit}

`knit()` is the most popular and well-known function from `knitr`. It lets you create a markdown document from an Rmarkdown file. I assume if you are on this page, you are familiar with `knit()` and Rmarkdown, so I won't explain it any further.

`spin()` is similar, but starts one step further back: it takes an R script as input, creates an Rmarkdown document from the R script, and then proceeds to create a markdown document from it. `spin()` can be useful in situations where you develop a large R script and want to be able to produce reports from it directly instead of having to copy chunks into a separate Rmarkdown file. You can read more about why I like `spin()` in the blog post ["knitr's best hidden gem: spin"](https://deanattali.com/2015/03/24/knitrs-best-hidden-gem-spin).

## Using rmarkdown::render() {#using-render}

When the core of this package was developed, none of its functionality was supported in any way by either `knitr` or `rmarkdown`. Over time, `rmarkdown::render()` got some new features that are very similar to features of `ezknitr`.  Native support for parameters inside Rmarkdown files using YAML is a big feature which makes the use of `set_default_params()` and the `params` argument of `ezknitr` less important.  However, the core problem that `ezknitr` wants to solve is the working directory issue, and this issue has yet to be addressed by `rmarkdown` or `knitr`, which makes `ezknitr` still useful.
