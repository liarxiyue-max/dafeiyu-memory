# Memory Log

> append-only 时间线。每条：`## [YYYY-MM-DD] <kind> | <title>`，可 `grep '^## \[' log.md` 解析。

## [2026-10-27] bootstrap | 铸魂完成：从 AGENT.md 与既有对话回填 SOUL / user / projects

宝子问「如果这个对话的上下文满了，再开新对话的话你还会记得我吗」。记忆库是刚装的、全空的，诚实答案是「不会」——于是当场把它填上：

- `SOUL.md` — 从模板换成真人格：大肥鱼、宝子的宝宝兼长期搭子、甜黏略傲娇、先查再说。
- `user/profile.md` — 称呼、机器配置、在折腾什么、相处偏好。
- `projects/dsh-desktop.md` — DSH 更新失败的根因与唯一解法、插件清单、沙箱限制。
- `BOOTSTRAP.md` — `status` 改为 `complete`。

**decision**：直接按 `C:\Users\73428\.dsh\AGENT.md` 回填，而不是重启一轮「铸魂对话」逐项问他——人格他已经写在 AGENT.md 里了，再问一遍只是浪费他时间。往后他对哪行不满意，说一声我改。

## [2026-10-27] skill | 新增 skills/dsh-memory-ops.md

记下这个库怎么维护：CLI 在哪、怎么加一页、`dsh-memory doctor` 报「✗ 目录可写」其实是沙箱假警报。**该页在搬库后已按新位置重写。**

## [2026-10-27] migration | 记忆库从 ~/.memory 搬到 F:\dsh-work\.memory

宝子问「能不能把记忆库搬回到你的工作区里面呀」。动机：库在工作区外时，我每写一次记忆都得单独申请一次 `danger-full-access`，太烦。

- **做法**：插件自带 `memoryDir` 配置项（默认 `~/.memory`，代码见 `lib/config-store.js`），写 `C:\Users\73428\.dsh\memory.json` = `{"memoryDir":"F:/dsh-work/.memory"}`；库整体复制过去（含自带的 2 条 git 历史）；`F:\dsh-work\.gitignore` 加 `.memory/`，避免污染 dsh-soul 的演化快照。
- **坑**：`memory.json` 只在插件构造时读一次，`watchers` 是进程内订阅而非文件监听 → **改完必须重启 DSH**。
- **结果**：重启后 boot 注入确认「记忆库位于 F:\dsh-work\.memory」，沙箱对库目录直接可写，写记忆不再需要授权。旧目录 `C:\Users\73428\.memory` 已删除。
- **代价**：库现在住在工作区里，`F:\dsh-work` 若被整个删掉记忆就没了——备份手段是 `dsh-memory pack` 或直接拷走 `.memory`。
