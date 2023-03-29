# Local Python package development using Conda and Cookiecutter

For developing a Python package it is important to be able to test different stages of its development cycle including
building, installation, updating as well as importing and usage in other Python projects. In
the beginning of a project it is often desirable to carry out development and testing within a local environment
without having to upload package sources to public repositories.

This Github project summarizes a few commands for local package building,
installation, update and removal using [Conda][https://conda.io] and
[Cookiecutter](https://www.cookiecutter.io/). The covered steps are outlined in the figure and further
described in the following text.


![cookiecutter_conda_package.png](cookiecutter_conda_package.png)


## Step 1. Create a conda environment

The first step consists of creating a Conda environment which in this example is named *envname*.
The *conda build* tool is required to build Conda packages.

```bash
conda create --name envname
conda install conda-build
conda activate envname
```

## Step 2. Install cookiecutter

Within the activated environment one installs the *cookiecutter* package. Cookiecutter is a tool to
initialize Python projects. Here *cookiecutter* is invoked with the Github URL of a template
to create a Python project for Conda.

```bash
(envname) conda install cookiecutter
(envname) cookiecutter https://github.com/conda/cookiecutter-conda-python.git
```

The tool prompts for some information such as name, email address and names for the package
and for its Github repository. With parameter values provided as shown below, *cookiecutter* creates
a folder named *MyLocalPackage* that contains initial, working Python source files as a
starting point for further development.

```
full_name [Full Name]: John Doe
email [Email Address]: jdoe@email.com
github_username [Destination github org or username]: jdoe
repo_name [repository-name]: MyLocalPackage
package_name [mylocalpackage]:
project_short_description [Short description]: Local Python package
noarch_python [y]:
include_cli [y]:
Select open_source_license:
1 - MIT
2 - BSD
3 - ISC
4 - Apache
5 - GNUv3
6 - Proprietary
Choose from 1, 2, 3, 4, 5, 6 [1]:
```

## Step 3. Package development

After *cookiecutter* is done creating the initial package files one can proceed with project-specific
developments, testing, initializing a *git* repository, commit, push, etc.

## Step 4. Build Conda package

The first step for deployment within the Conda environment is to create a Conda package using the
*conda build* command specifying the Python source folder created by *cookiecutter*.

```bash
(envname) conda build MyLocalPackage
```

The *build* process stores a package archive in a local Conda path for the active environment which
looks like this: conda/envs/*envname*/conda-bld/noarch/*mylocalpackage*-0+untagged.1.g8b99de0-py_0.tar.bz2

Thanks to *Cookiecutter* a version string is automatically generated on the basis of information from
the underlying *git* repository. For comparison, a package archive built without having initialized and committed
to *git* looks like this: *mylocalpackage*-0+unknown-py_0.tar.bz2

## Step 5. Install or update Conda package

### Installing the Conda package

Installation is carried using the *conda install* command with the local channel (-c) **${CONDA_PREFIX}/conda-bld/**
as given and the package name specified to *cookiecutter*.

```bash
(envname) conda install -c ${CONDA_PREFIX}/conda-bld/ mylocalpackage
```

### Updating the Conda package

After building a new version of the package, one can update the installed version using the
*conda update* command.

```bash
(envname) conda update -c ${CONDA_PREFIX}/conda-bld/ mylocalpackage
```

Both *install* and *update* tools have an argument *--offline* to restrict the Conda process from
connecting to the internet.


After installation and updating one can check in a Python console whether the package can be
imported and accessed as usual.

```bash
(envname) python3
Python 3.11.0 (main) [GCC 11.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import mylocalpackage
>>> mylocalpackage.__version__
'0+untagged.2.gc3e89d7'
```

The *cookiecutter* template used in this example also creates a commandline application that can be
invoked after with the chosen package name.

```bash
(envname) mylocalpackage -V
mylocalpackage 0+untagged.2.gc3e89d7
```

## Step 6. Removing the package

In case one wishes to remove the package one can do that using the *conda remove* command.

```bash
(envname) conda remove mylocalpackage
```
