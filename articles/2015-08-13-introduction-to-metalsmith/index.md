---
draft: true
---

# Introduction to Metalsmith

<where does metalsmith in the world of static site generators?>
middleman, jekyll, wintersmith

## What is Metalsmith?

The Metalsmith website has a good description of what it does:

> We keep referring to Metalsmith as a “static site generator”, but it’s a lot more than that. Since everything is a plugin, the core library is actually just an abstraction for manipulating a directory of files.

## What does Metalsmith do?

Metalsmith takes a source directory you specify and recursively reads the files inside to build a map object. The keys of this map are the paths of the files relative to the source directory, and the values are the contents of the file.

Now this map object (together with some other objects) are passed to plugins, which are just JavaScript functions. A plugin can then do some operations on the “files” in the map to transform them, filter them, or to create new files in the map.

For example, a “Markdown plugin” can take the values of all keys that end with ".md", transform the values from Markdown to HTML, then add new keys to the map object to represent ".html" versions of the ".md" files.

The user specifies the order the plugins are called. Transformations done by one plugin to the map are seen by the next plugin, so the whole process can be viewed as a modular build-pipeline.

The process ends with Metalsmith “building” the files by writing the files in the map to the user specified destination directory.

Metalsmith can be run programmatically in a NodeJS script, or via a command line tool. As a quick example, here’s a build-pipeline for turning Markdown files in a directory to HTML:

## How does it work?

## Popular applications of Metalsmith

