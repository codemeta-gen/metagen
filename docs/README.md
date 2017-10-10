# Metagen design doc

The purpose of Metagen is to automate the maintenance of [codemeta.json](https://github.com/codemeta/codemeta) files embedded in software repositories by extracting metadata latent in the software itself.

## The workflow

With Metagen, rendering a `codemeta.json` file has three phases:

1. Software maintainers add a `codemeta.json` to their software's repository.
   In this `codemeta.json` file they commit any subset of CodeMeta fields they wish.
   Typically they add metadata fields that are well-known, static (not changing frequently), or not accessible to Metagen's processors.

3. Software maintains add a `metagen.yaml` file to their software's repository.

2. A bot (such as a web app that responds to GitHub webhook events) or a human run Metagen on a repository.
   Metagen creates a fully-rendered `codemeta.json` object by reading in the repository's embedded `codemeta.json` file and applying a sequence of processors that augment and transform that `codemeta.json` file with metadata that the processors
   This fully-rendered `codemeta.json` object can either be passed to other web apps or embedded in a software release.

## Components

The Metagen project has three components:

1. Metagen (core). A python package that provides the UI and runs processors.
2. Processors. Setuptools entrypoints (Python) that implement individual metadata processing steps. For example, a processor might obtain authorship information from the GitHub API and merge it into the `codemeta:author` field.
3. metagen.yaml. The configuration file embedded in software repositories that configures processors and specifies the sequence in which Metagen runs them.

## Metagen (core)

The core Metagen software is implemented as a Python 3 package.
Python 3 is chosen because it is an popular scientific language, making it easier for the community to write processor plugins.
Just because Metagen is implemented in Python does not mean that it only renders metadata for Python software.
By writing new processors, metadata from projects in any language can be gathered and aggregated into a `codemeta.json` object.

Implementations of Metagen in other languages is conceivable since the core `metagen.yaml` configuration file should be language agnostic.
Other languages would have to re-implement processor plugins, which are Python for Metagen.

Metagen has the following functionality in its core:

- Provides a command-line interface (built with [click](http://click.pocoo.org/6/)).
- Registers processor plugins (using [setuptools entrypoints](http://setuptools.readthedocs.io/en/latest/setuptools.html#dynamic-discovery-of-services-and-plugins)).
- Validates the `metagen.yaml` configuration file embedded in a software repository.
- Validates the existing `codemeta.json` metadata file in a software repository.
- Reads the existing `codemeta.yaml` file and applies processors to it as configured in `metagen.yaml`.

## Processors

TODO:

- Decide on whether a processor maps 1:1 with a single codemeta field, or if one processor could update many fields at once.
- Overview of the entrypoints discovery mechanism.
- Describe the Python API for a processor. Probably two methods/functions are needed: one to help validate a `metagen.yaml` configuration file and another to process a `codemeta.json` file.
- Describe a method of passing around private information. For example, could one GitHub-based processor pre-fetch a lot of data from the GitHub API and then cache it in a temporary and private field of the `codemeta.json` so that other GitHub-based processors could use that cached data?

## metagen.yaml

TODO:

- Design top-level YAML schema
- Design processor-level YAML object schema.
- Design a YAML DSL that processor configurations can use for merging metadata (for example, append authors without duplication to the bottom of a list, or replace existing authors, or append and sort by alphabet, or append and sort by number of commits, and so on).
- Design a schema for configuring secrets used by processors. For example, how do we pass a GitHub token to processors that use the GitHub API?
