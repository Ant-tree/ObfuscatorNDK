# ObfuscatorNDK
Android Obfuscator NDK 23 for Windows

> Fully tested 

 * Origin NDK : 23c (23.2.8568313)
 * Clang : 12.0.9
 * LLVM : 12.0.9

## 1. Usage

Add CMAKE_C_FLAGS in CmakeLists.txt file

This will apply the obfuscation, module by module.

```shell
set(CMAKE_C_FLAGS "-mllvm -fla -mllvm -bcf -mllvm -sub -mllvm -sobf")
```

Or, in case of using ```Android.mk``` :

```bash
LOCAL_CFLAGS := -Wall -D__MODULE__=\"$(LOCAL_MODULE)\" -fcommon -mllvm -fla -mllvm -bcf -mllvm -sub -mllvm -sobf
```



Currently available options

| Option           | Description  |
| ---------------- | ------------ |
| **-mllvm -sub**  | substitution |
| **-mllvm -fla**  | flattening   |
| **-mllvm -bcf**  | bogus        |
| **-mllvm -sobf** | string obf   |

## 2. How to build this NDK?

| Env      | Desc              |
| -------- | ----------------- |
| Built on | Ubuntu 16.04 (VM) |
| Host     | windows           |

Avoid using ubuntu 20.04 -> might have sym-link problem



1. Check for exact NDK version and branch

> Check AndroidVersion.txt from```{NDK_PATH}}\toolchains\llvm\prebuilt\windows-x86_64```  

```
12.0.9
based on r416183c2
```

2. Download repo bin for clone from google source repository

```shell
curl https://storage.googleapis.com/git-repo-downloads/repo > /usr/bin/repo
chmod a+x /usr/bin/repo
```

 Upgrade to python 3.9 if lower

> ```shell
> ##### Download required packages
> sudo apt-get install build-essential checkinstall
> sudo apt-get install libreadline-gplv2-dev libncursesw5-dev libssl-dev \
>     libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev libffi-dev zlib1g-dev
> 
> ##### Download 
> cd /opt
> sudo wget https://www.python.org/ftp/python/3.9.1/Python-3.9.1.tgz
> sudo tar xzf Python-3.9.1.tgz
> cd Python-3.9.1
> sudo ./configure --enable-optimizations
> sudo make altinstall
> sudo update-alternatives --install /usr/bin/python python /usr/local/bin/python3.9 1
> ```

3. Download essential packages to build ndk toolchain

```shell
##### CMAKE and bison for toolchain build
apt install cmake bison
```

4. Check for manifest file in original ndk

```shell
##### Search for "manifest" in original ndk directory
-> Result : manifest_8481493.xml
```

5. Clone the repo

```shell
mkdir llvm-toolchain && cd llvm-toolchain
repo init -u https://android.googlesource.com/platform/manifest -b llvm-toolchain

## >>> [COPY {NDK_PATH}/~~/manifest_8181493.xml to llvm-toolchain/.repo/manifests]

repo init -m manifest_8481493.xml
repo sync -c
```

6. Build with ```--no-build linux``` option

```shell
python toolchain/llvm_android/build.py --no-build linux
```

7. Cherry pick the source from ollvm 12.x

```shell
# Changing Targets :
cp -r {TARGET}/llvm/lib/Transforms/Obfuscation {SOURCE}/llvm/lib/Transforms/Obfuscation
cp -r {TARGET}/llvm/include/Transforms/Obfuscation {SOURCE}/llvm/include/Transforms/Obfuscation

# add Obfuscation to 
-> lib/Transforms/IPO/CMakeList.txt
-> lib/Transforms/CMakeList.txt

#Cherry pick the sources from lib/Transforms/IPO/PassManagerBuilder.cpp
```

8. Build again

```shell
python toolchain/llvm_android/build.py --no-build linux
```



## 3. Handle the built output

```out/install/windows-x86/clang-dev``` from result corresponds to ```toolchains/llvm/prebuilt/windows-x86-64```

so, copy and paste the output(clang-dev) to original ndk directory

don't forget to manually substitute symbolic-linked binaries in ```bin/```

(usually 1KB files in output are symbolic linked, except lldb.cmd)



## 4. Known issues when apply

NONE



---



Android NDK
===========

Documentation
-------------

NDK documentation, guides, and API reference are available on
[our website](https://developer.android.com/ndk/index.html).

NDK code samples are available on
[GitHub](https://github.com/googlesamples/android-ndk).

Information about Android Studio can be found on [the Android Studio
website](https://developer.android.com/studio/index.html).

Filing Bugs
-----------

NDK bugs should be filed on
[GitHub](https://github.com/android-ndk/ndk/issues/new).

Android Studio and Gradle bugs should be filed in the [Android Studio Bug
Tracker](http://b.android.com). For the fastest response, make sure you follow
their guide on [Filing Bugs](http://tools.android.com/filing-bugs).
