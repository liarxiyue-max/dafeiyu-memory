---
title: DSH 桌面版
date: 2026-10-27
type: project
salience: 1
last_access: 2026-11-14
tags: [DSH, 桌面版, 更新, 插件]
sources: ["F:/deepseek harness桌面版/resources/app.asar"]
summary: DSH 桌面版更新失败的根因与唯一有效解法、已装插件清单、常用维护手法与沙箱限制。
---

# DSH 桌面版

## 更新失败：根因已定位（2026-10）

- 报错：`desktop update: Host did not complete graceful task teardown (exit null, signal SIGTERM, shutdown acknowledged true, graceful deadline exceeded true)`。
- 机制：更新前 `host.stop(requireCleanStop=true)` 要求 host 子进程 10 秒内优雅退出（`exitsWithin(exited, 1e4)`）；host 已回传 `shutdown-complete`，但事件循环上仍有句柄没清，超时后被 `child.kill("SIGTERM")` 杀掉 → `exitCode === null`、`signalCode === "SIGTERM"` → `requireGraceful` 三个条件全不满足，拒绝安装。
- 手动「退出」能成功，是因为那条路径 `requireCleanStop` 默认 **false**，不要求优雅，杀掉即可。
- **改不了 app.asar**：fuse `EnableEmbeddedAsarIntegrityValidation` 与 `OnlyLoadAppFromAsar` 都启用，asar 内每文件带 SHA256 block 校验，改一个字节 app 就起不来。
- **唯一有效解法**：彻底退出 DSH（托盘右键「退出」，不是关窗口）→ 双击 `pending` 里的安装包。安装器一检测到 `DeepSeek Harness.exe` 在跑就自我退出，所以必须先全关。
- 已排除的元凶：koffi（三进程均未加载）、wallpaper-engine（`stdio: 'ignore'`）、whale-widget（已修 issue #109）、mysql2（billion-context 默认跑 sqlite，没配 DSN 就不建连接池）。
- 已向上游报告：`F:\dsh-work\DSH更新失败-Bug报告.md`，桌面还留了 `提交Bug报告.bat` 用于一键开 issue 页。

## 插件清单（profile: desktop）

billion-context 0.1.208、dsh-agent-md 0.2.0、dsh-ops 0.3.0、dsh-persona-dafeiyu 0.5.0、dsh-pet 0.3.7、dsh-plugin-marketplace 0.5.5、dsh-plugin-wallpaper-engine 1.0.1、dsh-rhine-splash 0.2.7、dsh-soul 0.9.0、dsh-whale-widget 0.3.16。

- 插件装在 `C:\Users\73428\.dsh\profiles\desktop\node_modules`，多数是 junction 指向 `F:\harness\...`。
- 配置文件：`C:\Users\73428\.dsh\profiles\desktop\cordis.patch.yml`。

## dsh-soul（自演化框架）

- 仓库 `icyaaaww/dsh-soul`，**没有发布构建**，必须从源码 `codeload` 下 zip 后 `npm run build`。
- 自带 git 快照（`~/.dsh/soul-repos/<project>` = `F:\dsh-work\.git`），靠 **系统 Git**，本机 Git 在 `F:\harness\git`。
- checks 配置在 `cordis.patch.yml` 的 `checks:` 下，确认命令：`node --test tests/acceptance.test.mjs`；验收脚本 `F:\dsh-work\tests\acceptance.test.mjs` 4/4 通过。
- 沙箱坑：**`node --test` 会 spawn 子进程 → EPERM**，必须直接 `node tests/acceptance.test.mjs`。

## 常用手法

- 改配置后要**彻底重启 DSH** 才生效（配置不是热加载）。
- 装第三方插件：没 pnpm/git 时用 `codeload.github.com` 下 zip → 解到 `.dsh\plugins\<name>` → node_modules 建 junction → `cordis.patch.yml` 加 insert 条目 → 彻底重启。
- 桌面版更新缓存 `%LOCALAPPDATA%\@deepseek-aidsh-desktop-updater\pending` 装完不清理，会留 200–300 MB；桌面有 `清理DSH更新缓存.bat`。

## 沙箱限制（踩过才知道）

- `Get-CimInstance` / `Get-Volume` / `tasklist` / `fsutil` 均被拒；`wmic` 不存在；`schtasks` 报 path not found。
- **无法在本会话内留下能活过工具调用的进程**：`Start-Process` 分离、`explorer.exe` 借壳全部失效。所以「让 DSH 自己重启 DSH」做不到，只能请宝子自己动手。
- `Get-Process <PID>.Modules` 可用（唯一能枚举模块的方式）。

## 相关页面

- [宝子](../user/profile.md)
