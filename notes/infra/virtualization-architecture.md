# Virtualization Architecture

## Key Takeaways

- **Virtualization** (same-ISA, near-native speed via direct execution) is fundamentally different from **emulation** (cross-ISA, slow software translation) — virtualization is what makes cloud economics viable
- A **hypervisor** creates the illusion of dedicated hardware for each guest OS via three layers: physical hardware → hypervisor → VMs; this delivers isolation, portability, and resource sharing
- **Type 1** (bare-metal: ESXi, Hyper-V, KVM) hypervisors run directly on hardware and are preferred by cloud providers; **Type 2** (hosted: VirtualBox) run as an app on a host OS with extra overhead
- CPU virtualization works by demoting the guest OS to a lower privilege ring so the hypervisor can trap privileged instructions via **VM-exit / VM-entry** transitions (microsecond-scale, costly if frequent)
- **vCPUs** are time-sliced onto physical cores and can be overcommitted; **NUMA-aware** scheduling keeps a VM's vCPUs and memory on the same socket to avoid cross-node latency

## Emulation vs Virtualization

| Property | Emulation | Virtualization |
|---|---|---|
| ISA | Different (e.g., ARM on x86) | Same as host |
| Mechanism | Software translates every instruction | Most instructions run directly on physical CPU |
| Speed | Slow ("translator for every sentence") | Near-native |
| Use case | Run foreign architectures (game consoles, IoT cross-compile) | Cloud, multi-tenancy, dev environments |

Virtualization is the basis for practical cloud workloads — without it, cloud economics don't work.

## What a Virtual Machine Actually Is

A VM is presented with virtual CPU, memory, storage, and NICs; the guest OS believes it owns the hardware. The stack:

```
Physical hardware
   ↓
Hypervisor
   ↓
VMs (each with guest OS + apps)
```

Three properties this delivers:

- **Isolation** — VMs cannot see or affect each other
- **Portability** — VMs can move between hosts
- **Resource sharing** — one physical box runs many guests

## Hypervisor Types

### Type 1 — Bare-Metal

Runs directly on hardware. No host OS underneath. Minimal overhead.

- Examples: VMware ESXi, Microsoft Hyper-V, KVM
- Preferred by cloud providers (AWS, GCP, Azure)
- Better isolation, security, and performance

### Type 2 — Hosted

Runs as an application on top of a host OS.

- Examples: VirtualBox, VMware Workstation
- Easier setup (just install like any app)
- Extra overhead from the host OS layer
- Suited for dev/test on a laptop, not production

## Full vs Paravirtualization

| Approach | Guest OS | Performance | Compatibility |
|---|---|---|---|
| **Full virtualization** | Unmodified | Lower (hypervisor handles all abstraction) | High — any OS works |
| **Paravirtualization** | Modified to cooperate with hypervisor | Higher | Lower — guest must be ported |

## CPU Virtualization

Four interlocking techniques make a guest OS believe it owns the CPU while the hypervisor stays in control.

### Privilege Model

x86 has rings 0–3, with ring 0 the most privileged. Normally an OS runs at ring 0. Under virtualization, the **guest OS is silently demoted** to a lower-privilege ring so the hypervisor can intercept sensitive operations.

### vCPU Scheduling

- A **vCPU** is processing capacity time-sliced onto physical cores
- Hypervisor schedules vCPUs the way an OS schedules threads
- **Overcommit:** vCPUs can exceed physical cores when guest workloads don't all peak at the same time
- Overcommit ratio is a capacity-planning lever; too high → CPU contention and noisy-neighbor problems

### VM-Exit and VM-Entry

When a guest issues a privileged instruction (I/O, page-table update, etc.):

1. **VM-exit** — CPU halts the guest, transfers control to the hypervisor
2. Hypervisor handles the request safely (and possibly virtualizes it)
3. **VM-entry** — control returns to the guest at the exact stopping point

Each transition takes microseconds — fine occasionally, expensive if frequent. Hardware-assisted virtualization (Intel VT-x, AMD-V) reduces this cost.

### NUMA Awareness

On multi-socket systems, accessing memory on a remote socket is slower than local memory. **NUMA-aware** schedulers pin a VM's vCPUs and memory to the same NUMA node:

- Cross-node memory access: slower (extra interconnect hop)
- Same-node memory access: faster (local cache, no hop)

VM placement decisions should respect NUMA topology, especially for latency-sensitive workloads.

## Architectural Choices Summary

| Choice | Optimize for | Pick when |
|---|---|---|
| Type 1 vs Type 2 | Performance / dev convenience | Production → Type 1; Local dev → Type 2 |
| Full vs paravirt | Compatibility / performance | Unmodified guests → full; Controlled guests → paravirt |
| Overcommit ratio | Density vs predictability | Bursty workloads → higher; latency-critical → 1:1 |
| NUMA placement | Memory access latency | Always for production; especially for DB, in-memory caches |

See also: [docker-vs-kubernetes.md](docker-vs-kubernetes.md) for the container alternative that trades isolation strength for startup speed and density.

> **Note:** This article was paywalled past CPU virtualization. Topics including memory virtualization (shadow page tables, EPT), nested virtualization, hardware-assisted virtualization details, and I/O virtualization were not captured.

---

**Source:** https://newsletter.systemdesign.one/p/virtualization-architecture
**Date:** 2026-06-01
**Tags:** virtualization, virtual-machines, hypervisor, kvm, esxi, hyper-v, cpu-virtualization, numa, vm-exit, infra, cloud
