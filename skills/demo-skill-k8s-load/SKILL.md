---
name: aiops-k8s-load-handling
description: >
  通过 SSH 登录 kubectl 所在 Linux，对 K8s 集群执行节点高负载检测、TOP Pod 定位、
  Pod 驱逐与排障报告。适用于节点 CPU/内存>70%、Pod 资源争抢、需远程 kubectl 处置时使用。
version: "1.1.0"
skill_type: package_kube
instance_type: k8s
risk_level: medium
---

# AIOps K8s 负载检测与处理

平台原生 K8s 负载处置技能。**monitor-core 本机不直接跑 kubectl**，而是通过 **`param_instance_id`** 注入 SSH 凭证，登录 **kubectl 所在 Linux 服务器** 远程执行命令（与 `k8s_tool` 一致）。

## 何时使用

- 节点 CPU 或内存 **>70%**，需远程 kubectl 查看集群负载
- 定位高负载节点上的 **TOP Pod**
- **驱逐** 高资源 Pod 到低负载节点（需人工确认）
- 生成 Markdown 报告或推送飞书

## 执行规则

1. 必须先 `read_skill(skill_id)`。
2. 确认会话已绑定 **`param_instance_id`**（`server_instance`，`server_type=k8s_cluster`）。
3. **仅使用** `run_skill_script(skill_id, entry, action, extra_args)`。
4. **禁止**手写 SSH 密码、kubeconfig 或临时 kubectl。
5. 推荐：`node_metrics` → `pod_locate` → `pod_evict preview` → 确认 → `pod_evict_execute` → 复测 → `report_generate`。

## 平台集成说明（AIOps）

| 项 | 说明 |
|----|------|
| 凭证模式 | `env_kube_ssh` |
| 实例来源 | `param_instance_id` + `param_table_name` |
| 注入变量 | `KUBE_SSH_HOST/PORT/USER/PASSWORD`（可选 `KUBE_REMOTE_KUBECONFIG`） |
| 执行位置 | **远程 Linux**（跳板机已安装 kubectl 且配好 kubeconfig） |
| LLM | **不可见** SSH 密码 |

### 实例来源（与 Linux 巡检共用 server_instance）

| 字段 | 说明 |
|------|------|
| `server_type` | 填 **`k8s_cluster`**（K8s 集群跳板 / kubectl 所在 Linux） |
| `ip_address` | SSH 主机 IP |
| `ssh_port` / `ssh_username` / `ssh_password` | SSH 登录凭证 |
| `ssh_private_key` | 可选，密钥登录 |

远程 kubeconfig 默认使用跳板机当前用户下的配置；若路径非默认，可通过 `extra_args` 传 `remote-kubeconfig`（映射为 `KUBE_REMOTE_KUBECONFIG`）。

> **说明**：K8s 与 Linux 主机统一存于 `server_instance`，靠 `server_type` 区分；**不需要**单独的 `k8s_instance.kubectl_*` 字段。

## 推荐流程

```
node_metrics → pod_locate → pod_evict(preview) → [确认] → pod_evict_execute → node_metrics → report_generate
```

## 模块入口

| entry id | action | 说明 |
|----------|--------|------|
| `node_metrics` | query | 节点负载、高负载节点清单 |
| `pod_locate` | find | TOP Pod 定位 |
| `pod_evict` | preview | 驱逐预览 |
| `pod_evict_execute` | execute | 执行驱逐（需 confirm） |
| `report_generate` | generate | 生成报告 |
| `report_push` | push | 飞书推送 |

### 调用示例

```text
run_skill_script(skill_id, "node_metrics", "query", extra_args='{"threshold":"70"}')
run_skill_script(skill_id, "pod_evict", "preview",
  extra_args='{"pod-name":"app-xxx","namespace":"default"}')
run_skill_script(skill_id, "pod_evict_execute", "execute",
  extra_args='{"pod-name":"app-xxx","namespace":"default","confirm":"true"}')
```

## 安全红线

- 禁止驱逐 `kube-system` 等核心 Pod
- 禁止无 preview 直接 execute；单次建议驱逐 ≤1 个业务 Pod
- 结论必须来自脚本 JSON 输出
