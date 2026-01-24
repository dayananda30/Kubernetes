# Container Filesystems (Layers, Volumes, and Persistence)

## Table of Contents
- [Background Context](#background-context)
- [How Filesystems Work in Containers](#how-filesystems-work-in-containers)
- [Summary Table](#summary-table)
- [Copy-on-Write (CoW)](#copy-on-write-cow)
- [Storage Drivers at a Glance](#storage-drivers-at-a-glance)
- [Persistence Patterns](#persistence-patterns)
- [Docker Examples](#docker-examples)
- [Kubernetes Volumes Primer](#kubernetes-volumes-primer)
- [Security & Best Practices](#security--best-practices)

## Background Context
Every process in Linux (including those in containers) interacts with a filesystem for reading and writing files.
In traditional systems, all processes share the same root filesystem (/).
In containers, each container gets its own isolated filesystem view, which is one of the main reasons containers are portable and secure.

## How Filesystems Work in Containers
1. Layered Filesystem (Union Filesystem)
Container images are built in layers. Each layer represents a set of filesystem changes (additions, modifications, deletions).
When you run a container, these layers are stacked together using a union filesystem (like OverlayFS, AUFS, or Btrfs).
The bottom layers are read-only (from the image), and the top layer is writable (for changes made by the running container).
Example:

Base image layer: Ubuntu OS files
App layer: Your application and its dependencies
Writable layer: Any changes made while the container is running (logs, temp files, etc.)
2. Container Filesystem Isolation
Each container gets its own root filesystem (/), isolated from the host and other containers.
This is achieved using the mount namespace in Linux, so changes in one container’s filesystem don’t affect others or the host.
3. Ephemeral Nature
By default, changes made in a container’s filesystem (in the writable layer) are not saved when the container is deleted.
This makes containers stateless by default—great for scaling and replacing containers, but not for persistent data.
4. Sharing Data: Volumes and Bind Mounts
For persistent or shared data, containers can use:
Volumes: Managed by the container runtime, stored outside the container’s layered filesystem, and persist even if the container is deleted.
Bind mounts: Directly mount a host directory or file into the container.

## Summary Table
| Aspect            | Description                                     |
|---|---|
| Isolation         | Each container has its own root filesystem       |
| Layered structure | Built from image layers, stacked via union FS    |
| Writable layer    | Only the top layer is writable during runtime    |
| Ephemeral         | Changes are lost when the container is deleted   |
| Persistent storage| Use volumes or bind mounts for data persistence  |
| Sharing           | Volumes/bind mounts allow host/container sharing |

## Copy-on-Write (CoW)
- Upper writable layer stores changes; reads fall back to lower read-only layers.
- Deletions use whiteouts to mask files from lower layers.
- Reduces duplication and image size; improves startup speed.

## Storage Drivers at a Glance
| Driver    | Notes                                  |
|---|---|
| overlay2 | Default on modern Linux; fast, simple   |
| btrfs    | Snapshotting; integrated CoW filesystem |
| zfs      | Advanced features; requires ZFS setup   |

## Persistence Patterns
- Named volumes for app data (databases, uploads).
- Bind mounts for local dev (source code, configs).
- tmpfs for sensitive ephemeral data (secrets, caches).
- Prefer volumes over writing to the container’s writable layer.

## Docker Examples
```bash
# Persist data in a named volume
docker run -v mydata:/var/lib/app myimage

# Bind mount a host directory
docker run -v $(pwd)/cfg:/etc/app:ro myimage

# Read-only root FS with a writable volume
docker run --read-only -v scratch:/var/tmp myimage
```

## Kubernetes Volumes Primer
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app
spec:
  volumes:
    - name: data
      emptyDir: {}           # or PersistentVolumeClaim
  containers:
    - name: app
      image: myimage
      volumeMounts:
        - name: data
          mountPath: /var/lib/app
```

## Security & Best Practices
- Use read-only root filesystems; write only to mounted volumes.
- Avoid storing state in the container layer; keep images stateless.
- Pin paths for logs/temp to volumes; prefer /var/lib, /var/log, /tmp mounts.
- Cleanly separate config (bind mounts/ConfigMaps) from data (volumes).
