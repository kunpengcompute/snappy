# Introduction to Snappy

## Latest Updates

- [2026-06-30]: Released the optimized version v1.0.0 based on the Snappy 1.2.2 branch for the AArch64 architecture, achieving performance gains.

## Project Introduction

Snappy is a high-speed compression and decompression library developed by Google. Its goal is to provide extremely fast compression and decompression speeds rather than pursuing high compression ratios.

This project focuses on the performance optimization of the Snappy compression library on the AArch64 architecture. It leverages techniques such as hash table optimization and adaptive skipping steps to enhance the performance of the compression algorithm. The interfaces remain fully consistent with open-source Snappy.

## Directory Structure

The full project directory structure is as follows:

```text
snappy/
├── docs/                           # Documentation directory
│   ├── LICENSE                     # Documentation license (CC-BY 4.0)
│   └── en/                         # English documentation
│       ├── api_reference.md        # API reference
│       ├── installation_guide.md   # Installation guide
│       ├── quick_start.md          # Quick start guide
│       ├── release_notes.md        # Release notes
│       └── menu_snappy.md          # Navigation menu
├── snappy-1.2.2-opt.patch          # Optimization patch for the AArch64 architecture
├── LICENSE                         # Project license (BSD-3-Clause)
└── README_EN.md                    # Project introduction
```

## Release Notes

For details about feature changes in each released version, see [Release Notes](docs/en/release_notes.md).

## Quick Start

For details about how to install and enable Kunpeng Snappy optimized based on open-source Snappy, see [Quick Start](docs/en/quick_start.md).

## Documentation

| Document Name| Description|
| --------- | --------- |
| [Installation Guide](./docs/en/installation_guide.md)| Provides detailed instructions for compiling and installing Snappy optimized for the AArch64 architecture.|
| [Quick Start](./docs/en/quick_start.md)| Provides a quick start guide for installing and enabling Snappy optimized for the AArch64 architecture.|
| [Release Notes](./docs/en/release_notes.md)| Provides basic information and feature updates of Snappy optimized for the AArch64 architecture.|
| [API Reference](./docs/en/api_reference.md)| Provides API descriptions and API calling examples for Snappy optimized for the AArch64 architecture.|

## Disclaimer

This repository only performs performance optimizations on specific Snappy functions for AArch64 processors. It strictly adheres to the coding style and methods, as well as security design of the original open-source software. Any vulnerability and security issues of the software shall be resolved by the corresponding upstream communities according to their response mechanisms. Please pay attention to the notifications and version updates released by the upstream communities. The ARM64 computing community does not assume any responsibility for software vulnerabilities and security issues.

## License

This project complies with the same license (BSD-3-Clause) as Snappy. For details, see [LICENSE](LICENSE).

## Contributing Guide

If you have any questions or want to provide feedback on feature requirements and bug reports, you can submit an issue.

## Suggestions and Feedback

You are welcome to contribute to the community. If you have any questions or suggestions, please submit an issue. We will respond as soon as possible. Thank you for your support.

## Acknowledgments

Thank you to everyone in the community for your PRs. We warmly welcome your contributions.
