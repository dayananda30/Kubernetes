# vCPU vs Physical CPU

## Table of Contents
- [Overview](#overview)
- [Quick Comparison](#quick-comparison)

## Overview
- **vCPU**: A virtual CPU exposed to a VM by the hypervisor; scheduled onto physical cores.
- **Physical CPU (Core)**: A hardware execution unit on the host; runs native threads with no virtualization overhead.

## Quick Comparison
| Feature | vCPU | Physical CPU (Core) |
|---|---|---|
| Nature | Software abstraction | Hardware component |
| Creation | By hypervisor during VM setup | By CPU manufacturer |
| Scheduling | Managed by hypervisor | Managed by OS/hardware |
| Sharing | Multiple vCPUs can share one core | Typically one runnable thread per core at a time |
| Performance | Depends on core availability and virtualization overhead | Native speed |
| Privileged Ops | Trapped/emulated or assisted by hypervisor | Executed directly |

