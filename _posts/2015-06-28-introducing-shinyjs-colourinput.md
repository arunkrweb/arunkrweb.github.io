---
layout: post
title: "Shiny finally has a colour picker - use colourInput to select colours in Shiny apps"
tags: [professional, rstats, r-bloggers, shiny, packages, shinyjs]
date: 2015-06-28 13:00:00 -0700
permalink: /2015/06/28/introducing-shinyjs-colourinput/
share-img: https://deanattali.com/img/blog/colourInput/colourInputDemo.png
gh-repo: daattali/colourpicker
gh-badge: [star, watch, follow]
lastupdated: 2016-08-15
---

**NOTE: the colour input described here is no longer part of `shinyjs` and is now available in the `colourpicker` package instead.**

> I don't always think Shiny is missing anything, but when I do - I fill in the gap myself.

That was meant to be read as The Most Interesting Man In The World, but now that I think about it - maybe he's not the type of guy who would be building Shiny R packages...

Shiny has many useful input controls, but there was one that was always missing until today - a colour picker.  The package [**`colourpicker`**](https://github.com/daattali/colourpicker) now offers a **`colourInput()`** function and, of course, a corresponding **`updateColourInput()`**. There have been many times when I wanted to allow users in a Shiny app to select a colour, and I've seen that feature being requested multiple times on different online boards, so I decided to make my own such input control.  

**UPDATE 2016-03-27:** There is now an awesome RStudio addin/gadget that lets you select multiple colours interactively if you need help picking colours. [Read more here](https://github.com/daattali/colourpicker#to-select-colours-to-use-in-your-r-code-colourpicker).  

## Table of contents

- [Demo](#demo)
- [Availability](#availability)
- [Features](#features)
  - [Simple and familiar](#simple)
  - [Allowing "transparent"](#transparent)
  - [How the chosen colour is shown inside the input](#showColour)
  - [Limited colour selection](#limited)
  - [Updating a colourInput](#update)
  - [Flexible colour specification](#colour-spec)
  - [Works on any device](#compatibility)
- [Misc](#misc)

## Demo {#demo}

[Click here for a Shiny app showing several demos of colourInput](https://daattali.com/shiny/colourInput/). If you don't want to check out the Shiny app, here is a short GIF demonstrating the most basic functionality of `colourInput`.

[![colourInput demo]({{ site.url }}/img/blog/colourInput/colourInputDemo.gif)]({{ site.url }}/img/blog/colourInput/colourInputDemo.gif)

The colours of course don't look as ugly as in the GIF, here's a screenshot of what a plain `colourInput` looks like.

[![colourInput demo]({{ site.url }}/img/blog/colourInput/colourInputDemo.png)]({{ site.url }}/img/blog/colourInput/colourInputDemo.png)

## Availability {#availability}

`colourInput()` is available in [`colourpicker`](https://github.com/daattali/colourpicker).  You can either install it [from GitHub](https://github.com/daattali/colourpicker) with `devtools::install_github("daattali/colourpicker")` or from CRAN with `install.packages("colourpicker")`.

## Features {#features}

### Simple and familiar {#simple}

Using `colourInput` is extremely trivial if you've used Shiny, and it's as easy to use as any other input control.  It was implemented to very closely mimic all other Shiny inputs so that using it will feel very familiar. You can add a simple colour input to your Shiny app with `colourInput("col", "Select colour", value = "red")`. The return value from a `colourInput` is an uppercase HEX colour, so in the previous example the value of `input$col` would be `#FF0000` (#FF0000 is the HEX value of the colour red). The default value at initialization is white (#FFFFFF).

### Allowing "transparent" {#transparent}

Since most functions in R that accept colours can also accept the value "transparent", `colourInput` has an option to allow selecting the "transparent" colour. By default, only real colours can be selected, so you need to use the `allowTransparent = TRUE` parameter. When this feature is turned on, a checkbox appears inside the input box.

If the user checks the checkbox for "transparent", then the colour input is grayed out and the returned value of the input is `transparent`. This is the only case when the value returned from a `colourInput` is not a HEX value. When the checkbox is unchecked, the value of the input will be the last selected colour prior to selecting "transparent".

By default, the text of the checkbox reads "Transparent", but you can change that with the `transparentText` parameter. For example, it might be more clear to a user to use the word "None" instead of "Transparent". Note that even if you change the checkbox text, the return value will still be `transparent` since that's the actual colour name in R.

This is what a colour input with transparency enabled looks like

[![allowTransparent demo]({{ site.url }}/img/blog/colourInput/allowTransparent.png)]({{ site.url }}/img/blog/colourInput/allowTransparent.png)

### How the chosen colour is shown inside the input {#showColour}

By default, the colour input's background will match the selected colour and the text inside the input field will be the colour's HEX value. If that's too much for you, you can customize the input with the `showColour` parameter to either only show the text or only show the background colour.

Here is what a colour input with each of the possible values for `showColour` looks like

[![showColour demo]({{ site.url }}/img/blog/colourInput/showColour.png)]({{ site.url }}/img/blog/colourInput/showColour.png)

### Limited colour selection {#limited}

If you want to only allow the user to select a colour from a specific list of colours, rather than any possible HEX colour, you can use the `palette = "limited"` parameter.  By default, the limited palette will contain 40 common colours, but you can supply your own list of colours using the `allowedCols` parameter. Here is an image of the default limited colour palette.

[![colourInput demo]({{ site.url }}/img/blog/colourInput/limited-palette.png)]({{ site.url }}/img/blog/colourInput/limited-palette.png)

### Updating a colourInput {#update}

As with all other Shiny inputs, `colourInput` can be updated with the `updateColourInput` function.  Any parameter that can be used in `colourInput` can be used in `updateColourInput`. This means that you can start with a basic colour input such as `colourInput("col", "Select colour")` and completely redesign it with

```r
updateColourInput(session, "col", label = "COLOUR:", value = "orange",
  showColour = "background", allowTransparent = TRUE, transparentText = "None")
```

### Flexible colour specification {#colour-spec}

Specifying a colour to the colour input is made very flexible to allow for easier use. When giving a colour as the `value` parameter of either `colourInput` or `updateColourInput`, there are a few ways to specify a colour:

- Using a name of an R colour, such as `red`, `gold`, `blue3`, or any other name that R supports (for a full list of R colours, type `colours()`)
- If transparency is allowed in the `colourInput`, the value `transparent` (lowercase) can be used. This will update the UI to check the checkbox.
- Using a 6-character HEX value, either with or without the leading `#`.  For example, initializing a `colourInput` with any of the following values will all result in the colour red: `ff0000`, `FF0000`, `#ff0000`.
- Using a 3-character HEX value, either with or without the leading `#`. These values will be converted to full HEX values by automatically doubling every character. For example, all the following values would result in the same colour: `1ac`, `#1Ac`, `11aacc`.

### Works on any device {#compatibility}

If you're worried that maybe someone viewing your Shiny app on a phone won't be able to use this input properly - don't you worry. I haven't quite checked every single device out there, but I did spend extra time making sure the colour selection JavaScript works in most devices I could think of. `colourInput` will work fine in Shiny apps that are viewed on Android cell phones, iPhones, iPads, and even Internet Explorer 8+.

## Misc {#misc}

In order to build `colourInput`, I needed to use a JavaScript colour picker library. After experimenting with many different colour pickers, I decided to use [this popular jQuery colour picker](https://github.com/claviska/jquery-minicolors) as a base, and extend it myself to make it geared to work with Shiny. I simplified much of the code and added some features that would make it integrate with Shiny much easier. You can see the exact changes I've made in the [README for my version of the library](https://github.com/daattali/jquery-colourpicker). The main features I added were the support for a "transparent" checkbox, the complete look of the input field was redesigned, and I also changed the colour picker colours to render completely in CSS instead of using images.

The colour picker functions were initially developed in the `shinyjs` package but it has been pointed out by many people that these functions are not exactly in-line with the general `shinyjs` idea, so they eventually graduated into the `colourpicker` pacakge.

---

If anyone has any comments or feedback, both negative or positive, I'd love to [hear about it]({{ site.url }}/aboutme#contact)! Feel free to open issues [on GitHub](https://github.com/daattali/colourpicker) if there are any problems.
