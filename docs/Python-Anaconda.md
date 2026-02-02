# Anaconda

## Anaconda Information
Provides all configuration information about conda
~~~python
conda info
~~~
________________________
## Environments:
Adds environment to GUI options
~~~python
conda config --add envs_dirs C:\Apps\conda-envs
~~~
________________________
Create a new environment at location, then needs to be activated (Location and python version can be changed)
~~~python
conda create -p C:\Apps\conda-envs\MyEnv python=3.11
~~~
~~~python
conda activate C:\Apps\conda-envs\MyEnv
~~~
________________________