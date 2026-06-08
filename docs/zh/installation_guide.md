# 安装指南

本文档提供基于ARM64优化的Snappy压缩库的详细编译安装步骤。

## 环境要求

| 软件 | 版本要求 | 说明 |
| ------ | ---------- | ------ |
| 操作系统 | openEuler 24.03 / openEuler 22.03 / Debian 12 | Linux发行版 |
| 编译器 | GCC 10.3.1版本及以上 | 支持C++11标准，ARM64需支持NEON |
| CMake | 3.10版本及以上 | 构建工具 |
| Git | 2.0版本及以上 | 版本控制 |

## 编译安装

### 获取代码

可通过以下两种方式获取优化版Snappy源码。

#### 方式一：直接获取优化版源码（dev-1.2.2分支）

`dev-1.2.2`分支已包含优化代码，无需手动应用补丁。

```bash
git clone https://gitcode.com/boostkit/snappy.git -b dev-1.2.2
cd snappy
```

#### 方式二：拉取Snappy原版源码并应用补丁（master分支）

`master`分支仅包含文档和补丁文件，需先拉取Snappy原版源码，再应用补丁。

```bash
# 1. 拉取Snappy原版源码（1.2.2分支）
git clone https://github.com/google/snappy.git
cd snappy
git checkout -b dev-1.2.2

# 2. 获取优化补丁
git clone https://gitcode.com/boostkit/snappy.git snappy-patch -b master

# 3. 应用补丁
git apply snappy-patch/snappy-1.2.2-opt.patch
```

> **说明：** `dev-1.2.2`分支已包含ARM64优化代码，推荐使用方式一。补丁文件 `snappy-1.2.2-opt.patch` 仅供参考对比。

无论使用哪种方式，如果获取代码时未使用`git clone --recursive`拉取依赖，则需要手动初始化子模块。

- 方法一：使用git子模块（推荐）

  ```bash
  cd snappy
  git submodule update --init --recursive
  ```

- 方法二：手动下载GoogleTest

  ```bash
  cd snappy
  mkdir -p third_party/googletest
  cd third_party/googletest
  wget https://github.com/google/googletest/archive/refs/tags/v1.14.0.tar.gz
  tar -xzf v1.14.0.tar.gz --strip-components=1
  ```

> **说明：** `dev-1.2.2`分支已包含ARM64优化代码，无需手动应用补丁。补丁文件 `snappy-1.2.2-opt.patch` 仅供参考对比。

### 安装步骤

1. 创建自定义安装目录（可根据实际需求修改路径）。

   ```bash
   mkdir -p /path/to/install/snappy-bin
   export SNAPPY_INSTALL_DIR=/path/to/install/snappy-bin
   ```

2. 配置构建。

   ```bash
   mkdir build
   cd build
   ```

   * 优化版配置（默认：共享库ON，测试OFF，基准OFF）

     ```bash
     cmake -DCMAKE_BUILD_TYPE=Release \
           -DCMAKE_CXX_FLAGS="-O3" \
           -DCMAKE_INSTALL_PREFIX=${SNAPPY_INSTALL_DIR} \
           ..
     ```

   * 如果需要编译测试和基准（开发调试场景）

     ```bash
     cmake -DBUILD_SHARED_LIBS=ON \
           -DSNAPPY_BUILD_TESTS=ON \
           -DSNAPPY_BUILD_BENCHMARKS=ON \
           -DCMAKE_BUILD_TYPE=Release \
           -DCMAKE_CXX_FLAGS="-O3" \
           -DCMAKE_INSTALL_PREFIX=${SNAPPY_INSTALL_DIR} \
           ..
     ```

3. 编译。

   - 使用所有可用CPU核心并行编译。

     ```bash
     cmake --build . --parallel $(nproc)
     ```

   - 或指定核心数（例如32核）。

     ```bash
     cmake --build . --parallel 32
     ```

4. 安装。

   ```bash
   cmake --install .
   ```

5. 验证安装目录。

   ```bash
   ls -la ${SNAPPY_INSTALL_DIR}
   ```

   回显结果如下：

   ```text
   total 20
   drwxr-xr-x.  4 user user  4096 Mar 20 12:19 .
   drwxr-xr-x. 33 user user  4096 Mar 20 16:39 ..
   drwxr-xr-x.  5 user user  4096 Mar 20 12:19 include
   drwxr-xr-x.  4 user user  4096 Mar 20 12:19 lib
   ```

