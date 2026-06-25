# Quick Start

## Environment Requirements

- Verified hardware configuration: AArch64 processor with support for the NEON SIMD instruction set

- Verified OSs: openEuler 24.03, openEuler 22.03 LTS SP3, and Debian 12

- Verified compiler: GCC 10.3.1

## Enabling AArch64 Optimization

For AArch64 processors, this repository provides a performance optimization patch `snappy-1.2.2-opt.patch`, which further enhances compression and decompression performance. The patch implements the following optimizations for the AArch64 architecture:

- Optimizes the size of the hash table to achieve an ideal balance between compression ratio and performance.
- Inserts additional entries into the hash table during the fast match stage to improve efficiency.
- Increases the number of hash match attempts and implements adaptive skipping steps to optimize the lookup logic.
- Utilizes NEON SIMD instructions to boost data processing speeds.
- Optimizes cache prefetch to reduce memory access latency.

Obtain the Snappy source code (the `dev-1.2.2` branch contains the optimized code).

   ```bash
   git clone https://gitcode.com/boostkit/snappy.git -b dev-1.2.2
   cd snappy
   ```

The directory structure is as follows:

   ```text
   snappy/
   ├── build/                        # Build output directory
   │   ├── libsnappy.so              # Dynamic library
   │   ├── libsnappy.so.1            # Symbolic link
   │   └── libsnappy.so.1.2.2        # Dynamic library version file
   ├── CMakeLists.txt                # CMake configuration file (optimized)
   ├── snappy.cc                     # Core compression algorithm implementation (optimized)
   ├── snappy-sinksource.cc          # Data source and sink implementation (optimized)
   ├── snappy-stubs-internal.cc      # Internal helper functions
   ├── snappy-c.cc                   # C API wrapper implementation
   ├── snappy.h                      # Header file
   ├── snappy-sinksource.h           # Data source and sink header file
   ├── snappy-stubs-public.h         # Public definitions header file
   ├── snappy-stubs-internal.h       # Internal definitions header file
   ├── snappy-c.h                    # C API header file
   ├── snappy-1.2.2-opt.patch        # Optimization patch file
   ├── docs/                         # Documentation directory
   │   ├── LICENSE                   # Documentation license (CC-BY 4.0)
   │   └── en/                       # English documentation
   │       ├── api_reference.md      # API reference
   │       ├── installation_guide.md # Installation guide
   │       ├── quick_start.md        # Quick start guide
   │       ├── release_notes.md      # Release notes
   │       └── menu_snappy.md        # Navigation menu
   └── README.md                     # Project introduction
   ```

## Usage Example (Performance Benchmarking with lzbench)

Snappy is a dynamic link library, and its build outputs are located in the `build` directory.

1. Compile the optimized Snappy version.

   ```bash
   mkdir -p build && cd build
   cmake .. -DCMAKE_BUILD_TYPE=Release
   make -j
   cd ..
   ```

2. Perform benchmarking using lzbench.

   1. Set the dynamic library path.

      ```bash
      export LD_LIBRARY_PATH=./build:$LD_LIBRARY_PATH
      ```

   2. Run the benchmark (4 KB block size, compress for 20 seconds, decompress for 1 second).

      ```bash
      lzbench -esnappy -b4 -t20u20 silesia.tar
      ```

   Test result example:

   ```text
   Compressor name         Compress. Decompress. Compr. size  Ratio Filename
   snappy 2020-07-11         458 MB/s  1293 MB/s   123243361  58.14 silesia.tar
   ```

## Usage Example (Including Header File Enablement)

This section provides basic implementation examples for compression and decompression.

1. Edit the sample code as follows:

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

2. Run the compilation command.

   ```bash
   g++ -I./ -L./build -lsnappy -std=c++11 -O3 test_snappy.cpp -o test_snappy
   ```

3. Run the test.

   ```bash
   export LD_LIBRARY_PATH=./build:$LD_LIBRARY_PATH
   ./test_snappy
   ```

   Test results:

   ```text
   Original size: 97 bytes
   Compressed size: 82 bytes
   Compression ratio: 84.54%
   Decompressed matches: Yes
   ```

## Verifying Optimization Effects

You can use the lzbench tool to compare the performance between the original and the optimized versions.

1. Compile the original Snappy.

   ```bash
   git clone https://github.com/google/snappy.git snappy-original
   cd snappy-original
   mkdir -p build && cd build
   cmake .. -DCMAKE_BUILD_TYPE=Release
   make -j
   ```

2. Compile the optimized Snappy.

   ```bash
   cd ../snappy
   mkdir -p build && cd build
   cmake .. -DCMAKE_BUILD_TYPE=Release
   make -j
   ```

3. Compare the performance.

   - Benchmark the original version.

     ```bash
     export LD_LIBRARY_PATH=./snappy-original/build:$LD_LIBRARY_PATH
     lzbench -esnappy -b4 -t3u3 silesia.tar
     ```

   - Benchmark the optimized version.

     ```bash
     export LD_LIBRARY_PATH=./snappy/build:$LD_LIBRARY_PATH
     lzbench -esnappy -b4 -t3u3 silesia.tar
     ```

## FAQs

For frequently asked questions, please refer to the "FAQs" section in [Installation Guide](installation_guide.md#faqs). This section provides troubleshooting methods for common issues, including verifying the optimized version, compilation errors due to missing `snappy.h`, and runtime errors due to missing `libsnappy.so`.
