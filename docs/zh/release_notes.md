# 版本说明书

## 版本配套说明

### 产品版本信息

<a name="table62675726"></a>
<table><tbody><tr id="row41561572"><th class="firstcol" valign="top" width="42.17%" id="mcps1.1.3.1.1"><p id="p11044137"><a name="p11044137"></a><a name="p11044137"></a>产品名称</p>
</th>
<td class="cellrowborder" valign="top" width="57.830000000000005%" headers="mcps1.1.3.1.1 "><p id="p1597721693713"><a name="p1597721693713"></a><a name="p1597721693713"></a>Kunpeng BoostKit</p>
</td>
</tr>
<tr id="row24726251"><th class="firstcol" valign="top" width="42.17%" id="mcps1.1.3.2.1"><p id="p56669300"><a name="p56669300"></a><a name="p56669300"></a>产品版本</p>
</th>
<td class="cellrowborder" valign="top" width="57.830000000000005%" headers="mcps1.1.3.2.1 "><p id="p11923034"><a name="p11923034"></a><a name="p11923034"></a><span id="text189831542174711"><a name="text189831542174711"></a><a name="text189831542174711"></a>26.1.RC1</span></p>
</td>
</tr>
<tr id="row1930811171892"><th class="firstcol" valign="top" width="42.17%" id="mcps1.1.3.3.1"><p id="p2030912172097"><a name="p2030912172097"></a><a name="p2030912172097"></a>软件名称</p>
</th>
<td class="cellrowborder" valign="top" width="57.830000000000005%" headers="mcps1.1.3.3.1 "><p id="p1730912179911"><a name="p1730912179911"></a><a name="p1730912179911"></a><span id="text18970145618322"><a name="text18970145618322"></a><a name="text18970145618322"></a>Snappy优化补丁</span></p>
</td>
</tr>
<tr id="row1930811171892"><th class="firstcol" valign="top" width="42.17%" id="mcps1.1.3.3.1"><p id="p2030912172097"><a name="p2030912172097"></a><a name="p2030912172097"></a>软件版本</p>
</th>
<td class="cellrowborder" valign="top" width="57.830000000000005%" headers="mcps1.1.3.3.1 "><p id="p1730912179911"><a name="p1730912179911"></a><a name="p1730912179911"></a><span id="text18970145618322"><a name="text18970145618322"></a><a name="text18970145618322"></a>1.2.2-opt</span></p>
</td>
</tr>
</tbody>
</table>

### 与操作系统、编译器和CPU配套说明

|操作系统|CPU类型|编译器|
|--|--|--|
|openEuler 24.03 |ARM64 (aarch64)|GCC 10.3.1|
|openEuler 22.03 LTS SP3|ARM64|GCC 10.3.1|
|Debian 12|ARM64|GCC 10.3.1|

## 版本更新说明

### V1.0.0

**新增特性**

|特性描述|更新说明|
|--|--|
|新增Snappy优化补丁仓|基于ARM64架构，以1.2.2分支`6af9287` commit点为基线，进行压缩算法性能优化。|

## 优化内容详细说明

### 优化点列表

|序号|Commit|
|--|--|
|1|8b8896d|
|2|457e30c|
|3|59bb419|
|4|b815049|
|5|ee4b811|

### 测试环境

- **硬件环境**：ARM64服务器（aarch64）
- **操作系统**：openEuler 24.03 
- **编译器**：GCC 10.3.1
- **测试工具**：lzbench 1.8
- **测试参数**：`-esnappy -b4 -t20u20`（4KB块大小，压缩10秒，解压1秒）

## 已知限制

- 本补丁主要针对ARM64架构优化，在x86架构上性能提升可能不明显。
- 哈希表大小调整可能导致压缩率略有下降（在可接受范围内）。
- 需要支持NEON SIMD指令集的ARM64处理器。

## 版本配套文档

### 版本配套文档

<table>
<thead align="left">
<tr id="row28804032">
<th class="cellrowborder" valign="top" width="42.72%" id="mcps1.1.4.1.1"><p id="p4697041">文档名称</p></th>
<th class="cellrowborder" valign="top" width="40.32%" id="mcps1.1.4.1.2"><p id="p44916036">内容简介</p></th>
<th class="cellrowborder" valign="top" width="16.96%" id="mcps1.1.4.1.3"><p id="p14320308">交付形式</p></th>
</tr>
</thead>
<tbody>
<tr id="row19094280">
<td class="cellrowborder" valign="top" width="42.72%" headers="mcps1.1.4.1.1"><p id="p1341193722116">《版本说明书》</p></td>
<td class="cellrowborder" valign="top" width="40.32%" headers="mcps1.1.4.1.2"><p id="p2042183752117">提供针对ARM64架构优化的Snappy的发布版本的基础信息和特性更新信息。</p></td>
<td class="cellrowborder" valign="top" width="16.96%" headers="mcps1.1.4.1.3"><p id="p13623131318111">开源仓</p></td>
</tr>
<tr id="row1941037152117">
<td class="cellrowborder" valign="top" width="42.72%" headers="mcps1.1.4.1.1"><p id="p5143115122016">《安装指南》</p></td>
<td class="cellrowborder" valign="top" width="40.32%" headers="mcps1.1.4.1.2"><p id="p1914345202019">提供针对ARM64架构优化的Snappy的编译安装详细步骤指导。</p></td>
<td class="cellrowborder" valign="top" width="16.96%" headers="mcps1.1.4.1.3"><p id="p8622131312113">开源仓</p></td>
</tr>
<tr id="row01685019218">
<td class="cellrowborder" valign="top" width="42.72%" headers="mcps1.1.4.1.1"><p id="p6168140142118">《API参考》</p></td>
<td class="cellrowborder" valign="top" width="40.32%" headers="mcps1.1.4.1.2"><p id="p991893772013">提供针对ARM64架构优化的Snappy的接口说明、接口调用示例等。</p></td>
<td class="cellrowborder" valign="top" width="16.96%" headers="mcps1.1.4.1.3"><p id="p14138122613223">开源仓</p></td>
</tr>
<tr id="row01685019218">
<td class="cellrowborder" valign="top" width="42.72%" headers="mcps1.1.4.1.1"><p id="p6168140142118">《快速入门》</p></td>
<td class="cellrowborder" valign="top" width="40.32%" headers="mcps1.1.4.1.2"><p id="p991893772013">提供针对ARM64架构优化的Snappy的安装和使能快速入门指导。</p></td>
<td class="cellrowborder" valign="top" width="16.96%" headers="mcps1.1.4.1.3"><p id="p14138122613223">开源仓</p></td>
</tr>
</tbody>
</table>

### 获取文档的方法

您可以通过访问[开源仓](https://gitcode.com/boostkit/snappy)浏览和获取相关文档。
