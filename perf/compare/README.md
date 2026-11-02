# IDE comparison

`pnpm perf` measures OpenIDEA from inside its webview. This measures any IDE
from the outside, the same way for each, so OpenIDEA can be compared with
IntelliJ IDEA (or anything else listed in `apps.toml`).

```sh
pip install -r perf/compare/requirements.txt
python perf/compare/bench.py ~/src/spring-petclinic --runs 10
python perf/compare/bench.py ~/src/spring-petclinic --runs 5 --wipe-caches   # cold index
```

Each run launches the app on the project, waits until it is ready, idles,
closes it, and records:

| Metric                                      | How                                                                                                                   |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `window_s`                                  | first visible window whose title matches `window_title` (the project name)                                            |
| `ready_s`                                   | the process tree's CPU stays under `--quiet-cores` for `--quiet-secs`, after the window and any `ready_after` markers |
| markers                                     | first/last time a log line matches, e.g. OpenIDEA `indexed`, IDEA `exit dumb mode`                                    |
| `peak_rss_mb`, `rss_mb`, `uss_mb`, `pss_mb` | summed over every process the app started (WebView2/WebKit, jdtls, language servers, the JVM)                         |
| `total_cpu_s`, `idle_cpu_pct`               | CPU time until ready; CPU while idle for `--idle-secs`                                                                |
| `exit_s`                                    | until every process has exited                                                                                        |
| `cache_mb`, install size                    | `cache_dirs`, `install_dir`                                                                                           |

Apps run interleaved, rotating the order, after `--warmup` discarded runs.
Output goes to `results/<time>-<project>/`: `report.md` (median, min–max),
`results.json`, and each run's stdout.

## Fair runs

- Release builds only; a running instance of the same executable (a
  `pnpm tauri dev` build too) would take over the launch, so the script refuses.
- IDEA: open the project once by hand and trust it; turn off _Reopen projects
  on startup_ and _Confirm before exiting_; note the version, `-Xmx` and whether
  Shared Indexes were on.
- Same machine, power plan and plugged in; exclude the project and caches from
  antivirus scanning, or neither.
- `--drop-os-cache` runs a command before each launch for a cold OS cache:
  `sync; echo 3 | sudo tee /proc/sys/vm/drop_caches` (Linux), `sudo purge`
  (macOS), `RAMMap64.exe -Et` (Windows, admin).
- Typing latency is not covered: measure it on screen with Typometer for both.

## Platform notes

- Windows: windows via `user32`, closed with `WM_CLOSE`.
- macOS: windows via Quartz (`pyobjc-framework-Quartz`; titles need Screen
  Recording permission). WebKit helpers are children of launchd and are
  attributed through `responsibility_get_pid_responsible_for_pid`.
- Linux: windows via `wmctrl` (X11). Without it `window_s` is skipped and
  readiness counts from launch.

---

## Overview
Cross-platform black-box IDE benchmark runner (`bench.py`) and application configuration (`apps.toml`) comparing startup, readiness, memory (RSS/USS/PSS), CPU, exit time, and disk cache size across OpenIDEA and IntelliJ IDEA.

## Interface
- [bench.py:main] — CLI entry point (`python perf/compare/bench.py <project> [options]`) orchestrating interleaved warmup and measured runs across configured IDEs
- [bench.py:App] — Parses per-OS app definitions (`command`, `args`, `logs`, `markers`, `ready_after`, `window_title`, `cache_dirs`, `install_dir`, `quit_command`, `single_instance`, `daemons`) from `apps.toml`
- [bench.py:Tree] — Tracks process trees and attributes macOS `com.apple.WebKit` helper processes via `responsibility_get_pid_responsible_for_pid` (`[bench.py:_responsible]`)
- [bench.py:LogTail] — Tails log file globs and stdout for regex readiness markers (`[bench.py:LogTail.lines]`)
- [bench.py:run_once] — Executes a single benchmark launch, readiness wait, idle sampling window, and shutdown cycle
- [bench.py:report] — Formats median and min–max Markdown comparison tables across apps

## Invariants
- [bench.py:run_once] — Refuses to launch when `app.single_instance` is true and an existing process with the same executable name is already running (`[bench.py:running_instances]`)
- [bench.py:main] — Refuses `--wipe-caches` if any resolved cache path has fewer than 4 path components or equals `Path.home()`
- [bench.py:run_once] — Declares `ready_s` only after a visible matching window appears (when supported), all `ready_after` log markers have matched, and process-tree CPU stays below `--quiet-cores` for `--quiet-secs`

## Side Effects
- [bench.py:run_once] — Spawns and terminates IDE processes (`WM_CLOSE` on Windows, `SIGTERM`/kill on Unix), optionally deletes `cache_dirs` (`--wipe-caches`) and drops OS caches (`--drop-os-cache`)
- [bench.py:main] — Writes `<out>/<timestamp>-<project>/results.json`, `report.md`, and `<label>.stdout.log`

## Verification
- Run: `python perf/compare/bench.py --help`
