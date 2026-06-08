# 快速入门

## 环境要求

- 已验证的硬件配置：ARM64处理器（aarch64），支持NEON SIMD指令集

- 已验证的OS：openEuler 24.03、openEuler 22.03、Debian 12

- 已验证的编译器：GCC 10.3.1

## 使能ARM64优化

针对ARM64处理器，本仓库提供了性能优化补丁 `snappy-1.2.2-opt.patch`，可进一步提升压缩、解压缩性能。该补丁主要针对ARM64处理器进行了以下优化：

- 哈希表大小优化，平衡压缩率和性能。
- 快速匹配阶段额外插入哈希表，提升匹配效率。
- 增加哈希匹配次数和跳过步长，优化查找逻辑。
- NEON SIMD指令优化，提升数据处理速度。
- 缓存预取优化，减少内存访问延迟。

可通过以下两种方式获取优化版Snappy源码。

### 方式一：直接获取优化版源码（dev-1.2.2分支）

`dev-1.2.2`分支已包含优化代码，无需手动应用补丁。

   ```bash
   git clone https://gitcode.com/boostkit/snappy.git -b dev-1.2.2
   cd snappy
   ```

### 方式二：拉取Snappy原版源码并应用补丁（master分支）

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

获取后的目录结构（dev-1.2.2分支）：

   ```text
   snappy/
   ├── build/                        # 编译输出目录
   │   ├── libsnappy.so              # 动态库
   │   ├── libsnappy.so.1            # 符号链接
   │   └── libsnappy.so.1.2.2        # 动态库版本文件
   ├── CMakeLists.txt                # CMake配置文件（已优化）
   ├── snappy.cc                     # 核心压缩算法（已优化）
   ├── snappy-sinksource.cc          # 数据源实现（已优化）
   ├── snappy-stubs-internal.cc      # 内部辅助函数
   ├── snappy-c.cc                   # C接口封装
   ├── snappy.h                      # 头文件
   ├── snappy-sinksource.h           # 数据源头文件
   ├── snappy-stubs-public.h         # 公共定义头文件
   ├── snappy-stubs-internal.h       # 内部定义头文件
   ├── snappy-c.h                    # C接口头文件
   ├── snappy-1.2.2-opt.patch        # 优化补丁文件
   ├── docs/                         # 文档目录
   │   ├── LICENSE                   # 文档许可证（CC-BY 4.0）
   │   └── zh/                       # 中文文档
   │       ├── api_reference.md      # API参考
   │       ├── installation_guide.md # 安装指南
   │       ├── quick_start.md        # 快速入门
   │       ├── release_notes.md      # 版本说明书
   │       └── menu_snappy.md        # 导航菜单
   └── README.md                     # 项目介绍
   ```

## 使用示例（使用lzbench进行性能测试）

Snappy是动态链接库，编译产物在`build`文件夹下。

1. 编译snappy优化版本。

   ```bash
   mkdir -p build && cd build
   cmake .. -DCMAKE_BUILD_TYPE=Release
   make -j
   cd ..
   ```

2. 使用lzbench进行性能测试。

   1. 设置动态库路径。

      ```bash
      export LD_LIBRARY_PATH=./build:$LD_LIBRARY_PATH
      ```

   2. 运行测试（4KB块大小，压缩20秒，解压20秒）。

      ```bash
      lzbench -esnappy -b4 -t20u20 silesia.tar
      ```

   测试结果示例：

   ```text
   Compressor name         Compress. Decompress. Compr. size  Ratio Filename
   snappy 2020-07-11         458 MB/s  1293 MB/s   123243361  58.14 silesia.tar
   ```

## 使用示例（包含头文件使能方法示例）

本节提供基本压缩、解压缩使用样例。

1. 编辑样例代码如下。

   ```cpp
   #include "snappy.h"
   #include <iostream>
   #include <string>

   int main() {
       std::string input = "This is a test string for snappy compression. "
                          "Snappy is a fast compression/decompression library.";
       
       std::string compressed;
       snappy::Compress(input.data(), input.size(), &compressed);
       
       std::string decompressed;
       snappy::Uncompress(compressed.data(), compressed.size(), &decompressed);
       
       std::cout << "Original size: " << input.size() << " bytes" << std::endl;
       std::cout << "Compressed size: " << compressed.size() << " bytes" << std::endl;
       std::cout << "Compression ratio: " 
                 << (100.0 * compressed.size() / input.size()) << "%" << std::endl;
       std::cout << "Decompressed matches: " 
                 << (input == decompressed ? "Yes" : "No") << std::endl;
       
       return 0;
   }
   ```

2. 执行编译命令。

   ```bash
   g++ -I./ -L./build -lsnappy -std=c++11 -O3 test_snappy.cpp -o test_snappy
   ```

3. 运行测试。

   ```bash
   export LD_LIBRARY_PATH=./build:$LD_LIBRARY_PATH
   ./test_snappy
   ```

   运行结果：

   ```text
   Original size: 97 bytes
   Compressed size: 82 bytes
   Compression ratio: 84.54%
   Decompressed matches: Yes
   ```

## 验证优化效果

可以使用lzbench工具对比原版和优化版本的性能。

1. 编译原版Snappy。

   ```bash
   git clone https://github.com/google/snappy.git snappy-original
   cd snappy-original
   mkdir -p build && cd build
   cmake .. -DCMAKE_BUILD_TYPE=Release
   make -j
   ```

2. 编译优化版Snappy。

   ```bash
   cd ../snappy
   mkdir -p build && cd build
   cmake .. -DCMAKE_BUILD_TYPE=Release
   make -j
   ```

3. 对比性能。

   - 测试原版

     ```bash
     export LD_LIBRARY_PATH=./snappy-original/build:$LD_LIBRARY_PATH
     lzbench -esnappy -b4 -t10u1 test_data.tar
     ```

   - 测试优化版

     ```bash
     export LD_LIBRARY_PATH=./snappy/build:$LD_LIBRARY_PATH
     lzbench -esnappy -b4 -t20u20 silesia.tar
     ```

## 常见问题

常见问题解答请参见《[安装指南](installation_guide.md#常见问题)》中的"常见问题"章节，包括确认优化版本、编译找不到snappy.h、运行时找不到libsnappy.so等问题的解决方法。
