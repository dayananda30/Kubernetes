# Kubernetes Pause Containers

Overview
- The pause (infra) container holds a pod’s shared Linux namespaces (net, ipc, uts, pid) alive.
- Even if all app containers crashloop, the pod stays scheduled until you delete it.

Why it exists
- Without pause, when all containers exit, the namespaces would terminate and the pod would disappear.
- Pause keeps the pod anchored on the node, preserving its networking identity and shared resources.

How it works (tiny C program, ~50 lines)
- Registers signal handlers:
  - SIGTERM/SIGINT: exit.
  - SIGCHLD: call `waitpid()` to reap children.
- Main thread sleeps with `pause()` until a signal arrives.
- On `kubectl delete pod`, kubelet sends SIGTERM to all containers; when pause exits last, namespaces release and the pod is removed.

Key points
- Pause does almost nothing until signaled.
- It guarantees the pod’s shared namespace lifecycle outlives app container crashes.
- Pod termination happens cleanly only after all containers (including pause) exit.


## Diagramatic Representation
![Pause containers diagram (KodeKloud)](https://kodekloud.com/community/uploads/db1265/original/3X/3/6/3695280a298041722f7f4275f77db5c9973b211d.jpeg)
