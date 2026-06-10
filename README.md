# Snappy介绍

## 最新消息

- [2026.06.30]：基于Snappy 1.2.2分支针对ARM64架构发布优化版本v1.0.0，支持ARM64架构的性能优化。

## 项目介绍

Snappy是由Google研发的一款高速压缩/解压缩库，设计目标是提供极高的压缩和解压速度，而非追求高压缩率。

本项目针对Snappy压缩库在ARM64架构上进行性能优化，利用哈希表优化、跳过步长优化等技术提升压缩算法性能，函数接口与开源Snappy保持一致。

## 目录结构

项目全量目录层级介绍如下：

```text
snappy/
├── docs/                           # 文档目录
│   ├── LICENSE                     # 文档许可证（CC-BY 4.0）
│   └── zh/                         # 中文文档
│       ├── api_reference.md        # API参考
│       ├── installation_guide.md   # 安装指南
│       ├── quick_start.md          # 快速入门文档
│       ├── release_notes.md        # 版本说明书
│       └── menu_snappy.md          # 导航菜单
├── snappy-1.2.2-opt.patch          # ARM64架构优化补丁
├── LICENSE                         # 项目许可证（BSD-3-Clause）
└── README.md                       # 项目介绍
```

## 版本说明

每个发布版本特性变更详细信息，请参见《[版本说明书](docs/zh/release_notes.md)》。

## 快速上手

基于开源Snappy优化的鲲鹏Snappy的安装和使能的详细步骤，请参见《[快速入门](docs/zh/quick_start.md)》。

## 文档

| 资源名称 | 资源简介 |
| --------- | --------- |
| [安装指南](./docs/zh/installation_guide.md) | 提供针对ARM64架构优化的Snappy的编译安装详细步骤指导。 |
| [快速入门](./docs/zh/quick_start.md) | 提供针对ARM64架构优化的Snappy的安装和使能快速入门指导。 |
| [版本说明书](./docs/zh/release_notes.md) | 提供针对ARM64架构优化的Snappy的发布版本的基础信息和特性更新信息。 |
| [API参考](./docs/zh/api_reference.md) | 提供针对ARM64架构优化的Snappy的接口说明、接口调用示例等。 |

## 免责声明

此代码仓仅对snappy部分函数在ARM64处理器上进行性能优化，编码风格遵照原生开源软件，继承原生开源软件安全设计，不破坏原生开源软件设计及编码风格和方式，软件的任何漏洞与安全问题，均由相应的上游社区根据其漏洞和安全响应机制解决。请密切关注上游社区发布的通知和版本更新。ARM64计算社区对软件的漏洞及安全问题不承担任何责任。

## License

本项目遵循与Snappy相同的许可证（BSD-3-Clause），具体请参见[LICENSE](LICENSE)文件。

## 贡献指南

如果使用过程中有任何问题，或者需要反馈特性需求和bug报告，可以提交issues联系我们。

## 建议与交流

欢迎大家为社区做贡献。如果有任何疑问或建议，请提交Issues，我们会尽快回复。感谢您的支持。

## 致谢

感谢来自社区的每一个PR，欢迎贡献！
