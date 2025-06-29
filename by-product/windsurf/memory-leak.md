# Bug Report: Severe Memory Leak in IDE Application

**Date:** June 30, 2025  
**Subject:** Critical Bug Report: Memory Leak in IDE Application (Ubuntu 25.04)

## Issue Description

This report documents severe performance degradation on a system that appears to be directly caused by a memory leak in the IDE application version 1.10.5. After thorough diagnostics, it was identified that the application processes are consuming extraordinary amounts of system resources, with a single renderer process allocating over 1TB of virtual memory and using 238.5% CPU.

## System Information

- **OS:** Ubuntu 25.04 (Plucky)
- **CPU:** Intel i7-12700F 2.1GHz 25MB 1700
- **RAM:** 64GB
- **GPU:** AMD Radeon RX7700 XT PULSE GAMING 12GB (Driver: radeonsi, navi32, LLVM 19.1.7, DRM 3.61)
- **Kernel:** 6.14.0-15-generic
- **IDE Application Version:** 1.10.5
- **IDE Extension Version:** 1.48.1
- **OSS Version:** 1.99.3
- **Commit:** ff497a1ec3dde399fde9c001a3e69a58f2739dac
- **Date:** 2025-06-19T03:04:53.484Z
- **Electron:** 34.3.2
- **Node.js:** 20.18.3
- **V8:** 13.2.152.41-electron.0

## Installed Extensions

```
alefragnani.project-manager-12.8.0-universal
ide-language-server-1.28.0-universal
cweijan.dbclient-jdbc-1.4.2-universal
cweijan.vscode-mysql-client2-8.3.6-universal
janisdd.vscode-edit-csv-0.11.4-universal
kisstkondoros.vscode-gutter-preview-0.32.2-universal
ms-azuretools.vscode-containers-2.0.3-universal
ms-python.debugpy-2025.6.0-linux-x64
ms-python.python-2025.4.0-universal
pokey.cursorless-1.0.1520-universal
pokey.parse-tree-0.38.0-universal
postman.postman-for-vscode-1.12.1-universal
redhat.vscode-yaml-1.18.0-universal
slaugaus.visual-json-1.0.0-universal
yutengjing.open-in-external-app-0.11.0-universal
zokugun.cron-tasks-0.2.1-universal
zokugun.sync-settings-0.17.2-universal
```

## Diagnostic Data

### Process Resource Usage

```
top - 00:21:10 up 13 min, 2 users, load average: 2.52, 7.67, 6.44
Tasks: 602 total, 2 running, 598 sleeping, 0 stopped, 2 zombie
%Cpu(s): 39.0 us, 4.0 sy, 0.0 ni, 53.4 id, 2.8 wa, 0.0 hi, 0.8 si, 0.0 st
MiB Mem : 64137.7 total, 36257.8 free, 9689.9 used, 19060.9 buff/cache
MiB Swap: 0.0 total, 0.0 free, 0.0 used. 54447.8 avail Mem

PID USER    PR NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
16652 user 20  0 1163.5g 416920 114132 R 238.5  0.6   2:06.43 ide-app
```

### Detailed Process Analysis

