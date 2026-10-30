# Experiment 5 — Analyze Synchronization Mechanisms in Parallel Programs

## Objective
Implement and study synchronization mechanisms in shared-memory parallel programs: a race condition, critical sections, atomic operations, barriers, semaphores/locks, and compare correctness, execution time, and scalability as thread count increases.

## Environment
- VMware Workstation
- Ubuntu 24.04 guest OS (as shown in the terminal screenshot)
- GCC 13.3.0
- OpenMP
- POSIX threads and semaphore library

## Files
- `src/race.c` — unsynchronized shared counter
- `src/critical.c` — OpenMP critical section
- `src/atomic.c` — OpenMP atomic update
- `src/barrier.c` — OpenMP barrier
- `src/semaphore.c` — POSIX semaphore with two threads
- `src/compare_sync.c` — compares None, Critical, Atomic, and Lock for 1, 2, 4, and 8 threads
- `graphs/` — graphs generated from the measured comparison output shown in the submitted screenshot
- `screenshots/` — submitted terminal screenshots

## Installation
```bash
sudo apt update
sudo apt install build-essential -y
```

Verify GCC and OpenMP:
```bash
gcc --version
echo '#include <omp.h>' | gcc -E -fopenmp -xc - > /dev/null && echo OpenMP OK
```

## Compile
Run these commands from the repository root:
```bash
gcc src/race.c -fopenmp -o race
gcc src/critical.c -fopenmp -o critical
gcc src/atomic.c -fopenmp -o atomic
gcc src/barrier.c -fopenmp -o barrier
gcc src/semaphore.c -pthread -o semaphore
gcc src/compare_sync.c -fopenmp -o compare_sync
```

## Execute
```bash
./race
./critical
./atomic
./barrier
./semaphore
./compare_sync
```

The semaphore program is expected to print `Counter=200000`. The comparison program performs 16 measurements: four mechanisms at thread counts 1, 2, 4, and 8. Timing results depend on the system and may vary between runs.

## Results captured from the submitted screenshot
Measured output from `./compare_sync`:

| Mechanism | Threads | Final count | Correctness | Time (s) |
|---|---:|---:|---|---:|
| None | 1 | 10000000 | Correct | 0.0067 |
| Critical | 1 | 10000000 | Correct | 0.1162 |
| Atomic | 1 | 10000000 | Correct | 0.0671 |
| Lock | 1 | 10000000 | Correct | 0.1119 |
| None | 2 | 5680023 | INCORRECT | 0.0145 |
| Critical | 2 | 10000000 | Correct | 0.3658 |
| Atomic | 2 | 10000000 | Correct | 0.1059 |
| Lock | 2 | 10000000 | Correct | 0.2406 |
| None | 4 | 6138630 | INCORRECT | 0.0085 |
| Critical | 4 | 10000000 | Correct | 0.3603 |
| Atomic | 4 | 10000000 | Correct | 0.1062 |
| Lock | 4 | 10000000 | Correct | 0.2549 |
| None | 8 | 7231657 | INCORRECT | 0.0090 |
| Critical | 8 | 10000000 | Correct | 0.3294 |
| Atomic | 8 | 10000000 | Correct | 0.1048 |
| Lock | 8 | 10000000 | Correct | 0.2439 |

## Observations
- The unsynchronized `None` mechanism produced incorrect counts at 2, 4, and 8 threads, demonstrating a race condition.
- Critical, Atomic, and Lock produced the expected count in these runs.
- In this run, Atomic was faster than Critical and Lock for thread counts 1, 2, 4, and 8.
- The unsynchronized version was fastest, but it did not reliably preserve updates.
- A barrier coordinates when threads proceed; it does not directly protect shared data.

## Conclusion
Synchronization helps ensure correct updates to shared data. Critical sections, atomic operations, locks, and semaphores prevent unsafe concurrent updates when used correctly, while a barrier synchronizes phases of parallel execution. Atomic operations were the fastest of the synchronized approaches in the captured benchmark run. Performance depends on the machine and workload.

## Repository layout
```text
Experiment-5-Synchronization/
├── README.md
├── src/
│   ├── race.c
│   ├── critical.c
│   ├── atomic.c
│   ├── barrier.c
│   ├── semaphore.c
│   └── compare_sync.c
├── graphs/
│   ├── execution_time_comparison.png
│   └── final_count_comparison.png
└── screenshots/
    ├── 01-program-execution.png
    └── 02-comparison-results.png
```
