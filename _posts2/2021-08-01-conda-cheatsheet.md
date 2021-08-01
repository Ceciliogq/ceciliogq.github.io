---
title: How to Conda
excerpt: >-
  Useful conda commands and tricks.
authors: C. García-Quirós
date: '2021-08-01'
layout: post
---

Conda is an packages and environments manager. It allows to install python (and other software) packages, and to create environment to isolate all the software dependencies and avoid incompatibilities with other existing software.

Useful links:

- Official [Conda cheatsheet](https://docs.conda.io/projects/conda/en/4.6.0/_downloads/52a95608c49671267e40c689e0bc00ca/conda-cheatsheet.pdf)

- [Introduction to Conda for (data) Scientist](https://carpentries-incubator.github.io/introduction-to-conda-for-data-scientists/index.html). Very easy to read and understand the basics.

- [Official Conda Docs](https://docs.conda.io/projects/conda/en/latest/index.html). Extensive documentation of conda commands and how to manage packages and environments (Tasks section).

Here I will list some of the most useful commands which I have used more frequently.

## Syntax
The structure of a `conda` command can be decomposed as follows:

`conda` + <command> + <argument> + --options

Sometimes the order of each instructions is not relevant and you can add the options before the argument. (Disclaimer: I am calling options to what is added with --something, but for some commands they are not optional but needed.)

## Managing environments
- `conda create --name name-of-env packages-of-env`
E.g. `conda create --name igwn-py38 python numpy pandas`. The version of each packages can be added with '=' or '==': `conda create --name igwn-py38 python=3.8 numpy=1.20 pandas=1.3`.
Typically the third version number (build number) is left free so there is freedom to add bugfixes and avoid some incompatibilities between other dependencies. The problem when specifying the versions is that you need to know that those versions are compatibles. If not, conda will throw an error and will not create the environment.

- `conda (de)activate name-of-env`
- `conda activate` activate base (now conda) environment which is always available with miniconda/anaconda

- By default the environment will be located under `~/.conda/envs/name-of-env`. To specify the location use `--prefix /path/to/folder`. E.g. `conda create --prefix ./env python numpy pandas`.
- The `--prefix` option does not allow us to use `--name` and the name of the env will be the whole path. However, this can be shortened doing
`conda config --set env_prompt '({name})'`. This will create or modify your .condarc file and will set the name of the env to the last item of the path specified in prefix, in this case `env`. But the environment will continue without a name.

- `--name` and `--prefix` can be used in other commands to refer to a specific environment. If not present, the command will apply to the current active environment.

- `conda env list` to see the list of available environments

- `conda remove --name name-of-env (--prefix /path/to/env) --all` revome the whole environment (you can also do rm -rf the folder of the env). But be careful not to delete the environment you are currently working on or if you are inside the env folder.


## Managing packages

- `conda install package-name(=version.number)`. This will install a new package in the current environemnt, but you can specify in which environment to install using the `--name` or the `--prefix` options. E.g. `conda install --name name-of-env panda=1.3`. You can specify several packages in the same line.

- *Not install packages in the base environment*. Always leave that clean and create an environment for your project.

- `conda search package-name`. This will list the different versions available for that package.

- `conda list (--name env-name / --prefix /path)` to see the packages installed in current(specified) environment.

- Extra info: a conda package is a tarball file with the structure: bin/; info/; lib/python3.x/site-packages/folders-of-python-packages.

- The conda packages can be obtained from different repositories or "channels". Some channels may have more updated packages or packages that do not exist in other channels. Some channels are: main (or defaults), conda-forge, bioconda, pytorch, etc.

- `conda install scipy=1.6 --channel conda-forge` specify the channel where to install from. Add a hierarchy of channels with `... --channel conda-forge --channel bioconda ...`.

- `conda search package-name --channel conda-forge` see versions available of this package in the channel conda-forge.

- We can specify different channels for different packages both when installing a package or createing the environment with this syntax:
`conda create --name name-of-env conda-forge::python=3.6\
pytorch::pytorc=1.1`

- If the package is not available in any channel, we can use `pip`, but only as last resource. *Don't use your system pip or the (ana)miniconda pip*, always install `pip` in your conda env (`conda install pip`) and use this to avoid conflicts. Then do `python -m pip install scipy==1.2` notice now the double '=='.

- *IMPORTANT WARNING*: try not to install anything with `pip --user` or delete your `~/.local` folder. For some weird reason, conda looks for python packages first under `~/.local/lib/python3.8/site-packages` and then looks at the environment specific `~/.conda/envs/your-env/lib/python3.8/site-packages`. This is a very contraintuitive behaviour and quite annoying since we lose the whole point of an environment. I would understand that you use the system's package as last resource, if they are not in the conda environment... <br/> By doing `export PYTHONNOUSERSITE=True` you will skip the `.local` however this will also be true when you do not use conda and you are just using the system's python (all the pip installed packages go to ~.local when using system's pip). There must be a way of adding this environment variable only when activating a conda environment (I haven't found it yet, all the issues are open years ago or closed without a solution...).

### Sharing environments

- Environments can be created from and exported to YAML files. They have a simple syntax and use identation for indicate nesting.

We can indicate the name, the channels and the packages to be installed. We can also tell to install packages with pip (which must be a dependency) and include a requirements.txt file with the pip packages.

It is good practice to version control the environment.yml files.

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

- This will list all the dependencies, to export only the original packages that you used to create the environment do:
`conda env export --from-history --file my-env.yml`

- `conda env update --prefix ./env --file myenv.yml --prune`   update the env after the yml being modified. `--prune` will remove the dependencies that are no longer necessary. If you delete one dependency it will not remove it. For that do
`conda env create --prefix ./env --file myenv.yml --force`.


## Adding Conda envs to Jupyter

We do not need to install jupyter in every conda environment, we can do our jupyter installation aware of our conda environments:

- Install in your environment `ipykernel`.
- (Activate environment if it was not.)
- `python -m ipykernel install --user --name env-name --display-name "Env-name"`
This command will create a Kernel spec file in JSON format to be used by Jupyter. The `--user` option will put this file under `~/.local/share/jupyter/kernels/env-name/kernel.json`. Notice that the argument `--name env-name` is to be used internally by jupyter and does not need to correspond with the actual name of the environment (it may not have if it was created with `--prefix`).
- Then you can open jupyter (even from another conda env) and it will show the new kernel in the dropdown menu with the `--display-name "Env-name"`. Deleting the `~/.local/share/jupyter/kernels/env-name` folder will remove the kernel.
