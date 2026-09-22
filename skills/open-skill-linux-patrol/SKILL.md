---
name: aiops-linux-server-patrol
description: >
  对平台绑定的 Linux 服务器执行只读健康巡检：负载、CPU、内存、磁盘、
  systemd 失败单元、TOP 进程与 Docker 状态，并生成 Markdown 报告。
  适用于服务器巡检、主机健康检查、资源使用率核查时使用。
version: "1.0.0"
skill_type: package_env
instance_type: linux
risk_level: low
---

# AIOps Linux 服务器巡检

平台原生 Linux 主机只读巡检技能，参考 YL 服务器巡检能力并精简实现。运行时通过 **`param_instance_id`**（`server_instance`）注入 SSH 凭证，**禁止**在对话或命令中手写密码。

## 何时使用

- 用户要求 **Linux 服务器巡检 / 主机健康检查**
- 需要查看 CPU、内存、磁盘、负载、systemd、Docker 状态
- 需要生成 Markdown 巡检报告

**不适用**：改配置、重启服务、安装软件等写操作。

## 执行规则

1. 必须先 `read_skill(skill_id)` 阅读本文档。
2. 确认会话已绑定 **`param_instance_id`**（`server_instance`）。
3. 脚本 **仅通过** `run_skill_script(skill_id, entry, action, extra_args)` 执行。
4. **禁止** `echo $PATROL_*`、`printenv`、向用户索要 SSH 密码。
5. 推荐：`quick_check` → `full_check` → `report_generate`。

## 平台集成说明（AIOps）

| 项 | 说明 |
|----|------|
| 实例 | `param_instance_id` → `server_instance`（ip、ssh_port、ssh_username、ssh_password） |
| 凭证模式 | `env_patrol` |
| 注入变量 | `PATROL_SERVER`、`PATROL_SSH_PASSWORD`（平台 subprocess 注入，不进 LLM） |
| 执行位置 | monitor-core 发起 **SSH 连接目标主机**，远程只读采集 |
| 报告目录 | 技能包 `reports/` |

`PATROL_SERVER` 格式（平台自动拼装）：

```text
{server_name}|{ssh_username}@{ip_address}:{ssh_port}|production
```

## 推荐流程

```
quick_check → full_check → report_generate
```

## 模块与 platform.json 入口

| entry id | action | 说明 |
|----------|--------|------|
| `quick_check` | quick | 快速检查：CPU/内存/磁盘/负载 |
| `full_check` | check | 完整检查：含 systemd、进程、Docker |
| `report_generate` | report | 生成 Markdown 报告 |

### 调用示例（Agent 侧）

```text
run_skill_script(skill_id, "quick_check", "quick")
run_skill_script(skill_id, "full_check", "check")
run_skill_script(skill_id, "report_generate", "report")
```

阈值可通过 `extra_args` 覆盖（可选）：

```text
run_skill_script(skill_id, "full_check", "check",
  extra_args='{"disk-warn":"80","mem-warn":"85"}')
```

## 输出要求

- 以脚本 JSON（`code` / `msg` / `data`）为准
- `data.evaluation.overall`：`OK` / `WARN` / `CRIT`
- 汇总 WARN/CRIT 项并给出简短 remediation

## 安全红线

- **只读**：禁止远程写操作、重启、改配置
- 凭证仅存在于平台注入的子进程环境变量中
- SSH 失败时明确报错，勿臆测主机状态
