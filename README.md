# Open-AIOps

**炎龙智能 Agentic AIOps**

大模型驱动的多智能体协同运维平台。把告警、巡检、诊断、配置变更和受控处置串成一条线，覆盖基础设施、平台层与应用层 60+ IT 组件。

[官网](https://www.yanlong-ai.com) · [立即体验](https://aiops.yanlong-ai.com) · [申请免费部署](https://www.yanlong-ai.com)

[![官网](https://img.shields.io/badge/官网-yanlong--ai.com-1f6feb)](https://www.yanlong-ai.com)
[![立即体验](https://img.shields.io/badge/Demo-aiops.yanlong--ai.com-22c55e)](https://aiops.yanlong-ai.com)
[![Agentic AIOps](https://img.shields.io/badge/Product-Agentic%20AIOps%20V4-0ea5e9)](https://www.yanlong-ai.com)

## 产品界面

**AI 天穹首页** — 值班总览、快捷入口、告警与待办

<img src="img/screenshot-home.png" alt="Agentic AIOps 天穹首页" width="100%" />

**AI 根因分析** — 告警归组、分析状态、一键分配与追问

<img src="img/screenshot-rca.png" alt="AI 根因分析" width="100%" />

**AI 巡检报告** — 只读巡检、命令可核验、导出报告

<img src="img/screenshot-inspection.png" alt="AI 巡检详情" width="100%" />

**AI 预测** — 趋势曲线、打满预警、风险详情

<img src="img/screenshot-forecast.png" alt="AI 预测分析详情" width="100%" />

**智能体配置** — 内置 / 自定义智能体、组件专家编排

<img src="img/agents.png" alt="智能体配置管理" width="100%" />

**组件纳管** — Elasticsearch 等 60+ 组件查询与分析

<img src="img/components.png" alt="Elasticsearch 组件查询" width="100%" />

**工单 Duty** — 审核、执行、驳回全流程闭环

<img src="img/others.png" alt="工单 Duty" width="100%" />

**工作流 / Chatflow** — 可视化编排、发布为对话应用

<img src="img/workflow.png" alt="Chatflow 工作流编排" width="100%" />

**主机纳管** — 添加 Linux / 网络 / 虚拟化等主机，绑定智能体

<img src="img/servers.png" alt="添加主机" width="100%" />

---

## 解决什么问题

监控工具有了，值班还是靠人。告警、排障、改配置、修故障四个环节仍卡在经验和班次上。

| 传统痛点 | 平台能力 |
|---------|---------|
| 巡检靠人，漏检是常态 | **AI 智能体巡检** |
| 告警风暴，根因靠经验 | **AI 智能体根因分析** |
| 配置手改，变更风险高 | **AI 智能体智能化配置** |
| 故障等人上，无法 7×24 | **AI 智能体自愈** |

完整能力与案例见官网：[www.yanlong-ai.com](https://www.yanlong-ai.com)

---

## 产品能力

- **对话式运维**：用自然语言查指标、看日志、做诊断与变更，高危操作可审批
- **智能巡检**：对主机、数据库、K8s、网络设备等定时 / 手动巡检，输出可读报告
- **根因分析**：告警自动归组分析，跨组件定位，分析后可继续追问
- **容量预测**：资源趋势与打满预警，提前扩容 / 清理
- **工单闭环**：审核、执行、驳回可追溯
- **工作流编排**：把常用运维场景编排成可复用流程
- **全栈纳管**：对话式覆盖 60+ IT 组件，支持自定义扩展
- **私有化交付**：数据不出企业边界，支持等保场景与离线授权

多模型即插即用（DeepSeek、Qwen、GPT、Kimi 等），云端 API 与私有化推理均可。

---

## 支持组件（节选）

完整清单见官网 [组件智能运维](https://www.yanlong-ai.com)。

| 类别 | 组件 |
|------|------|
| **关系型 / 国产库** | MySQL、PostgreSQL、Oracle、SQL Server、达梦、openGauss、金仓、OceanBase |
| **NoSQL / 分析** | Redis、MongoDB、Elasticsearch、ClickHouse、Neo4j、InfluxDB、TDengine、StarRocks、Doris |
| **消息 / 注册** | Kafka、RabbitMQ、RocketMQ、Nacos、ZooKeeper、etcd |
| **云原生** | Kubernetes、Prometheus、Grafana、SkyWalking |
| **基础设施** | Linux / Windows、交换机 / 路由器 / 防火墙、vCenter / 超融合 |
| **可观测 / DevOps** | Zabbix、Loki、Jenkins、GitLab |
| **物联网** | MQTT、传感器与边缘节点 |

---

## 适用场景

- 7×24 告警值班与故障定位
- 周期性健康巡检与等保辅助
- 日常对话运维，少切工具窗口
- 已有监控平台的智能化升级（不必推倒重来）
- MSP / 集成商多客户交付

---

## 如何体验

1. **在线试用**：[https://aiops.yanlong-ai.com](https://aiops.yanlong-ai.com)
2. **申请免费部署**：[https://www.yanlong-ai.com](https://www.yanlong-ai.com) 提交表单，或扫码加微信
3. **资源评估**：官网「资源评估」页，按规模给出部署建议

标准环境通常约 1 周完成部署；复杂环境定制集成约 2 周。

---

## 联系我们（微信最快）

<p align="center">
  <img src="img/szh1.jpg" alt="售前微信二维码" width="220" />
  <br />
  <sub>扫码添加售前微信</sub>
</p>

- 扫上方二维码添加 **售前** 销售经理微信
- 官网首页「申请免费部署」在线提交
- 电话：0532-83868627
- 地址：中国（山东）自由贸易试验区青岛片区前湾保税港区鹏湾路 45 号东办公楼一楼 102 室

---

## 版权

© 2025–2026 炎龙智能（Yanlong AI）· [www.yanlong-ai.com](https://www.yanlong-ai.com)

本仓库仅作产品介绍与体验入口。**炎龙智能 Agentic AIOps** 平台版权归炎龙智能所有，须经授权使用。

炎龙智能、Agentic AIOps、AI 天穹等为炎龙智能相关品牌或产品名称。
