# Installation Guide

This document describes how to compile and install the Snappy compression library optimized for AArch64 platforms.

## Environment Requirements

| Software| Version Requirement| Description|
| ------ | ---------- | ------ |
| OS| openEuler 22.03 LTS SP3 / 24.03 | Linux distribution|
| Compiler| GCC 10.3.1 or later| Supports the C++11 standard. AArch64 requires NEON support.|
| CMake | 3.10 or later| Build tool|
| Git | 2.0 or later| Version control|

## Compilation and Installation

### Obtaining Code

Run the following commands to obtain code:

```bash
git clone https://gitcode.com/boostkit/snappy.git -b dev-1.2.2
cd snappy
```

If the code is retrieved without using `git clone --recursive` to pull dependencies, you need to manually initialize the submodules.

- Method 1: Use Git submodules (recommended).

  ```bash
  cd snappy
  git submodule update --init --recursive
  ```

- Method 2: Manually download GoogleTest.

  ```bash
  cd snappy
  mkdir -p third_party/googletest
  cd third_party/googletest
  wget https://github.com/google/googletest/archive/refs/tags/v1.14.0.tar.gz
  tar -xzf v1.14.0.tar.gz --strip-components=1
  ```

> **NOTE:** The `dev-1.2.2` branch includes the AArch64 optimized code, so there is no need to apply the patch manually. The patch file `snappy-1.2.2-opt.patch` is provided solely for reference and comparison.

### Installation Procedure

1. Create a custom installation directory. You can change the directory as required.

   ```bash
   mkdir -p /path/to/install/snappy-bin
   export SNAPPY_INSTALL_DIR=/path/to/install/snappy-bin
   ```

2. Configure the build.

   ```bash
   mkdir build
   cd build
   ```

   * Optimized configuration (default: shared libraries ON, tests OFF, benchmarks OFF)

     ```bash
     cmake -DCMAKE_BUILD_TYPE=Release \
           -DCMAKE_CXX_FLAGS="-O3" \
           -DCMAKE_INSTALL_PREFIX=${SNAPPY_INSTALL_DIR} \
           ..
     ```

   * To build tests and benchmarks (for development and debugging scenarios)

     ```bash
     cmake -DBUILD_SHARED_LIBS=ON \
           -DSNAPPY_BUILD_TESTS=ON \
           -DSNAPPY_BUILD_BENCHMARKS=ON \
           -DCMAKE_BUILD_TYPE=Release \
           -DCMAKE_CXX_FLAGS="-O3" \
           -DCMAKE_INSTALL_PREFIX=${SNAPPY_INSTALL_DIR} \
           ..
     ```

3. Perform compilation.

   - Use all available CPU cores for parallel compilation.

     ```bash
     cmake --build . --parallel $(nproc)
     ```

   - Alternatively, specify the number of cores (for example, 32 cores).

     ```bash
     cmake --build . --parallel 32
     ```

4. Perform installation.

   ```bash
   cmake --install .
   ```

5. Verify the installation directory.

   ```bash
   ls -la ${SNAPPY_INSTALL_DIR}
   ```

   Command output:

   ```text
   total 20
   drwxr-xr-x.  4 user user  4096 Mar 20 12:19 .
   drwxr-xr-x. 33 user user  4096 Mar 20 16:39 ..
   drwxr-xr-x.  5 user user  4096 Mar 20 12:19 include
   drwxr-xr-x.  4 user user  4096 Mar 20 12:19 lib
   ```

6. Verify the dynamic libraries.

   ```bash
   ls -la ${SNAPPY_INSTALL_DIR}/lib*/libsnappy*
   ```

   Command output:

   ```text
   lrwxrwxrwx. 1 user user      22 Mar 20 12:19 /home/user/snappy-bin/lib/libsnappy.so -> libsnappy.so.1.2.2
   -rwxr-xr-x. 1 user user  265360 Mar 20 12:16 /home/user/snappy-bin/lib/libsnappy.so.1.2.2
   lrwxrwxrwx. 1 user user      22 Mar 20 12:19 /home/user/snappy-bin/lib/libsnappy.so.1 -> libsnappy.so.1.2.2
   ```

