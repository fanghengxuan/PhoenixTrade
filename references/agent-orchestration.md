# 子 Agent 编排规范

## Agent 列表

| Agent | 名称 | 角色 | 通信方式 | context | 调用方 | 时机 |
|-------|------|------|----------|---------|--------|------|
| research-coordinator | 投研秘书 | 调度取数中枢 | sessions_spawn | isolated | 投委会 | 阶段一 |
| fundamental-analyst | 基本面分析师 | 财务数据取数 | sessions_spawn | isolated | 投研秘书 | 阶段一 |
| quant-analyst | 量化分析师 | 行情数据取数 | sessions_spawn | isolated | 投研秘书 | 阶段一 |
| compliance-officer | 合规官 | ESG 扫描 | sessions_spawn | isolated | 投研秘书 | 阶段一 |
| bull-analyst | 多头研究员 | 辩论多方 | sessions_spawn + exec(cat) | isolated | 投委会 | 阶段二 |
| bear-analyst | 空头研究员 | 辩论空方 | sessions_spawn + exec(cat) | isolated | 投委会 | 阶段二 |
| risk-control | 风控部 | 独立审计 | sessions_send | — | 投委会 | 阶段二 |
| archivist | 档案管理员 | 持久化存档 | sessions_spawn | isolated | 投委会 | 阶段三 |

## 通信模式

### spawn 模式（投委会 → 子 Agent）
- 使用 sessions_spawn 派出
- context=isolated（不需对话上下文）
- mode=run（后台运行）
- 子 Agent 嵌套深度最大 1 层

### 辩论文件模式（多头/空头）
- 多头研究员：将论据写入 `memory/bull_{代码}.md`
- 空头研究员：将论据写入 `memory/bear_{代码}.md`
- 投委会通过 `exec(cat ...)` 读取结果文件
- 子 Agent 超时 → 尝试读文件 → 文件也不存在则标注 UNA

### sessions_send 模式（投委会 ↔ 风控部）
- 平级通信，投委会不是风控部的上级
- 发送时设置 timeoutSeconds=120，同步等待
- 超时重试 1 次，仍超时则上报人工介入

## 嵌套深度规则
- maxSpawnDepth=1，子 Agent 不能再 spawn 自己的子 Agent
