# Conda Environments

## Anaconda Information

Provides all configuration information about conda.

```python
conda info
```

---

## Environments

Adds environment to GUI options.

```python
conda config --add envs_dirs C:\Apps\conda-envs
```

---

Create a new environment at location, then needs to be activated (location and Python version can be changed).

```python
conda create -p C:\Apps\conda-envs\MyEnv python=3.11
```

```python
conda activate C:\Apps\conda-envs\MyEnv
```
