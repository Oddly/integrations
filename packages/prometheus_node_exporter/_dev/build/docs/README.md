# Prometheus Node Exporter Integration

The Prometheus Node Exporter integration collects system metrics from [Prometheus node_exporter](https://github.com/prometheus/node_exporter) instances using Elastic Agent. It scrapes the `/metrics` endpoint via the `prometheus/metrics` input type.

Where possible, metrics are mapped to `system.*` fields matching the Elastic System integration, so existing dashboards and queries work without modification. Additional detail fields are stored under the `node_exporter.*` namespace.

## Compatibility

This integration works with node_exporter 1.x and later.

## Data Streams

### Load

The `load` data stream collects system load averages (1, 5, and 15 minute) from the `node_load1`, `node_load5`, and `node_load15` metrics. Fields map to `system.load.*` (ECS).

{{event "load"}}

{{fields "load"}}

### CPU

The `cpu` data stream collects per-CPU time counters from `node_cpu_seconds_total` and `node_cpu_guest_seconds_total`. One document per CPU core per mode. Values are counters in seconds — use rate aggregations in Kibana to compute CPU utilization percentages. Fields use `node_exporter.cpu.*` namespace since the per-core dimensional model differs from the System integration's aggregated percentages.

{{event "cpu"}}

{{fields "cpu"}}

### Memory

The `memory` data stream collects system memory metrics from `node_memory_*` gauges. Core fields (`total`, `free`, `used.bytes`, `actual.free`, `actual.used.bytes`, `swap.*`) map to `system.memory.*` for dashboard compatibility. Additional detail (buffers, cached, slab, active/inactive, hugepages, etc.) is stored under `node_exporter.memory.*`.

{{event "memory"}}

{{fields "memory"}}

### Filesystem

The `filesystem` data stream collects per-mountpoint filesystem metrics from `node_filesystem_*`. Core fields (`device_name`, `type`, `mount_point`, `total`, `free`, `available`, `used.bytes`, `files`, `free_files`) map to `system.filesystem.*` for dashboard compatibility. Extra status fields (`readonly`, `device_error`) are stored under `node_exporter.filesystem.*`.

{{event "filesystem"}}

{{fields "filesystem"}}

### Disk I/O

The `diskstats` data stream collects per-device disk I/O metrics from `node_disk_*`. Fields map to `system.diskio.*` matching the System integration (`read.count`, `write.count`, `read.bytes`, `write.bytes`, `read.time`, `write.time`, `io.time` in milliseconds). Native seconds-precision values are also available under `node_exporter.diskio.*`. With `rate_counters` enabled, the data stream also computes `system.diskio.iostat.*` fields (`await`, `service_time`, `busy`, per-second rates) matching the Linux integration's iostat metricset.

{{event "diskstats"}}

{{fields "diskstats"}}

### Network

The `network` data stream collects per-interface network metrics from `node_network_*`. Fields map directly to `system.network.*` (`in.bytes`, `out.bytes`, `in.packets`, `out.packets`, `in.errors`, `out.errors`, `in.dropped`, `out.dropped`).

{{event "network"}}

{{fields "network"}}

### Process Summary

The `process_summary` data stream collects system-wide process counts from `node_procs_running`, `node_procs_blocked`, `node_processes_pids`, and `node_processes_threads`. Fields map to `system.process.summary.*` matching the System integration. The `total` and `threads` fields require the `processes` collector to be enabled on node_exporter (`--collector.processes`); `running` and `threads.blocked` are available from the default `stat` collector.

{{event "process_summary"}}

{{fields "process_summary"}}

### Socket Summary

The `socket_summary` data stream collects socket statistics from `node_sockstat_*` and `node_netstat_Tcp_CurrEstab`. Fields map to `system.socket.summary.*` matching the System integration: total sockets in use, TCP allocated/established/time_wait/orphan counts, TCP and UDP memory usage. An extra `node_exporter.socket.tcp.inuse` field captures active TCP connections (sockstat TCP inuse).

{{event "socket_summary"}}

{{fields "socket_summary"}}

## Setup

1. Add the **Prometheus Node Exporter** integration in Fleet.
2. Set **Node Exporter Hosts** to point at your node_exporter instance(s), for example `http://localhost:9100`.
3. Enable or disable individual data streams (load, cpu, memory, filesystem, diskstats, network, process_summary, socket_summary) as needed.
4. If your `/metrics` endpoint requires authentication, provide the **Username** and **Password**.
