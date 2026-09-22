---
name: aiops-network-device-patrol
description: >
  对平台绑定的网络设备（交换机/路由器/防火墙）执行只读健康巡检：版本、CPU/内存、
  接口状态、风扇电源、活跃告警，并生成 Markdown 报告。须指定设备厂商（华为/H3C/思科等），
  适用于网络设备巡检、交换机健康检查、链路状态核查时使用。
version: "1.0.0"
skill_type: package_env
instance_type: network
risk_level: low
---

# AIOps 网络设备巡检

平台原生网络设备只读巡检技能，遵循 AIOps Skill 规范 v1。运行时通过 **`param_instance_id`**（`server_instance`，`server_type=network`）注入 SSH 凭证，**必须指定设备厂商**，**禁止**在对话或命令中手写密码。

## 为何必须指定厂商

网络设备 CLI 无统一标准，各厂商命令体系不同：

| 厂商 | 系统 | 典型命令风格 |
|------|------|-------------|
| **华为** | VRP | `display version`、`display interface brief` |
| **H3C** | Comware | 与华为相近，部分子命令有差异 |
| **思科** | IOS / IOS-XE / NX-OS | `show version`、`show interfaces status` |
| **锐捷** | RGOS | 混合风格，部分兼容思科 `show` |
| **中兴** | ZXROS | 自有 `show` / `display` 混用 |

本技能 v1 内置驱动：**huawei**、**h3c**、**cisco**。其他厂商请先确认 CLI 族再扩展驱动。

### 国内主流选型（2024–2026）

- **政企 / 运营商 / 金融**：**华为** 份额最高，**H3C（新华三）** 次之
- **教育 / 园区网**：**锐捷**、H3C 较多
- **跨国企业 / 传统 IDC**：**思科** 仍常见
- **白盒 / 云数据中心**：SONiC、自研较多，本技能暂不支持

绑定实例时请在备注或 `extra_args` 中明确 `vendor`，避免命令误用导致误报。

## 何时使用

- 用户要求 **网络设备巡检 / 交换机健康检查 / 路由器状态核查**
- 需要查看 CPU、内存、接口 up/down、风扇电源、活跃告警
- 需要生成 Markdown 巡检报告

**不适用**：改配置、保存配置、重启设备、下发 ACL/VLAN 等写操作。

## 执行规则

1. 必须先 `read_skill(skill_id)` 阅读本文档。
2. 确认会话已绑定 **`param_instance_id`**（`server_instance`，建议 `server_type=network`）。
3. **必须**通过 `extra_args` 传入 `vendor`（`huawei` / `h3c` / `cisco`），未指定时脚本尝试自动识别，识别失败则报错。
4. 脚本 **仅通过** `run_skill_script(skill_id, entry, action, extra_args)` 执行。
5. **禁止** `echo $PATROL_*`、`printenv`、向用户索要 SSH 密码。
6. 推荐：`quick_check` → `full_check` → `report_generate`。

## 平台集成说明（AIOps）

| 项 | 说明 |
|----|------|
| 实例 | `param_instance_id` → `server_instance`（ip、ssh_port、ssh_username、ssh_password） |
| 凭证模式 | `env_patrol`（与 Linux 巡检相同，SSH 登录设备 CLI） |
| 注入变量 | `PATROL_SERVER`、`PATROL_SSH_PASSWORD`（平台 subprocess 注入，不进 LLM） |
| 厂商参数 | Agent 通过 `extra_args` 传 `vendor`；思科可选 `enable-password` |
| 执行位置 | monitor-core 发起 **SSH 连接目标设备**，远程只读采集 |
| 报告目录 | 技能包 `reports/` |

`PATROL_SERVER` 格式（平台自动拼装）：

```text
{device_name}|{ssh_username}@{ip_address}:{ssh_port}|network
```

### 调用示例（Agent 侧）

```text
# 华为交换机快速巡检
run_skill_script(skill_id, "quick_check", "quick",
  extra_args='{"vendor":"huawei"}')

# H3C 完整巡检
run_skill_script(skill_id, "full_check", "check",
  extra_args='{"vendor":"h3c"}')

# 思科（需 enable 密码时）
run_skill_script(skill_id, "full_check", "check",
  extra_args='{"vendor":"cisco","enable-password":"***"}')

run_skill_script(skill_id, "report_generate", "report",
  extra_args='{"vendor":"huawei"}')
```

阈值可通过 `extra_args` 覆盖（可选）：

```text
extra_args='{"vendor":"huawei","cpu-warn":"80","mem-warn":"85","iface-down-warn":"3"}'
```

## 推荐流程

```
quick_check → full_check → report_generate
```

## 模块与 platform.json 入口

| entry id | action | 说明 |
|----------|--------|------|
| `quick_check` | quick | 快速：版本、CPU、内存、接口摘要 |
| `full_check` | check | 完整：含风扇/电源、活跃告警、异常接口 |
| `report_generate` | report | 生成 Markdown 报告 |

## 巡检项说明

### quick_check

- 设备型号 / 软件版本 / 运行时长
- CPU 使用率（5 秒 / 1 分钟）
- 内存使用率
- 接口 up/down 统计

### full_check（在 quick 基础上）

- 风扇、电源模块状态
- 活跃告警（`display alarm active` / `show logging`）
- down 接口清单、错误计数较高的接口

## 输出要求

- 以脚本 JSON（`code` / `msg` / `data`）为准
- `data.evaluation.overall`：`OK` / `WARN` / `CRIT`
- `data.vendor`：实际使用的厂商驱动
- 汇总 WARN/CRIT 项并给出简短 remediation

## 错误处理

- SSH 连接失败：检查管理 IP、SSH 服务、账号权限
- 厂商未识别：在 `extra_args` 中显式传 `vendor`
- 思科无 enable 权限：部分 `show` 命令可能受限，补充 `enable-password`
- 某子项采集失败不阻塞整体，在 `data.partial_errors` 中列出

## 安全红线

- **只读**：禁止 `save`、`write`、`reload`、`reset`、`delete` 等写操作
- 凭证仅存在于平台注入的子进程环境变量中
- SSH 失败时明确报错，勿臆测设备状态

## 注意事项

- 巡检账号需具备 **只读（monitor/audit）** 权限即可
- 防火墙、负载均衡等设备若 CLI 与交换机差异大，需单独扩展驱动
- 大规模巡检建议先 `quick_check` 批量筛查，再对异常设备 `full_check`
