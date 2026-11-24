# 计划清单、归档与恢复

需要恢复、审阅或交接已有任务时，从目标项目根目录调用技能中的辅助脚本。

```sh
sh "<skill-dir>/scripts/set-active-plan.sh" --list
sh "<skill-dir>/scripts/set-active-plan.sh" --list --json --limit 10 --offset 0
```

```powershell
& "<skill-dir>/scripts/set-active-plan.ps1" -List
& "<skill-dir>/scripts/set-active-plan.ps1" -List -Json -Limit 10 -Offset 0
```

本地规划位于 `docs/.planning/`。列表以当前工作目录为项目范围，仅读取文件元数据和阶段状态。先确认返回的 `scope.project_root`，再使用文件路径。清单不读取代理会话存储。

## JSON 字段

Shell 输出一行 UTF-8 JSON。PowerShell 通过 `-File` 直接运行时也输出 UTF-8 JSON；在会话中调用时，非 ASCII 字符会转义为 `\uXXXX`，可直接接 `ConvertFrom-Json`。

| 字段 | 含义 |
| --- | --- |
| `schema_version` | 当前为 `1`，独立于技能版本 |
| `scope` | 检查的绝对项目路径和 `source: "cwd"` |
| `candidates` | 兼容根目录旧规划，随后按名称列出命名计划；归档模式仅列归档 |
| `shared_default` | `.active_plan` 中的 ID 及 `present`、`missing`、`dangling`、`invalid` 或 `excluded` 状态 |
| `selection` | 本地解析器的选择结果，包含 `status` 和 `plan_id` |
| `host_session` | 固定为 `null`，清单无法判定其他宿主会话的任务绑定 |
| `archived_count` | `docs/.planning/.archive/` 中有效归档计划的数量 |
| `limits` | 分页的 `limit`、`offset`、`returned`、`truncated`、`next_offset` 和 `skipped` |
| `warnings` | 元数据不完整或有条目被排除时的原因 |

候选项包含 `kind`（`root`、`named`、`archived`）、`plan_id`、`ownership: "unknown"`、`files` 和 `phases`。根目录候选项的 ID 为 `null`。三个规划文件分别包含绝对 `path`、`status`（`readable`、`missing`、`unreadable` 或 `excluded`）和 Unix 秒数 `modified_at`。

`phases` 包含 `total`、`complete`、`in_progress`、`pending` 和解析状态 `status`（`parsed`、`empty`、`malformed`、`unavailable` 或 `too_large`）。解析忽略代码块，只读取不超过 256 KiB 的计划正文。空计划或格式异常不能证明任务已完成；正式格式检查使用 `check-complete --validate`。

`selection.status` 为 `selected`、`legacy_root`、`none`、`rejected` 或 `unknown`。本地解析器继续采用 `PLAN_ID`、共享指针、最新有效目录的选择顺序。清单会先拒绝无效、已归档或缺少 `task_plan.md` 的显式 ID，避免把回退结果误报为绑定任务。分配任务前仍须确认该 ID 就是当前工作的计划。

默认最多返回 20 项，输出上限 16 KiB。`limit` 范围为 1–100，`offset` 范围为 0–999999。`truncated` 为真时，使用同一运行环境返回的 `next_offset` 继续分页。目录变化可能改变页边界。清单只扫描直接子目录，并排除链接和超出项目范围的路径。

## 归档与恢复

用户确认要归档的计划后，先预览，再执行。

```sh
sh "<skill-dir>/scripts/set-active-plan.sh" --archive <id>
sh "<skill-dir>/scripts/set-active-plan.sh" --archive <id> --yes
sh "<skill-dir>/scripts/set-active-plan.sh" --list --json --archived
sh "<skill-dir>/scripts/set-active-plan.sh" --restore <id> --yes
```

```powershell
& "<skill-dir>/scripts/set-active-plan.ps1" -Archive <id>
& "<skill-dir>/scripts/set-active-plan.ps1" -Archive <id> -Yes
& "<skill-dir>/scripts/set-active-plan.ps1" -List -Json -Archived
& "<skill-dir>/scripts/set-active-plan.ps1" -Restore <id> -Yes
```

省略 `--yes` 或 `-Yes` 时显示阶段状态、源路径、目标路径，以及活动指针是否会被清除。执行归档将 `docs/.planning/<id>/` 整个重命名为 `docs/.planning/.archive/<id>/`，完整保留认证、模式标记和其他文件。共享指针指向此计划时随归档清除。恢复将目录移回原位置，再按需要选择共享指针或设置 `PLAN_ID`。

ID 必须与目录名大小写完全一致，并符合 `^[A-Za-z0-9_][A-Za-z0-9._-]*$`。源必须是项目内含普通 `task_plan.md` 的真实目录。目标已存在、路径为链接或超出项目范围、当前环境的 `PLAN_ID` 正选择待归档计划时，脚本拒绝操作。Shell 还会检查源与目标位于同一文件系统，以支持完整目录重命名。

清单无法知道其他宿主的 `PLAN_ID`。归档前确认相关工作已结束；恢复后检查并重新选择该计划。旧解析器具有回退行为，当前宿主的显式 ID 有效性检查仍须按 `SKILL.md` 执行。

## 交接任务

给协作者提供项目路径、明确选择的计划 ID、三个规划文件路径、允许写入的文件或账本，以及维护共享摘要的负责人。共享指针无法替代会话绑定；继续执行已有的单一负责人规则。
