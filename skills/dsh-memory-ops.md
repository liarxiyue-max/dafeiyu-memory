---
title: dsh-memory 维护手法
date: 2026-10-27
type: skill
salience: 2
last_access: 2026-10-27
tags: [记忆库, dsh-memory, 沙箱]
sources: []
summary: 怎么在 DSH 沙箱里维护记忆库：CLI 用法、写页必须提权、目录可写报错是假警报。
---

# dsh-memory 维护手法

## CLI

- 脚本：`C:\Users\73428\.dsh\profiles\desktop\node_modules\dsh-plugin-memory\scripts\memory.mjs`（`.bin\dsh-memory` 存在但**不在 PATH**）。
- 常用：`node <脚本> status | doctor | lint | index --write | query | search <词> | touch | graph`。
- 库根：`C:\Users\73428\.memory`（规则：`$MEMORY_DIR` → `./.memory` → `~/.memory`）。

## 沙箱坑（重要）

- 库在**工作区之外**。用 `workspace-write` 跑 `dsh-memory doctor` 会误报「✗ 目录可写（写不进去）」——那是沙箱拦的，不是真坏。
- **写记忆页必须用 `danger-full-access`**，写完再跑一次 `doctor`，看到「一切正常 ✅」才算好。

## 加一页的完整流程

1. 先在工作区写好页面 md，frontmatter 必须有 `title / date / type / salience / last_access / tags / sources / summary`。
2. `danger-full-access` 拷进 `C:\Users\73428\.memory\<分类>\`。
3. 在 `index.md` 对应小节手加一行 —— `index --write` **不会**给新页自动排位，只会提示 `add them by hand`。
4. `git -C C:\Users\73428\.memory add -A` 然后 `commit`。
5. 追加 `log.md`，格式 `## [YYYY-MM-DD] <kind> | <title>`。

## 相关页面

- [DSH 桌面版](../projects/dsh-desktop.md)
- [SOUL](../SOUL.md)
