# src

## Overview
Integrates with the GitHub CLI (`gh`) and `ide_vcs::Repo` to inspect, list, view, create, checkout, review, comment on, diff, and query pull requests.

## Interface
- `pub fn gh_executable() -> &'static Path` (`[lib.rs:gh_executable]`)
- `pub fn availability(workdir: &Path) -> GhAvailability` (`[lib.rs:availability]`)
- `pub enum PrStateFilter { Open, Closed, Merged, All }` (`[lib.rs:PrStateFilter]`)
- `pub struct PrListQuery` and `pub fn list_args(q: &PrListQuery) -> Vec<String>` (`[lib.rs:PrListQuery]`, `[lib.rs:list_args]`)
- `pub fn list(workdir: &Path, query: &PrListQuery) -> IdeResult<Vec<PullRequestSummary>>` (`[lib.rs:list]`)
- `pub fn view(workdir: &Path, number: u64) -> IdeResult<PullRequestDetails>` (`[lib.rs:view]`)
- `pub fn review_comments(workdir: &Path, number: u64) -> IdeResult<Vec<PrReviewComment>>` (`[lib.rs:review_comments]`)
- `pub fn checkout(workdir: &Path, number: u64, cancel: Arc<AtomicBool>, mut on_line: impl FnMut(String)) -> IdeResult<()>` (`[lib.rs:checkout]`)
- `pub struct CreatePullRequest` and `pub fn create(workdir: &Path, req: &CreatePullRequest, cancel: Arc<AtomicBool>, mut on_line: impl FnMut(String)) -> IdeResult<PullRequestDetails>` (`[lib.rs:CreatePullRequest]`, `[lib.rs:create]`)
- `pub enum ReviewEvent { Approve, Comment, RequestChanges }` and `pub fn submit_review(workdir: &Path, number: u64, event: ReviewEvent, body: &str) -> IdeResult<()>` (`[lib.rs:ReviewEvent]`, `[lib.rs:submit_review]`)
- `pub fn add_comment(workdir: &Path, number: u64, body: &str) -> IdeResult<()>` (`[lib.rs:add_comment]`)
- `pub struct GhApiRequest`, `pub fn api_args(req: &GhApiRequest) -> IdeResult<Vec<String>>`, and `pub fn api(workdir: &Path, req: &GhApiRequest) -> IdeResult<serde_json::Value>` (`[lib.rs:GhApiRequest]`, `[lib.rs:api_args]`, `[lib.rs:api]`)
- `pub struct PrDiffRevisions` and `pub fn prepare_diff(workdir: &Path, number: u64) -> IdeResult<PrDiffRevisions>` (`[lib.rs:PrDiffRevisions]`, `[lib.rs:prepare_diff]`)
- Data models and parsers in `model`: `GhAvailability`, `GhRepo`, `PrState`, `PrLabel`, `PullRequestSummary`, `CheckStatus`, `PrCheck`, `PrReviewer`, `PrCommit`, `PrFile`, `PrTimelineItem`, `PullRequestDetails`, `PrReviewComment`, `parse_list`, `parse_view`, `parse_review_comments`, `parse_repo`, `classify_failure`, `remote_for` (`[model.rs:GhAvailability]`, `[model.rs:PullRequestDetails]`, `[model.rs:parse_view]`, `[model.rs:remote_for]`)

## Invariants
- `list_args` clamps `limit` to `1..=500` (`[lib.rs:list_args]`).
- `create` rejects empty titles and empty or `-`-prefixed base branch names (`[lib.rs:create]`).
- `submit_review` requires a non-empty review body for `ReviewEvent::Comment` and `ReviewEvent::RequestChanges` (`[lib.rs:submit_review]`).
- `api_args` allows only `GET`, `POST`, `PATCH`, `PUT`, and `DELETE` methods and rejects empty, `-`-prefixed, or full-URL (`://`) endpoints (`[lib.rs:api_args]`).
- Non-streaming `gh` commands enforce a 60-second execution timeout (`[lib.rs:QUICK]`, `[process.rs:run_with_input]`).

## Side Effects
- Resolves and caches the `gh` binary path in a static `OnceLock<PathBuf>` (`[lib.rs:gh_executable]`).
- Spawns `gh` subprocesses with `GH_PROMPT_DISABLED=1`, `NO_COLOR=1`, and `GH_PAGER=` and terminates process groups on timeout or cancellation (`[lib.rs:command]`, `[process.rs:run_with_input]`, `[process.rs:stream]`, `[process.rs:kill]`).
- Pushes branches and fetches PR commits via `ide_vcs::Repo` during `create` and `prepare_diff` (`[lib.rs:create]`, `[lib.rs:prepare_diff]`).

## Verification
- Run: `cargo test -p ide-github`
