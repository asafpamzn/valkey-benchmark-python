# Valkey Benchmark Python

Valkey Benchmark Python is a benchmark tool for Valkey. This tool allows developers to conduct performance testing in Python. It supports various testing scenarios, including throughput testing, latency measurements, and custom command benchmarking.

Key Advantages:

1. **Enhanced Python Developer Experience** Built with native Valkey clients in Python, providing authentic end-to-end performance measurements in your production environment.

2. **Advanced Cluster Testing**: Leverages the [valkey-GLIDE](https://github.com/valkey-io/valkey-glide) client to support:
   - Dynamic cluster topology updates
   - Read replica operations

3. **Extensible Command Framework**:
   - Supports custom commands and complex operations
   - Test Lua scripts and multi-key transactions
   - Benchmark your specific use cases and data patterns

## Installation

1. Clone the repository:
    ```bash
    git clone <repository-url>
    cd valkey-benchmark-python
    ```

2. Create and activate virtual environment (recommended):
    ```bash
    python -m venv myenv
    source myenv/bin/activate  # On Linux/Mac
    ```

3. Install dependencies:
    ```bash
    pip install valkey-glide
    ```

## Dependencies

This tool requires the following Python packages:
- `valkey-glide`: Valkey GLIDE client library

## Basic Usage

Run a basic benchmark:
```bash
python valkey-benchmark.py -H localhost -p 6379
```

Common usage patterns:
```bash
# Run SET benchmark with 50 parallel clients
python valkey-benchmark.py -c 50 -t set

# Run GET benchmark with rate limiting
python valkey-benchmark.py -t get --qps 1000

# Run benchmark for specific duration
python valkey-benchmark.py --test-duration 60

# Run benchmark with sequential keys
python valkey-benchmark.py --sequential 1000000
```

## Configuration Options

### Basic Options
- `-H, --host <hostname>`: Server hostname (default: "127.0.0.1")
- `-p, --port <port>`: Server port (default: 6379)
- `-c, --clients <num>`: Number of parallel connections (default: 50)
- `-n, --requests <num>`: Total number of requests (default: 100000)
- `-d, --datasize <bytes>`: Data size for SET operations (default: 3)
- `-t, --type <command>`: Command to benchmark (e.g., SET, GET)

### Advanced Options
- `--threads <num>`: Number of worker threads (default: 1)
- `--test-duration <seconds>`: Run test for specified duration
- `--sequential <keyspace>`: Use sequential keys
- `-r, --random <keyspace>`: Use random keys from keyspace

### Rate Limiting Options
- `--qps <num>`: Limit queries per second
- `--start-qps <num>`: Starting QPS for dynamic rate
- `--end-qps <num>`: Target QPS for dynamic rate
- `--qps-change-interval <seconds>`: Interval for QPS changes
- `--qps-change <num>`: QPS change amount per interval

### Security Options
- `--tls`: Enable TLS connection

### Cluster Options
- `--cluster`: Use cluster client
- `--read-from-replica`: Read from replica nodes

## Test Scenarios

### Throughput Testing
```bash
# Maximum throughput test
python valkey-benchmark.py -c 100 -n 1000000

# Rate-limited test
python valkey-benchmark.py -c 50 --qps 5000
```

### Latency Testing
```bash
# Low-concurrency latency test
python valkey-benchmark.py -c 1 -n 10000

# High-concurrency latency test
python valkey-benchmark.py -c 200 -n 100000
```

### Duration-based Testing
```bash
# Run test for 5 minutes
python valkey-benchmark.py --test-duration 300
```

### Key Space Testing
```bash
# Sequential keys
python valkey-benchmark.py --sequential 1000000

# Random keys
python valkey-benchmark.py -r 1000000
```

## Output and Statistics

The benchmark tool provides real-time and final statistics including:

### Real-time Metrics
```
[1.5s] Progress: 1,234/100,000 (1.2%), RPS: current=1,234 avg=1,230.5, Errors: 0 | Latency (ms): avg=0.12 p50=0.11 p95=0.15 p99=0.18
```

### Final Report
```
Final Results:
=============
Total time: 20.01 seconds
Requests completed: 1365377
Requests per second: 68234.73
Total errors: 0

Latency Statistics (ms):
=====================
Minimum: 0.000
Average: 0.116
Maximum: 11.000
Median (p50): 0.000
95th percentile: 1.000
99th percentile: 1.000

Latency Distribution:
====================
<= 0.1 ms: 88.47% (1208014 requests)
<= 0.5 ms: 0.00% (0 requests)
<= 1.0 ms: 11.50% (157005 requests)
...
```

## Custom Commands

To implement custom commands, create a Python file with your custom command implementation:

```python
# custom_commands.py
from typing import Any

class CustomCommands:
    def __init__(self):
        self.counter = 0

    async def execute(self, client: Any) -> bool:
        """Execute custom command with the client"""
        try:
            key = f'custom:key:{self.counter}'
            self.counter += 1
            
            await client.set(key, 'custom:value')
            return True
        except Exception as e:
            print(f'Custom command error: {str(e)}')
            return False
```

Run custom command benchmark:
```bash
python valkey-benchmark.py -t custom --custom-command-file custom_commands.py
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
