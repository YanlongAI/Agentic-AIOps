---
name: redis-memory-oom-playbook
description: >
  当 Redis 内存接近 maxmemory、出现 OOM、evicted_keys 飙升或业务报缓存失效时使用。
  引导只读排查大 key / 过期策略，再给出受控清理与扩容方案。
version: "1.0.0"
skill_type: doc
instance_type: redis
risk_level: medium
---

# Redis 内存打满处置（文档型 Skill）

## 何时使用

- Redis 内存使用率持续 >85%，或告警 `used_memory` 逼近 `maxmemory`
- `evicted_keys` 快速增长、业务偶发缓存穿透
- 用户明确要求「先排查再动手」

**不适用**：未确认的 `FLUSHALL` / 批量 `DEL`；生产直接改 `maxmemory-policy` 而不评估影响。

## 执行规则

1. 必须先 `read_skill` 阅读本文档，再调用平台 Redis 只读工具。
2. 会话需绑定 `param_instance_id`（Redis 实例）。
3. 写操作（删 key、改配置）必须先出方案，用户确认后再执行。

## 推荐流程

1. **只读摸底**：`INFO memory`、`INFO stats`、`CONFIG GET maxmemory*`、`DBSIZE`
2. **定位大 key**：抽样扫描 / 大 key 分析，记录 TopN key 与类型
3. **判断策略**：淘汰策略是否合理；是否存在无 TTL 的业务缓存
4. **处置方案**（需确认）：
   - 临时：对确认的冷 key 分批删除或设置 TTL
   - 中期：上调 `maxmemory` 或扩容分片
   - 长期：业务侧限流 / 缓存分层 / 禁止大 value
5. **复测**：对比内存曲线与 `evicted_keys`，输出简报

## 输出要求

- 给出证据（命令摘要）与风险等级
- 写操作前后对比
- 明确「已做 / 建议 / 勿做」
