<h1 align="center">
  <br>KVM-VMI</br>
</h1>

<h3 align="center">
KVM-based Virtual Machine Instrospection.
</h3>

<p align="center">
  <a href="https://kvm-vmi.slack.com">
    <img src="https://img.shields.io/badge/Slack-KVM--VMI-important" alt="Slack">
  </a>
  <a href="mailto:mathieu.tarral@protonmail.com">
    <img src="https://img.shields.io/badge/📧-Ask Slack Invite-blue">
  <a>
  <a href="https://kvm-vmi.github.io/kvm-vmi/master/">
    <img src="https://img.shields.io/badge/📖-Documentation-green">
  <a>
</p>

## Table of Contents

- [Overview](#overview)
- [Installation](#installation)
  - [For Malware Analysis (VMware + Windows 10)](#for-malware-analysis-vmware--windows-10)
  - [General Installation](#general-installation)
- [Optimizations for Malware Analysis](#optimizations-for-malware-analysis)
- [Presentations](#presentations)
- [References](#references)
- [Maintainers](#maintainers)
- [License](#license)

## Overview

This project adds virtual machine introspection to the KVM hypervisor.

_Virtual Machine Introspection_ is a technology that aims to understand the guest's execution context, solely based on the VM's hardware state, for various purposes:

- Debugging
- Malware Analysis
- Live-Memory Analysis
- OS Hardening
- Monitoring
- Fuzzing

See the [presentations](#presentations) section for more information.

This project is divided into 4 components:
- `kvm`: linux kernel with _vmi_ patches for KVM
- `qemu`: patched to allow introspection
- `nitro` (legacy): userland library which receives events, introspects the virtual
  machine state, and fills the semantic gap
- `libvmi`: virtual machine instrospection library with unified API
  across `Xen` and `KVM`

At the moment, 2 versions of VMI patches are available for `QEMU/KVM`
in this repository:

## Installation

### For Malware Analysis (VMware + Windows 10)

This repository has been optimized for malware analysis on VMware with Windows 10. See:
- **[Quick Start Guide](QUICK_START.md)** - 빠른 시작 가이드 (한국어/English)
- **[Malware Analysis Setup](MALWARE_ANALYSIS_SETUP.md)** - Detailed English documentation
- **[악성코드 분석 가이드](악성코드_분석_설정_가이드.md)** - 한국어 상세 가이드
- **[Configuration Reference](CONFIGURATION.md)** - System configuration details

**Quick Setup:**
```bash
cd vagrant
vagrant up --provider=vmware_desktop
```

### General Installation

Follow the [Setup guide](https://kvm-vmi.github.io/kvm-vmi/master/setup.html)

## Optimizations for Malware Analysis

This fork has been optimized for malware analysis with the following enhancements:

### Infrastructure
- **VMware Desktop Provider**: Full support with 8GB RAM and nested virtualization
- **Ubuntu 22.04**: Updated base system for better compatibility
- **Storage Path**: `/home/sec` for centralized malware analysis workspace

### Windows 10 Configuration
- **Memory**: 4GB RAM for better performance during malware execution
- **vCPUs**: 2 cores for responsive analysis
- **Disk**: 100GB for extensive sample storage
- **Format**: qcow2 with snapshot support for clean state recovery

### Analysis Tools
- Network monitoring tools (tcpdump, wireshark)
- Debugging tools (gdb, strace, ltrace)
- Isolated network for safe malware execution
- Automated environment setup scripts
- Pre-configured aliases for common operations

### Workflow Enhancements
- Quick snapshot creation and restoration
- Network traffic capture automation
- Organized directory structure for samples and reports
- Comprehensive bilingual documentation (English/Korean)

## Presentations

- [Bringing Commercial Grade Virtual Machine Introspection to KVM](https://www.linux-kvm.org/images/7/72/KVMForum2017_Introspection.pdf)
- [KVM Forum 2019: Advanced VMI on KVM - A Progress Report](https://static.sched.com/hosted_files/kvmforum2019/f6/Advanced%20VMI%20on%20KVM%3A%20A%20progress%20Report.pdf)
- [Hack.lu 2019: Leveraging KVM as a Debugging Platform](https://drive.google.com/file/d/1nFoCM62BWKSz2TKhNkrOjVwD8gP51VGK/view?usp=sharing)
- [Advanced VMI on KVM: A Progress Report](https://static.sched.com/hosted_files/kvmforum2019/f6/Advanced%20VMI%20on%20KVM%3A%20A%20progress%20Report.pdf)

## References

The legacy VMI system contained in this repo (_Nitro_) is based on `Jonas Pfoh`'s work:
- [Nitro: Hardware-based System Call Tracing for Virtual Machines](https://www.sec.in.tum.de/assets/staff/pfoh/PfohSchneider2011a.pdf)
- [Nitro - VMI Extensions for Linux/KVM](http://nitro.pfoh.net/)

## Maintainers

[@Wenzel](https://github.com/Wenzel)

## License

[GNU General Public License v3.0](https://github.com/KVM-VMI/kvm-vmi/blob/master/LICENSE)
