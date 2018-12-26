# caffe-install-centos




- Issue
```
[mwopr@fc-sit3-smart-v-l-01:/home/mwopr]#apt-get update
apt-get: error while loading shared libraries: 
libapt-private.so.0.0: cannot open shared object file: No such file or directory
```
- Solve
- [apt-get: error while loading shared libraries:libapt-pkg [duplicate]](https://askubuntu.com/questions/674356/apt-get-error-while-loading-shared-librarieslibapt-pkg)
```
$ wget http://security.ubuntu.com/ubuntu/pool/main/a/apt/libapt-pkg4.12_0.8.16~exp12ubuntu10.21_amd64.deb
結果她是centos，不是ubuntu，用yum來搞
```


- Issue
```
Could NOT find Atlas (missing: Atlas_LAPACK_LIBRARY)
```
- Solve
```
$ cmake -DBLAS=open -DCMAKE_CXX_COMPILER=g++-5 ..
$ cmake -DBLAS=open ..
$ cmake -DBLAS=open -DCMAKE_CXX_COMPILER="/usr/local/bin/x86_64-unknown-linux-gnu-g++" -DCMAKE_C_COMPILER="/usr/local/bin/x86_64-unknown-linux-gnu-gcc-5.4.0" ..

```


- Issue
```
$ vim CMakeFiles/CMakeError.log
...
CheckSymbolExists.c:(.text+0x16): undefined reference to `pthread_create'
...
/bin/ld: cannot find -lpthreads
...

搞了很久
結果發現
$ cmake ..的output message裡出現

CMake Error at /usr/local/share/cmake-3.3/Modules/FindBoost.cmake:1245 (message):
  Unable to find the requested Boost libraries.

  Boost version: 1.53.0

  Boost include path: /usr/include

  Detected version of Boost is too old.  Requested version was 1.54 (or
  newer).

```

- Solve

 安裝新版本的boost(1.6.0)
 
- [Centos 安装boost](https://www.jianshu.com/p/22d366c1fbef)




``` 
$ cmake -DBOOSTROOT="/home/mwopr/boost_1_60_0" ..
$ make all -j8
$ make install
$ make runtest
```

### opencv2.4.13 centos

- [CentOS安装OpenCV-2.4.13](https://blog.csdn.net/shine_journey/article/details/69544219)

```
$ cmake -DCMAKE_CXX_COMPILER=/usr/local/bin/x86_64-unknown-linux-gnu-g++ -DCMAKE_C_COMPILER=/usr/local/bin/x86_64-unknown-linux-gnu-gcc-5.4.0 -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=/usr/local -DWITH_QT=OFF -DWITH_OPENGL=OFF -DWITH_TBB=OFF -DBUILD_NEW_PYTHON_SUPPORT=ON -DWITH_V4L=ON INSTALL_C_EXAMPLES=ON -DINSTALL_PYTHON_EXAMPLES=ON -DBUILD_EXAMPLES=ON -DWITH_XINE=ON -DINSTALL_TESTS=ON -DWITH_GSTREAMER=ON -DWITH_CUDA=OFF -DBUILD_EXAMPLES=ON -DWITH_FFMPEG=OFF -DBUILD_TIFF=ON ..
$ make all -j8
$ make install
```

- Issue 
```
libopencv_highgui.so:undefined reference to `TIFFIsTiled@LIBTIFF_4.0'
```
- Solve
```
cmake時加上-DBUILD_TIFF=ON
```

- Issue
```
cap_ffmpeg_impl.hpp:71:6: error: #error "libswscale is necessary to build the newer OpenCV ffmpeg wrapper"
```
- Solve
```
cmake時加上-DWITH_FFMPEG=OFF
```


### TODO [[[caffe cmake 遇到 pycaffe issue]]]

- [The dependency target "pycaffe" of target "pytest" does not exist !](https://github.com/BVLC/caffe/issues/5177)

尚未解決






### update cmake to 3.9.2 for installing leveldb from source

```
$ wget https://cmake.org/files/v3.9/cmake-3.9.2.tar.gz
$ tar -xzvf cmake-3.9.2.tar.gz
$ cd cmake-3.9.2
$ ./configure
$ sudo make && make install
```

### install leveldb

```
$ git clone https://github.com/google/leveldb.git
$ mkdir -p build && cd build
$ cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=1 .. && cmake --build .
```

### install gflags

```

$ wget https://github.com/schuhschuh/gflags/archive/master.zip 
$ unzip master.zip 
$ cd gflags-master 
$ mkdir build && cd build
Edit glfags/build/CMakeCache.txt  
--->CMAKE_CXX_FLAGS:STRING=-fPIC
$ export CXXFLAGS="-fPIC" && cmake .. -DBUILD_SHARED_LIBS=ON -DCMAKE_CXX_COMPILER=/usr/local/bin/x86_64-unknown-linux-gnu-g++ -DCMAKE_C_COMPILER=/usr/local/bin/x86_64-unknown-linux-gnu-gcc-5.4.0 && make VERBOSE=1
$ make -j8
$ make install
```

### install glog
```
$ git clone https://github.com/google/glog
$ yum install autoconf automake libtool
$ cd glog
$ ./autogen.sh 
$ ./configure CPPFLAGS="-I/usr/local/include -fPIC" LDFLAGS="-L/usr/local/lib"
$ ./configure CPPFLAGS="-I/usr/local/include -fPIC" LDFLAGS="-L/usr/local/lib" --with-gflags=/home/mwopr/gflags
$ sudo make -j8
$ sudo make install
```

- Issue
```
libgflags.so.2.1, needed by /lib/../lib64/libglog.so, may conflict with libgflags.so.2.2
```
- Solve
- [Fail To Link gflag](https://github.com/jolibrain/deepdetect/issues/386)

```
$ Sudo yum search gflags
> gflags-devel.x86_64 : Development files for gflags
> gflags.x86_64 : Library for commandline flag processing
> python2-gflags.noarch : Commandline flags module for Python 2
$ sudo yum remove gflags-devel.x86_64
```


### TODO 
- Issue
```
$ cd ~/caffe/build
$ cmake -DBLAS=open ..
$ make all -j8
undefined reference to `leveldb::Status::ToString
undefined reference to `gflags::FlagRegisterer::FlagRegisterer
undefined reference to `leveldb::DB::Open
undefined reference to `google::base::CheckOpMessageBuilder::NewString
```


GFLAGS_INCLUDE_DIR:PATH=/usr/local/include/gflags
GFLAGS_LIBRARY:FILEPATH=/home/mwopr/gflags/build/lib/libgflags.so
GFLAGS_ROOT_DIR:PATH=/home/mwopr/gflags
GLOG_INCLUDE_DIR:PATH=/usr/local/include/glog
GLOG_LIBRARY:FILEPATH=/lib64/libglog.so
GLOG_ROOT_DIR:PATH=/home/mwopr/glog
LevelDB_INCLUDE:PATH=/app/leveldb/include/leveldb
LevelDB_LIBRARY:FILEPATH=/app/leveldb/build/libleveldb.so
OpenCV_3RDPARTY_LIB_DIR_OPT:PATH=/app/opencv-2.4.13/3rdparty/
OpenCV_CONFIG_PATH:FILEPATH=/app/opencv-2.4.13/
OpenCV_DIR:PATH=/usr/local/share/OpenCV
OpenCV_LIB_DIR_DBG:PATH=/usr/local/lib/
OpenCV_LIB_DIR_OPT:PATH=/usr/local/lib/
PROTOBUF_INCLUDE_DIR:PATH=/usr/local/protobuf/include/
PROTOBUF_LIBRARY:FILEPATH=/usr/local/protobuf/lib/libprotobuf.so
PROTOBUF_LITE_LIBRARY:FILEPATH=/usr/local/protobuf/lib/libprotobuf-lite.so
PROTOBUF_LITE_LIBRARY_DEBUG:FILEPATH=/usr/local/protobuf/lib/libprotobuf-lite.so
PROTOBUF_PROTOC_EXECUTABLE:FILEPATH=/usr/local/protobuf/bin/protoc
PROTOBUF_PROTOC_LIBRARY:FILEPATH=/usr/local/protobuf/lib/libprotoc.so
PROTOBUF_PROTOC_LIBRARY_DEBUG:FILEPATH=/usr/local/protobuf/lib/libprotoc.so

- Issue
```
Could NOT find NumPy (missing: NUMPY_INCLUDE_DIR NUMPY_VERSION) (Required is at least version "1.7.1
```
- Solve
```
$ yum install blas-devel lapack-devel
$ wget http://jaist.dl.sourceforge.net/project/numpy/NumPy/1.8.0/numpy-1.8.0.tar.gz
$ tar -xzvf numpy-1.8.0.tar.gz
$ cd numpy-1.8.0
$ python2.7 setup.py build --fcompiler=gnu95
$ python2.7 setup.py install --user

```

更新GCC6.3.1
把gflags，glog，protobuf，boost都重新編譯

```
$ rm /usr/bin/g++ /usr/bin/gcc
$ ln -s /opt/rh/devtoolset-6/root/usr/bin/g++ /usr/bin/g++
$ ln -s /opt/rh/devtoolset-6/root/usr/bin/gcc /usr/bin/gcc

$ cmake -DBLAS=open .. -DCMAKE_CXX_COMPILER=/usr/local/bin/x86_64-unknown-linux-gnu-g++ -DCMAKE_C_COMPILER=/usr/local/bin/x86_64-unknown-linux-gnu-gcc-5.4.0
$ cmake -DBLAS=open .. -DCMAKE_CXX_COMPILER=/opt/rh/devtoolset-6/root/usr/bin/g++ -DCMAKE_C_COMPILER=/opt/rh/devtoolset-6/root/usr/bin/gcc
```


- Issue

```
../lib/libcaffe.so.1.0.0: undefined reference to `cv::imread(std::string const&, int)'
```

- Solve

- [.build_release/lib/libcaffe.so: undefined reference to `cv::imdecode](https://github.com/BVLC/caffe/issues/4621)

```
$ vim MakeFile, line 181
LIBRARIES += glog gflags protobuf leveldb snappy \
  lmdb boost_system boost_filesystem hdf5_hl hdf5 m \
  opencv_core opencv_highgui opencv_imgproc opencv_imgcodecs opencv_videoio

```


### install opencv2.4.13 with gcc6.3.1 & cmake3.9.2 in centos

```
-----opencv-----
$ cmake -DBUILD_TIFF=ON -DCMAKE_CXX_COMPILER=/opt/rh/devtoolset-6/root/usr/bin/g++ -DCMAKE_C_COMPILER=/opt/rh/devtoolset-6/root/usr/bin/gcc -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=/usr/local -DWITH_QT=OFF -DWITH_OPENGL=OFF -DWITH_TBB=OFF -DBUILD_NEW_PYTHON_SUPPORT=ON -DWITH_V4L=ON INSTALL_C_EXAMPLES=ON -DINSTALL_PYTHON_EXAMPLES=ON -DBUILD_EXAMPLES=ON -DWITH_XINE=ON -DINSTALL_TESTS=ON -DWITH_GSTREAMER=ON -DWITH_CUDA=OFF -DBUILD_EXAMPLES=ON -DWITH_FFMPEG=OFF -DENABLE_PRECOMPILED_HEADERS=OFF ..
$ make all -j8
$ make install
-----caffe-----
$ cmake -DBLAS=open .. -DCMAKE_CXX_COMPILER=/opt/rh/devtoolset-6/root/usr/bin/g++ -DCMAKE_C_COMPILER=/opt/rh/devtoolset-6/root/usr/bin/gcc
$ make clean
$ make all -j8
$ make install
$ make runtest
```
- Issue when make runtest
```
[----------] 6 tests from GPUMathFunctionsTest/1, where TypeParam = double
[ RUN      ] GPUMathFunctionsTest/1.TestSgnbit
F1224 11:12:55.641230 21568 syncedmem.hpp:22] Check failed: error == cudaSuccess (30 vs. 0)  unknown error
*** Check failure stack trace: ***
    @     0x7fdf63a20bba  google::LogMessage::Fail()
    @     0x7fdf63a20b16  google::LogMessage::SendToLog()
    @     0x7fdf63a204e3  google::LogMessage::Flush()
    @     0x7fdf63a23818  google::LogMessageFatal::~LogMessageFatal()
    @     0x7fdf64b3aa33  caffe::SyncedMemory::mutable_cpu_data()
    @     0x7fdf649c3435  caffe::Blob<>::Reshape()
    @     0x7fdf649c386a  caffe::Blob<>::Reshape()
    @           0xb49982  caffe::MathFunctionsTest<>::SetUp()
    @           0xdb4874  testing::internal::HandleExceptionsInMethodIfSupported<>()
    @           0xdad526  testing::Test::Run()
    @           0xdad6c8  testing::TestInfo::Run()
    @           0xdad7a5  testing::TestCase::Run()
    @           0xdaf637  testing::internal::UnitTestImpl::RunAllTests()
    @           0xdaf933  testing::UnitTest::Run()
    @           0x89ca5d  main
    @     0x7fdf55cbc3d5  __libc_start_main
    @           0x8a3a5f  (unknown)
/bin/sh: line 1: 21568 Aborted                 (core dumped) /home/mwopr/github/caffe/build/test/test.testbin --gtest_shuffle
src/caffe/test/CMakeFiles/runtest.dir/build.make:57: recipe for target 'src/caffe/test/CMakeFiles/runtest' failed
make[3]: *** [src/caffe/test/CMakeFiles/runtest] Error 134
CMakeFiles/Makefile2:394: recipe for target 'src/caffe/test/CMakeFiles/runtest.dir/all' failed
make[2]: *** [src/caffe/test/CMakeFiles/runtest.dir/all] Error 2
CMakeFiles/Makefile2:401: recipe for target 'src/caffe/test/CMakeFiles/runtest.dir/rule' failed
make[1]: *** [src/caffe/test/CMakeFiles/runtest.dir/rule] Error 2
Makefile:268: recipe for target 'runtest' failed
make: *** [runtest] Error 2
```

- Issue python2.7 with wrong gcc version
```
[mwopr@fc-sit3-smart-v-l-01:/home/mwopr]#python2.7
Python 2.7.5 (default, Oct 30 2018, 23:45:53) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-36)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import caffe
/usr/lib/python2.7/site-packages/dask/array/numpy_compat.py:14: RuntimeWarning: divide by zero encountered in divide
  not np.allclose(np.divide(1, .5, dtype='i8'), 2) or
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/mwopr/github/caffe/python/caffe/__init__.py", line 1, in <module>
    from .pycaffe import Net, SGDSolver, NesterovSolver, AdaGradSolver, RMSPropSolver, AdaDeltaSolver, AdamSolver, NCCL, Timer
  File "/home/mwopr/github/caffe/python/caffe/pycaffe.py", line 15, in <module>
    import caffe.io
  File "/home/mwopr/github/caffe/python/caffe/io.py", line 2, in <module>
    import skimage.io
  File "/usr/lib64/python2.7/site-packages/skimage/__init__.py", line 157, in <module>
    from .util.dtype import *
  File "/usr/lib64/python2.7/site-packages/skimage/util/__init__.py", line 6, in <module>
    from .apply_parallel import apply_parallel
  File "/usr/lib64/python2.7/site-packages/skimage/util/apply_parallel.py", line 8, in <module>
    import dask.array as da
  File "/usr/lib/python2.7/site-packages/dask/array/__init__.py", line 5, in <module>
    from .core import (Array, block, concatenate, stack, from_array, store,
  File "/usr/lib/python2.7/site-packages/dask/array/core.py", line 30, in <module>
    from . import chunk
  File "/usr/lib/python2.7/site-packages/dask/array/chunk.py", line 17, in <module>
    broadcast_to = npcompat.broadcast_to
AttributeError: 'module' object has no attribute 'broadcast_to'
```

- Solve
```
$ gcc -v，是指向6.3.1
$ g++ -v，是指向6.3.1
$ python2.7，是指向4.8.5


$ update-alternatives --install /usr/bin/g++ g++ /opt/rh/devtoolset-6/root/usr/bin/g++ 100
$ update-alternatives --install /usr/bin/gcc gcc /opt/rh/devtoolset-6/root/usr/bin/gcc 100

$ python2.7
$ import sys
$ print(sys.path)

$ yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel
$ yum install readline-devel sqlite-devel bzip2-devel.i686 openssl-devel.i686 gdbm-devel.i686 libdbi-devel.i686 ncurses-libs zlib-devel.i686
$ wget https://www.python.org/ftp/python/2.7.8/Python-2.7.8.tgz
$ tar xf Python-2.7.8.tgz
$ cd Python-2.7.8
$ ./configure --prefix=/usr/local CC=/usr/bin/gcc
$ ./configure --prefix=/usr/local CC=/opt/rh/devtoolset-6/root/usr/bin/gcc --enable-shared
$ ./configure --prefix=/usr/ CC=/opt/rh/devtoolset-6/root/usr/bin/gcc --enable-shared
$ make -j8 && make install
$ python setup.py install
$ ln -fs /usr/local/bin/python2.7  /usr/bin/python
$ ll -tr /usr/bin/python*
```
- [CentOS下编译安装Python2.7.6](http://blog.useasp.net/archive/2014/05/07/compile-and-install-python-2-dot-7-dot-6-on-centos.aspx)
```
Python build finished, but the necessary bits to build these modules were not found:
_bsddb             _tkinter           bsddb185        
dl                 imageop            sunaudiodev     
To find the necessary bits, look in setup.py in detect_modules() for the module's name.


Failed to build these modules:
_hashlib           _ssl               bz2             
dbm                gdbm 
```
```
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/readline-devel-6.2-10.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/openssl-devel-1.0.2k-16.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/gdbm-devel-1.10-8.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/libdbi-devel-0.8.4-6.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/ncurses-libs-5.9-14.20130511.el7_4.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/zlib-devel-1.2.7-18.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/krb5-devel-1.15.1-34.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/libkadm5-1.15.1-34.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/libpipeline-devel-1.2.3-3.el7.x86_64.rpm
$ rpm -ivh *

$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/readline-devel-6.2-10.el7.i686.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/openssl-devel-1.0.2k-16.el7.i686.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/gdbm-1.10-8.el7.i686.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/libdbi-devel-0.8.4-6.el7.i686.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/ncurses-libs-5.9-14.20130511.el7_4.i686.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/zlib-devel-1.2.7-18.el7.i686.rpm
$ rpm -ivh *

$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/openssl-1.0.2k-16.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/openssl-devel-1.0.2k-16.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/openssl-devel-1.0.2k-16.el7.i686.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/openssl-libs-1.0.2k-16.el7.i686.rpm 
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/openssl-libs-1.0.2k-16.el7.x86_64.rpm 
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/openssl-perl-1.0.2k-16.el7.x86_64.rpm 
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/openssl-static-1.0.2k-16.el7.i686.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/openssl-static-1.0.2k-16.el7.x86_64.rpm 
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/openssl098e-0.9.8e-29.el7.centos.3.i686.rpm 
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/openssl098e-0.9.8e-29.el7.centos.3.x86_64.rpm 
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/perl-5.16.3-293.el7.x86_64.rpm


```



- Issue 

```
[mwopr@fc-sit3-smart-v-l-01:/home/mwopr/github/caffe/build]#yum install openssl-devel
There was a problem importing one of the Python modules
required to run yum. The error leading to this problem was:

   No module named yum

Please install a package which provides this module, or
verify that the module is installed correctly.

It's possible that the above module doesn't match the
current version of Python, which is:
2.7.8 (default, Dec 24 2018, 14:45:13) 
[GCC 6.3.1 20170216 (Red Hat 6.3.1-3)]

```

- Solve


first of two way

```
$ rpm -qa |grep yum
$ rpm -e --nodeps yum yum-metadata-parser yum-plugin-fastestmirror
$ whereis python
$ which yum
$ vim /usr/bin/yum
$ vi /usr/bin/yum-updatest
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/yum-3.4.3-161.el7.centos.noarch.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/yum-metadata-parser-1.1.4-10.el7.x86_64.rpm 
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.31-50.el7.noarch.rpm 
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/python-iniparse-0.4-9.el7.noarch.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/python-2.7.5-76.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/rpm-python-4.11.3-35.el7.x86_64.rpm


$ rpm -ivh yum-*

$ cd /etc/yum.repos.d/
$ wget http://docs.linuxtone.org/soft/lemp/CentOS-Base.repo
```

second of two way

```
$ wget http://yum.baseurl.org/download/3.4/yum-3.4.3.tar.gz
$ tar xvf yum-3.4.3.tar.gz
$ cd yum-3.4.3
$ python yummain.py install yum
```

```
python yummain.py install yum  import rpm ImportError: No module named rpm
```



rpm -q centos-release

```

$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/python-libs-2.7.5-76.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/python-2.7.5-76.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/python-iniparse-0.4-9.el7.noarch.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/python-pycurl-7.19.0-19.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/rpm-python-4.11.3-35.el7.x86_64.rpm
$ rpm -ivh *

$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/rpm-4.11.3-35.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/rpm-build-libs-4.11.3-35.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/rpm-libs-4.11.3-35.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/rpm-build-4.11.3-35.el7.x86_64.rpm
$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/rpm-sign-4.11.3-35.el7.x86_64.rpm
$ rpm -Uvh *

$ wget http://mirrors.163.com/centos/7/os/x86_64/Packages/


$ wget https://www.python.org/ftp/python/2.7.5/Python-2.7.5.tar.xz

$ wget http://www.openssl.org/source/openssl-1.0.2f.tar.gz
$ tar -xzf openssl-1.0.2f.tar.gz
$ cd openssl-1.0.2f
$ mkdir /usr/local/openssl
$ ./config --prefix=/usr/local/openssl
$ make -j8
$ make install


$ yum -y install openssl-devel


```


- [centos---运行yum报错：No module named yum](https://blog.csdn.net/zzq900503/article/details/79551715)
