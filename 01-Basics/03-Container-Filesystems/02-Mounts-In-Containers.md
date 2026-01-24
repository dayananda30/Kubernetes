# Container Mounts (Volumes, Bind Mounts, tmpfs)

## Table of Contents
- [Background Context](#background-context)
- [What Are Mounts in Containers?](#what-are-mounts-in-containers)
- [How Mounts Work in Containers](#how-mounts-work-in-containers)
  - [Linux Mount Namespace](#linux-mount-namespace)
  - [Mounting Process](#mounting-process)
  - [Why Mounts Work](#why-mounts-work)
- [Why Mounts Are Useful](#why-mounts-are-useful)
- [Summary Table](#summary-table)
- [Additional Types and Examples](#additional-types-and-examples)
  - [tmpfs Mounts](#tmpfs-mounts)
  - [Permissions and SELinux](#permissions-and-selinux)
  - [Kubernetes Example](#kubernetes-example)
- [Best Practices](#best-practices)

## Background Context
By default, a container’s filesystem is isolated and ephemeral—meaning any changes made inside the container are lost when the container is deleted. However, many applications need to persist data (like databases) or share files (like logs or configs) between the container and the host, or between containers.

This is where mounts come in.

## What Are Mounts in Containers?
Mounts allow you to attach storage from outside the container (usually from the host) into the container’s filesystem at a specific path.

There are two main types:

Volumes: Managed by the container runtime (like Docker). They are stored in a special location on the host and can be reused by multiple containers.
Bind mounts: Directly map a specific file or directory from the host into the container.
## How Mounts Work in Containers

### Linux Mount Namespace
Containers use the mount namespace to isolate their view of the filesystem.
The container runtime (e.g., Docker) can mount directories or files from the host into the container’s isolated filesystem.
This is done using standard Linux mount operations, but scoped to the container’s namespace.
### Mounting Process
When you start a container with a mount, the runtime sets up the mount before the container process starts.
Inside the container, the mounted directory or file appears at the specified path, just like any other part of the filesystem.
Any changes made in the mounted path are reflected on the host (for bind mounts) or in the volume (for volumes).
#### Example: Docker bind mount
```bash
docker run -v /host/data:/container/data myapp
```

/host/data is a directory on the host.
/container/data is the path inside the container.
Files written to /container/data by the container are actually stored in /host/data on the host.
### Why Mounts Work
Linux’s flexible mount system allows filesystems, directories, or devices to be mounted at any point in the directory tree.
Mount namespaces ensure that mounts are isolated per container, so containers don’t see each other’s mounts unless explicitly shared.
The container runtime orchestrates these mounts, making sure they’re set up before the container starts.
## Why Mounts Are Useful
Persistence: Data written to a mount survives container deletion or recreation.
Sharing: Multiple containers can access the same data (e.g., logs, configs, shared databases).
Backup and Restore: Host directories or volumes can be backed up independently of the container.
Configuration: You can inject configuration files or secrets into containers at runtime.

## Summary Table
| Mount Type | Description                         | Use Case                         |
|---|---|---|
| Volume     | Managed by container runtime        | Persistent, reusable storage     |
| Bind mount | Direct mapping from host to container | Share host files, configs, logs |


## Additional Types and Examples

### tmpfs Mounts
- In-memory, ephemeral storage; ideal for caches or sensitive temp data.
```bash
docker run --tmpfs /tmp myapp
```

### Permissions and SELinux
- Read-only mounts: add :ro
- SELinux relabeling: :z (shared) or :Z (private)
```bash
docker run -v /host/data:/container/data:ro myapp
docker run -v /secure:/container/secure:Z myapp
```

### Kubernetes Example
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app
spec:
  volumes:
    - name: data
      hostPath:
        path: /host/data
        type: Directory
  containers:
    - name: app
      image: myapp
      volumeMounts:
        - name: data
          mountPath: /container/data
```

## Best Practices
- Prefer volumes over bind mounts in production.
- Use read-only mounts unless writes are required.
- Keep state out of the container’s writable layer; persist to volumes.
- Back up volumes independently of containers.
