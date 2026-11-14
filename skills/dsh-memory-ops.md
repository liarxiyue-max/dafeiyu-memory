---
title: dsh-memory 维护手法
date: 2026-10-27
type: skill
salience: 2
last_access: 2026-11-15
tags: [记忆库, dsh-memory, 沙箱]
sources: []
summary: 记忆库在工作区内，写页不提权；CLI 用法、加页流程、doctor 假警报的坑。
---

# dsh-memory 维护手法

## 位置

- **库根**：`F:\dsh-work\.memory`——2026-10-27 从 `C:\Users\73428\.memory` 搬进来的，**现在在工作区里，`workspace-write` 就能直接写，不用提权**。
- **配置**：`C:\Users\73428\.dsh\memory.json` → `{"memoryDir":"F:/dsh-work/.memory"}`。这个文件**只在插件构造时读一次**（`config-store.js` 的 `watchers` 是进程内订阅，不是文件监听），改完必须**重启 DSH**才生效。
- **CLI**：`C:\Users\73428\.dsh\profiles\desktop\node_modules\dsh-plugin-memory\scripts\memory.mjs`（`.bin\dsh-memory` 存在但**不在 PATH**）。

## 常用命令

```powershell
node C:\Users\73428\.dsh\profiles\desktop\node_modules\dsh-plugin-memory\scripts\memory.mjs status
```

`status | doctor | lint | index --write | query [--tag x] [--salience n] | search <词> | touch | graph | pack | unpack`

## 坑（踩过）

- **`doctor` 报「✗ 目录可写」未必是真坏**：库还在 `~/.memory` 时，用 `workspace-write` 跑它就会被沙箱拦住报这个——那是权限假警报。搬家后不会再有。
- **`index --write` 不会给新页自动排位**：只会提示 `add them by hand`，得自己在对应小节加一行。
- **`node --test` 会 spawn 子进程 → EPERM**：跑测试用例要直接 `node tests/xxx.mjs`。
- 库自带 `.git`（在 `F:\dsh-work` 里靠 `.gitignore` 排除，避免污染 dsh-soul 的演化快照）。

## 加一页的完整流程

1. 直接在 `F:\dsh-work\.memory\<分类>\` 写页面 md，frontmatter 必须有 `title / date / type / salience / last_access / tags / sources / summary`。
2. 在 `index.md` 对应小节手加一行。
3. `git -C F:\dsh-work\.memory add -A` 然后 `commit`。
4. 追加 `log.md`，格式 `## [YYYY-MM-DD] <kind> | <title>`。

## 相关页面

- [SOUL](../SOUL.md)
- [宝子](../user/profile.md)
