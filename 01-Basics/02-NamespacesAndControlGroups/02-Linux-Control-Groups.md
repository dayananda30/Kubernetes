# Linux Control Groups (cgroups)

## Table of Contents
- [Background Context](#background-context)
- [What Are Control Groups (cgroups)?](#what-are-control-groups-cgroups)
- [Key Features of cgroups](#key-features-of-cgroups)
- [How cgroups Are Useful for Containers](#how-cgroups-are-useful-for-containers)
- [How cgroups Work (Simplified)](#how-cgroups-work-simplified)
- [Example: Docker and cgroups](#example-docker-and-cgroups)
- [Summary Table](#summary-table)
- [In Short](#in-short)

## Background Context
In a multi-user, multi-process system like Linux, processes compete for resources (CPU, memory, disk I/O, network).
By default, the Linux kernel doesn’t limit how much of these resources a process can use.
Control groups (cgroups) provide a way to limit, prioritize, and account for resource usage by groups of processes.

## What Are Control Groups (cgroups)?
cgroups are a Linux kernel feature that allows you to allocate, limit, and monitor resources (CPU, memory, disk, network, etc.) for groups of processes.
You can think of a cgroup as a “container” for processes, with rules about how much of each resource they can use.

## Key Features of cgroups
- Resource Limiting: Set maximum CPU, memory, disk I/O, or network usage for a group of processes.
- Prioritization: Give some groups higher priority for resources.
- Accounting: Track how much resources each group uses.
- Isolation: Prevent one group from starving others of resources.

## How cgroups Are Useful for Containers
Containers are just Linux processes with extra isolation. cgroups are used to:

1. Limit Resource Usage
Prevent a container from using too much CPU, memory, or disk I/O.
Example: If a container tries to use more than its allocated 512MB of RAM, the kernel can kill processes in that container.
2. Fair Resource Sharing
Ensure that no single container can monopolize system resources, affecting others.
Example: If you run two containers, you can give each 50% of the CPU.
3. Resource Accounting
Track exactly how much CPU, memory, or I/O each container uses.
Useful for monitoring, billing, or debugging.
4. Isolation
Combined with namespaces, cgroups ensure that containers are isolated not just in what they can see (namespaces), but also in what they can use (cgroups).

## How cgroups Work (Simplified)
The kernel organizes processes into a hierarchy of cgroups.
Each cgroup can have rules for resource usage.
When a process (or container) runs, it is placed into a cgroup, and the kernel enforces the limits set for that group.

## Example: Docker and cgroups
When you run a Docker container and specify resource limits, Docker uses cgroups under the hood:

```bash
docker run --memory=256m --cpus=1 myapp
```

This command tells Docker to create a cgroup for the container, limiting it to 256MB of RAM and 1 CPU core.

## Summary Table
| Feature           | What cgroups do for containers              |
|-------------------|---------------------------------------------|
| CPU limits        | Prevent CPU hogging                         |
| Memory limits     | Prevent memory exhaustion                   |
| I/O limits        | Prevent disk/network monopolization         |
| Resource tracking | Enable monitoring and billing               |
| Prioritization    | Ensure fair resource allocation             |


## In Short
cgroups are how Linux controls “how much” of each resource a container can use.
Namespaces control “what” a container can see.

Together, they make containers efficient, isolated, and safe to run side-by-side on the same host.
