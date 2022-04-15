# A Case for Mamba

Mamba - A drop-in replacement for Conda :

## **Why?**

While there aren’t a LOT of problems with Conda, of the 2 problems that we care about, one of them is extremely time consuming and indeterminate when it happens - 

- a) Identifying the right channel for a package
- b) Dependency resolution during installation of packages of particular versions. 

a) The first problem is trivial enough. Go to anaconda.com, and you can identify if the package you want is in a channel outside of the defaults. Alternatively, you can also use this information to prioritize your channel to be searched for first. It IS a problem, but not one without solutions.

b) The second problem of Dependency Resolution by Anaconda is very real. Over the last 2 years that I’ve been handling conda environments, I’ve seen a lot of cases when package A of version v1 doesn’t sit well with package B of version v2 when these versions are the “supported” versions. And in the balance, in trying to resolve these conflicts, conda dives into an abyss whose depth we do not and more often than not, this conflict resolution, after a lot of time, ends up in either a cancellation from a frustrated user or an unreadable number of error messages that tell us this exercise has failed.



## Enter Mamba[0]

While this is not the one-metaphorical-ring-to-rule-them-all of package managers (or it would be more widely used), it has a lot of pros over conda’s implementation, its package installation methodologies, querying methods and package resolution techniques.
The problem with Conda’s installation and dependency resolution, I understand, is that it’s not the resolution itself that’s slow, which depends on a SAT Solver[1], but the process that leads up to it, in indexing all the items in the JSON index file pertaining to a particular channel such as conda-forge and processing as many python objects. 

By contrast, Mamba searches across different channels in parallel, with priority of channels respected, and finds solvables[2] to use and install. It also uses libsolv, the solver used by openSuse for its package resolution - apparently internally omits a lot of processing that conda undertakes. The exact nature of this processing is still unclear to me and I’m reading into the C++ code internals for my own understanding.

_Tl;dr :_
- Mamba searches across channels for packages in parallel
- It is verbose to the extent that it is informative and also informs what packages are being installed and when packages are being upgraded etc a lot more clearly than conda.

## Working examples - 

### Installation of Mamba:

The very first step is still conda. So the assumption is that conda is installed. The installation of Mamba is as you would install a conda package. 
Except this is done in the “base” conda environment : 

`conda install mamba -n base -c conda-forge`


### Creation of environments using mamba: 

Just substitute conda with mamba in the creation of an environment -

`mamba create -n spark_env_test python=3.9`


### Installation of packages in an environment created using conda/mamba

For this example, I installed pmdarima using mamba on a pre-created environment. 

The usage is as simple as replacing “conda install pmdarima” in an activated conda environment with “mamba install pmdarima”. If we had to use command line arguments to specify the name of the environment to install it in, we could.

`mamba install pmdarima`



### Usage : 

As you can see in the screenshot on the next page, the different sources for each of the dependencies have been fetched in parallel and the installation set is proposed with a lot of information regarding sizes, sources, versions etc. It also informs us clearly what is being upgraded and the time per package’s installation and speed of download etc is also beautifully logged. 
**Clarity** in what is happening is Important and mamba seems to be delivering on that. 


![image](https://user-images.githubusercontent.com/342533/163648507-76c37fed-e52a-4b38-9b19-d080d3b44969.png)

![image](https://user-images.githubusercontent.com/342533/163648514-8f6b1b36-7ec9-4c43-a192-c5ad9b4b54d6.png)


### Understanding dependencies of a package X

If you wanted to find out what all packages {X1,X2,X3,...Xn} a package X depends on in an environment, using conda to do this is still (in 2021 -_-) weirdly a pain! [3]

This is possible out of the box with mamba using - 

`mamba repoquery depends <dependent-package>`

![image](https://user-images.githubusercontent.com/342533/163648520-90dab58c-6802-4d07-ace8-055d200f06e3.png)



### What about conda-build though?

We use conda-build to create packages etc. What do we do if we use mamba? We still might have to stick to conda-build for all production-intents and purposes (😔) because the replacement offering “boa” (https://github.com/mamba-org/boa) is still admittedly a “work-in-progress”. But for local experiments, it is still very usable. 
As is the equivalent locally hostable server for conda packages - “quetz” https://github.com/mamba-org/quetz


### Overall takeaways :

Mamba is a possible drop-in replacement for Conda and is faster while being very informative in its usage.
The dev-team could possibly incorporate it in its day-to-day usage and consultants can use it to try out different combinations of compatible package versions on their local a lot more easily. 


### References

[0] https://github.com/mamba-org/mamba

[1]  https://www.anaconda.com/blog/understanding-conda-and-pip

[2] https://mamba.readthedocs.io/en/latest/developer_zone/internals.html

[3] https://stackoverflow.com/questions/26101972/how-to-identify-conda-package-dependents



