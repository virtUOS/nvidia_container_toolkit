# Ansible Role: nvidia_container_toolkit

Installs and configures the [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/overview.html) for Docker on RPM-based systems.

## What it does

1. Adds the official NVIDIA Container Toolkit RPM repository.
2. Installs the `nvidia-container-toolkit` package.
3. Configures `/etc/docker/daemon.json` with the NVIDIA runtime.
4. Reloads Docker when the daemon configuration changes (running containers keep running).

## Requirements

- RPM-based OS (RHEL, Rocky, AlmaLinux, CentOS, Fedora)
- Docker already installed
- NVIDIA GPU drivers already installed on the host

## Role Variables

| Variable | Default | Description |
|---|---|---|
| `nvidia_container_toolkit_repo_url` | `https://nvidia.github.io/libnvidia-container/stable/rpm/nvidia-container-toolkit.repo` | URL to the NVIDIA Container Toolkit RPM repository file. |
| `nvidia_docker_default_runtime` | `nvidia` | The default container runtime for Docker. |

## Dependencies

- nvidia_driver_cuda
- elan.docker_install

## Example Playbook

### Basic usage

```yaml
- hosts: gpu_nodes
  roles:
    - nvidia_driver_cuda
    - elan.docker_install
    - nvidia_container_toolkit
```

### With custom variables

```yaml
- hosts: gpu_nodes
  roles:
    - role: nvidia_container_toolkit
      vars:
        nvidia_docker_default_runtime: "runc"
```

## Handlers

- **Reload Docker:** Triggered when /etc/docker/daemon.json is modified. Every key the template sets is one dockerd applies on reload, so no container is restarted.

## Important Notes

- This role overwrites /etc/docker/daemon.json. If you have existing Docker daemon configuration, merge it into the templates/daemon.json.j2 template.
- This role does not install GPU drivers. Make sure drivers are present before running it.
- This role only supports RPM-based distributions. For Debian/Ubuntu, the repository setup task needs to be adapted.
