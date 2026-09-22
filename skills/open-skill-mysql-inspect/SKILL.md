---
name: aiops-mysql-full-inspection
description: >
  对平台绑定的 MySQL 实例执行全维度深度巡检：基础健康、连接负载、性能慢查询、
  主从复制、账号安全、Prometheus 时序趋势与报告生成。
  适用于用户要求 MySQL 健康检查、综合巡检、风险扫描、主从核查、安全检测并生成报告时使用。
version: "1.0.0"
skill_type: package_cli
instance_type: db
risk_level: low
---

# AIOps MySQL 全维度深度巡检

基于 AIOps Skill 规范 v1 的平台原生 MySQL 巡检技能。巡检逻辑参考 YL MySQL 巡检能力，运行时通过 **`param_instance_id`** 注入数据库凭证，**禁止**在对话或命令中手写密码。

## 何时使用

- 用户要求 **MySQL 全链路健康检查 / 综合巡检 / 风险扫描**
- 需要检查慢查询、连接堆积、主从延迟、账号安全
- 需要结合 Prometheus 做 24h 趋势分析（可选）
- 需要生成 Markdown 巡检报告或推送飞书（可选）

**不适用**：DDL/DML 变更、账号创建、实例重启等写操作（本 skill 模块均为只读巡检，报告推送需确认）。

## 执行规则

1. 必须先调用 `read_skill(skill_id)` 阅读本文档。
2. 确认当前会话已绑定 **`param_instance_id`**（`sys_db_instance`）。
3. 执行脚本 **仅使用** `run_skill_script(skill_id, entry, action, extra_args)`。
4. **禁止**手写 `python3 ... --password`、禁止向用户索要数据库密码。
5. 按推荐顺序执行各 entry，根据 JSON 输出汇总风险与 remediation。
6. Prometheus / 飞书推送所需 URL 通过 `extra_args` JSON 传入（见下表）。

## 平台集成说明（AIOps）

| 项 | 说明 |
|----|------|
| 实例 | 前端传入 `param_instance_id`，平台解析 `$instance.host/port/username/password` |
| 执行 | `run_skill_script(skill_id, entry, action, extra_args?)` |
| 凭证 | 由 `db_resolver` 注入 CLI，不进 LLM prompt |
| 报告目录 | 技能包内 `reports/`（运行时 cwd 为 skill 根目录） |

依赖安装（monitor-core 宿主机）：

```bash
pip3 install -r requirements.txt
```

## 推荐巡检流程

```
basic_check → connection_scan → performance_audit → architecture_check
  → security_scan → [prometheus_metrics] → report_generate → [report_push]
```

## 模块与 platform.json 入口

| entry id | action | 说明 |
|----------|--------|------|
| `basic_check` | check | 连通性、版本、运行时长、max_connections、字符集 |
| `connection_scan` | scan | 会话数、连接使用率、超时空闲连接（threshold=300） |
| `performance_audit` | audit | 慢查询、长事务、冗余索引等 |
| `architecture_check` | check | 主从角色、IO/SQL 线程、复制延迟 |
| `security_scan` | scan | 空密码/通配符账号、高危配置 |
| `prometheus_metrics` | metrics | Prometheus 时序趋势（需 extra_args） |
| `report_generate` | generate | 生成 Markdown 报告 |
| `report_push` | push | 飞书推送（需 extra_args，需确认） |

### 调用示例（Agent 侧）

```text
run_skill_script(skill_id, "basic_check", "check")
run_skill_script(skill_id, "connection_scan", "scan")
run_skill_script(skill_id, "prometheus_metrics", "metrics",
  extra_args='{"prom-url":"http://PROMETHEUS:9090"}')
run_skill_script(skill_id, "report_push", "push",
  extra_args='{"webhook-url":"https://...","report-path":"./reports/xxx.md"}')
```

`prometheus_metrics` 的 `instance` 标签由平台自动拼为 `{host}:{port}`。

## 输出要求

- 以各脚本 stdout 的 JSON（含 `code` / `msg` / `data` / `risk`）为准
- 汇总 CRIT/WARN 项并给出简短 remediation
- 某模块失败不阻塞其他模块，但在最终结论中说明失败原因

## 错误处理

脚本返回非 0 或 JSON 中 `code != 0` 时，优先阅读 `msg`、`solve_tips` 字段，勿臆测根因。

## 注意事项

- 兼容 MySQL 5.7 / 8.0；巡检账号需具备 PROCESS、REPLICATION CLIENT 等只读权限
- Prometheus、飞书为可选能力，未配置时可跳过对应 entry
- 本包为 **AIOps 平台内置技能**，位于 `monitor-core/skills/MySQL`，上传时打包为 zip（含 `platform.json`）
