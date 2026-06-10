# API参考

## 函数说明

Snappy已优化函数如[**表 1** Snappy已优化函数列表](#Snappy已优化函数列表)所示。

**表 1** Snappy已优化函数列表<a id="Snappy已优化函数列表"></a>

|名称|说明|
|--|--|
|Compress|将输入数据压缩为Snappy格式，支持任意大小的数据压缩。|
|Uncompress|将Snappy格式的压缩数据解压缩为原始数据。|
|CompressFromIOVec|从分散的IOVec结构压缩数据，返回压缩后数据长度。|
|RawUncompressToIOVec|将压缩数据解压缩到分散的IOVec结构中。|

## 函数定义

### Compress

**函数功能**

将输入数据压缩为Snappy格式。Snappy使用LZ77算法的变种，通过哈希表查找重复字符串，实现高速压缩。

**函数定义**

```cpp
size_t Compress(const char* input, size_t input_length, std::string* compressed);
```

**参数说明**

|参数名|描述|取值范围|输入/输出|
|--|--|--|--|
|input|指向待压缩原始数据的指针。|非空指针，指向至少input_length字节的有效数据|输入|
|input_length|输入数据的长度（字节数）。|大于0的整数|输入|
|compressed|指向std::string对象的指针，用于存储压缩后的数据。原始内容会被覆盖。|非空指针，指向一个有效的std::string实例|输出|

**返回值**

返回压缩后数据的长度（字节数）。压缩后的数据会写入compressed指向的字符串中。

> **说明：**
> 压缩后的数据大小通常比原始数据小，但如果数据已经高度压缩或随机性强，压缩后可能会更大。Snappy设计目标是速度而非压缩率。

**示例**

```cpp
#include <iostream>
#include <string>
#include "snappy.h"

int main() {
    std::string input = "This is a test string that will be compressed. "
                       "Repeated data: test test test test test";
    
    std::string compressed;
    snappy::Compress(input.data(), input.size(), &compressed);
    
    std::cout << "Original size: " << input.size() << " bytes" << std::endl;
    std::cout << "Compressed size: " << compressed.size() << " bytes" << std::endl;
    std::cout << "Compression ratio: " 
              << (100.0 * compressed.size() / input.size()) << "%" << std::endl;
    
    return 0;
}
```

运行结果：

```text
Original size: 86 bytes
Compressed size: 85 bytes
Compression ratio: 98.84%
```

### Uncompress

**函数功能**

将Snappy格式的压缩数据解压缩为原始数据。

**函数定义**

```cpp
bool Uncompress(const char* compressed, size_t compressed_length, std::string* uncompressed);
```

**参数说明**

|参数名|描述|取值范围|输入/输出|
|--|--|--|--|
|compressed|指向压缩数据的指针。|非空指针，指向有效的snappy压缩数据|输入|
|compressed_length|压缩数据的长度。|大于0的整数|输入|
|uncompressed|指向std::string对象的指针，用于存储解压后的原始数据。|非空指针，指向一个有效的std::string实例|输出|

**返回值**

- 成功：返回true，解压成功，*uncompressed中包含原始数据
- 失败：返回false（例如数据损坏、格式错误、长度不匹配或缓冲区不足）。此时uncompressed指向的字符串内容未定义（可能为空或部分数据，不建议依赖）。

> **说明：**
> 解压缩时需要确保压缩数据是完整的Snappy格式数据。如果数据损坏或格式错误，函数会返回false。

**示例**

```cpp
#include <iostream>
#include <string>
#include "snappy.h"

int main() {
    std::string original = "This is test data for compression and decompression.";
    
    // 压缩
    std::string compressed;
    snappy::Compress(original.data(), original.size(), &compressed);
    
    // 解压缩
    std::string decompressed;
    bool success = snappy::Uncompress(compressed.data(), compressed.size(), &decompressed);
    
    if (success) {
        std::cout << "Decompression successful!" << std::endl;
        std::cout << "Original matches: " 
                  << (original == decompressed ? "Yes" : "No") << std::endl;
    } else {
        std::cout << "Decompression failed!" << std::endl;
    }
    
    return 0;
}
```

运行结果：

```text
Decompression successful!
Original matches: Yes
```

### CompressFromIOVec

**函数功能**

从分散的IOVec结构压缩数据，适用于分散的数据源。

**函数定义**

```cpp
size_t CompressFromIOVec(const struct iovec* iov, size_t iov_cnt,
                         std::string* compressed);
```

**参数说明**

|参数名|描述|取值范围|输入/输出|
|--|--|--|--|
|iov|指向iovec结构体数组的指针，每个元素描述一个数据缓冲区的起始地址和长度。|有效的iovec数组指针|输入|
|iov_cnt|IOVec数组元素数量。|大于0的整数|输入|
|compressed|指向std::string对象的指针，用于存储压缩后的数据。|非空指针，指向一个有效的std::string实例|输出|

**返回值**

返回压缩后数据的长度。

### RawUncompressToIOVec

**函数功能**

将压缩数据解压缩到分散的IOVec结构中，适用于避免数据拷贝的零拷贝场景。

**函数定义**

```cpp
bool RawUncompressToIOVec(const char* compressed, size_t compressed_length,
                           const struct iovec* iov, size_t iov_cnt);
```

**参数说明**

|参数名|描述|取值范围|输入/输出|
|--|--|--|--|
|compressed|指向压缩数据的指针。|非空指针，指向有效的Snappy压缩数据|输入|
|compressed_length|压缩数据的长度。|大于0的整数|输入|
|iov|指向iovec结构体数组的指针，每个元素描述一个输出缓冲区的起始地址和长度。其累积大小必须至少等于解压缩后的数据长度。|有效的iovec数组指针|输出|
|iov_cnt|IOVec数组元素数量。|大于0的整数|输入|

**返回值**

- 成功：返回true
- 失败：返回false

## C接口

Snappy也提供C语言接口，用于非C++环境。

### snappy_compress

```c
snappy_status snappy_compress(const char* input, size_t input_length,
                              char* compressed, size_t* compressed_length);
```

### snappy_uncompress

```c
snappy_status snappy_uncompress(const char* compressed, size_t compressed_length,
                                char* uncompressed, size_t* uncompressed_length);
```

### snappy_max_compressed_length

```c
size_t snappy_max_compressed_length(size_t source_length);
```

返回给定源数据长度下，压缩后数据的最大可能长度。