## Compilation Option Description

**Common CMake Options**

| Option| Default Value| Description|
| ------ | -------- | ------ |
| `-DBUILD_SHARED_LIBS` | ON | Specifies whether to build shared libraries.|
| `-DSNAPPY_BUILD_TESTS` | OFF | Specifies whether to build tests.|
| `-DSNAPPY_BUILD_BENCHMARKS` | OFF | Specifies whether to build benchmark tests.|
| `-DSNAPPY_FUZZING_BUILD` | OFF | Specifies whether to build for fuzz testing.|
| `-DCMAKE_BUILD_TYPE` | Release | Specifies the build type (Release or Debug).|
| `-DCMAKE_CXX_FLAGS` | - | Specifies additional compilation flags (`-O3` recommended).|

**Performance Optimization Compilation Flags**

Recommended compilation flags for the Arm architecture (such as Kunpeng processors).

```bash
cmake -DCMAKE_CXX_FLAGS="-O3 -march=armv8-a+simd+crc -mtune=native" ..

# (Optional) Advanced optimization example
cmake -DCMAKE_CXX_FLAGS="-O3 -march=armv8-a+simd+crc -flto=thin" \
      -DCMAKE_EXE_LINKER_FLAGS="-fuse-ld=lld" \
      -DCMAKE_SHARED_LINKER_FLAGS="-fuse-ld=lld" \
      ..
```

> **NOTE:** `-march=armv8-a+simd+crc` ensures that NEON SIMD and CRC instructions are available, which are critical dependencies for AArch64 optimizations.

## Running Test

If tests are enabled during compilation (`-DSNAPPY_BUILD_TESTS=ON`), they can be run in the following manner:

1. Go to the test directory.

   ```shell
   cd build
   ```

2. Run the unit tests.

   ```shell
   ctest --verbose
   ```

3. Run the benchmark tests (if built).

   ```shell
   ./snappy_benchmark
   ```

4. Verify the optimization effects using lzbench.

   ```bash
   export LD_LIBRARY_PATH=${SNAPPY_INSTALL_DIR}/lib:$LD_LIBRARY_PATH
   lzbench -esnappy -b4 -t20u20 silesia.tar
   ```

   Test result example:

   ```text
   Compressor name         Compress. Decompress. Compr. size  Ratio Filename
   snappy 2020-07-11         458 MB/s  1293 MB/s   123243361  58.14 silesia.tar
   ```

## FAQs

### Q1: How do I verify that I have obtained the optimized version?

**Q**: How do I verify that I have obtained the optimized version?

**A**: Run the following commands to confirm the branch and key optimizations:

```bash
cd snappy
git branch  # Should display * dev-1.2.2
grep -n "SNAPPY_PREFETCH" snappy.cc  # Should return matching lines, indicating that NEON optimizations are included.
```

### Q2: What should I do if `snappy.h` cannot be found during compilation?

**Q**: What should I do if `snappy.h` cannot be found during compilation?

**A**: Verify that the header file path is specified correctly.

```bash
g++ -I./ test.cpp -L./build -lsnappy -o test
```

### Q3: What should I do if `libsnappy.so` cannot be found at runtime?

**Q**: What should I do if `libsnappy.so` cannot be found at runtime?

**A**: Configure the dynamic library path.

```bash
export LD_LIBRARY_PATH=${SNAPPY_INSTALL_DIR}/lib:$LD_LIBRARY_PATH
# Or:
ldconfig  # If it has been installed to the system
```

### Q4: What should I do if NEON-related errors occur during compilation on an AArch64 platform?

**Q**: What should I do if NEON-related errors occur during compilation on an AArch64 platform?

**A**: Verify that the compiler supports the NEON instruction set.

```bash
gcc -dM -E - < /dev/null | grep NEON
```

The output should be: `#define __ARM_NEON 1`

If the output is empty, it indicates that the compiler or the target architecture does not support NEON. In this case, you must switch to an AArch64 toolchain that provides NEON support.
