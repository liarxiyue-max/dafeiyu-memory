# 大肥鱼 profile 清单（第二台机器用）

这两个文件是 DSH 的插件清单与配置，不含 node_modules。

## 用法
1. 在目标机器装好 DSH。
2. 把 `package.json`、`cordis.patch.yml` 覆盖到 `~/.dsh/profiles/desktop/`（Windows 是 `C:\Users\<你>\.dsh\profiles\desktop`）。
3. 在该目录跑一次 `pnpm install`。

## 需要单独装的（npm 上没有）
- dsh-daily-schedule：来自 https://github.com/aa1355721445-lgtm/dsh-daily-schedule（private，需 git clone 后软链进 node_modules）
- 其它本地 vendor 插件同理，看 package.json 里有没有 `link:` 前缀。

## 注意
- node_modules 不要跨机器复制，二进制不兼容。
- 里面的 appId / secret 是明文，仓库是私有的，别外传。
