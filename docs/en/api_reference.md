# API Reference

## Functions

[**Table 1** Optimized Snappy functions](#optimized-snappy-functions) lists the optimized functions of Snappy.

**Table 1** Optimized Snappy functions<a id="optimized-snappy-functions"></a>

|Name|Description|
|--|--|
|Compress|Compresses input data into the Snappy format, supporting data compression of any size.|
|Uncompress|Decompresses Snappy-formatted compressed data into the original data.|
|CompressFromIOVec|Compresses data from a scattered IOVec structure and returns the compressed data length.|
|RawUncompressToIOVec|Decompresses compressed data into a scattered IOVec structure.|

## Function Definitions

### Compress

**Function Usage**

Compresses input data into the Snappy format. Snappy utilizes a variant of the LZ77 algorithm, using a hash table to look up duplicate strings to achieve high-speed compression.

**Function Syntax**

```cpp
size_t Compress(const char* input, size_t input_length, std::string* compressed);
```

**Parameters**

|Parameter|Description|Value Range|Input/Output|
|--|--|--|--|
|input|Pointer to the original data to be compressed.|A non-null pointer pointing to valid data of at least `input_length` bytes.|Input|
|input_length|Length of the input data, in bytes.|An integer greater than 0.|Input|
|compressed|Pointer to a `std::string` object used to store the compressed data. The original content will be overwritten.|A non-null pointer pointing to a valid `std::string` instance.|Output|

**Return Value**

Returns the length of the compressed data, in bytes. The compressed data is written into the string pointed to by `compressed`.

> **NOTE:**
> The size of the compressed data is typically smaller than that of the original data. However, if the input data is already highly compressed or exhibits high randomness, the compressed data size may exceed the original size. Snappy is designed for fast speeds rather than high compression ratios.

**Example**

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

Output:

```text
Original size: 86 bytes
Compressed size: 85 bytes
Compression ratio: 98.84%
```

### Uncompress

**Function Usage**

Decompresses Snappy-formatted compressed data into the original data.

**Function Syntax**

```cpp
bool Uncompress(const char* compressed, size_t compressed_length, std::string* uncompressed);
```

**Parameters**

|Parameter|Description|Value Range|Input/Output|
|--|--|--|--|
|compressed|Pointer to the compressed data.|A non-null pointer pointing to valid Snappy-compressed data.|Input|
|compressed_length|Length of the compressed data.|An integer greater than 0.|Input|
|uncompressed|Pointer to a `std::string` object used to store the decompressed original data.|A non-null pointer pointing to a valid `std::string` instance.|Output|

**Return Value**

- Success: Returns `true`. The decompression succeeded, and `*uncompressed` contains the original data.
- Failure: Returns `false` (e.g., due to data corruption, format errors, length mismatches, or insufficient buffer space). In this case, the content of the string pointed to by `uncompressed` is undefined (it may be empty or contain partial data, and should not be relied upon).

> **NOTE:**
> During decompression, you must ensure that the compressed data is complete Snappy-formatted data. If the data is corrupted or malformed, the function will return `false`.

**Example**

```cpp
#include <iostream>
#include <string>
#include "snappy.h"

int main() {
    std::string original = "This is test data for compression and decompression.";
    
    // Compression
    std::string compressed;
    snappy::Compress(original.data(), original.size(), &compressed);
    
    // Decompression
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

Output:

```text
Decompression successful!
Original matches: Yes
```

### CompressFromIOVec

**Function Usage**

Compresses data from a scattered IOVec structure, making it ideal for non-contiguous data sources.

**Function Syntax**

```cpp
size_t CompressFromIOVec(const struct iovec* iov, size_t iov_cnt,
                         std::string* compressed);
```

**Parameters**

|Parameter|Description|Value Range|Input/Output|
|--|--|--|--|
|iov|Pointer to an `iovec` array. Each element defines the start address and length of a data buffer.|A pointer to a valid `iovec` array.|Input|
|iov_cnt|Number of elements in the `iovec` array.|An integer greater than 0.|Input|
|compressed|Pointer to a `std::string` object used to store the compressed data.|A non-null pointer pointing to a valid `std::string` instance.|Output|

**Return Value**

Returns the length of the compressed data.

### RawUncompressToIOVec

**Function Usage**

Decompresses compressed data into a scattered IOVec structure, which is ideal for zero-copy scenarios to avoid data copy overhead.

**Function Syntax**

```cpp
bool RawUncompressToIOVec(const char* compressed, size_t compressed_length,
                           const struct iovec* iov, size_t iov_cnt);
```

**Parameters**

|Parameter|Description|Value Range|Input/Output|
|--|--|--|--|
|compressed|Pointer to the compressed data.|A non-null pointer pointing to valid Snappy-compressed data.|Input|
|compressed_length|Length of the compressed data.|An integer greater than 0.|Input|
|iov|Pointer to an `iovec` array. Each element defines the start address and length of an output buffer. The cumulative size must be at least equal to the length of the decompressed data.|A pointer to a valid `iovec` array.|Output|
|iov_cnt|Number of elements in the `iovec` array.|An integer greater than 0.|Input|

**Return Value**

- Success: Returns `true`.
- Failure: Returns `false`.

## C APIs

Snappy also provides C APIs for non-C++ environments.

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

Returns the maximum possible length of the compressed data for a given source data length.
