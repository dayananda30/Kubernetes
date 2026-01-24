# Hypervisors

A hypervisor is software that creates and runs virtual machines (VMs). Each VM acts like a separate computer with its own OS and apps, while sharing the same physical hardware.

## Table of Contents
- [Overview](#overview)
- [Types of Hypervisors](#types-of-hypervisors)
  - [Type 1 (Bare-metal)](#type-1-bare-metal)
  - [Type 2 (Hosted)](#type-2-hosted)
- [How Hypervisors Work](#how-hypervisors-work)
  - [Hardware Abstraction](#hardware-abstraction)
  - [Virtual Hardware Devices](#virtual-hardware-devices)
  - [Device Emulation and Paravirtualization](#device-emulation-and-paravirtualization)
  - [CPU Virtualization](#cpu-virtualization)
  - [Memory Virtualization](#memory-virtualization)
- [Example: Virtual Disk Abstraction](#example-virtual-disk-abstraction)
- [Summary](#summary)

## Overview

A hypervisor is a piece of software that creates and runs virtual machines (VMs). Each VM acts like a separate computer, running its own operating system and applications, but all VMs share the same physical hardware.

## Types of Hypervisors

### Type 1 (Bare-metal)

- Run directly on the host’s hardware.
- Examples: VMware ESXi, Microsoft Hyper-V, Xen, KVM.
- Used in data centers and enterprise environments.

### Type 2 (Hosted)

- Run on top of a host operating system.
- Examples: VMware Workstation, Oracle VirtualBox.
- Used for desktop virtualization and development/testing.

## How Hypervisors Work

### Hardware Abstraction

Hardware abstraction means that the hypervisor presents a simplified, standardized view of the underlying physical hardware to each virtual machine (VM)

### Virtual Hardware Devices

- Hypervisor creates virtual devices (e.g., `vCPU`, `vNIC`, `vDisk`) per VM.
- Guest OS interacts with these virtual devices as if they were physical.

### Device Emulation and Paravirtualization

- Emulation: Hypervisor intercepts hardware access and emulates responses.
- Paravirtualization: Guest OS uses special drivers (e.g., `virtio` in KVM) for efficient communication.

### CPU Virtualization

- vCPUs are scheduled onto physical CPU cores.
- Hardware assist: Intel VT-x, AMD-V handle privileged instructions.

### Memory Virtualization

- VM is given virtual memory mapped to physical memory.
- Uses shadow page tables or EPT (Intel) / NPT (AMD).

## Example: Virtual Disk Abstraction

Suppose a VM thinks it has a 100GB hard drive. In reality:

- The hypervisor presents a virtual disk controller and disk to the VM.
- Reads/writes from the VM are redirected to a file (like `vm1.vmdk`) or a logical volume on the host.
- The VM’s OS is unaware that it’s not talking to a real hard drive.

## Summary

| Component | Hypervisor Abstraction Method |
|---|---|
| CPU | vCPUs mapped to physical CPUs; hardware assist (VT-x/AMD-V) |
| Memory | Virtual memory mapped to physical; page tables (EPT/NPT) |
| Disk | Virtual disk mapped to file or logical volume |
| Network | Virtual NIC mapped to physical NIC or bridge |
| Devices | Emulated, paravirtualized, or pass-through |

