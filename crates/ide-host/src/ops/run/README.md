# run

## Overview
Implements run configuration templates, .NET launch target resolution, Spring Boot hot-update staging, Gradle JVM args scripts, JUnit report loading, and Tomcat deployment commands.

## Interface
- `pub async fn run_dotnet_resolve(host: &Host, window: &str, config: RunConfiguration) -> IdeResult<DotnetLaunchTarget>` (`[commands.rs:run_dotnet_resolve]`)
- `pub async fn run_application_build(host: &Host, window: &str, config: RunConfiguration) -> IdeResult<String>` (`[commands.rs:run_application_build]`)
- `pub async fn run_application_publish(host: &Host, window: &str, id: String, config: RunConfiguration, trigger_file: Option<String>) -> IdeResult<usize>` (`[commands.rs:run_application_publish]`)
- `pub fn run_templates(host: &Host, window: &str) -> IdeResult<HashMap<String, Value>>` and `pub fn run_save_templates(host: &Host, window: &str, templates: HashMap<String, Value>) -> IdeResult<()>` (`[commands.rs:run_templates]`, `[commands.rs:run_save_templates]`)
- `pub fn run_system_env() -> HashMap<String, String>` (`[commands.rs:run_system_env]`)
- `pub async fn run_gradle_jvm_args_script(host: &Host, args: Vec<String>) -> IdeResult<PathBuf>` (`[commands.rs:run_gradle_jvm_args_script]`)
- `pub async fn run_test_reports(host: &Host, window: &str, cwd: Option<PathBuf>, kind: String, started_ms: u64) -> IdeResult<Vec<TestSuiteResult>>` (`[commands.rs:run_test_reports]`)
- `pub async fn run_tomcat_home(catalina_home: PathBuf) -> IdeResult<TomcatHomeInfo>` (`[commands.rs:run_tomcat_home]`)
- `pub async fn run_tomcat_artifacts(host: &Host, window: &str) -> IdeResult<Vec<TomcatArtifact>>` (`[commands.rs:run_tomcat_artifacts]`)
- `pub async fn run_tomcat_build(host: &Host, window: &str, config: RunConfiguration) -> IdeResult<PathBuf>` (`[commands.rs:run_tomcat_build]`)
- `pub async fn run_tomcat_update(host: &Host, window: &str, id: String, config: RunConfiguration, mode: TomcatUpdateMode) -> IdeResult<TomcatUpdateResult>` (`[commands.rs:run_tomcat_update]`)
- `pub(crate) fn register(r: &mut RpcRegistry)` (`[commands.rs:register]`)

## Invariants
- `run_dotnet_resolve`, `run_application_build`, `run_application_publish`, `run_tomcat_build`, and `run_tomcat_update` require project trust (`host.require_trust(window)`) and reject non-local execution targets (`[commands.rs:run_dotnet_resolve]`, `[commands.rs:run_application_build]`, `[commands.rs:run_tomcat_update]`).
- `run_tomcat_update` requires the target run configuration `id` to be actively running in `host.runs` (`[commands.rs:run_tomcat_update]`).

## Side Effects
- Writes Gradle init scripts under `<cache_dir>/gradle`, persists run templates to `.openidea/run-templates.json`, stages Spring Boot classes/resources, and builds/deploys Tomcat artifacts on disk (`[commands.rs:run_gradle_jvm_args_script]`, `[commands.rs:run_save_templates]`, `[commands.rs:run_application_publish]`, `[commands.rs:run_tomcat_update]`).

## Verification
- Run: `cargo test -p ide-host`
