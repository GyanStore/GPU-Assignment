# GPU Assignment — Problem 4: Parallel SHA-256 on GPU

**Course:** GPU Programming — IIT Jodhpur  
**Problem:** Implement a cryptography algorithm over GPU (SHA-256 / MD5 hashing)  
**Authors:** Shruthi Chinnasamy, Swaraj Mahindrakar (G25AIT1179)

This repository contains the CUDA implementation, benchmark suite, and report documents for **bulk SHA-256 digest computation on GPU**.

---

## Repository layout

```
GPU-Assignment/
├── docs/
│   ├── GPU_Prog_ASSIGNMENT.pdf          # Official assignment (Problem 4)
│   ├── SHA256_GPU_CUDA_IEEE_Paper.pdf   # Original report (baseline B0 design)
│   └── SHA256_GPU_Paper_Revised.docx    # Revised report (B0 + B2 pipeline + operating map)
└── sha256_gpu/
    ├── main.cu           # Benchmark suite (experiments E0–E5)
    ├── kernels.cuh       # GPU SHA-256 kernels (B0 baseline, B2 stream variant)
    ├── sha256.cuh        # Device constants and macros
    ├── sha256_cpu.h      # CPU reference + FIPS 180-4 self-test
    ├── utils.h           # Dataset generation and verification
    ├── Makefile          # Build (auto-detects GPU compute capability)
    ├── plot_results.py   # Generate figures from benchmark output
    └── README.md         # Detailed build/run instructions
```

---

## Quick start

Requires an **NVIDIA GPU**, **CUDA toolkit**, and `nvcc` in your PATH.

```bash
cd sha256_gpu
make
./sha256_bench 0    # Correctness (FIPS self-test) — run this first
./sha256_bench 1    # Main benchmark: B0 vs B2 vs CPU (1M messages)
./sha256_bench all  # Run all experiments E0–E5
```

See [`sha256_gpu/README.md`](sha256_gpu/README.md) for experiment details, Colab instructions, and how to generate plots.

---

## What the code implements

| Component | Description |
|-----------|-------------|
| **B0** | Synchronous baseline — one GPU thread hashes one message |
| **B2** | Stream-pipelined variant — overlaps H2D transfer, kernel, and D2H with pinned memory |
| **CPU** | Single-threaded reference for correctness and speedup comparison |

Experiments **E0–E5** measure correctness, throughput, block-size tuning, transfer/compute crossover, scalability, and a GPU operating map (when GPU beats CPU).

---

## Reports

- **Original paper** (`docs/SHA256_GPU_CUDA_IEEE_Paper.pdf`): Full design and methodology for the baseline GPU SHA-256 engine.
- **Revised paper** (`docs/SHA256_GPU_Paper_Revised.docx`): Adds stream pipelining (B2), empirical operating map, and submission-ready results tables (fill with measured numbers from your GPU runs).

---

## Google Colab (no local GPU)

```python
!nvidia-smi
!nvcc -O3 -arch=sm_75 -std=c++17 --use_fast_math main.cu -o sha256_bench
!./sha256_bench 0
```

Replace `sm_75` with your Colab GPU's compute capability if different.
