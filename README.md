# Caffe

## Caffe with MACOS Catalina v10.15.7
System Details:
- MACOS Catalina v10.15.7
- Python 2.7.16 (came with system)
- OpenCV4
- CPU Only

## Instructions
clone this repository. Run the following:

```
brew install -vd snappy leveldb gflags glog szip lmdb
brew tap brewsci/science
brew install hdf5 opencv

brew install --build-from-source -vd protobuf
brew install --build-from-source -vd boost boost-python

cp Makefile.config.macosxCurrent Makefile.config
make all
make test
make runtest
make pycaffe

cd python
for req in $(cat requirements.txt); do pip install $req; done
```

add the following to `.zshrc`:
```
export PATH="/$YOUR_PATH_TO/caffe/build/tools:$PATH"
export PYTHONPATH="/$YOUR_PATH_TO/caffe/python:$PYTHONPATH"
```

Where `$YOUR_PATH_TO` is replaced by your path to the caffe directory.
This allows you to import caffe in python, as well as run caffe in terminal using the command `caffe`.


## General Notes

1. You have to compile in CPU Only mode because this Mac does not support CUDA. Do not attempt to download CUDA 7
2. Most of the issues that arise come with conflicting versions of python. In your Makefile.config, you have to choose 1 version of python (be it system, anaconda or Pyenv) and stick to it.


## Encoutered Errors and Fixes

### 1. `homebrew/science` Deprecated
__Error Message:__
```
Error: homebrew/science was deprecated. This tap is now empty as all its formulae were migrated.
```

__Fix:__
use `brew tap brewsci/science` instead

__Source:__
https://github.com/nest/nest-simulator/issues/916


### 2. `--with-python` Invalid
__Error Message:__
```
Error: invalid option: --with-python
```

__Fix:__ 
use `brew install --build-from-source -vd protobuf` instead

__Source:__
https://stackoverflow.com/questions/54509753/install-caffe-on-mac-error-invalid-option-with-python

### 3. C++11 Required
__Error Message:__
```
error: #error "Protobuf requires at least C++11."
#error "Protobuf requires at least C++11."
```

__Fix:__
in Makefile.config line 27, use custom compiler with std flag
```
 CUSTOM_CXX := clang++ -std=c++11 -DBoost_NO_BOOST_CMAKE=ON
```

__Source:__
https://github.com/BVLC/caffe/issues/6359

### 4. Can't Find `pyconfig.h`
__Error Message:__
```
In file included from /usr/include/boost/python/detail/prefix.hpp:13:0,
                 from /usr/include/boost/python/args.hpp:8,
                 from /usr/include/boost/python.hpp:11,
                 from src/caffe/layer_factory.cpp:4:
/usr/include/boost/python/detail/wrap_python.hpp:50:23: fatal error: pyconfig.h: No such file or directory
compilation terminated.
Makefile:565: recipe for target '.build_release/src/caffe/layer_factory.o' failed
```

__Fix:__
The problem occured because the python path given in `Makefile.config.example` wasn't correct. Changing it to the following did the trick.
```
PYTHON_INCLUDE := /System/Library/Frameworks/Python.framework/Versions/Current/include/python2.7 \
		/System/Library/Frameworks/Python.framework/Versions/Current/Extras/lib/python/numpy/core/include
```

### 5. Boost Library Not Found
__Error Message:__
```
ld: library not found for -lboost_python
```

__Fix:__
The `PYTHON_LIBRARIES` given in `Makefile` gave the wrong boost-python name. Change it to the following:

```
PYTHON_LIBRARIES ?= boost_python27 python2.7
```

__Source:__
https://stackoverflow.com/questions/49961216/pycaffe-build-fails-lboost-python-not-found

### 6. Can't Find `numpy/arrayobject.h`
__Error Message:__
```
python/caffe/_caffe.cpp:10:10: fatal error: 'numpy/arrayobject.h' file not found
```

__Fix:__
The problem occured because the python path given in `Makefile.config.example` wasn't correct. Changing it to the following did the trick.
```
PYTHON_INCLUDE := /System/Library/Frameworks/Python.framework/Versions/Current/include/python2.7 \
		/System/Library/Frameworks/Python.framework/Versions/Current/Extras/lib/python/numpy/core/include
```

### 7. Seg Fault When Importing caffe in Python
__Error Message:__
```
Segmentation fault: 11
```
__Fix:__
This occurred when I was trying to use PyEnv with python ver 2.7.18. The Makefile.config for this can be found in `Makefile.config.macosxPyenv`
I fixed it by using the system's default python of 2.7.16 instead of using Pyenv. If you would like to use pyenv, you have to make sure all your python paths point to the pyenv one. However, I am not able to find a .so or .dylib for `PYTHON_LIB` with pyenv.

__Source:__
https://github.com/BVLC/caffe/issues/591
https://stackoverflow.com/questions/35461536/error-on-import-caffe

<br/>
<br/>
<br/>


*Good Luck!*
<br/>
<br/>
<br/>


---


[![Build Status](https://travis-ci.org/BVLC/caffe.svg?branch=master)](https://travis-ci.org/BVLC/caffe)
[![License](https://img.shields.io/badge/license-BSD-blue.svg)](LICENSE)

Caffe is a deep learning framework made with expression, speed, and modularity in mind.
It is developed by Berkeley AI Research ([BAIR](http://bair.berkeley.edu))/The Berkeley Vision and Learning Center (BVLC) and community contributors.

Check out the [project site](http://caffe.berkeleyvision.org) for all the details like

- [DIY Deep Learning for Vision with Caffe](https://docs.google.com/presentation/d/1UeKXVgRvvxg9OUdh_UiC5G71UMscNPlvArsWER41PsU/edit#slide=id.p)
- [Tutorial Documentation](http://caffe.berkeleyvision.org/tutorial/)
- [BAIR reference models](http://caffe.berkeleyvision.org/model_zoo.html) and the [community model zoo](https://github.com/BVLC/caffe/wiki/Model-Zoo)
- [Installation instructions](http://caffe.berkeleyvision.org/installation.html)

and step-by-step examples.

## Custom distributions

 - [Intel Caffe](https://github.com/BVLC/caffe/tree/intel) (Optimized for CPU and support for multi-node), in particular Intel® Xeon processors.
- [OpenCL Caffe](https://github.com/BVLC/caffe/tree/opencl) e.g. for AMD or Intel devices.
- [Windows Caffe](https://github.com/BVLC/caffe/tree/windows)

## Community

[![Join the chat at https://gitter.im/BVLC/caffe](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/BVLC/caffe?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Please join the [caffe-users group](https://groups.google.com/forum/#!forum/caffe-users) or [gitter chat](https://gitter.im/BVLC/caffe) to ask questions and talk about methods and models.
Framework development discussions and thorough bug reports are collected on [Issues](https://github.com/BVLC/caffe/issues).

Happy brewing!

## License and Citation

Caffe is released under the [BSD 2-Clause license](https://github.com/BVLC/caffe/blob/master/LICENSE).
The BAIR/BVLC reference models are released for unrestricted use.

Please cite Caffe in your publications if it helps your research:

    @article{jia2014caffe,
      Author = {Jia, Yangqing and Shelhamer, Evan and Donahue, Jeff and Karayev, Sergey and Long, Jonathan and Girshick, Ross and Guadarrama, Sergio and Darrell, Trevor},
      Journal = {arXiv preprint arXiv:1408.5093},
      Title = {Caffe: Convolutional Architecture for Fast Feature Embedding},
      Year = {2014}
    }
