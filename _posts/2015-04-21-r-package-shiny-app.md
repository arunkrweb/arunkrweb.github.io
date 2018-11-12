---
layout: post
title: "Supplementing your R package with a Shiny app"
tags: [professional, rstats, r-bloggers, shiny, packages, tutorial]
share-img: "https://deanattali.com/img/blog/ggExtra/ggmarginal-basic-example.png"
date: 2015-04-21 22:00:00 -0700
permalink: /2015/04/21/r-package-shiny-app/
lastupdated: 2018-04-03
---

The R community is generally very fond of open-source-ness and the idea of releasing all code to the public. Writing packages has become such an easy experience now that Hadley's `devtools` is so powerful, and as a result there are new packages being released by useRs every single day.

A good package needs to have two things: useful functionality, and clear usage instructions. The former is a no-brainer, while the latter is what developers usually dread the most - the D-word (Documentation. Yikes.). Proper documentation is essential so that others will know what your package can do and how to do it. And with the use of Shiny, we now have another great tool we can use to showcase a package's capabilities.

### Incorporating Shiny

In a nutshell, [Shiny](https://shiny.rstudio.com/) is a package that lets you run your R code as an interactive webpage. What this means for package developers is that you can have an interactive webpage that lets users experiment with your package and see what it can do before having to read through the potentially lengthy function documentations/vignette.

As an example, I recently released a package for adding marginal plots to ggplot2. You be the judge: after that one-sentence description of some functionality, would you rather go straight to the [README](https://github.com/daattali/ggExtra), or see it in action first [in a Shiny app online](https://daattali.com/shiny/ggExtra-ggMarginal-demo/)? I might be wrong, but I think it's useful to interactively see what the package can do.

In fact, I'm such a strong believer in this idea, that almost every package I have released has a corresponding demo shiny app. The `shinyjs` [demo app](https://daattali.com/shiny/shinyjs-demo/) and `colourpicker` [demo app](https://daattali.com/shiny/colourInput/) are good examples of that.

Making a Shiny app doesn't necessarily always make sense for every package, but there are certainly many times when it can be a great addition to a package's "documentation". I think that if a new package has some functions that *can* be easily illustrated in a simple Shiny app, it's worth it to take the extra 1-2 hours to develop it. This way, a user who finds your package and isn't quite sure what to do with it can try the Shiny app to see whether or not this is the functionality they were looking for. You can have several Shiny apps, each showing the usage of a particular function, or one app that is representative of a whole package. Whatever makes the most sense. Of course, having a Shiny app is in no way a replacement to documentation, it's just a useful add-on.

---

There are two ways to complement a package with a Shiny app that shows its main usage. These two methods are NOT mutually exclusive; I personally do both of them together:

#### 1. Host the Shiny app online

You can host your Shiny app somewhere that is publicly available, such as [shinyapps.io](http://www.shinyapps.io/) or on your own [Shiny Server](https://deanattali.com/2015/05/09/setup-rstudio-shiny-server-digital-ocean). Then you can include a link in the package's README or vignette or function documentation that points to the Shiny app.

As an example, I host [my own Shiny Server](https://daattali.com/shiny) where I can host my Shiny apps, and whenever I release a new package, I include a link in the README to a demo app.

The advantage of doing this is that people can play around with your package before even downloading it.

#### 2. Include the app in the package and add a function to launch it

I recommend including the source code of the Shiny app in your package, and having a function such as `runExample()` that will launch the app. Here are the steps to do this (I've learned a lot from looking at `shiny::runExample` source code - thanks RStudio):

First, add `shiny` as a dependency in your `DESCRIPTION` file.

Then place your Shiny app folder under `inst/shiny-examples/` and add an R file called `runExample.R`. The package's tree structure should look like this

~~~
- mypacakge
  |- inst
     |- shiny-examples
        |- myapp
           |- ui.R
           |- server.R
  |- R
     |- runExample.R
     |- ...
  |- DESCRIPTION
  |- ...
~~~

Your `runExample.R` will be simple - it will just look for the Shiny app and launch it

```r
#' @export
runExample <- function() {
  appDir <- system.file("shiny-examples", "myapp", package = "mypackage")
  if (appDir == "") {
    stop("Could not find example directory. Try re-installing `mypackage`.", call. = FALSE)
  }

  shiny::runApp(appDir, display.mode = "normal")
}
```

Of course, don't forget to document this function! Now users can try out an app showcasing your package by running `mypackage::runExample()`.

This method can easily support more than one Shiny app as well, simply place each app under `inst/shiny-examples/` and change the runExample code to something like this

```r
runExample <- function(example) {
  # locate all the shiny app examples that exist
  validExamples <- list.files(system.file("shiny-examples", package = "mypackage"))

  validExamplesMsg <-
    paste0(
      "Valid examples are: '",
      paste(validExamples, collapse = "', '"),
      "'")

  # if an invalid example is given, throw an error
  if (missing(example) || !nzchar(example) ||
      !example %in% validExamples) {
    stop(
      'Please run `runExample()` with a valid example app as an argument.\n',
      validExamplesMsg,
      call. = FALSE)
  }

  # find and launch the app
  appDir <- system.file("shiny-examples", example, package = "mypackage")
  shiny::runApp(appDir, display.mode = "normal")
}
```

Now running `runExample("myapp")` will launch the "myapp" app, and running `runExample()` will generate a message telling the user what examples are allowed.

#### 3. The best of both worlds: include the app in a package AND host it on a shiny server

The solution that I end up using most often myself is a combination of both of the above. I like to supplement my pacakges with a shiny app (or sometimes create a package that is mostly a shiny app), and showcase it on my [shiny server](https://daattali.com/shiny/).

To do that, follow the instructions above to create a package. Then follow [my instructions to create a shiny server](https://deanattali.com/2015/05/09/setup-rstudio-shiny-server-digital-ocean/) and make sure to install your new package. The last step is to make your new package's shiny app runnable from the shiny server; simply create a file `/srv/shiny-server/myapp/app.R` with the following code:

```r
dir <- system.file("shiny-examples", "myapp", package = "mypackage")
setwd(dir)
shiny::shinyAppDir(".")
```

And now your app will be available at `http://<your_shiny_server_url>/myapp`.
