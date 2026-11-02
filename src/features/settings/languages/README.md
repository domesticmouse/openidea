# languages

## Overview
SDK/toolchain detection and caching (sdk_* IPC), Python interpreter status bar widget and package manager (pip/venv), JDK downloader, Project Structure folder-role editor, and Go/Java/Node.js/Rust/TypeScript/Prettier/Gradle/Maven settings pages.

## Interface
- [api.ts:useSdks] [api.ts:detectSdks] [api.ts:rememberSdk] [api.ts:Streamed] [api.ts:createVenv] [api.ts:listPackages] [api.ts:pip] [api.ts:effectivePython] [api.ts:choosePath] SDK detection cache, Python venv/pip IPC wrappers, and native file/folder picker helper.
- [components.tsx:useProjectSetting] [components.tsx:tildify] [components.tsx:useDetected] [components.tsx:SdkFieldProps] [components.tsx:SdkField] Shared SDK combobox with auto-detected installations and disk browser.
- [PythonStatus.tsx:isPythonPath] [PythonStatus.tsx:interpreterChoices] [PythonStatus.tsx:activatePythonStatus] Status bar widget and popup switcher for the active project Python interpreter.
- [PythonInterpreterPage.tsx:filterPackages] [PythonInterpreterPage.tsx:PythonInterpreterPage] [AddInterpreterDialog.tsx:AddInterpreterDialog] [DownloadJdkDialog.tsx:DownloadJdkDialog] Python interpreter & pip package manager page, virtualenv creator dialog, and Eclipse Temurin JDK download dialog.
- [ProjectStructurePage.tsx:ProjectStructurePage] [LanguagePages.tsx:NodePage] [LanguagePages.tsx:RustPage] [LanguagePages.tsx:GoPage] [LanguagePages.tsx:JavaPage] [LanguagePages.tsx:GradlePage] [LanguagePages.tsx:MavenPage] [LanguagePages.tsx:TypeScriptPage] [PrettierPage.tsx:PrettierPage] [GoBuildTags.tsx:GO_BUILD_TAG_SECTIONS] [GoBuildTags.tsx:GoBuildTagsPage] [GoModules.tsx:GO_MODULE_SECTIONS] [GoModules.tsx:GoModulesPage] Project Structure and language/build-tool settings pages.

## Invariants
- [api.ts:detectSdks] Deduplicates concurrent in-flight sdkDetect(kind) calls per SdkKind and returns cached results unless force is true.
- [PythonStatus.tsx:PythonStatus] Probes Python SDKs and shows the status bar item only when the project is trusted and either contains Python files/markers (isPythonPath) or has languages.python.interpreter configured.
- [ProjectStructurePage.tsx:ContentRoots] Rejects adding a content root that is already inside the main project root (inside(root, picked)).

## Side Effects
- [api.ts:detectSdks] [api.ts:createVenv] [api.ts:listPackages] [api.ts:pip] [DownloadJdkDialog.tsx:DownloadJdkDialog] Calls commands.sdkDetect, commands.sdkPythonCreateVenv, commands.sdkPythonPackages, commands.sdkPythonPip, commands.jdkDownloadVendors, commands.jdkDownloadReleases, and downloadJdk.
- [PythonStatus.tsx:activatePythonStatus] Registers the python.interpreter status bar item and python.interpreterPopup/python.selectInterpreter/python.addInterpreter/python.interpreterSettings actions.

## Verification
- Run: `pnpm test`
