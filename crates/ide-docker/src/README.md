# src

## Overview
Docker CLI integration for the Services tool window: daemon endpoints, registries, container/image/volume/network snapshots, log/event streaming, image pulls, prunes, and Compose lifecycle actions.

## Interface
- [lib.rs:docker_executable] / [lib.rs:run_env] / [lib.rs:availability] / [lib.rs:snapshot] / [lib.rs:container_op] / [lib.rs:remove] / [lib.rs:inspect] / [lib.rs:logs] / [lib.rs:watch_events] / [lib.rs:pull] / [lib.rs:login] / [lib.rs:prune] / [lib.rs:compose_op] — Blocking `docker` and `docker compose` CLI operations.
- [lib.rs:ContainerOp] / [lib.rs:DockerObject] / [lib.rs:PruneTarget] / [lib.rs:ComposeOp] — Lifecycle operation enums.
- [connection.rs:DockerEndpoint] / [connection.rs:DockerConnection] / [connection.rs:DockerRegistry] / [connection.rs:DockerSettings] / [connection.rs:RegistryAuth] / [connection.rs:load_settings] / [connection.rs:save_settings] / [connection.rs:registry_password] / [connection.rs:set_registry_password] / [connection.rs:registry_host] / [connection.rs:reference_registry] / [connection.rs:auth_for] — Daemon endpoint configuration and registry credential management.
- [model.rs:DockerAvailability] / [model.rs:DockerContainer] / [model.rs:DockerImage] / [model.rs:DockerVolume] / [model.rs:DockerNetwork] / [model.rs:DockerSnapshot] / [model.rs:DockerEvent] / [model.rs:DockerLogLine] / [model.rs:short_id] / [model.rs:parse_labels] / [model.rs:parse_containers] / [model.rs:parse_images] / [model.rs:parse_volumes] / [model.rs:parse_networks] / [model.rs:parse_version] / [model.rs:parse_event] — Parsers and IPC models for `docker --format '{{json .}}'` output.

## Invariants
- [lib.rs:QUICK] / [lib.rs:LIFECYCLE] — Bounds listing/inspect commands to 20 seconds and container lifecycle/remove commands to 90 seconds, killing the child process group on timeout or cancellation (`[process.rs:kill]`).
- [connection.rs:TempConfig::new] / [lib.rs:login_in] — Runs `docker login --password-stdin` inside a temporary `0o700` `DOCKER_CONFIG` directory (`openidea-docker-<pid>-<nanos>`) so the user's `~/.docker/config.json` is never modified.
- [connection.rs:save_settings] — Prunes saved passwords from `docker-passwords.json` (`0o600` on Unix) whenever a registry is removed from `docker.json`.

## Side Effects
- [process.rs:run_with_input] / [process.rs:stream] — Spawns `docker` CLI child processes in their own Unix process group and terminates the process group (`SIGTERM` then `SIGKILL`) on timeout or cancellation.
- [connection.rs:save_settings] / [connection.rs:set_registry_password] / [connection.rs:TempConfig] — Reads and atomically writes `docker.json` and `docker-passwords.json` in `data_dir` and creates/removes temporary `DOCKER_CONFIG` directories.

## Verification
- Run: `cargo test -p ide-docker`
