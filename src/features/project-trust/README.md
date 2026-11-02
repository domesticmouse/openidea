# project-trust

## Overview
Project trust prompt dialog, Restricted Mode workbench banner, Security › Project Trust settings page, and trust management actions.

## Interface
- [index.tsx:activate] Initializes remote trust migration (initializeRemoteTrustMigration), provides the project trust prompt (provideProjectTrustPrompt), registers project.trustDialog overlay, project.restrictedMode workbench banner, projectTrust settings page, trust actions (project.trust, project.previewRestricted, project.dontOpen, project.revokeTrust, project.removeTrustedLocation), and projectTrustChanged event listener.

## Invariants
- [index.tsx:activate] [index.tsx:decide] When scope === "local", trust status and decisions are routed via invokeLocal ("project_trust_status", "project_trust_set") instead of the remote backend channel.
- [index.tsx:activate] Opening a new trust prompt or disposing the feature resolves any pending prompt with false.

## Side Effects
- [index.tsx:decide] [index.tsx:TrustSettings] [index.tsx:activate] Calls commands.projectTrustStatus, commands.projectTrustSet, commands.projectTrustList, commands.projectTrustRemoveLocation, commands.projectCurrent, and invokeLocal, and updates useProject state on projectTrustChanged.

## Verification
- Run: `pnpm test`