6. 查看动态库。

   ```bash
   ls -la ${SNAPPY_INSTALL_DIR}/lib*/libsnappy*
   ```

   回显结果如下：

   ```text
   lrwxrwxrwx. 1 user user      22 Mar 20 12:19 /home/user/snappy-bin/lib/libsnappy.so -> libsnappy.so.1.2.2
   -rwxr-xr-x. 1 user user  265360 Mar 20 12:16 /home/user/snappy-bin/lib/libsnappy.so.1.2.2
   lrwxrwxrwx. 1 user user      22 Mar 20 12:19 /home/user/snappy-bin/lib/libsnappy.so.1 -> libsnappy.so.1.2.2
   ```

## 编译选项说明

**CMake常用选项**

| 选项 | 默认值 | 说明 |
| ------ | -------- | ------ |
| `-DBUILD_SHARED_LIBS` | ON | 是否构建共享库 |
| `-DSNAPPY_BUILD_TESTS` | OFF | 是否编译测试 |
| `-DSNAPPY_BUILD_BENCHMARKS` | OFF | 是否编译基准测试 |
| `-DSNAPPY_FUZZING_BUILD` | OFF | 是否为模糊测试构建 |
| `-DCMAKE_BUILD_TYPE` | Release | 构建类型（Release/Debug） |
| `-DCMAKE_CXX_FLAGS` | - | 额外编译标志（推荐-O3） |

**性能优化编译标志**

针对ARM架构（鲲鹏等处理器）的推荐编译标志。

```bash
cmake -DCMAKE_CXX_FLAGS="-O3 -march=armv8-a+simd+crc -mtune=native" ..

# 进阶优化示例（可选）
cmake -DCMAKE_CXX_FLAGS="-O3 -march=armv8-a+simd+crc -flto=thin" \
      -DCMAKE_EXE_LINKER_FLAGS="-fuse-ld=lld" \
      -DCMAKE_SHARED_LINKER_FLAGS="-fuse-ld=lld" \
      ..
```

> **说明：** `-march=armv8-a+simd+crc`确保NEON SIMD和CRC指令可用，是ARM64优化的关键依赖。

## 运行测试

如果编译时开启了测试（`-DSNAPPY_BUILD_TESTS=ON`），可按以下方式运行。

1. 进入测试目录。

   ```shell
   cd build
   ```

2. 运行单元测试。

   ```shell
   ctest --verbose
   ```

3. 运行基准测试（如果已编译）。

   ```shell
   ./snappy_benchmark
   ```

4. 使用lzbench验证优化效果。

   ```bash
   export LD_LIBRARY_PATH=${SNAPPY_INSTALL_DIR}/lib:$LD_LIBRARY_PATH
   lzbench -esnappy -b4 -t20u20 silesia.tar
   ```

   测试结果示例：

   ```text
   Compressor name         Compress. Decompress. Compr. size  Ratio Filename
   snappy 2020-07-11         458 MB/s  1293 MB/s   123243361  58.14 silesia.tar
   ```

## 常见问题

### 问题一：如何确认获取的是优化版本？

**Q**：如何确认获取的是优化版本？

**A**：使用以下命令确认分支和关键优化内容。

```bash
cd snappy
git branch  # 应显示 * dev-1.2.2
grep -n "SNAPPY_PREFETCH" snappy.cc  # 应有匹配结果，表示NEON优化已包含
```

### 问题二：编译时找不到snappy.h怎么办？

**Q**：编译时找不到snappy.h怎么办？

**A**: 确保头文件路径正确。

```bash
g++ -I./ test.cpp -L./build -lsnappy -o test
```

### 问题三：运行时找不到libsnappy.so怎么办？

**Q**：运行时找不到libsnappy.so怎么办？

**A**: 设置动态库路径。

```bash
export LD_LIBRARY_PATH=${SNAPPY_INSTALL_DIR}/lib:$LD_LIBRARY_PATH
# 或者
ldconfig  # 如果已安装到系统
```

### 问题四：ARM64平台编译报NEON相关错误怎么办？

**Q**：ARM64平台编译报NEON相关错误怎么办？

**A**: 确认编译器支持NEON指令集。

```bash
gcc -dM -E - < /dev/null | grep NEON
```

应输出: `#define __ARM_NEON 1`。

如果输出为空，说明编译器或目标架构不支持NEON，需要使用支持NEON的ARM64工具链。