```
user     16652 43.9  0.6 1220052888 408508 ?   Rl   00:16   2:03 /usr/share/ide-app/ide-app --type=renderer --crashpad-handler-pid=16581 --enable-crash-reporter=fb2d717e-1650-434e-a507-e1e75b19b424,no_channel --user-data-dir=/home/user/.config/IDE-App --standard-schemes=vscode-webview,vscode-file --enable-sandbox --secure-schemes=vscode-webview,vscode-file --cors-schemes=vscode-webview,vscode-file --fetch-schemes=vscode-webview,vscode-file --service-worker-schemes=vscode-webview --code-cache-schemes=vscode-webview,vscode-file --app-path=/usr/share/ide-app/resources/app --enable-sandbox --enable-blink-features=HighlightAPI --js-flags=--nodecommit_pooled_pages --disable-blink-features=FontMatchingCTMigration,StandardizedBrowserZoom, --lang=en-GB --num-raster-threads=4 --enable-main-frame-before-activation --renderer-client-id=4 --time-ticks-at-unix-epoch=-1751231244933540 --launch-time-ticks=534883255 --shared-files=v8_context_snapshot_data:100 --field-trial-handle=3,i,573731369270729020,2184749861517979623,262144 --enable-features=DocumentPolicyIncludeJSCallStacksInCrashReports --disable-features=CalculateNativeWinOcclusion,SpareRendererForSitePerProcess --variations-seed-version --window-config=[REDACTED]

user     16594  8.4  0.3 34553312 201640 ?     Sl   00:16   0:23 /usr/share/ide-app/ide-app --type=gpu-process --crashpad-handler-pid=16581 --enable-crash-reporter=fb2d717e-1650-434e-a507-e1e75b19b424,no_channel --user-data-dir=/home/user/.config/IDE-App --gpu-preferences=UAAAAAAAAAAgAAAEAAAAAAAAAAAAAAAAAABgAAEAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAQAAABAAAAAAAAAAEAAAAAAAAAAIAAAAAAAAAAgAAAAAAAAA --shared-files --field-trial-handle=3,i,573731369270729020,2184749861517979623,262144 --enable-features=DocumentPolicyIncludeJSCallStacksInCrashReports --disable-features=CalculateNativeWinOcclusion,SpareRendererForSitePerProcess --variations-seed-version

user     16554  3.9  0.3 1218123564 208480 ?   Ssl  00:16   0:11 /usr/share/ide-app/ide-app

user     17103 12.2  0.9 3479648 611188 ?      Sl   00:16   0:33 /usr/share/ide-app/resources/app/extensions/ide-app/bin/language_server_linux_x64 --api_server_url https://server.example.com --run_child --enable_lsp --extension_server_port 39453 --ide_name ide-app --csrf_token [REDACTED] --random_port --inference_api_server_url https://inference.example.com --database_dir /home/user/.extension/database/[REDACTED] --enable_index_service --enable_local_search --search_max_workspace_file_count 0 --indexed_files_retention_period_days 30 --workspace_id [REDACTED] --sentry_telemetry --sentry_environment stable --extension_dir .extension/ide-app --parent_pipe_path /tmp/server_[REDACTED] --ide_version 1.10.5 --stdin_initial_metadata

user     16823 10.2  0.4 1217898956 302296 ?   Sl   00:16   0:28 /usr/share/ide-app/ide-app --type=utility --utility-sub-type=node.mojom.NodeService --lang=en-GB --service-sandbox-type=none --dns-result-order=ipv4first --inspect-port=0 --crashpad-handler-pid=16581 --enable-crash-reporter=fb2d717e-1650-434e-a507-e1e75b19b424,no_channel --user-data-dir=/home/user/.config/IDE-App --standard-schemes=vscode-webview,vscode-file --enable-sandbox --secure-schemes=vscode-webview,vscode-file --cors-schemes=vscode-webview,vscode-file --fetch-schemes=vscode-webview,vscode-file --service-worker-schemes=vscode-webview --code-cache-schemes=vscode-webview,vscode-file --shared-files=v8_context_snapshot_data:100 --field-trial-handle=3,i,573731369270729020,2184749861517979623,262144 --enable-features=DocumentPolicyIncludeJSCallStacksInCrashReports --disable-features=CalculateNativeWinOcclusion,SpareRendererForSitePerProcess --variations-seed-version
```

### Failed Related Services

```
Jun 30 00:09:24 hostname systemd[5556]: Failed to start sync-ide.service - Sync Extension Servers.
Jun 30 00:09:24 hostname (onfig.py)[12999]: sync-ide.service: Failed to execute config script: Exec format error
Jun 30 00:09:24 hostname (onfig.py)[12999]: sync-ide.service: Failed at step EXEC spawning config script: Exec format error
```

## Impact

The memory leak is causing:

1. Extreme system sluggishness
2. Unresponsive UI
3. High CPU usage (238.5% on a single process)
4. Abnormal virtual memory allocation (over 1TB)
5. General system instability

## Steps to Reproduce

1. Start IDE Application 1.10.5 on Ubuntu 25.04
2. Open a project with multiple files
3. Let the application run for approximately 10-15 minutes
4. Observe resource usage with `top` or system monitor

## Additional Notes

- The issue persists across system reboots
- The problem appears to be specifically with the renderer process
- A language server extension might be contributing to the issue, as it is also consuming significant resources
- The system has adequate hardware resources (64GB RAM, modern CPU and GPU)
- No swap space is being used, indicating memory is not a bottleneck

## Attempted Workarounds

- Restarting the IDE application provides temporary relief but the issue recurs
- Killing specific IDE processes temporarily improves system performance

## Questions

1. Whether this is a known issue with version 1.10.5
2. If any specific extension could be triggering this memory leak
3. Any configuration changes that might mitigate this issue
4. If there is a more stable version recommended until this is fixed

Additional information can be provided to help diagnose and resolve this issue if needed.
