---
name: linux-firewall-port-hardening
description: >
  发现主机存在非必要端口对外暴露时使用。引导只读核查监听端口与防火墙状态，
  再开启防火墙并将对外放行收敛为仅 22（SSH）与 80（HTTP），关闭其余暴露。
version: "1.0.0"
skill_type: doc
instance_type: linux
risk_level: high
---

# 端口暴露加固：开启防火墙并仅放行 22 / 80

## 何时使用

- 巡检 / 安全扫描发现主机有多余端口对公网或非受信网段开放
- 用户提到「端口暴露了」「要关端口」「开防火墙」
- 应急处置：对外只保留 **22（SSH）** 与 **80（HTTP）**

**不适用**：未确认业务依赖就直接全阻断；生产变更窗口外的批量改防火墙；需要 HTTPS 时未评估 **443**（本 Skill 默认不放行 443）。

## 执行规则

1. 必须先 `read_skill` 阅读本文档。
2. 会话需绑定 `param_instance_id`（`server_instance` / Linux 主机）。
3. **先只读核查，再出方案；写操作（启防火墙、改规则）必须用户确认后执行。**
4. 禁止未经确认执行 `iptables -F`、`ufw --force reset`、关闭 SSH(22) 等可能导致失联的操作。
5. 变更前后保留命令输出，便于回滚对照。

## 目标策略

| 方向 | 端口 | 动作 |
|------|------|------|
| 入站 | 22/tcp | 允许（SSH 管理） |
| 入站 | 80/tcp | 允许（HTTP 业务） |
| 入站 | 其他 | 拒绝 / 不开放 |
| 出站 | — | 保持默认（除非另有要求） |

## 推荐流程

### 1. 只读摸底（必须）

在目标 Linux 上采集（按环境选用）：

- 监听端口：`ss -lntup` 或 `netstat -lntup`
- 防火墙状态：`ufw status verbose` / `firewall-cmd --state` / `iptables -L -n -v`
- 公网可达性（可选）：从外部或平台侧核对当前暴露面

输出：**当前开放端口清单**、**防火墙是否已启用**、**哪些端口超出 22/80**。

### 2. 影响评估（必须向用户说明）

对照业务确认：

- 关闭后是否影响管理通道（务必保留 22，且确认本机有可达 SSH）
- 80 是否为真实业务入口；若实际走 443，需用户明确是否调整放行策略
- 数据库、中间件、调试端口（如 3306、6379、8080、9090）是否误暴露

### 3. 处置方案（用户确认后再执行）

推荐优先 **ufw**（Ubuntu/Debian）：

```bash
# 启用防火墙（确认 SSH 可达后再开）
ufw default deny incoming
ufw default allow outgoing
ufw allow 22/tcp
ufw allow 80/tcp
ufw --force enable
ufw status numbered
```

若为 **firewalld**（RHEL/CentOS）：

```bash
systemctl enable --now firewalld
firewall-cmd --permanent --set-default-zone=public
firewall-cmd --permanent --add-service=ssh
firewall-cmd --permanent --add-service=http
# 视情况删除多余的 permanent rich rule / port
firewall-cmd --reload
firewall-cmd --list-all
```

原则：

- **先加允许规则，再启用防火墙**，避免把自己锁在门外
- 对已监听但不应对外的端口：用防火墙阻断入站；能关服务则建议后续下线监听
- 云厂商安全组若存在：提醒同步收紧（主机防火墙 ≠ 安全组）

### 4. 复测与回滚

复测：

- `ufw status` / `firewall-cmd --list-all` 仅见 22、80（及系统必要项）
- `ss -lntup` 对照：多余端口对外不可达

回滚（需确认）：按变更前备份的规则恢复，或临时 `ufw disable` / 放行紧急管理端口。

## 输出要求

- 变更前暴露面 vs 变更后放行清单（强调仅 22、80）
- 已执行命令摘要与结果
- 残留风险：仍在监听但已被防火墙挡住的端口、安全组是否一致
- 明确「已做 / 建议 / 勿做」
