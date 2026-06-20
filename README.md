# Phoenix+ 投资决策委员会 Skill

**Phoenix+ Investment Committee** — 机构化投资决策 AI 编排框架。

将 Phoenix+ 投资系统的多 Agent 决策流程打包为跨平台 Skill，支持 OpenClaw / OpenAI / Claude / VS Code 等平台。

## 架构

```
用户 → 投委会（决策中枢）
           │
           ├── spawn → 投研秘书 → spawn × 3（基本面/量化/合规）
           ├── spawn → 多头研究员 → 写文件 → exec(cat) 读
           ├── spawn → 空头研究员 → 写文件 → exec(cat) 读
           ├── sessions_send → 风控部（独立审计）
           └── spawn → 档案管理员（存档）
```

## 三阶段流程

| 阶段 | 内容 | 入口 | 出口 |
|------|------|------|------|
| 一 | 数据获取与出身证核验 | 用户旨意 | 出身证摘要 |
| 二 | 规则锚定、估值、多空辩论 | 出身证摘要 | 结论草案（已审计）|
| 三 | 存档与输出 | 审计通过 | 最终结论 |

## 输出

- 五级评级（⭐⭐⭐⭐⭐ 强制买入 ~ ⭐ 回避）
- 置信度标签（高/中/低）
- 操作建议（买入/卖出/持有/观察/回避）
- 安全边际评估 + 交易点位
- 关键风险提示

## 触发关键词

支持自然口语触发：`分析一下` / `帮我看看` / `跑一下` / `评估` / `XX 能不能买` / `XX 怎么样`

## 跨平台适配

| 平台 | 适配方式 |
|------|----------|
| OpenClaw | SKILL.md + interface.yaml |
| OpenAI | metadata-adapter |
| Claude | neutral-source + adapter |
| VS Code | vscode-agent-skills-adapter |
| Generic | 通用 Agent Skills 中立包 |

## 数据源

通过 `stock_check_v614.py`（3104 行，Python 3，依赖 akshare）从新浪 + 东方财富 + 同花顺 + 腾讯财经 + 巨潮获取数据。

## Licence

MIT
