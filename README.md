# Open-AIOps

**炎龙智能 Agentic AIOps**

大模型驱动的多智能体协同运维平台。把对话运维、巡检、根因分析、容量预测、告警处置和资产台账串成一条线，覆盖主机、数据库、中间件、云与可观测组件。

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

监控工具有了，值班还是靠人。告警归组、巡检、容量判断和处置留痕，仍然卡在经验和班次上。

| 传统痛点 | 平台能力 |
|---------|---------|
| 巡检靠人，漏检是常态 | **AI 巡检**，定时或手动出报告 |
| 告警堆在一起，根因靠经验 | **AI 根因分析**，归组后可继续追问 |
| 容量问题总是事后才发现 | **AI 预测**，看趋势和风险等级 |
| 处置没有留痕 | **工单 Duty**，审核、执行、驳回可追溯 |

完整能力与案例见官网：[www.yanlong-ai.com](https://www.yanlong-ai.com)

---

## 产品能力

下面是当前对外提供的能力。

### 智能运维

- **AI ChatOps**：用自然语言查状态、做诊断，按场景组或具体实例限定范围；高风险处置进入工单审核
- **AI 根因分析**：告警归组、查看分析结论与告警详情，分析后可以继续追问
- **AI 巡检**：按实例定时或手动巡检，查看待执行 / 执行中 / 成功 / 失败，导出可读报告
- **AI 预测**：按风险大类和等级查看容量与资源风险，打开趋势和风险详情

### 实例纳管

在「实例管理」中登记并查询这些对象，对话和巡检都基于已纳管实例：

| 类别 | 组件 |
|------|-----------|
| **主机** | Linux、Windows，以及交换机 / 路由器 / 防火墙等网络设备 |
| **关系型与国产库** | MySQL、PostgreSQL、Oracle、SQL Server、DB2、达梦、openGauss、人大金仓、OceanBase、神州通用、南大通用 |
| **缓存 / 文档 / 分析** | Redis、MongoDB、Elasticsearch、ClickHouse |
| **消息 / 注册** | Kafka、RabbitMQ、RocketMQ、Nacos |
| **云与虚拟化** | Kubernetes 集群、vCenter、阿里云 ECS |
| **可观测** | Prometheus、Loki、Zabbix、链路追踪（SkyWalking） |
| **交付** | Jenkins、GitLab、Slurm |

### 智能体与编排

- **LLM**：接入云端或私有化模型
- **智能体**：内置与自定义智能体、按组件编排
- **MCP 与工具、AI 快捷键**：扩展对话能调用的能力，把常用问法收成快捷入口
- **Skills**：把一类运维场景收成可复用技能。本仓库 [`skills/`](skills/) 收录 8 个技能，上传后即可在对话和巡检里选用

| Skill | 能做什么 |
|------|----------|
| **Linux 服务器巡检** | 只读检查负载、CPU、内存、磁盘、失败服务、高占用进程和容器状态，并生成报告 |
| **MySQL 全维度巡检** | 检查实例健康、连接、慢查询、主从复制和账号安全，可结合近期趋势出报告 |
| **网络设备巡检** | 对华为、H3C、思科等交换机 / 路由器 / 防火墙做只读巡检，覆盖版本、资源、接口、电源风扇和活跃告警 |
| **K8s 负载检测与处理** | 找出高负载节点和占用高的 Pod，确认后再做迁移，并留下报告 |
| **等保 2.0 技术测评** | 对已授权的 Linux 主机做基线、端口边界、通信加密和漏洞核查，汇总测评结果 |
| **Redis 内存打满** | 先排查大 Key 和过期策略，再给出清理或扩容方案；改动需确认后执行 |
| **告警风暴降噪** | 把短时间刷屏的同类告警归拢，临时压住噪声，真实故障再进入根因分析 |
| **端口暴露加固** | 先核对主机对外端口，确认后把放行范围收敛到管理与业务所需端口 |
- **场景组**：把一组实例收成一个运维场景，对话时一次选中
- **工作流编排**：把常用场景画成流程，发布为对话应用

### 告警与值班

- **可视化大屏**：值班一眼看到运行态势
- **告警规则**：按来源、指标和覆盖范围配置条件，支持静默
- **告警通知**：飞书、钉钉、企业微信、邮件、短信，并保留发送记录
- **监控源配置**：接入已有监控数据
- **工单**：待审核、已通过待执行、已驳回、已执行
- **值班**：排班与交接

### 资产

- **资产总览**：按类型、部门和生命周期看数量，提示即将到期的维保
- **资产台账**：服务器、网络设备、终端、办公外设、云资产、系统软件、应用软件、机房机柜、UPS、精密空调
- **生命周期**：采购入库、领用 / 退还、维修 / 维保、报废处置
- **CMDB**：架构拓扑，查看和补齐资产之间的关系
- **盘点审计**、**资产统计**、**维保成本**

### 对接与权限

- **应用管理**、**推送第三方**：把告警和事件交给外部系统
- **组织权限**：租户、用户、角色、部门、岗位
- **审计与系统**：操作日志、文件、字典、参数、定时任务、令牌
- **私有化交付**：数据留在企业内，支持等保场景与离线授权

多模型可切换（DeepSeek、Qwen、GPT、Kimi 等），云端 API 与私有化推理均可。

完整介绍见官网：[www.yanlong-ai.com](https://www.yanlong-ai.com)

---

## 适用场景

- 7×24 告警值班与故障定位
- 周期性健康巡检与等保辅助
- 日常对话运维，少切工具窗口
- 在已有监控上增加对话、巡检和根因分析
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
