# CLAUDE.md

## 写入边界（重要）

- 只操作本仓库内的文件。
- **严禁写入、删除或同步仓库外的技能安装/分发目录**，包括但不限于 `~/.cc-switch/`、`~/.claude/skills/`、`~/.codex/skills/`。这些副本锁定，由用户自行管理与分发；即使发现它们与仓库内容不一致（滞后、多余文件），也只允许口头提示，不允许动手。
- 技能的新增与更新只落在仓库内 `skills/<name>/`；上游同步流程见 [scripts/README.md](scripts/README.md)，由项目技能 `.claude/skills/sync-skills` 驱动。

## 仓库结构速览

- `skills/` —— 上游开源技能的中文本地化版本，由 `scripts/sync.config.json` 跟踪上游、`scripts/state.json` 记录版本。
- `.claude/skills/` —— 本仓库自研的项目级技能（不跟踪上游）。
- `scripts/sync-skills.mjs` —— 上游同步脚本（check / accept / init / status）。
