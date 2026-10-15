# Google Cloud Bigtable Benchmarking & Performance Analysis

A practical benchmarking project to evaluate Google Cloud Bigtable performance under sequential, concurrent, and mixed read/write workloads. This project demonstrates how to measure throughput, latency, workload correctness, and scaling behavior using Python scripts and Google Cloud Monitoring.

## Overview

The goal of this project is to understand how Google Cloud Bigtable behaves under different workload patterns and concurrency levels. It provides a repeatable starting point for performance testing, operational monitoring, and cost evaluation.

The benchmark results can help engineers understand:
- Read and write throughput.
- Average, p50, p95, and p99 client-observed latency.
- Performance changes as worker concurrency increases.
- Data correctness through read-back verification.
- The relationship between workload activity and Bigtable resource utilization.
- Considerations for performance optimization and Total Cost of Ownership (TCO).

**Important:** The results in this repository represent an initial benchmark in a specific test environment. They are not a maximum-capacity test or proof that Bigtable outperforms other databases. A fair comparison requires equivalent datasets, workload patterns, infrastructure, and cost assumptions.

## Architecture

The benchmark scripts run from a Python client and connect to a Google Cloud Bigtable instance using the Bigtable API.

```text
Python Benchmark Scripts
          |
          v
Google Cloud Bigtable API
          |
          v
Bigtable Instance
          |
          +----> Benchmark Table
          |        |
          |        +---- Read workloads
          |        +---- Write workloads
          |        +---- Mixed workloads
          |
          +----> Cloud Monitoring
                    |
                    +---- CPU utilization
                    +---- Request activity
                    +---- Latency metrics
                    +---- Storage metrics
```

## Workloads Covered

| Workload | Purpose |
|---|---|
| Sequential point reads | Establish a baseline for individual row reads |
| Sequential point writes | Measure individual row-write performance |
| Concurrent reads | Evaluate read throughput with multiple workers |
| Concurrent writes | Evaluate concurrent write throughput |
| Mixed read/write workload | Observe behavior when reads and writes run together |
| Read-scaling test | Compare read throughput at 1, 5, 10, and 20 workers |
| Data verification | Confirm that expected rows can be read back successfully |

## Initial Benchmark Results

The following are the measured results from the initial test environment.

### Read-scaling results

| Workers | Average throughput | Approx. p95 latency |
|---:|---:|---:|
| 1 | 4.67 reads/sec | 216 ms |
| 5 | 22.56 reads/sec | 228 ms |
| 10 | 44.63 reads/sec | 226 ms |
| 20 | 85.87 reads/sec | 227 ms |

Each worker level was tested with 200 reads per run and three measured runs. All 2,400 measured reads succeeded, with no reported errors or missing rows.

The results show that client-observed read throughput increased as concurrency increased, while p95 latency remained broadly similar for this workload.

### Other initial workload results

| Scenario | Operations | Workers | Observed throughput |
|---|---:|---:|---:|
| Sequential reads | 100 reads | 1 | 4.32 reads/sec |
| Sequential writes | 100 writes | 1 | 4.14 writes/sec |
| Concurrent reads | 100 reads | 10 | 30.14 reads/sec |
| Concurrent writes | 100 writes | 10 | 28.40 writes/sec |
| Mixed workload | 50 reads + 50 writes | 10 | 28.98 total ops/sec |

These results are client-side measurements and include factors such as RPC overhead, network latency, and client behavior. They should be treated as preliminary observations, not guaranteed production performance.

## Project Structure

```text
.
├── index.html
├── README.md
├── bigtable-benchmark-kit.zip
├── scripts/
│   ├── benchmark_reads.py
│   ├── benchmark_reads_direct.py
│   ├── benchmark_reads_concurrent.py
│   ├── benchmark_reads_scaling.py
│   ├── benchmark_writes.py
│   ├── benchmark_writes_concurrent.py
│   ├── benchmark_mixed_workload.py
│   ├── seed_bigtable.py
│   ├── check_bigtable.py
│   └── verify_bigtable.py
└── reports/
    ├── seed_report.json
    ├── read_benchmark_report.json
    ├── concurrent_read_benchmark_report.json
    ├── bigtable_read_scaling_report.json
    ├── write_benchmark_report.json
    ├── concurrent_write_benchmark_report.json
    └── mixed_workload_report.json
```

The exact directory layout may differ depending on how the files are uploaded. The JSON reports contain saved outputs from the original benchmark runs.

## Prerequisites

- Google Cloud project with the Bigtable API enabled.
- A Bigtable instance and table.
- Google Cloud CLI (`gcloud`).
- Python 3.12 or a compatible Python version.
- Appropriate permissions to access and modify the Bigtable table.
- Billing enabled for the Google Cloud project.

## Installation and Setup

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
cd YOUR_REPOSITORY
```

### 2. Create a virtual environment

```bash
python3 -m venv bigtable-bench-venv
source bigtable-bench-venv/bin/activate
```

On Windows, activate the environment with:

```powershell
bigtable-bench-venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install --upgrade pip
pip install google-cloud-bigtable google-auth
```

### 4. Configure Google Cloud

Authenticate for local development:

```bash
gcloud auth login
gcloud config set project YOUR_PROJECT_ID
gcloud auth application-default login
```

Ensure the active identity has the necessary Bigtable permissions.

### 5. Configure the benchmark scripts

Review the project, instance, and table identifiers in each script:

```python
PROJECT_ID = "YOUR_PROJECT_ID"
INSTANCE_ID = "YOUR_INSTANCE_ID"
TABLE_ID = "YOUR_TABLE_ID"
```

Use
