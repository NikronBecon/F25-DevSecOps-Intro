# Lab 12 — Kata Containers: VM-backed Container Sandboxing

## Task 1 — Install & Configure Kata
- Built Kata Rust shim via `labs/lab12/setup/build-kata-runtime.sh`; version saved in `labs/lab12/setup/kata-built-version.txt`.
- Installed shim to host PATH (`containerd-shim-kata-v2` 3.23.0) and ran a smoke container with `--runtime io.containerd.kata.v2` (guest kernel 6.12.47-kata).

## Task 2 — Run & Compare (runc vs kata)
- Juice Shop under runc reachable: `labs/lab12/runc/health.txt` shows HTTP 200 on port 3012.
- Kata Alpine runs succeeded; uname/kernel/CPU evidence in `labs/lab12/kata/test1.txt`, `kernel.txt`, `cpu.txt`.
- Kernel comparison: host `6.8.0-48-generic` vs Kata guest `6.12.47-kata`; CPU host `Intel(R) Core(TM) i7-10700K` vs Kata VM `Intel Core Processor (Skylake, IBRS)` (see `labs/lab12/analysis/kernel-comparison.txt`, `cpu-comparison.txt`).
- Isolation implication: runc shares host kernel/CPU; Kata places the workload in a VM with its own kernel/virtual CPU boundary.

## Task 3 — Isolation Tests
- `dmesg` in Kata shows VM boot logs (separate kernel): `labs/lab12/isolation/dmesg.txt`.
- `/proc` entries: host 452 vs Kata 188 (`labs/lab12/isolation/proc.txt`); Kata network interface isolated (`labs/lab12/isolation/network.txt`); guest kernel modules 0 vs host 312 (`labs/lab12/isolation/modules.txt`).
- Security note: runc escape targets host kernel; Kata escape must cross guest kernel + hypervisor boundary, raising the bar.

## Task 4 — Performance Snapshot
- Startup: runc `real 0.42s`, Kata `real 3.85s` (`labs/lab12/bench/startup.txt`).
- HTTP latency baseline (runc/port 3012): avg 0.0362s, min 0.0310s, max 0.0492s, n=50 (`labs/lab12/bench/http-latency.txt`, raw `labs/lab12/bench/curl-3012.txt`).
- Trade-offs: noticeable Kata cold-start overhead; steady-state latency close to runc; CPU overhead minimal on this small workload.
- Recommendations: use runc for fast, short-lived workloads; choose Kata for stronger isolation/compliance boundaries despite extra startup cost.

## Evidence Files
- `labs/lab12/setup/kata-built-version.txt`
- `labs/lab12/runc/health.txt`
- `labs/lab12/kata/test1.txt`
- `labs/lab12/kata/kernel.txt`
- `labs/lab12/kata/cpu.txt`
- `labs/lab12/analysis/kernel-comparison.txt`
- `labs/lab12/analysis/cpu-comparison.txt`
- `labs/lab12/isolation/dmesg.txt`
- `labs/lab12/isolation/proc.txt`
- `labs/lab12/isolation/network.txt`
- `labs/lab12/isolation/modules.txt`
- `labs/lab12/bench/startup.txt`
- `labs/lab12/bench/http-latency.txt`
- `labs/lab12/bench/curl-3012.txt`
