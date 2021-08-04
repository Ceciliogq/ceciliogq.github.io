---
title: How to Conda
excerpt: >-
  Useful conda commands and tricks.
authors: C. García-Quirós
date: '2021-08-01'
layout: post
---

Here I will discuss some of the most useful commands as well as comment about related topics that may be useful when working with conda.

# Introduction

Conda is a package and environment manager. It allows to install python (and other software) packages, and to create environment to isolate all the software dependencies and avoid incompatibilities with other existing software.

`conda` < `miniconda` < `Anaconda`.<br/> miniconda includes conda with python, pip and a few more. Anaconda includes miniconda with a lot of scientific packages.

Useful links:

- Official [Conda cheatsheet](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf)

- [Introduction to Conda for (data) Scientist](https://carpentries-incubator.github.io/introduction-to-conda-for-data-scientists/index.html). Very easy to read and understand the basics.

- [Official Conda Docs](https://docs.conda.io/projects/conda/en/latest/index.html). Extensive documentation of conda commands and how to manage packages and environments (Tasks section).



# Command Syntax
The structure of a `conda` command can be decomposed as follows:

`conda` + `command` + `argument` + `--options`

Sometimes the order of each instruction is not relevant and you can add the options before the argument. <br/>(Disclaimer: I am calling options to what is added with `--something`, but for some commands they are not optional so they are in fact arguments.)

# Managing environments
- `conda create --name name-of-env packages-of-env` create environment named 'name-of-env' with `packages-of-env` installed. <br/>
E.g. `conda create --name igwn-py38 python numpy pandas`. <br/>The version of each packages can be added with '=' or '==': `conda create --name igwn-py38 python=3.8 numpy=1.20 pandas=1.3`.<br/>
Typically the third version number (build number) is left free so there is freedom to add bugfixes and avoid some incompatibilities between other dependencies. <br/>The problem with specifying the versions is that you need to know if those versions are compatible. If not, conda will throw an error and will not create the environment.

- `conda create --clone original-env --name copy-env`  clone a conda environment with new name.

- `conda (de)activate name-of-env`
- `conda activate` activate base (now conda) environment which is always available with miniconda/anaconda

- By default the environment will be located under `~/.conda/envs/name-of-env`. To specify the location use `--prefix /path/to/folder`. E.g. `conda create --prefix ./env python numpy pandas`.
- The `--prefix` option does not allow us to use `--name` and the "name" of the env will be the whole path. However, this can be shortened doing
`conda config --set env_prompt '({name})'`. <br/>This will create or modify your .condarc file and will set the name of the env to the last item of the path specified in prefix, in this case `env`. But the environment will continue without a real name.

- `--name` and `--prefix` can be used in other commands to refer to a specific environment. If not present, the command will apply to the current active environment.

- `conda env list` to see the list of available environments

- `conda remove --name name-of-env (--prefix /path/to/env) --all` revome the whole environment (you can also do rm -rf the folder of the env). But be careful not to delete the environment you are currently working on or if you are inside the env folder.


# Managing packages

- `conda install package-name(=version.number)`. This will install a new package in the current environemnt, but you can specify in which environment to install it using the `--name` or the `--prefix` options. <br/>E.g. `conda install --name name-of-env panda=1.3`. <br/>You can specify several packages in the same line.

- **Not install packages in the base environment**. Always leave it clean and create another environment for your project.

- `conda search package-name`. This will list the different versions available for that package.

- `conda list (--name env-name / --prefix /path)` to see the packages installed in current(specified) environment.

- Extra info: a conda package is a tarball file with the structure: bin/; info/; lib/python3.x/site-packages/folders-of-python-packages.

- The conda packages can be obtained from different repositories or "channels". Some channels may have more updated packages or packages that do not exist in other channels. Some channels are: main (or defaults), conda-forge, bioconda, pytorch, etc.

- `conda install scipy=1.6 --channel conda-forge` specify the channel where to install from. Add a hierarchy of channels with `... --channel conda-forge --channel bioconda ...`.

- `conda search package-name --channel conda-forge` see versions available of this package in the channel conda-forge.

- We can specify different channels for different packages both when installing a package or createing the environment with this syntax:
`conda create --name name-of-env conda-forge::python=3.6 pytorch::pytorc=1.1`

- If the package is not available in any channel, we can use `pip`, but only as a last resource.<br/> **Don't use your system pip or the (ana)miniconda's pip**, always install `pip` in your conda env (`conda install pip`) and use this to avoid conflicts. Then do `python -m pip install scipy==1.2` notice now the double '=='.

- ***IMPORTANT WARNING***: try not to install anything with `pip --user` or delete your `~/.local` folder. For some weird reason, conda looks for python packages first under `~/.local/lib/python3.8/site-packages` and then looks at the environment specific `~/.conda/envs/your-env/lib/python3.8/site-packages`. This is a very contraintuitive behaviour and quite annoying since we lose the whole point of an environment. I would understand that you use the system's package as last resource, if they are not in the conda environment... <br/> By doing `export PYTHONNOUSERSITE=True` you will skip the `.local` however this will also be true when you do not use conda and you are just using the system's python (all the pip installed packages go to ~.local when using system's pip). There must be a way of adding this environment variable only when activating a conda environment e.g. with .condarc (I haven't found it yet, all the issues have been open for years or closed without a solution...).
I have just modified my `activate_conda` [alias](https://github.com/Ceciliogq/dotfiles/blob/main/.bash_aliases) which activate CVMFS's conda and added `export PYTHONNOUSERSITE=True` there. There are other aliases to set and unset `PYTHONNOUSERSITE` but it easier to open a new terminal. There is another alias ([`sys-path`](https://github.com/Ceciliogq/dotfiles/blob/main/.bash_aliases)) to check where python is looking for packages.

# Sharing environments

- Environments can be created from and exported to YAML files. They have a simple syntax and use identation for indicate nesting.

- We can indicate the name, the channels and the packages to be installed. We can also tell to install packages with pip (which must be a dependency) or include a requirements.txt file with the pip packages. This is just a column with the package name and the version e.g. `numpy>=1.20`.

- It is good practice to version control the environment.yml files.

- Example:

```yaml
name: name-of-env

channels:
  - pytorch
  - defaults

dependencies:
  - ipython
  - matplotlib=3.1
  - pytorch=1.1
  - pip=19.1
  - pip:
    -kaggle==1.5
    (or - -r file:requirements.txt)
```

When using the `--file` option we have to add `env` to the `conda create` command:

- `conda env create --file environment.yml`  this will create an env with the name specified in the yml. With `--prefix` it will not have name.

- `conda env export (--name name-of-env/--prefix ...) --file my-env.yml`   export current(specific env) to a file. If no `--file` option is set, it will default to `environment.yml`.

- Previous command will add all the dependencies into the file. To export only the original packages that you used to create the environment do:
`conda env export --from-history --file my-env.yml`

- `conda env update --prefix ./env --file myenv.yml --prune`   update the env after the yml has been modified. <br/>`--prune` will remove the dependencies that are no longer necessary. If you delete one dependency it will not remove it.

- `conda env create --prefix ./env --file myenv.yml --force` to rebuild the environment from scratch.


# Adding Conda envs to Jupyter

We do not need to install Jupyter in every conda environment, we can make our jupyter installation be aware of our conda environments:

- Install in your environment `ipykernel`.
- (Activate environment if it was not.)
- `python -m ipykernel install --user --name env-name --display-name "Env-name"`.<br/>
This command will create a Kernel spec file in JSON format to be used by Jupyter. The `--user` option will put this file under `~/.local/share/jupyter/kernels/env-name/kernel.json`. Notice that the argument `--name env-name` is to be used internally by jupyter and does not need to correspond with the actual name of the environment (it may not have it if it was created with `--prefix`).
- Then you can open Jupyter (from your local installation or from another conda env which has it) and it will show the new kernel in the dropdown menu with the "Env-name" in `--display-name`. Deleting the `~/.local/share/jupyter/kernels/env-name` folder will remove the kernel.


# Installing LALSuite under Conda environments

The igwn-pyXY environments already provide LALSuite.
If you need to install your own LAL:
 1. Activate igwn environment (or better a copy 12GB)
 2. `./00boot` as usual
 3. `configure --prefix=$CONDA_PREFIX --options`. In Hawk it was necessary to add `CFLAGS=-Wno-error`.
 3.b My usual command for only LALSimulation is: <br/>`configure --prefix=$CONDA_PREFIX --disable-lalinference --disable-lalinspiral --disable-lalapps --disable-lalpulsar --disable-lalburst`
 4. `make -j; make install` as usual.

This will add and replace several folders, files and binaries under `$CONDA_PREFIX`. Inside this location the structure is something like this
 - `etc/`
 - `lib/*, pkg-config, python3.8/site-packages`
 - `include/`
 - `share/`
 - ...

The `make install` distributes the LAL files in that folder structure.

### Multiple LAL installations under one Conda environment

It is recommendable to have one conda environment per LAL installation. However, the `igwn` environments are 12GB in size so it is not practical to have several of them. What I would advise is to create smaller environments, where you install whatever you need just to install LAL (in my case it would be only LALSimulation).

Nevertheless, if you need to demostrate that you LAL code works in an official `igwn` environment here is what you need to do.

1. Compile your LAL as in the previous section but adding a extra subfolder: `--prefix=$CONDA_PREFIX/lalsuite-new`.

2. If now you source this LAL `source $CONDA_PREFIX/lalsuite-new/etc/lalsuite-user-env.sh` it will pick the correct LAL installation, branch, etc. Check with aliases [`check-lal`](https://github.com/Ceciliogq/dotfiles/blob/main/.bash_aliases) and `sys-path`. **However**, importing `lalsimulation` will not work due to a swig error related.

3. The reason seems to be that the LAL files live in their own `etc/`, `bin/`, `include/`, ... structure and they are not shared with the files in the conda's structure. So the obvious solution seems to move the LAL files to the conda structure.

4. The script [`switch-conda-lal.sh`](https://github.com/Ceciliogq/dotfiles/blob/main/switch-conda-lal.sh) does this. Instead of moving the files it will create symlinks in the conda's structure pointing to the files in the `new-lal` structure and it will back up in `lalsuite-default` those files that will be replaced.

5. Before creating the symlinks, it will check if the files in conda's structure already exists in the folder of the `old-lal` installation. This `old-lal` is the LAL installation of the current LAL, it corresponds to `lalsuite-default` if it has been already created or any other LAL installation which has been sourced before. Then it will remove from conda's structure those files which are already present in old-lal.

6. Finally, it will source the `new-lal` installation `source $CONDA_PREFIX/lalsuite-new/etc/lalsuite-user-env.sh`. In the default LAL installation there is no such a file, instead, we will source all the `etc/lal*-user-env.sh` (`lalsuite-user-env.sh` is just a pointer to them). In case we are switching to the default LAL, the environment variables `LAL_PREFIX` and `PYTHONPATH` are unset. The former because it is used in the script to detect the default installation and the later to keep `sys-path` clean and not to mix several installations.

7. The script needs to be run with `. switch-conda-lal.sh` or `source switch-conda-lal.sh`. Using `bash` will not make the environment variables persistent. I created an alias for easy run: [`switch-conda-lal`](https://github.com/Ceciliogq/dotfiles/blob/main/.bash_aliases).

8. To test that it worked you can again use the alias `check-lal`, `sys-path` and now you can `import lalsimulation`. You can also run some of the tests in LALSimulation e.g.: `python lalsimulation/test/pythont/test_phenomX.py`. I tested also in Jupyter notebooks and it worked.

9. Further test to carry out:
     - Check bilby
     - HTCondor, getenv=true


10. EXTRA: It might be that there is a more elegant solution.
The XLAL functions in the main package lal could be accessed through swig just by doing the source.
It is also true that lalsimulation appears in some other places in the file structure where lal is not present, so it is difficult to work out a solution without an understanding of the swig interface.
The file that was causing the import problem seemed to be `lalsimulation.py`, modifying this could fix the problem, but bear in mind that this is a file "automatically generated by the swig interface" and I do not know if it is during compilation or at run time.
