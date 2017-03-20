---
title: 'Problems Solving For Installing Sklearn on Windows'
layout: post
tags:
  - Python
  - Sklearn
category: Notes
---
<!--more-->

## Problem 1. pip install sklearn/scipy failed
```shell=
$ pip install sklearn
failed building wheel for scikit-learn
```
So I check the requirements on the [sklearn official site](http://scikit-learn.org/stable/install.html) and found that I didn't install scipy before.
```
Scikit-learn requires:
Python (>= 2.6 or >= 3.3),
NumPy (>= 1.6.1),
SciPy (>= 0.9).
```
Since sklearn needs the dependency of `scipy`, I need to install scipy before installing sklearn. However, teh same error occurs.
```shell=
$ pip install sklearn
failed building wheel for scikit-learn
```
To solve this problem, I need to download the needed wheel manually and install it by the following command:
```shell=
$ pip install <filename>.whl
```

- Python Wheels Download Sites
    - [Numpy](http://www.lfd.uci.edu/~gohlke/pythonlibs/#numpy)
    - [Scipy](http://www.lfd.uci.edu/~gohlke/pythonlibs/#scipy)
    - [Sklearn](http://www.lfd.uci.edu/~gohlke/pythonlibs/#scikit-learn)

## Problem 2. `filename.whl` is not a supported wheel on this platform

To check which version of wheel should be downloaded and installed, you can input the following python commands in shell:
```sh=
>>> import pip;
>>> print(pip.pep425tags.get_supported())
[('cp36', 'cp36m', 'win32'), ('cp36', 'none', 'win32'), ('py3', 'none', 'win32'), ('cp36', 'none', 'any'), ('cp3', 'none', 'any'), ('py36', 'none', 'any'), ('py3', 'none', 'any'), ('py35', 'none', 'any'), ('py34', 'none', 'any'), ('py33', 'none', 'any'), ('py32', 'none', 'any'), ('py31', 'none', 'any'), ('py30', 'none', 'any')][('cp36', 'cp36m', 'win32'), ('cp36', 'none', 'win32'), ('py3', 'none', 'win32'), ('cp36', 'none', 'any'), ('cp3', 'none', 'any'), ('py36', 'none', 'any'), ('py3', 'none', 'any'), ('py35', 'none', 'any'), ('py34', 'none', 'any'), ('py33', 'none', 'any'), ('py32', 'none', 'any'), ('py31', 'none', 'any'), ('py30', 'none', 'any')][('cp36', 'cp36m', 'win32'), ('cp36', 'none', 'win32'), ('py3', 'none', 'win32'), ('cp36', 'none', 'any'), ('cp3', 'none', 'any'), ('py36', 'none', 'any'), ('py3', 'none', 'any'), ('py35', 'none', 'any'), ('py34', 'none', 'any'), ('py33', 'none', 'any'), ('py32', 'none', 'any'), ('py31', 'none', 'any'), ('py30', 'none', 'any')]
```

Make sure that every tag section(separated by '-') in your wheel file name is included in the supported tags.

## Successfully Installed

After install numpy, scipy, and sklearn respectively from wheel, sklearn is successfully installed.
```shell=
$ pip install numpy-1.12.1+mkl-cp36-cp36m-win32.whl
$ pip install scipy-0.19.0-cp36-cp36m-win32.whl
$ pip install scikit_learn-0.18.1-cp36-cp36m-win32.whl
```
