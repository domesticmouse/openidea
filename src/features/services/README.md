# services

## Overview
Services tool window (services) hosting extensible service views and providers, with built-in Docker daemon/registry management, container log/inspect streaming, Dockerfile/Compose editor gutter runners, and Docker run configuration types.

## Interface
- [index.ts:activate] Registers the services bottom tool window (ServicesHost), built-in Docker service view/provider (dockerService, dockerServiceProvider), Services actions (services.refresh, services.newService, services.groupByType, docker.*), services.dialogs overlay (DockerDialogHost), docker.gutter editor contribution, and Docker run configuration types (registerDockerRunTypes).
- [ServicesHost.tsx:ServicesHost] [ServicesHost.tsx:ServicesHeaderActions] [ServicesTree.tsx:ServicesTreeView] [ServicesTree.tsx:newServiceMenu] Master-detail Services tool window host, multi-provider tree view, inline action strip, and Add Service (+) menu.
- [tree.ts:Located] [tree.ts:ServicesTree] [tree.ts:buildServicesTree] [tree.ts:findFirstItem] Builds the unified Services tree across ServiceProviderContribution instances with optional Group by Type headers.
- [selection.ts:selectedServices] [selection.ts:selectionActions] [selection.ts:selectionMenu] Deduplicates ancestor/descendant multi-selection and intersects batchable inline actions and context menu items.
- [docker.tsx:dockerService] [docker.tsx:dockerServiceProvider] [docker.tsx:runContainerTerminal] [docker.tsx:openComposeLocation] Built-in Docker ServiceProviderContribution and detail pane (Dashboard, Log, Inspect, Properties tabs).
- [store.ts:DockerDialog] [store.ts:ConnState] [store.ts:useDocker] [store.ts:EMPTY_CONN] [store.ts:connState] [store.ts:loadDockerConfig] [store.ts:refreshConnection] [store.ts:refreshDocker] [store.ts:saveConnection] [store.ts:deleteConnection] [store.ts:saveRegistry] [store.ts:deleteRegistry] [store.ts:loadRegistryTags] [store.ts:selectKey] [store.ts:selectedTarget] [store.ts:selectDockerNode] [store.ts:ensureLogs] [store.ts:stopLogs] [store.ts:clearLogs] [store.ts:loadInspect] [store.ts:runOp] [store.ts:openDialog] [store.ts:resetDocker] Zustand store for Docker daemon connections, registries, container logs, inspect JSON, and operations.
- [model.ts:GroupKind] [model.ts:ServiceNode] [model.ts:DOCKER_KEY] [model.ts:shortId] [model.ts:isRunning] [model.ts:imageRef] [model.ts:rootNodes] [model.ts:childrenOf] [model.ts:groupChildren] [model.ts:nodeLabel] [model.ts:nodeDetail] [model.ts:nodeIcon] [model.ts:nodeContainers] [model.ts:findNode] [model.ts:DockerTarget] [model.ts:connectionKey] [model.ts:registryKey] [model.ts:treeKey] [model.ts:parseTreeKey] Pure Docker snapshot tree builder grouping Compose projects before standalone containers.
- [ops.ts:containerOp] [ops.ts:composeOp] [ops.ts:deleteResource] [ops.ts:pruneDocker] [ops.ts:pullImage] [ops.ts:pushImage] [ops.ts:createContainer] [ops.ts:buildImage] [ops.ts:composeUp] [dialogs.tsx:DockerDialogHost] [compose.ts:isDockerfile] [compose.ts:isComposeFile] [compose.ts:scanDockerFile] [dockerGutter.ts:dockerGutter] [runTypes.ts:dockerImageType] [runTypes.ts:dockerfileType] [runTypes.ts:dockerComposeType] [runTypes.ts:registerDockerRunTypes] Docker operations, modal dialogs, Dockerfile/Compose line scanner, CodeMirror gutter, and Run Configuration types.

## Invariants
- [selection.ts:selectedServices] When both a group/parent node and its descendants are selected, descendants are omitted so batch operations do not invoke children twice.
- [store.ts:appendLog] Caps buffered log lines per connection/container at MAX_LOG_LINES (5000).
- [model.ts:groupChildren] Under the Containers group, containers with a composeProject label are grouped and sorted by project and service name before standalone containers.

## Side Effects
- [store.ts:loadDockerConfig] [store.ts:refreshConnection] [store.ts:ensureLogs] [ops.ts:containerOp] Invokes commands.dockerConnections, commands.dockerSaveConnection, commands.dockerDeleteConnection, commands.dockerRegistries, commands.dockerSaveRegistry, commands.dockerDeleteRegistry, commands.dockerRegistryCatalog, commands.dockerSnapshot, commands.dockerContainerOp, commands.dockerComposeOp, commands.dockerComposeConfigFiles, commands.dockerInspect, commands.dockerDelete, commands.dockerPrune, commands.dockerPull, commands.dockerPush, commands.dockerRun, commands.dockerBuild, commands.dockerComposeUp, commands.dockerStreamLogs, and commands.dockerStopLogs.
- [ServicesHost.tsx:useGroupByType] Persists the Group by Type toggle in localStorage under openidea.services.groupByType.
- [docker.tsx:runContainerTerminal] [docker.tsx:openComposeLocation] Opens interactive docker exec / docker run tabs in the Terminal tool window and opens Compose YAML files in the editor.

## Verification
- Run: `pnpm test`
