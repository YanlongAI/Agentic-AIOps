---
name: aiops-djbh-assessment
description: >
  对平台绑定的 Linux 目标主机执行等保2.0 技术测评：计算环境基线、端口边界、TLS、
  漏洞扫描与报告汇总。适用于等保二级/三级差距分析、现场技术核查时使用。
version: "1.1.0"
skill_type: package_env
instance_type: linux
risk_level: high
---

# AIOps 等保2.0 测评技能

平台原生 Linux 等保技术测评技能。运行时通过 **`param_instance_id`**（`server_instance`）注入 SSH 凭证，**禁止**在对话或命令中手写密码。凭证与执行方式与 **Linux 服务器巡检** 完全一致。

## 何时使用

- 用户要求对 **Linux 服务器** 做等保测评、差距分析、基线核查
- 需要计算环境 + 区域边界（端口）+ 通信网络（TLS）+ 漏洞扫描
- 需要生成技术测评 artifact 与 Markdown 附件

**不适用**：未获书面授权的渗透、口令爆破、DoS；非 Linux 主机（Windows/网络设备需其他方式）。

## 执行规则

1. 必须先 `read_skill(skill_id)` 阅读本文档。
2. 确认会话已绑定 **`param_instance_id`**（`server_instance`，`server_type=linux_host`）。
3. 脚本 **仅通过** `run_skill_script(skill_id, entry, action, extra_args)` 执行。
4. **禁止** `echo $PATROL_*`、`printenv`、向用户索要 SSH 密码。
5. 推荐：`target_assessment` →（授权后）`vuln_scan` → `report_generate`。
6. 多台被测主机：逐台切换 `param_instance_id` 后重复执行。

## 平台集成说明（AIOps）

| 项 | 说明 |
|----|------|
| 实例 | `param_instance_id` → `server_instance`（`ip_address`、`ssh_port`、`ssh_username`、`ssh_password`） |
| 凭证模式 | `env_patrol` |
| 注入变量 | `PATROL_SERVER`、`PATROL_SSH_PASSWORD`（平台 subprocess 注入，不进 LLM） |
| 执行位置 | monitor-core 发起 **SSH 连接目标主机**，远程只读采集/扫描 |
| 报告目录 | 技能包 `reports/` |

`PATROL_SERVER` 格式（平台自动拼装，与 Linux 巡检相同）：

```text
{server_name}|{ssh_username}@{ip_address}:{ssh_port}|production
```

## 推荐流程

```
target_assessment → [授权] vuln_scan → report_generate
```

管理域（制度/访谈/物理环境）见 `references/`；与脚本并行由 Agent 引导完成。

## 模块与 platform.json 入口

| entry id | action | 说明 |
|----------|--------|------|
| `target_assessment` | assess | **主入口**：基线 + 端口 + TLS |
| `linux_baseline` | check | 仅计算环境基线 |
| `port_scan` | scan | 高危端口（默认绑定主机 IP） |
| `tls_check` | check | TLS/SSL（默认绑定主机:443） |
| `vuln_scan` | scan | 漏洞扫描（需 `confirm=true`） |
| `asset_discovery` | scan | 可选网段发现（需 `target-cidr`） |
| `report_generate` | generate | 汇总 Markdown 报告 |

### 调用示例（Agent 侧）

```text
run_skill_script(skill_id, "target_assessment", "assess", extra_args='{"level":"3"}')
run_skill_script(skill_id, "linux_baseline", "check", extra_args='{"level":"3"}')
run_skill_script(skill_id, "port_scan", "scan")
run_skill_script(skill_id, "vuln_scan", "scan", extra_args='{"confirm":"true"}')
run_skill_script(skill_id, "report_generate", "generate",
  extra_args='{"project-name":"XX系统","level":"3"}')
```

等保级别可通过 `extra_args` 覆盖：

```text
run_skill_script(skill_id, "target_assessment", "assess", extra_args='{"level":"2"}')
```

## 输出要求

- 以脚本 JSON（`code` / `msg` / `data`）为准
- `data.overall`：`OK` / `WARN` / `CRIT`
- artifact 存 `reports/*.json`；汇总 CRIT/WARN 并引用 `references/remediation-guide.md`

## 安全红线

- **只读**（`vuln_scan` 为授权扫描，非改配置）
- 凭证仅存在于平台注入的子进程环境变量中
- SSH 失败时明确报错，勿臆测主机状态
- 无书面授权禁止 `vuln_scan`

## 法规与流程参考

GB/T 22239-2019、GB/T 28448-2019 — 详见 `references/legal-refs.md`、`references/domains-overview.md`。
