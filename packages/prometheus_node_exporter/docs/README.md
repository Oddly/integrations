# Prometheus Node Exporter Integration

The Prometheus Node Exporter integration collects system metrics from [Prometheus node_exporter](https://github.com/prometheus/node_exporter) instances using Elastic Agent. It scrapes the `/metrics` endpoint via the `prometheus/metrics` input type.

Where possible, metrics are mapped to `system.*` fields matching the Elastic System integration, so existing dashboards and queries work without modification. Additional detail fields are stored under the `node_exporter.*` namespace.

## Compatibility

This integration works with node_exporter 1.x and later.

## Data Streams

### Load

The `load` data stream collects system load averages (1, 5, and 15 minute) from the `node_load1`, `node_load5`, and `node_load15` metrics. Fields map to `system.load.*` (ECS).

An example event for `load` looks as following:

```json
{
    "@timestamp": "2026-02-22T12:00:00.000Z",
    "data_stream": {
        "type": "metrics",
        "dataset": "prometheus_node_exporter.load",
        "namespace": "default"
    },
    "event": {
        "kind": "metric",
        "module": "prometheus_node_exporter",
        "dataset": "prometheus_node_exporter.load"
    },
    "system": {
        "load": {
            "1": 0.42,
            "5": 0.35,
            "15": 0.28,
            "cores": 16,
            "norm": {
                "1": 0.02625,
                "5": 0.021875,
                "15": 0.0175
            }
        },
        "uptime": {
            "duration": {
                "ms": 604800000
            }
        }
    },
    "service": {
        "address": "localhost:9100",
        "name": "node"
    },
    "host": {
        "name": "myhost"
    }
}
```

**Exported fields**

| Field | Description | Type | Unit | Metric Type |
|---|---|---|---|---|
| @timestamp | Event timestamp. | date |  |  |
| cloud.account.id | Cloud account ID. | keyword |  |  |
| cloud.availability_zone | Cloud availability zone. | keyword |  |  |
| cloud.image.id | Image ID for the cloud instance. | keyword |  |  |
| cloud.instance.id | Cloud instance ID. | keyword |  |  |
| cloud.provider | Cloud provider name. | keyword |  |  |
| cloud.region | Cloud region. | keyword |  |  |
| container.id | Container ID. | keyword |  |  |
| data_stream.dataset | Data stream dataset. | constant_keyword |  |  |
| data_stream.namespace | Data stream namespace. | constant_keyword |  |  |
| data_stream.type | Data stream type. | constant_keyword |  |  |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |  |  |
| error.message | Error message. | match_only_text |  |  |
| event.dataset | Event dataset. | constant_keyword |  |  |
| event.kind | This is one of four ECS Categorization Fields, and indicates the highest level in the ECS category hierarchy. `event.kind` gives high-level information about what type of information the event contains, without being specific to the contents of the event. For example, values of this field distinguish alert events from metric events. The value of this field can be used to inform how these kinds of events should be handled. They may warrant different retention, different access control, it may also help understand whether the data is coming in at a regular interval or not. | keyword |  |  |
| event.module | Event module. | constant_keyword |  |  |
| host.containerized | Whether the host is a container. | boolean |  |  |
| host.name | Host name. | keyword |  |  |
| host.os.build | OS build information. | keyword |  |  |
| host.os.codename | OS codename, if any. | keyword |  |  |
| prometheus.labels.\* | Prometheus metric labels. | keyword |  |  |
| service.address | Address where data about this service was collected from. This should be a URI, network address (ipv4:port or [ipv6]:port) or a resource path (sockets). | keyword |  |  |
| service.name | Name of the service data is collected from. The name of the service is normally user given. This allows for distributed services that run on multiple hosts to correlate the related instances based on the name. In the case of Elasticsearch the `service.name` could contain the cluster name. For Beats the `service.name` is by default a copy of the `service.type` field if no name is specified. | keyword |  |  |
| system.load.1 | System load average over 1 minute. | scaled_float |  | gauge |
| system.load.15 | System load average over 15 minutes. | scaled_float |  | gauge |
| system.load.5 | System load average over 5 minutes. | scaled_float |  | gauge |
| system.load.cores | Number of CPU cores. Used to compute normalized load averages. | long |  | gauge |
| system.load.norm.1 | Normalized system load average over 1 minute (load / cores). | scaled_float | percent | gauge |
| system.load.norm.15 | Normalized system load average over 15 minutes (load / cores). | scaled_float | percent | gauge |
| system.load.norm.5 | Normalized system load average over 5 minutes (load / cores). | scaled_float | percent | gauge |
| system.uptime.duration.ms | System uptime in milliseconds, computed from node_boot_time_seconds. | long | ms | gauge |


### CPU

The `cpu` data stream collects per-CPU time counters from `node_cpu_seconds_total` and `node_cpu_guest_seconds_total`. One document per CPU core per mode. Values are counters in seconds — use rate aggregations in Kibana to compute CPU utilization percentages. Fields use `node_exporter.cpu.*` namespace since the per-core dimensional model differs from the System integration's aggregated percentages.

An example event for `cpu` looks as following:

```json
{
    "@timestamp": "2026-02-22T12:00:00.000Z",
    "data_stream": {
        "type": "metrics",
        "dataset": "prometheus_node_exporter.cpu",
        "namespace": "default"
    },
    "event": {
        "kind": "metric",
        "module": "prometheus_node_exporter",
        "dataset": "prometheus_node_exporter.cpu"
    },
    "node_exporter": {
        "cpu": {
            "id": "0",
            "mode": "user",
            "seconds": {
                "total": 1234.56
            },
            "guest_seconds": {
                "total": 0
            }
        }
    },
    "service": {
        "address": "localhost:9100",
        "name": "node"
    },
    "host": {
        "name": "myhost"
    }
}
```

**Exported fields**

| Field | Description | Type | Unit | Metric Type |
|---|---|---|---|---|
| @timestamp | Event timestamp. | date |  |  |
| cloud.account.id | Cloud account ID. | keyword |  |  |
| cloud.availability_zone | Cloud availability zone. | keyword |  |  |
| cloud.image.id | Image ID for the cloud instance. | keyword |  |  |
| cloud.instance.id | Cloud instance ID. | keyword |  |  |
| cloud.provider | Cloud provider name. | keyword |  |  |
| cloud.region | Cloud region. | keyword |  |  |
| container.id | Container ID. | keyword |  |  |
| data_stream.dataset | Data stream dataset. | constant_keyword |  |  |
| data_stream.namespace | Data stream namespace. | constant_keyword |  |  |
| data_stream.type | Data stream type. | constant_keyword |  |  |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |  |  |
| error.message | Error message. | match_only_text |  |  |
| event.dataset | Event dataset. | constant_keyword |  |  |
| event.kind | This is one of four ECS Categorization Fields, and indicates the highest level in the ECS category hierarchy. `event.kind` gives high-level information about what type of information the event contains, without being specific to the contents of the event. For example, values of this field distinguish alert events from metric events. The value of this field can be used to inform how these kinds of events should be handled. They may warrant different retention, different access control, it may also help understand whether the data is coming in at a regular interval or not. | keyword |  |  |
| event.module | Event module. | constant_keyword |  |  |
| host.containerized | Whether the host is a container. | boolean |  |  |
| host.name | Host name. | keyword |  |  |
| host.os.build | OS build information. | keyword |  |  |
| host.os.codename | OS codename, if any. | keyword |  |  |
| node_exporter.cpu.guest_seconds.total | Total seconds spent running a virtual CPU for guest operating systems (counter). | double | s | counter |
| node_exporter.cpu.id | CPU core identifier (from Prometheus cpu label). | keyword |  |  |
| node_exporter.cpu.mode | CPU mode (user, system, idle, iowait, irq, softirq, steal, nice, guest, guest_nice). | keyword |  |  |
| node_exporter.cpu.seconds.total | Total seconds the CPU has spent in this mode (counter). | double | s | counter |
| prometheus.labels.\* | Prometheus metric labels. | keyword |  |  |
| service.address | Address where data about this service was collected from. This should be a URI, network address (ipv4:port or [ipv6]:port) or a resource path (sockets). | keyword |  |  |
| service.name | Name of the service data is collected from. The name of the service is normally user given. This allows for distributed services that run on multiple hosts to correlate the related instances based on the name. In the case of Elasticsearch the `service.name` could contain the cluster name. For Beats the `service.name` is by default a copy of the `service.type` field if no name is specified. | keyword |  |  |


### Memory

The `memory` data stream collects system memory metrics from `node_memory_*` gauges. Core fields (`total`, `free`, `used.bytes`, `actual.free`, `actual.used.bytes`, `swap.*`) map to `system.memory.*` for dashboard compatibility. Additional detail (buffers, cached, slab, active/inactive, hugepages, etc.) is stored under `node_exporter.memory.*`.

An example event for `memory` looks as following:

```json
{
    "@timestamp": "2026-02-22T12:00:00.000Z",
    "data_stream": {
        "type": "metrics",
        "dataset": "prometheus_node_exporter.memory",
        "namespace": "default"
    },
    "event": {
        "kind": "metric",
        "module": "prometheus_node_exporter",
        "dataset": "prometheus_node_exporter.memory"
    },
    "system": {
        "memory": {
            "total": 16694882304,
            "free": 1073741824,
            "cached": 6442450944,
            "used": {
                "bytes": 15621140480,
                "pct": 0.9357
            },
            "actual": {
                "free": 8589934592,
                "used": {
                    "bytes": 8104947712,
                    "pct": 0.4855
                }
            },
            "swap": {
                "total": 8589934592,
                "free": 8589934592,
                "used": {
                    "bytes": 0,
                    "pct": 0.0
                }
            }
        }
    },
    "node_exporter": {
        "memory": {
            "total_bytes": 16694882304,
            "free_bytes": 1073741824,
            "available_bytes": 8589934592,
            "swap": {
                "total_bytes": 8589934592,
                "free_bytes": 8589934592
            },
            "buffers_bytes": 268435456,
            "cached_bytes": 6442450944,
            "active_bytes": 5368709120,
            "inactive_bytes": 8589934592,
            "slab_bytes": 536870912,
            "dirty_bytes": 1048576,
            "hugepages": {
                "total": 0,
                "free": 0
            }
        }
    },
    "service": {
        "address": "localhost:9100",
        "name": "node"
    },
    "host": {
        "name": "myhost"
    }
}
```

**Exported fields**

| Field | Description | Type | Unit | Metric Type |
|---|---|---|---|---|
| @timestamp | Event timestamp. | date |  |  |
| cloud.account.id | Cloud account ID. | keyword |  |  |
| cloud.availability_zone | Cloud availability zone. | keyword |  |  |
| cloud.image.id | Image ID for the cloud instance. | keyword |  |  |
| cloud.instance.id | Cloud instance ID. | keyword |  |  |
| cloud.provider | Cloud provider name. | keyword |  |  |
| cloud.region | Cloud region. | keyword |  |  |
| container.id | Container ID. | keyword |  |  |
| data_stream.dataset | Data stream dataset. | constant_keyword |  |  |
| data_stream.namespace | Data stream namespace. | constant_keyword |  |  |
| data_stream.type | Data stream type. | constant_keyword |  |  |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |  |  |
| error.message | Error message. | match_only_text |  |  |
| event.dataset | Event dataset. | constant_keyword |  |  |
| event.kind | This is one of four ECS Categorization Fields, and indicates the highest level in the ECS category hierarchy. `event.kind` gives high-level information about what type of information the event contains, without being specific to the contents of the event. For example, values of this field distinguish alert events from metric events. The value of this field can be used to inform how these kinds of events should be handled. They may warrant different retention, different access control, it may also help understand whether the data is coming in at a regular interval or not. | keyword |  |  |
| event.module | Event module. | constant_keyword |  |  |
| host.containerized | Whether the host is a container. | boolean |  |  |
| host.name | Host name. | keyword |  |  |
| host.os.build | OS build information. | keyword |  |  |
| host.os.codename | OS codename, if any. | keyword |  |  |
| node_exporter.memory.active_anon_bytes | Active anonymous memory in bytes. | long | byte | gauge |
| node_exporter.memory.active_bytes | Active memory in bytes (Active). | long | byte | gauge |
| node_exporter.memory.active_file_bytes | Active file-backed memory in bytes. | long | byte | gauge |
| node_exporter.memory.available_bytes | Estimated available RAM in bytes (MemAvailable). | long | byte | gauge |
| node_exporter.memory.buffers_bytes | Memory used by kernel buffers in bytes (Buffers). | long | byte | gauge |
| node_exporter.memory.cached_bytes | Memory used by page cache in bytes (Cached). | long | byte | gauge |
| node_exporter.memory.commit_limit_bytes | Total memory available for commit in bytes. | long | byte | gauge |
| node_exporter.memory.committed_as_bytes | Total committed memory in bytes. | long | byte | gauge |
| node_exporter.memory.dirty_bytes | Dirty memory in bytes. | long | byte | gauge |
| node_exporter.memory.free_bytes | Free RAM in bytes (MemFree). | long | byte | gauge |
| node_exporter.memory.hugepages.free | Number of free huge pages. | long |  | gauge |
| node_exporter.memory.hugepages.total | Total number of huge pages. | long |  | gauge |
| node_exporter.memory.hugepagesize_bytes | Size of each huge page in bytes. | long | byte | gauge |
| node_exporter.memory.inactive_anon_bytes | Inactive anonymous memory in bytes. | long | byte | gauge |
| node_exporter.memory.inactive_bytes | Inactive memory in bytes (Inactive). | long | byte | gauge |
| node_exporter.memory.inactive_file_bytes | Inactive file-backed memory in bytes. | long | byte | gauge |
| node_exporter.memory.mapped_bytes | Memory-mapped file memory in bytes. | long | byte | gauge |
| node_exporter.memory.page_tables_bytes | Memory used by page tables in bytes. | long | byte | gauge |
| node_exporter.memory.shmem_bytes | Shared memory in bytes. | long | byte | gauge |
| node_exporter.memory.slab_bytes | Kernel slab memory in bytes. | long | byte | gauge |
| node_exporter.memory.sreclaimable_bytes | Reclaimable slab memory in bytes. | long | byte | gauge |
| node_exporter.memory.sunreclaim_bytes | Unreclaimable slab memory in bytes. | long | byte | gauge |
| node_exporter.memory.swap.cached_bytes | Cached swap in bytes (SwapCached). | long | byte | gauge |
| node_exporter.memory.swap.free_bytes | Free swap space in bytes (SwapFree). | long | byte | gauge |
| node_exporter.memory.swap.total_bytes | Total swap space in bytes (SwapTotal). | long | byte | gauge |
| node_exporter.memory.total_bytes | Total usable RAM in bytes (MemTotal). | long | byte | gauge |
| node_exporter.memory.writeback_bytes | Memory being written back to disk in bytes. | long | byte | gauge |
| node_exporter.memory.writeback_tmp_bytes | Temporary writeback memory in bytes. | long | byte | gauge |
| prometheus.labels.\* | Prometheus metric labels. | keyword |  |  |
| service.address | Address where data about this service was collected from. This should be a URI, network address (ipv4:port or [ipv6]:port) or a resource path (sockets). | keyword |  |  |
| service.name | Name of the service data is collected from. The name of the service is normally user given. This allows for distributed services that run on multiple hosts to correlate the related instances based on the name. In the case of Elasticsearch the `service.name` could contain the cluster name. For Beats the `service.name` is by default a copy of the `service.type` field if no name is specified. | keyword |  |  |
| system.memory.actual.free | Estimated available RAM in bytes (MemAvailable). | long | byte | gauge |
| system.memory.actual.used.bytes | Actual used RAM in bytes (total - available). | long | byte | gauge |
| system.memory.actual.used.pct | The percentage of actual used memory (actual.used.bytes / total). | scaled_float | percent | gauge |
| system.memory.cached | Total cached memory in bytes (Cached). | long | byte | gauge |
| system.memory.free | Free RAM in bytes (MemFree). | long | byte | gauge |
| system.memory.swap.free | Free swap space in bytes. | long | byte | gauge |
| system.memory.swap.total | Total swap space in bytes. | long | byte | gauge |
| system.memory.swap.used.bytes | Used swap space in bytes (total - free). | long | byte | gauge |
| system.memory.swap.used.pct | The percentage of used swap (swap.used.bytes / swap.total). | scaled_float | percent | gauge |
| system.memory.total | Total usable RAM in bytes. | long | byte | gauge |
| system.memory.used.bytes | Used RAM in bytes (total - free). | long | byte | gauge |
| system.memory.used.pct | The percentage of used memory (used.bytes / total). | scaled_float | percent | gauge |


### Filesystem

The `filesystem` data stream collects per-mountpoint filesystem metrics from `node_filesystem_*`. Core fields (`device_name`, `type`, `mount_point`, `total`, `free`, `available`, `used.bytes`, `files`, `free_files`) map to `system.filesystem.*` for dashboard compatibility. Extra status fields (`readonly`, `device_error`) are stored under `node_exporter.filesystem.*`.

An example event for `filesystem` looks as following:

```json
{
    "@timestamp": "2026-02-22T12:00:00.000Z",
    "data_stream": {
        "type": "metrics",
        "dataset": "prometheus_node_exporter.filesystem",
        "namespace": "default"
    },
    "event": {
        "kind": "metric",
        "module": "prometheus_node_exporter",
        "dataset": "prometheus_node_exporter.filesystem"
    },
    "system": {
        "filesystem": {
            "device_name": "/dev/sda1",
            "type": "ext4",
            "mount_point": "/",
            "total": 107374182400,
            "free": 53687091200,
            "available": 48318382080,
            "used": {
                "bytes": 53687091200,
                "pct": 0.5
            },
            "files": 6553600,
            "free_files": 6000000
        }
    },
    "node_exporter": {
        "filesystem": {
            "device": "/dev/sda1",
            "type": "ext4",
            "mount_point": "/",
            "size_bytes": 107374182400,
            "free_bytes": 53687091200,
            "available_bytes": 48318382080,
            "used_bytes": 53687091200,
            "files": 6553600,
            "files_free": 6000000,
            "readonly": 0,
            "device_error": 0
        }
    },
    "service": {
        "address": "localhost:9100",
        "name": "node"
    },
    "host": {
        "name": "myhost"
    }
}
```

**Exported fields**

| Field | Description | Type | Unit | Metric Type |
|---|---|---|---|---|
| @timestamp | Event timestamp. | date |  |  |
| cloud.account.id | Cloud account ID. | keyword |  |  |
| cloud.availability_zone | Cloud availability zone. | keyword |  |  |
| cloud.image.id | Image ID for the cloud instance. | keyword |  |  |
| cloud.instance.id | Cloud instance ID. | keyword |  |  |
| cloud.provider | Cloud provider name. | keyword |  |  |
| cloud.region | Cloud region. | keyword |  |  |
| container.id | Container ID. | keyword |  |  |
| data_stream.dataset | Data stream dataset. | constant_keyword |  |  |
| data_stream.namespace | Data stream namespace. | constant_keyword |  |  |
| data_stream.type | Data stream type. | constant_keyword |  |  |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |  |  |
| error.message | Error message. | match_only_text |  |  |
| event.dataset | Event dataset. | constant_keyword |  |  |
| event.kind | This is one of four ECS Categorization Fields, and indicates the highest level in the ECS category hierarchy. `event.kind` gives high-level information about what type of information the event contains, without being specific to the contents of the event. For example, values of this field distinguish alert events from metric events. The value of this field can be used to inform how these kinds of events should be handled. They may warrant different retention, different access control, it may also help understand whether the data is coming in at a regular interval or not. | keyword |  |  |
| event.module | Event module. | constant_keyword |  |  |
| host.containerized | Whether the host is a container. | boolean |  |  |
| host.name | Host name. | keyword |  |  |
| host.os.build | OS build information. | keyword |  |  |
| host.os.codename | OS codename, if any. | keyword |  |  |
| node_exporter.filesystem.available_bytes | Available space in bytes (for unprivileged users). | long | byte | gauge |
| node_exporter.filesystem.device | Filesystem device name. | keyword |  |  |
| node_exporter.filesystem.device_error | Whether a device error occurred while getting stats (1 = error, 0 = ok). | double |  | gauge |
| node_exporter.filesystem.files | Total number of inodes. | long |  | gauge |
| node_exporter.filesystem.files_free | Number of free inodes. | long |  | gauge |
| node_exporter.filesystem.free_bytes | Free space in bytes (including reserved blocks). | long | byte | gauge |
| node_exporter.filesystem.mount_point | Filesystem mount point. | keyword |  |  |
| node_exporter.filesystem.readonly | Whether the filesystem is mounted read-only (1 = readonly, 0 = readwrite). | double |  | gauge |
| node_exporter.filesystem.size_bytes | Total filesystem size in bytes. | long | byte | gauge |
| node_exporter.filesystem.type | Filesystem type (ext4, xfs, tmpfs, etc.). | keyword |  |  |
| node_exporter.filesystem.used_bytes | Used space in bytes (size - free). | long | byte | gauge |
| prometheus.labels.\* | Prometheus metric labels. | keyword |  |  |
| service.address | Address where data about this service was collected from. This should be a URI, network address (ipv4:port or [ipv6]:port) or a resource path (sockets). | keyword |  |  |
| service.name | Name of the service data is collected from. The name of the service is normally user given. This allows for distributed services that run on multiple hosts to correlate the related instances based on the name. In the case of Elasticsearch the `service.name` could contain the cluster name. For Beats the `service.name` is by default a copy of the `service.type` field if no name is specified. | keyword |  |  |
| system.filesystem.available | Available space in bytes (for unprivileged users). | long | byte | gauge |
| system.filesystem.device_name | Filesystem device name. | keyword |  |  |
| system.filesystem.files | Total number of inodes. | long |  | gauge |
| system.filesystem.free | Free space in bytes (including reserved blocks). | long | byte | gauge |
| system.filesystem.free_files | Number of free inodes. | long |  | gauge |
| system.filesystem.mount_point | Filesystem mount point. | keyword |  |  |
| system.filesystem.total | Total filesystem size in bytes. | long | byte | gauge |
| system.filesystem.type | Filesystem type (ext4, xfs, tmpfs, etc.). | keyword |  |  |
| system.filesystem.used.bytes | Used space in bytes (total - free). | long | byte | gauge |
| system.filesystem.used.pct | The percentage of used disk space (used / (used + available)). | scaled_float | percent | gauge |


### Disk I/O

The `diskstats` data stream collects per-device disk I/O metrics from `node_disk_*`. Fields map to `system.diskio.*` matching the System integration (`read.count`, `write.count`, `read.bytes`, `write.bytes`, `read.time`, `write.time`, `io.time` in milliseconds). Native seconds-precision values are also available under `node_exporter.diskio.*`. With `rate_counters` enabled, the data stream also computes `system.diskio.iostat.*` fields (`await`, `service_time`, `busy`, per-second rates) matching the Linux integration's iostat metricset.

An example event for `diskstats` looks as following:

```json
{
    "@timestamp": "2026-02-22T12:00:00.000Z",
    "data_stream": {
        "type": "metrics",
        "dataset": "prometheus_node_exporter.diskstats",
        "namespace": "default"
    },
    "event": {
        "kind": "metric",
        "module": "prometheus_node_exporter",
        "dataset": "prometheus_node_exporter.diskstats"
    },
    "system": {
        "diskio": {
            "name": "sda",
            "read": {
                "count": 20287,
                "bytes": 1254116352,
                "time": 6364
            },
            "write": {
                "count": 5432100,
                "bytes": 98765432320,
                "time": 1802690
            },
            "io": {
                "time": 1023604
            },
            "iostat": {
                "read": {
                    "request_per_sec": 0.5,
                    "per_sec_bytes": 25600.0,
                    "await": 4.0
                },
                "write": {
                    "request_per_sec": 120.3,
                    "per_sec_bytes": 5242880.0,
                    "await": 1.247
                },
                "await": 1.258,
                "service_time": 0.662,
                "busy": 8.0
            }
        }
    },
    "node_exporter": {
        "diskio": {
            "name": "sda",
            "read": {
                "count": 20287,
                "bytes": 1254116352,
                "time_seconds": 6.364
            },
            "write": {
                "count": 5432100,
                "bytes": 98765432320,
                "time_seconds": 1802.69
            },
            "io": {
                "time_seconds": 1023.604
            }
        }
    },
    "service": {
        "address": "localhost:9100",
        "name": "node"
    },
    "host": {
        "name": "myhost"
    }
}
```

**Exported fields**

| Field | Description | Type | Unit | Metric Type |
|---|---|---|---|---|
| @timestamp | Event timestamp. | date |  |  |
| cloud.account.id | Cloud account ID. | keyword |  |  |
| cloud.availability_zone | Cloud availability zone. | keyword |  |  |
| cloud.image.id | Image ID for the cloud instance. | keyword |  |  |
| cloud.instance.id | Cloud instance ID. | keyword |  |  |
| cloud.provider | Cloud provider name. | keyword |  |  |
| cloud.region | Cloud region. | keyword |  |  |
| container.id | Container ID. | keyword |  |  |
| data_stream.dataset | Data stream dataset. | constant_keyword |  |  |
| data_stream.namespace | Data stream namespace. | constant_keyword |  |  |
| data_stream.type | Data stream type. | constant_keyword |  |  |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |  |  |
| error.message | Error message. | match_only_text |  |  |
| event.dataset | Event dataset. | constant_keyword |  |  |
| event.kind | This is one of four ECS Categorization Fields, and indicates the highest level in the ECS category hierarchy. `event.kind` gives high-level information about what type of information the event contains, without being specific to the contents of the event. For example, values of this field distinguish alert events from metric events. The value of this field can be used to inform how these kinds of events should be handled. They may warrant different retention, different access control, it may also help understand whether the data is coming in at a regular interval or not. | keyword |  |  |
| event.module | Event module. | constant_keyword |  |  |
| host.containerized | Whether the host is a container. | boolean |  |  |
| host.name | Host name. | keyword |  |  |
| host.os.build | OS build information. | keyword |  |  |
| host.os.codename | OS codename, if any. | keyword |  |  |
| node_exporter.diskio.io.time_seconds | Total time spent doing I/O in seconds (native node_exporter unit). | double | s | counter |
| node_exporter.diskio.name | Disk device name. | keyword |  |  |
| node_exporter.diskio.read.bytes | Total bytes read. | long | byte | counter |
| node_exporter.diskio.read.count | Total completed read operations. | long |  | counter |
| node_exporter.diskio.read.time_seconds | Total time spent reading in seconds (native node_exporter unit). | double | s | counter |
| node_exporter.diskio.write.bytes | Total bytes written. | long | byte | counter |
| node_exporter.diskio.write.count | Total completed write operations. | long |  | counter |
| node_exporter.diskio.write.time_seconds | Total time spent writing in seconds (native node_exporter unit). | double | s | counter |
| prometheus.labels.\* | Prometheus metric labels. | keyword |  |  |
| service.address | Address where data about this service was collected from. This should be a URI, network address (ipv4:port or [ipv6]:port) or a resource path (sockets). | keyword |  |  |
| service.name | Name of the service data is collected from. The name of the service is normally user given. This allows for distributed services that run on multiple hosts to correlate the related instances based on the name. In the case of Elasticsearch the `service.name` could contain the cluster name. For Beats the `service.name` is by default a copy of the `service.type` field if no name is specified. | keyword |  |  |
| system.diskio.io.time | Total time spent doing I/O in milliseconds. | long |  | counter |
| system.diskio.iostat.await | Average time for I/O requests in milliseconds. | double |  | gauge |
| system.diskio.iostat.busy | Percentage of time the device was busy with I/O operations. | double |  | gauge |
| system.diskio.iostat.read.await | Average time for read requests in milliseconds. | double |  | gauge |
| system.diskio.iostat.read.per_sec_bytes | Bytes read per second. | double | byte | gauge |
| system.diskio.iostat.read.request_per_sec | Read requests per second. | double |  | gauge |
| system.diskio.iostat.service_time | Average service time for I/O requests in milliseconds. | double |  | gauge |
| system.diskio.iostat.write.await | Average time for write requests in milliseconds. | double |  | gauge |
| system.diskio.iostat.write.per_sec_bytes | Bytes written per second. | double | byte | gauge |
| system.diskio.iostat.write.request_per_sec | Write requests per second. | double |  | gauge |
| system.diskio.name | Disk device name. | keyword |  |  |
| system.diskio.read.bytes | Total bytes read. | long | byte | counter |
| system.diskio.read.count | Total completed read operations. | long |  | counter |
| system.diskio.read.time | Total time spent reading in milliseconds. | long |  | counter |
| system.diskio.write.bytes | Total bytes written. | long | byte | counter |
| system.diskio.write.count | Total completed write operations. | long |  | counter |
| system.diskio.write.time | Total time spent writing in milliseconds. | long |  | counter |


### Network

The `network` data stream collects per-interface network metrics from `node_network_*`. Fields map directly to `system.network.*` (`in.bytes`, `out.bytes`, `in.packets`, `out.packets`, `in.errors`, `out.errors`, `in.dropped`, `out.dropped`).

An example event for `network` looks as following:

```json
{
    "@timestamp": "2026-02-22T12:00:00.000Z",
    "data_stream": {
        "type": "metrics",
        "dataset": "prometheus_node_exporter.network",
        "namespace": "default"
    },
    "event": {
        "kind": "metric",
        "module": "prometheus_node_exporter",
        "dataset": "prometheus_node_exporter.network"
    },
    "system": {
        "network": {
            "name": "ens18",
            "in": {
                "bytes": 5485550525,
                "packets": 8765432,
                "errors": 0,
                "dropped": 42
            },
            "out": {
                "bytes": 3210987654,
                "packets": 6543210,
                "errors": 0,
                "dropped": 0
            }
        }
    },
    "service": {
        "address": "localhost:9100",
        "name": "node"
    },
    "host": {
        "name": "myhost"
    }
}
```

**Exported fields**

| Field | Description | Type | Unit | Metric Type |
|---|---|---|---|---|
| @timestamp | Event timestamp. | date |  |  |
| cloud.account.id | Cloud account ID. | keyword |  |  |
| cloud.availability_zone | Cloud availability zone. | keyword |  |  |
| cloud.image.id | Image ID for the cloud instance. | keyword |  |  |
| cloud.instance.id | Cloud instance ID. | keyword |  |  |
| cloud.provider | Cloud provider name. | keyword |  |  |
| cloud.region | Cloud region. | keyword |  |  |
| container.id | Container ID. | keyword |  |  |
| data_stream.dataset | Data stream dataset. | constant_keyword |  |  |
| data_stream.namespace | Data stream namespace. | constant_keyword |  |  |
| data_stream.type | Data stream type. | constant_keyword |  |  |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |  |  |
| error.message | Error message. | match_only_text |  |  |
| event.dataset | Event dataset. | constant_keyword |  |  |
| event.kind | This is one of four ECS Categorization Fields, and indicates the highest level in the ECS category hierarchy. `event.kind` gives high-level information about what type of information the event contains, without being specific to the contents of the event. For example, values of this field distinguish alert events from metric events. The value of this field can be used to inform how these kinds of events should be handled. They may warrant different retention, different access control, it may also help understand whether the data is coming in at a regular interval or not. | keyword |  |  |
| event.module | Event module. | constant_keyword |  |  |
| host.containerized | Whether the host is a container. | boolean |  |  |
| host.name | Host name. | keyword |  |  |
| host.os.build | OS build information. | keyword |  |  |
| host.os.codename | OS codename, if any. | keyword |  |  |
| prometheus.labels.\* | Prometheus metric labels. | keyword |  |  |
| service.address | Address where data about this service was collected from. This should be a URI, network address (ipv4:port or [ipv6]:port) or a resource path (sockets). | keyword |  |  |
| service.name | Name of the service data is collected from. The name of the service is normally user given. This allows for distributed services that run on multiple hosts to correlate the related instances based on the name. In the case of Elasticsearch the `service.name` could contain the cluster name. For Beats the `service.name` is by default a copy of the `service.type` field if no name is specified. | keyword |  |  |
| system.network.in.bytes | Total bytes received. | long | byte | counter |
| system.network.in.dropped | Total incoming packets dropped. | long |  | counter |
| system.network.in.errors | Total receive errors. | long |  | counter |
| system.network.in.packets | Total packets received. | long |  | counter |
| system.network.name | Network interface name. | keyword |  |  |
| system.network.out.bytes | Total bytes transmitted. | long | byte | counter |
| system.network.out.dropped | Total outgoing packets dropped. | long |  | counter |
| system.network.out.errors | Total transmit errors. | long |  | counter |
| system.network.out.packets | Total packets transmitted. | long |  | counter |


### Process Summary

The `process_summary` data stream collects system-wide process counts from `node_procs_running`, `node_procs_blocked`, `node_processes_pids`, and `node_processes_threads`. Fields map to `system.process.summary.*` matching the System integration. The `total` and `threads` fields require the `processes` collector to be enabled on node_exporter (`--collector.processes`); `running` and `threads.blocked` are available from the default `stat` collector.

An example event for `process_summary` looks as following:

```json
{
    "@timestamp": "2026-02-22T12:00:00.000Z",
    "data_stream": {
        "type": "metrics",
        "dataset": "prometheus_node_exporter.process_summary",
        "namespace": "default"
    },
    "event": {
        "kind": "metric",
        "module": "prometheus_node_exporter",
        "dataset": "prometheus_node_exporter.process_summary"
    },
    "system": {
        "process": {
            "summary": {
                "total": 312,
                "running": 2,
                "dead": 0
            }
        }
    },
    "node_exporter": {
        "process": {
            "threads": 1547
        }
    },
    "service": {
        "address": "localhost:9100",
        "name": "node"
    },
    "host": {
        "name": "myhost"
    }
}
```

**Exported fields**

| Field | Description | Type | Metric Type |
|---|---|---|---|
| @timestamp | Event timestamp. | date |  |
| cloud.account.id | Cloud account ID. | keyword |  |
| cloud.availability_zone | Cloud availability zone. | keyword |  |
| cloud.image.id | Image ID for the cloud instance. | keyword |  |
| cloud.instance.id | Cloud instance ID. | keyword |  |
| cloud.provider | Cloud provider name. | keyword |  |
| cloud.region | Cloud region. | keyword |  |
| container.id | Container ID. | keyword |  |
| data_stream.dataset | Data stream dataset. | constant_keyword |  |
| data_stream.namespace | Data stream namespace. | constant_keyword |  |
| data_stream.type | Data stream type. | constant_keyword |  |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |  |
| error.message | Error message. | match_only_text |  |
| event.dataset | Event dataset. | constant_keyword |  |
| event.kind | This is one of four ECS Categorization Fields, and indicates the highest level in the ECS category hierarchy. `event.kind` gives high-level information about what type of information the event contains, without being specific to the contents of the event. For example, values of this field distinguish alert events from metric events. The value of this field can be used to inform how these kinds of events should be handled. They may warrant different retention, different access control, it may also help understand whether the data is coming in at a regular interval or not. | keyword |  |
| event.module | Event module. | constant_keyword |  |
| host.containerized | Whether the host is a container. | boolean |  |
| host.name | Host name. | keyword |  |
| host.os.build | OS build information. | keyword |  |
| host.os.codename | OS codename, if any. | keyword |  |
| node_exporter.process.threads | Total number of threads. | long | gauge |
| prometheus.labels.\* | Prometheus metric labels. | keyword |  |
| service.address | Address where data about this service was collected from. This should be a URI, network address (ipv4:port or [ipv6]:port) or a resource path (sockets). | keyword |  |
| service.name | Name of the service data is collected from. The name of the service is normally user given. This allows for distributed services that run on multiple hosts to correlate the related instances based on the name. In the case of Elasticsearch the `service.name` could contain the cluster name. For Beats the `service.name` is by default a copy of the `service.type` field if no name is specified. | keyword |  |
| system.process.summary.dead | Number of processes in uninterruptible sleep (D state). | long | gauge |
| system.process.summary.idle | Number of processes in idle state. | long | gauge |
| system.process.summary.running | Number of processes in running state. | long | gauge |
| system.process.summary.sleeping | Number of processes in sleeping state. | long | gauge |
| system.process.summary.stopped | Number of processes in stopped state. | long | gauge |
| system.process.summary.total | Total number of processes (PIDs). | long | gauge |
| system.process.summary.unknown | Number of processes in unknown state. | long | gauge |
| system.process.summary.zombie | Number of zombie processes. | long | gauge |


### Socket Summary

The `socket_summary` data stream collects socket statistics from `node_sockstat_*` and `node_netstat_Tcp_CurrEstab`. Fields map to `system.socket.summary.*` matching the System integration: total sockets in use, TCP allocated/established/time_wait/orphan counts, TCP and UDP memory usage. An extra `node_exporter.socket.tcp.inuse` field captures active TCP connections (sockstat TCP inuse).

An example event for `socket_summary` looks as following:

```json
{
    "@timestamp": "2026-02-22T12:00:00.000Z",
    "data_stream": {
        "type": "metrics",
        "dataset": "prometheus_node_exporter.socket_summary",
        "namespace": "default"
    },
    "event": {
        "kind": "metric",
        "module": "prometheus_node_exporter",
        "dataset": "prometheus_node_exporter.socket_summary"
    },
    "system": {
        "socket": {
            "summary": {
                "all": {
                    "count": 110
                },
                "tcp": {
                    "all": {
                        "count": 98,
                        "established": 42,
                        "time_wait": 12,
                        "orphan": 0
                    },
                    "memory": 40960
                },
                "udp": {
                    "all": {
                        "count": 12
                    },
                    "memory": 8192
                }
            }
        }
    },
    "node_exporter": {
        "socket": {
            "tcp": {
                "alloc": 98,
                "inuse": 56
            },
            "all": {
                "used": 487
            }
        }
    },
    "service": {
        "address": "localhost:9100",
        "name": "node"
    },
    "host": {
        "name": "myhost"
    }
}
```

**Exported fields**

| Field | Description | Type | Unit | Metric Type |
|---|---|---|---|---|
| @timestamp | Event timestamp. | date |  |  |
| cloud.account.id | Cloud account ID. | keyword |  |  |
| cloud.availability_zone | Cloud availability zone. | keyword |  |  |
| cloud.image.id | Image ID for the cloud instance. | keyword |  |  |
| cloud.instance.id | Cloud instance ID. | keyword |  |  |
| cloud.provider | Cloud provider name. | keyword |  |  |
| cloud.region | Cloud region. | keyword |  |  |
| container.id | Container ID. | keyword |  |  |
| data_stream.dataset | Data stream dataset. | constant_keyword |  |  |
| data_stream.namespace | Data stream namespace. | constant_keyword |  |  |
| data_stream.type | Data stream type. | constant_keyword |  |  |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |  |  |
| error.message | Error message. | match_only_text |  |  |
| event.dataset | Event dataset. | constant_keyword |  |  |
| event.kind | This is one of four ECS Categorization Fields, and indicates the highest level in the ECS category hierarchy. `event.kind` gives high-level information about what type of information the event contains, without being specific to the contents of the event. For example, values of this field distinguish alert events from metric events. The value of this field can be used to inform how these kinds of events should be handled. They may warrant different retention, different access control, it may also help understand whether the data is coming in at a regular interval or not. | keyword |  |  |
| event.module | Event module. | constant_keyword |  |  |
| host.containerized | Whether the host is a container. | boolean |  |  |
| host.name | Host name. | keyword |  |  |
| host.os.build | OS build information. | keyword |  |  |
| host.os.codename | OS codename, if any. | keyword |  |  |
| node_exporter.socket.all.used | Total sockets in use across all protocols (from /proc/net/sockstat). | long |  | gauge |
| node_exporter.socket.tcp.alloc | Total allocated TCP sockets (from /proc/net/sockstat TCP alloc). | long |  | gauge |
| node_exporter.socket.tcp.inuse | Number of IPv4 TCP sockets in use (excludes LISTEN and TIME_WAIT). | long |  | gauge |
| prometheus.labels.\* | Prometheus metric labels. | keyword |  |  |
| service.address | Address where data about this service was collected from. This should be a URI, network address (ipv4:port or [ipv6]:port) or a resource path (sockets). | keyword |  |  |
| service.name | Name of the service data is collected from. The name of the service is normally user given. This allows for distributed services that run on multiple hosts to correlate the related instances based on the name. In the case of Elasticsearch the `service.name` could contain the cluster name. For Beats the `service.name` is by default a copy of the `service.type` field if no name is specified. | keyword |  |  |
| system.socket.summary.all.count | Total number of TCP and UDP sockets. | long |  | gauge |
| system.socket.summary.tcp.all.count | Total number of TCP sockets (IPv4 inuse + IPv6 inuse + TIME_WAIT). | long |  | gauge |
| system.socket.summary.tcp.all.established | Number of established TCP connections. | long |  | gauge |
| system.socket.summary.tcp.all.orphan | Number of orphaned TCP connections. | long |  | gauge |
| system.socket.summary.tcp.all.time_wait | Number of TCP connections in TIME_WAIT state. | long |  | gauge |
| system.socket.summary.tcp.memory | Memory used by TCP sockets in bytes. | long | byte | gauge |
| system.socket.summary.udp.all.count | Total number of UDP sockets (IPv4 inuse + IPv6 inuse). | long |  | gauge |
| system.socket.summary.udp.memory | Memory used by UDP sockets in bytes. | long | byte | gauge |


## Setup

1. Add the **Prometheus Node Exporter** integration in Fleet.
2. Set **Node Exporter Hosts** to point at your node_exporter instance(s), for example `http://localhost:9100`.
3. Enable or disable individual data streams (load, cpu, memory, filesystem, diskstats, network, process_summary, socket_summary) as needed.
4. If your `/metrics` endpoint requires authentication, provide the **Username** and **Password**.
