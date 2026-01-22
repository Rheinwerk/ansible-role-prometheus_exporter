# Ansible Role: Prometheus Exporter

Generic Ansible role for installing Prometheus exporters from GitHub releases.

## Features

- Downloads and installs any Prometheus exporter from GitHub releases
- Creates system user/group for security
- Generates systemd service with security hardening
- Supports version checking to avoid unnecessary reinstalls
- Configurable for different GitHub organizations and URL patterns
- Supports installing multiple exporters in a single role call

## Requirements

- Ansible >= 2.12
- Target system: Debian/Ubuntu with systemd

## Role Variables

### Main Variable

The role uses a list-based configuration via `prometheus_exporters`:

```yaml
prometheus_exporters:
  - name: elasticsearch_exporter      # (required) Name of the exporter
    version: "1.10.0"                 # (required) Version to install
    listen_port: "9114"               # (required) Port to listen on
    listen_address: "127.0.0.1"       # (optional) Address to listen on
    github_org: prometheus-community  # (optional) GitHub organization
    github_repo: elasticsearch_exporter  # (optional) Repository name (default: same as name)
    arch: amd64                       # (optional) Architecture override (default: auto-detected)
    download_url: ""                  # (optional) Full download URL (default: auto-detected)
    binary_name: elasticsearch_exporter  # (optional) Binary name (default: same as name)
    system_user: elasticsearch_exporter  # (optional) System user (default: derived from name)
    system_group: elasticsearch_exporter # (optional) System group (default: same as system_user)
    config_dir: /etc/elasticsearch_exporter  # (optional) Config directory
    service_name: elasticsearch_exporter  # (optional) Systemd service name
    service_enabled: true             # (optional) Enable service (default: true)
    service_state: stopped            # (optional) Service state (default: stopped)
    extra_args: []                    # (optional) Extra command line arguments
    env_vars: {}                      # (optional) Environment variables for systemd service
```

### Global Defaults

```yaml
# Default listen address for all exporters
prometheus_exporter_listen_address: "127.0.0.1"

# Default service configuration
prometheus_exporter_service_enabled: true
prometheus_exporter_service_state: stopped

# Installation path
prometheus_exporter_install_dir: "/usr/local/bin"

# Architecture mapping
prometheus_exporter_arch_map:
  x86_64: "amd64"
  aarch64: "arm64"
  armv7l: "armv7"
```

## Example Usage

### Single Exporter

```yaml
- hosts: elasticsearch
  roles:
    - role: prometheus-exporter
      vars:
        prometheus_exporters:
          - name: elasticsearch_exporter
            version: "1.10.0"
            listen_port: "9114"
            github_org: prometheus-community
            extra_args:
              - "--es.uri=http://localhost:9200"
```

### Multiple Exporters

```yaml
- hosts: all
  roles:
    - role: prometheus-exporter
      vars:
        prometheus_exporters:
          - name: elasticsearch_exporter
            version: "1.10.0"
            listen_port: "9114"
            github_org: prometheus-community
            extra_args: []

          - name: postfix_exporter
            version: "2.1.0"
            listen_port: "9154"
            github_org: sergeymakinen
            extra_args: []

          - name: process-exporter
            version: "0.8.7"
            listen_port: "9256"
            github_org: ncabatoff
            extra_args: []

          - name: systemd_exporter
            version: "0.7.0"
            listen_port: "9558"
            github_org: prometheus-community
            extra_args: []
```

### With Environment Variables

```yaml
- hosts: elasticsearch
  roles:
    - role: prometheus-exporter
      vars:
        prometheus_exporters:
          - name: elasticsearch_exporter
            version: "1.10.0"
            listen_port: "9114"
            github_org: prometheus-community
            extra_args:
              - "--es.uri=http://localhost:9200"
            env_vars:
              ES_USERNAME: "elastic"
              ES_PASSWORD: "changeme"
```

## Supported Exporters

This role can install any exporter that follows the standard Prometheus release format:

| Exporter | GitHub Org | Port | Notes |
|----------|------------|------|-------|
| elasticsearch_exporter | prometheus-community | 9114 | |
| postfix_exporter | sergeymakinen | 9154 | |
| process-exporter | ncabatoff | 9256 | |
| systemd_exporter | prometheus-community | 9558 | |
| rabbitmq_exporter | kbudde | 9419 | |
| redis_exporter | oliver006 | 9121 | |
| nginx_exporter | nginxinc | 9113 | |
| blackbox_exporter | prometheus | 9115 | |
| snmp_exporter | prometheus | 9116 | |

## License

MIT

## Author

Rheinwerk
