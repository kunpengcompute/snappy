# Release Notes

## Version Mapping

### Product Version Information

<a name="table62675726"></a>
<table><tbody><tr id="row41561572"><th class="firstcol" valign="top" width="42.17%" id="mcps1.1.3.1.1"><p id="p11044137"><a name="p11044137"></a><a name="p11044137"></a>Product Name</p>
</th>
<td class="cellrowborder" valign="top" width="57.830000000000005%" headers="mcps1.1.3.1.1 "><p id="p1597721693713"><a name="p1597721693713"></a><a name="p1597721693713"></a>Kunpeng BoostKit</p>
</td>
</tr>
<tr id="row24726251"><th class="firstcol" valign="top" width="42.17%" id="mcps1.1.3.2.1"><p id="p56669300"><a name="p56669300"></a><a name="p56669300"></a>Product Version</p>
</th>
<td class="cellrowborder" valign="top" width="57.830000000000005%" headers="mcps1.1.3.2.1 "><p id="p11923034"><a name="p11923034"></a><a name="p11923034"></a><span id="text189831542174711"><a name="text189831542174711"></a><a name="text189831542174711"></a>26.1.RC1</span></p>
</td>
</tr>
<tr id="row1930811171892"><th class="firstcol" valign="top" width="42.17%" id="mcps1.1.3.3.1"><p id="p2030912172097"><a name="p2030912172097"></a><a name="p2030912172097"></a>Software Name</p>
</th>
<td class="cellrowborder" valign="top" width="57.830000000000005%" headers="mcps1.1.3.3.1 "><p id="p1730912179911"><a name="p1730912179911"></a><a name="p1730912179911"></a><span id="text18970145618322"><a name="text18970145618322"></a><a name="text18970145618322"></a>Snappy optimization patch</span></p>
</td>
</tr>
<tr id="row1930811171892"><th class="firstcol" valign="top" width="42.17%" id="mcps1.1.3.3.1"><p id="p2030912172097"><a name="p2030912172097"></a><a name="p2030912172097"></a>Software Version</p>
</th>
<td class="cellrowborder" valign="top" width="57.830000000000005%" headers="mcps1.1.3.3.1 "><p id="p1730912179911"><a name="p1730912179911"></a><a name="p1730912179911"></a><span id="text18970145618322"><a name="text18970145618322"></a><a name="text18970145618322"></a>1.2.2-opt</span></p>
</td>
</tr>
</tbody>
</table>

### OS, Compiler, and CPU

|OS|CPU|Compiler|
|--|--|--|
|openEuler 24.03 |AArch64|GCC 10.3.1|
|openEuler 22.03 LTS SP3|AArch64|GCC 10.3.1|
|Debian 12|AArch64|GCC 10.3.1|

## Change Description

### V1.0.0

**New Features**

|Feature|Description|
|--|--|
|Snappy optimization patch repository|Based on the AArch64 architecture, performance optimizations are applied to the compression algorithm using the 1.2.2 branch at commit `6af9287` as the baseline.|

## Optimization Details

### Optimizations List

|No.|Commit|
|--|--|
|1|8b8896d|
|2|457e30c|
|3|59bb419|
|4|b815049|
|5|ee4b811|

### Test Environment

- **Hardware**: AArch64 server
- **OS**: openEuler 24.03
- **Compiler**: GCC 10.3.1
- **Test tool**: lzbench 1.8
- **Test parameters**: `-esnappy -b4 -t20u20` (4 KB block size, compress for 10 seconds, decompress for 1 second)

## Known Limitations

- This patch is primarily optimized for the AArch64 architecture; performance improvements on the x86 architecture may not be significant.
- Adjusting the hash table size may lead to a slight decrease in the compression ratio (within an acceptable range).
- An AArch64 processor that supports the NEON SIMD instruction set is required.

## Documentation

### V1.0.0 Documentation

<table>
<thead align="left">
<tr id="row28804032">
<th class="cellrowborder" valign="top" width="42.72%" id="mcps1.1.4.1.1"><p id="p4697041">Document Name</p></th>
<th class="cellrowborder" valign="top" width="40.32%" id="mcps1.1.4.1.2"><p id="p44916036">Description</p></th>
<th class="cellrowborder" valign="top" width="16.96%" id="mcps1.1.4.1.3"><p id="p14320308">Delivery Method</p></th>
</tr>
</thead>
<tbody>
<tr id="row19094280">
<td class="cellrowborder" valign="top" width="42.72%" headers="mcps1.1.4.1.1"><p id="p1341193722116">Release Notes</p></td>
<td class="cellrowborder" valign="top" width="40.32%" headers="mcps1.1.4.1.2"><p id="p2042183752117">Provides basic information and feature updates of Snappy optimized for the AArch64 architecture.</p></td>
<td class="cellrowborder" valign="top" width="16.96%" headers="mcps1.1.4.1.3"><p id="p13623131318111">Open-source repository</p></td>
</tr>
<tr id="row1941037152117">
<td class="cellrowborder" valign="top" width="42.72%" headers="mcps1.1.4.1.1"><p id="p5143115122016">Installation Guide</p></td>
<td class="cellrowborder" valign="top" width="40.32%" headers="mcps1.1.4.1.2"><p id="p1914345202019">Provides detailed instructions for compiling and installing Snappy optimized for the AArch64 architecture.</p></td>
<td class="cellrowborder" valign="top" width="16.96%" headers="mcps1.1.4.1.3"><p id="p8622131312113">Open-source repository</p></td>
</tr>
<tr id="row01685019218">
<td class="cellrowborder" valign="top" width="42.72%" headers="mcps1.1.4.1.1"><p id="p6168140142118">API Reference</p></td>
<td class="cellrowborder" valign="top" width="40.32%" headers="mcps1.1.4.1.2"><p id="p991893772013">Provides API descriptions and API calling examples for Snappy optimized for the AArch64 architecture.</p></td>
<td class="cellrowborder" valign="top" width="16.96%" headers="mcps1.1.4.1.3"><p id="p14138122613223">Open-source repository</p></td>
</tr>
<tr id="row01685019218">
<td class="cellrowborder" valign="top" width="42.72%" headers="mcps1.1.4.1.1"><p id="p6168140142118">Quick Start</p></td>
<td class="cellrowborder" valign="top" width="40.32%" headers="mcps1.1.4.1.2"><p id="p991893772013">Provides a quick start guide for installing and enabling Snappy optimized for the AArch64 architecture.</p></td>
<td class="cellrowborder" valign="top" width="16.96%" headers="mcps1.1.4.1.3"><p id="p14138122613223">Open-source repository</p></td>
</tr>
</tbody>
</table>

### Obtaining Documentation

Visit the [open-source repository](https://gitcode.com/boostkit/snappy) to view or download related documents.
