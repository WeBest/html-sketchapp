# html-sketchapp [![Build Status](https://travis-ci.org/brainly/html-sketchapp.svg?branch=master)](https://travis-ci.org/brainly/html-sketchapp) [![npm version](https://badge.fury.io/js/%40brainly%2Fhtml-sketchapp.svg)](https://badge.fury.io/js/%40brainly%2Fhtml-sketchapp)

HTML to Sketch export solution.

## What it can do?

html-sketchapp turns HTML nodes into Sketch layers or symbols. Additionally, it allows to export shared text styles and document colors.

<img src="https://i.imgur.com/yPjMwFU.png" width="100%" />

## Why?

The motivation behind this project was ability to easily share Front-End style guide with our Design team. Although similar project, [react-sketchapp](https://github.com/airbnb/react-sketchapp), already exists it does require you to:

- use React,
- build everything using generic components (`<View>`, `<Text>`, `<Image>`),
- and keep your styles in JS.

We were unable to quickly work around these limitations, so we created html-sketchapp.

*You can learn more about this project from the excellent article ["Sketching in the Browser"](https://medium.com/seek-blog/sketching-in-the-browser-33a7b7aa0526) by @markdalgleish.*

## Limitations

Comprehensive summary of what is, and what is not supported can be found [here](https://github.com/brainly/html-sketchapp/wiki/What's-supported%3F), but the TLDR is as follows:

- pseudoelements are not supported,
- some CSS properties (e.g. overflow) are not supported or not fully supported,
- not all types of images are supported (animated gifs, webp),
- resizing information is not generated,
- all fonts have to be locally installed.

The good news is that all of those are fixable and that we welcome pull requests ❤️

## How does it work?

Ideally, this project should be an, OS independent, NodeJS library that allows to create valid Sketch files. Unfortunately, it's not possible at this point due to Sketch format limitations.

Current solution consists of two parts. First one (`html2asketch`) runs in a browser (either regular or headless) and creates an *almost* valid Sketch file (`page.asketch.json` and `document.asketch.json`). Second one (`asketch2sketch`) is a Sketch plugin that takes `asketch.json` files and imports them into Sketch.

Why two parts? `html2asketch` and `asketch2sketch` are built in different technologies and run in different environments. `html2asketch` is written in JavaScript and runs in a browser where it can easily extract all information from DOM nodes: their position, size, styles and children. Extracted information are then translated into Sketch's `document.json` and `page.json` files. Unfortunately, at the moment Sketch file format is not fully readable and some parts can't be easily generated from JavaScript (most notably text styling information which is saved as a binary blob). Additionally, the script running in the browser is limited by CORS and may not be able to download all of the images used on page. That's where we need `asketch2sketch` which is a Sketch plugin written in [cocoascript](http://developer.sketchapp.com/introduction/cocoascript/) (JavaScript + Objective-C). It "fixes" `.asketch.json` files (changes text styling information format, downloads and inlines images) and loads them into the Sketch app.

*You can read more about `.asketch` format in the [wiki](https://github.com/brainly/html-sketchapp/wiki/Differences-between-.asketch-and-.sketch).*

## How do I run it?

`html2asketch` is a library that you can use to create a script that extracts specific parts of your website and saves them as layers, shared text styles, document colors and symbols. There is no one right way of using `html2asketch`, but you can start by checking out the two examples that we provide:

- [html-sketchapp-example](https://github.com/brainly/html-sketchapp-example) - minimal script that takes an URL and produces a `page.asketch.json` file
- [html-sketchapp-style-guide](https://github.com/brainly/html-sketchapp-style-guide) - script that takes parts of the Brainly style-guide and exports them as Sketch symbols, shared text styles and document colors. This script produces `document.asketch.json` and `page.asketch.json`.

All `.asketch.json` files should be loaded to Sketch via the `asketch2sketch.sketchplugin` plugin.

<img src="https://i.imgur.com/9eDm6NQ.png" width="450" alt="Installing Sketch plugin" title="Installing Sketch plugin" />

You can install html-sketchapp from npm:

```
npm i @brainly/html-sketchapp
```

You can download ready to use Sketch plugin from the ["Releases"](https://github.com/brainly/html-sketchapp/releases/latest) section, or build it yourself from the sources:

```
npm i # install dependencies
npm run build # build the plugin
```

## Projects using html-sketchapp

- [html-sketchapp-cli](https://github.com/seek-oss/html-sketchapp-cli) - "Quickly generate Sketch libraries from HTML documents and living style guides."
- [story2sketch](https://github.com/chrisvxd/story2sketch) - "Convert Storybook stories into Sketch symbols."

## Standing on the shoulders of giants :heart:

This project uses huge bits and pieces from the fantastic [react-sketchapp](https://github.com/airbnb/react-sketchapp) and wouldn't be possible without [skpm](https://github.com/skpm/skpm) and information from [Sketch-Headers](https://github.com/abynim/Sketch-Headers).
