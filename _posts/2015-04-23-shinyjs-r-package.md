---
layout: post
title: "Introducing shinyjs: perform common JavaScript operations in Shiny apps using plain R code"
tags: [professional, rstats, r-bloggers, shiny, packages, shinyjs, popular]
date: 2015-04-23 17:30:00 -0700
permalink: /2015/04/23/shinyjs-r-package/
gh-repo: daattali/shinyjs
gh-badge: [star, watch, follow]
lastupdated: 2016-12-23
---

`shinyjs` is my second R package that managed to find its way past the CRAN review process.
It lets you perform common useful JavaScript operations in Shiny
applications without having to know any JavaScript.

# Important note

**The package has improved a lot since writing this post. I highly recommend you stop reading this page and instead go the [shinyjs website](https://deanattali.com/shinyjs).**

<div style="text-align:center;">
<a style="text-decoration: none;" class="btn btn-success btn-lg" href="https://deanattali.com/shinyjs">shinyjs website</a>
</div>

## Demos

You can [check out a demo Shiny app](https://daattali.com/shiny/shinyjs-demo/) that lets you
play around with some of the functionality that `shinyjs` makes
available, or [have a look at a very basic Shiny
app](https://daattali.com/shiny/shinyjs-basic/) that uses `shinyjs` to
enhance the user experience with very minimal and simple R code.

## Availability

`shinyjs` is available through both [CRAN](http://cran.r-project.org/web/packages/shinyjs/)
(`install.packages("shinyjs")`) and [GitHub](https://github.com/daattali/shinyjs)
(`devtools::install_github("daattali/shinyjs")`).

## Motivation

Shiny is a fantastic R package provided by RStudio that lets you turn
any R code into an interactive webpage. It's very powerful and one of
the most useful packages in my opinion. But there are just a few simple
pieces of functionality that I always find missing and I implement
myself in my Shiny apps using JavaScript (JS) because it's either not
supported natively by Shiny or it's just cleaner to do so. Simple things
like showing/hiding elements, enabling/disabling a button, showing a
popup message to the user, manipulating the CSS class or HTML content of
an element, etc.

After noticing that I'm writing the same JS code in all my apps, and
since making Shiny talk to JS is a bit tedious and annoying with all the
message passing, I decided to just package it to make it easily
reusable. Now I can simply call `hide("panel")` or `disable("button")`.
I was lucky enough to have previous experience with JS so I knew how to
achieve the results that I wanted, but for any Shiny developer who is
not proficient in JS, hopefully this package will make it easy to extend
the power of their Shiny apps.

## Overview of main functions

-   `show`/`hide`/`toggle` - display or hide an element. There are
    arguments that control the animation as well, though animation is
    off by default.

-   `hidden` - initialize a Shiny tag as invisible (can be shown later
    with a call to `show`)

-   `enable`/`disable`/`toggleState` - enable or disable an input
    element, such as a button or a text input.

-   `info` - show a message to the user (using JavaScript's `alert`
    under the hood)

-   `html` - change the text/HTML of an element (using JavaScript's
    `innerHTML` under the hood)

-   `onclick` - run R code when an element is clicked. Was originally
    developed with the sole purpose of running a `shinyjs` function when
    an element is clicked, though any R code can be used.

-   `addClass`/`removeClass`/`toggleClass` - add or remove a CSS class
    from an element

-   `inlineCSS` - easily add inline CSS to a Shiny app

-   `logjs` - print a message to the JavaScript console (mainly used for
    debugging purposes)

[Check out the demo Shiny app](https://daattali.com/shiny/shinyjs-demo/) to see some of these in action, or install `shinyjs` and run
`shinyjs::runExample()` to see more demo apps.

## Basic use case - working example

*You can view the final Shiny app developed in this simple example
[here](https://daattali.com/shiny/shinyjs-basic/).*

Suppose we want to have a simple Shiny app that collects a user's basic
information (name, age, company) and submits it, along with the time of
submission. Here is a very simple implementation of such an app (nothing
actually happens when the user "submits").

```r
library(shiny)
shinyApp(
  ui = fluidPage(
    div(id = "myapp",
      h2("shinyjs demo"),
      textInput("name", "Name", ""),
      numericInput("age", "Age", 30),
      textInput("company", "Company", ""),
      p("Timestamp: ", span(date())),
      actionButton("submit", "Submit")
    )
  ),
  
  server = function(input, output) {
  }
)
```

*Note that I generally don't like running Shiny apps like this and
prefer to declare the UI and server separately, but I do it like this
here for brevity.*

Here is what that app would look like

[![Demo app]({{ site.url }}/img/blog/shinyjs/basic-v1.png)]({{ site.url }}/img/blog/shinyjs/basic-v1.png)

Now suppose we want to add a few features to the app to make it a bit
more user-friendly. First we need to set up the app to use `shinyjs`
with two small changes

1.  A call to `useShinyjs()` needs to be made in the Shiny app's UI.
    This is required to set up all the JavaScript and a few other
    things.

2.  The app's server needs to have the `session` parameter declared, ie.
    initialize the server as `server(input, output, session)` instead of
    `server(input, output)`.

Here are 6 features we'll add to the app, each followed with the code to
implement it using `shinyjs`:

**1. The "Name" field is mandatory and thus the "Submit" button should
not be enabled if there is no name**

In the server portion, add the following code

```r
observe({
  if (is.null(input$name) || input$name == "") {
    shinyjs::disable("submit")
  } else {
    shinyjs::enable("submit")
  }
})
```

Or instead you can use the `toggleState` function and pass it a `condition`:

```r
observe({
  shinyjs::toggleState("submit", !is.null(input$name) && input$name != "")
})
```

You can use the optional `condition` in some other functions as well, which can be very useful to make your code shorter and more understandable.

**2. The "Age" and "Company" fields are optional and we want to have the
ability to hide that section of the form**

First, we need to section off the "Age" and "Company" elements into
their own section, so we surround them with a `div`

```r
div(id = "advanced",
  numericInput("age", "Age", 30),
  textInput("company", "Company", "")
)
```

We also need to add a link in the UI that will be used to hide/show the
section

```r
a(id = "toggleAdvanced", "Show/hide advanced info")
```

Lastly, we need to tell Shiny to show/hide the section when the link is
clicked by adding this code to the server

```r
shinyjs::onclick("toggleAdvanced",
                  shinyjs::toggle(id = "advanced", anim = TRUE))
```

**3. Similarly, since we don't really care about "Age" and "Company" too
much, we want to hide them initially when the form loads**

Simply surround the section we want to hide initially with
`shinyjs::hidden`

```r
shinyjs::hidden(
  div(id = "advanced",
    ...
))
```

**4. The user should be able to update the "Timestamp" in case he spends
way too long filling out the form (not very realistic here, and the
timestamp should ideally be determined when the button is clicked, but
it's good enough for illustration purposes)**

First, we need to add an "Update" link to click on, and we need to give
the element showing the time an `id` so that we can refer to it later
when we want to change its contents.

To do that, replace `p("Timestamp: ", span(date()))` with

```r
p("Timestamp: ", span(id = "time", date()), a(id = "update", "Update"))
```

Now we need to tell Shiny what to do when "Update" is clicked by adding
this to the server

```r
shinyjs::onclick("update", shinyjs::html("time", date()))
```

**5. Some users may find it hard to read the small text in the app, so
there should be an option to increase the font size**

First, we need to add checkbox to the UI

```r
checkboxInput("big", "Bigger text", FALSE)
```

In order to make the text bigger, we will use CSS. So let's add an
appropriate CSS rule by adding this code to the UI
```r
shinyjs::inlineCSS(list(.big = "font-size: 2em"))
```

Lastly, we want the text to be big or small depending on whether the
checkbox is checked by adding this code to the server

```r
observe({
  if (input$big) {
    shinyjs::addClass("myapp", "big")
  } else {
    shinyjs::removeClass("myapp", "big")
  }
})
```

Or, again, we can use the `toggleClass` function with the `condition` argument:

```r
observe({
  toggleClass("myapp", "big", input$big)
})
```

**6. Give the user a "Thank you" message upon submission**

Simply add the following to the server

```r
observe({
  if (input$submit > 0) {
    shinyjs::info("Thank you!")
  }
})
```

**The final code looks like this** (I'm using the more compact `toggle*` version where possible)

```r
library(shiny)
shinyApp(
  ui = fluidPage(
    shinyjs::useShinyjs(),
    shinyjs::inlineCSS(list(.big = "font-size: 2em")),
    div(id = "myapp",
        h2("shinyjs demo"),
        checkboxInput("big", "Bigger text", FALSE),
        textInput("name", "Name", ""),
        a(id = "toggleAdvanced", "Show/hide advanced info", href = "#"),
        shinyjs::hidden(
          div(id = "advanced",
            numericInput("age", "Age", 30),
            textInput("company", "Company", "")
          )
        ),
        p("Timestamp: ",
          span(id = "time", date()),
          a(id = "update", "Update", href = "#")
        ),
        actionButton("submit", "Submit")
    )
  ),
  
  server = function(input, output, session) {
    observe({
      toggleState("submit", !is.null(input$name) && input$name != "")
    })
    
    shinyjs::onclick("toggleAdvanced",
                     shinyjs::toggle(id = "advanced", anim = TRUE))    
    
    shinyjs::onclick("update", shinyjs::html("time", date()))
    
    observe({
      toggleClass("myapp", "big", input$big)
    })
    
    observe({
      if (input$submit > 0) {
        shinyjs::info("Thank you!")
      }
    })    
  }
)
```

You can view the final app
[here](https://daattali.com/shiny/shinyjs-basic/).

## Altenatives using native Shiny

### shiny::conditionalPanel vs shinyjs::hide/show/toggle/hidden

It is possible to achieve a similar behaviour to `hide` and `show` by
using `shiny::conditionalPanel`, though I've experienced that using
`conditionalPanel` often gets my UI to a messier state. I still use
`conditionalPanel` sometimes for basic use cases, but when there is some
logic involved in hiding/showing, I find it much easier to move that
logic to the server and use `hide`/`show`. I also think it's generally a
better idea to keep most of the logic in the server, and using
`conditionalPanel` violates that rule.  
Implementing the `shinyjs::toggle` or `shinyjs::hidden` behaviour with
pure Shiny is also possible but it also results in messier and less
intuitive code.

### shiny::render\* and shiny::update\* vs shinyjs::html

The `shinyjs::html` function can be used to change the text inside an
element by either overwriting it or appending to it. I mostly intended
for this function to be used to change the text, though it can also be
used to add HTML elements. There are many Shiny functions that allow you
to change the text of an element. For example, `renderText` is used on a
`textOutput` tag and `updateTextInput` is used on a `textInput` tag.
These functions are useful, but sometimes I like to be able to just
cange the text of a tag without having to know/specify exactly what it
was declared in the UI. These functions also don't work on tags that are
not defined as reactive, so if I just have a `p(id = "time", date())` it
would be impossible to change it. I also don't think it's possible to
append rather than overwrite with Shiny, and you can't use HTML unless
the element is declared as `uiOutput` or something similar.

There is something to be said about the fact that the pure Shiny
functions are safer and more strict, but I personally like having the
extra flexibility sometimes, even though the `html` function feels like
it doesn't really follow Shiny's patterns. I still use the Shiny
functions often, but I find `html` useful as well.

### shiny::observeEvent vs shinyjs::onclick

The `onclick` function was initially written because I wanted a way to
click on a button that will cause a section to show/hide, like so:

```r
shinyjs::onclick("toggleLink", shinyjs::toggle("section"))
```

RStudio very recently published an article describing several design
patterns for using buttons, and from that article I learned that I can
do what I wanted with `observeEvent`:

```r
observeEvent("input$toggleLink", shinyjs::toggle("section"))
```

When I first discovered this, I thought of removing the `onclick`
function because it's not useful anymore, but then I realized there are
differences that still make it useful. `observeEvent` responds to
"event-like" reactive values, while `onclick` responds to a mouse click
on an element. This means that `observeEvent` can be used for any input
element (not only clickable things), but `onclick` can be used for
responding to a click on any element, even if it is not an input tag.
Another small feature I wanted to support is the ability to overwrite vs
add the click handler (= R code to run on a click). This would not be
used for most basic apps, but for more complex dynamic apps it might
come in handy.


## Main TODO
There are several improvements I still want to provide, but the biggest thing on my TODO list is to easily allow users to extend `shinyjs`. This would mean that you could just write a JavaScript function, tell `shinyjs` where to find it, and then `shinyjs` will do some magic to let you call that function as if it was regular R code.


## Known issues

- There are some input tags that `shiny` wraps in extra HTML, and this can interfere with `shinyj` functions.  For example, using `selectInput("foo")` by default uses selectize JS, which hides the real select box that has id `foo` and instead makes a more visually appealing box. By this means that now calling `show` or `hide` on `foo` won't work. **A workaround** is to wrap the tag with a div, and call `hide`/`show` on that div instead.

- The previous workaround works for some `shinyjs` functions such as `hide` and `show`, but not all. For example, the `enable` and `disable` functions won't work on fake select boxes (the ones generated with selectize) because they don't follow the normal HTML rules for disabling inputs. This is one instance of the problem that I intend on solving becuase there is indeed a way to disable selectize inputs, but it's important to be aware of the underlying isuee - because `shiny` sometimes adds HTML around an element that you create, trying to retrieve that element by its id might not always work unfortunately.


## Contributions

If anyone has any suggestions or feedback, I would love to hear about it. If you have improvements, feel free to make a pull request.  I'd like to give a special thanks to the Shiny developers, especially Joe Cheng for always answering all my Shiny questions.
