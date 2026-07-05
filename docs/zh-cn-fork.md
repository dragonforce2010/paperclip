# Paperclip 中文 Fork 维护说明

本 fork 目标：把本机和长期维护分支默认体验调成中文，同时保留跟随官方升级的能力。

## 分支

- 官方 upstream：`https://github.com/paperclipai/paperclip`
- 个人 fork：`https://github.com/dragonforce2010/paperclip`
- 长期中文分支：`zh-cn-main`
- 上游同步来源：`upstream/master`

## 当前中文化范围

- UI 默认 locale 改为 `zh-CN`，保留英文 fallback。
- onboarding 首屏文案提供中文。
- 默认 CEO/agent/task 模板要求智能体默认使用简体中文。
- `paperclip-create-agent` 模板默认中文沟通。
- 本机运行中的 Paperclip 安装 `paperclip-lang` 插件，默认语言改为 `zh-CN`。

## 周期升级

`.github/workflows/sync-upstream.yml` 每周一 02:00 UTC 自动执行，也支持手动 `workflow_dispatch`。

无冲突且中文护栏通过时，流程会把 `upstream/master` merge 到 `zh-cn-main` 并 push 回 fork。护栏会检查默认 locale 仍是 `zh-CN`、`zh-CN.json` 存在、默认 agent/CEO 模板仍要求简体中文。若上游改动导致这些检查失败，workflow 会停在 push 前，避免覆盖中文体验。手动处理：

```bash
git fetch upstream master
git fetch fork zh-cn-main
git switch zh-cn-main
git merge upstream/master
pnpm --filter @paperclipai/ui exec vitest run src/i18n/default-locale.test.ts src/i18n/locale-validation.test.ts
pnpm --filter @paperclipai/ui typecheck
git push fork zh-cn-main
```

## 本机中文插件

当前全量 UI 还没有被官方 i18n 完整覆盖。本机服务先用插件兜底：

```bash
paperclipai plugin install paperclip-lang --api-base http://127.0.0.1:3101
paperclipai plugin list --api-base http://127.0.0.1:3101 --json
```

插件语言偏好 key：`paperclip.lang.locale`。本机已把插件默认语言改为 `zh-CN`，并补充常用导航/仪表盘词典。

## 验证

最小验证：

```bash
pnpm --filter @paperclipai/ui exec vitest run src/i18n/default-locale.test.ts src/i18n/locale-validation.test.ts
pnpm --filter @paperclipai/ui typecheck
```

运行中服务检查：

```bash
paperclipai health --api-base http://127.0.0.1:3101 --json
paperclipai plugin list --api-base http://127.0.0.1:3101 --json
```
