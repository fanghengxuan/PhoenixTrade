# Phoenix+ 投资决策委员会

**把机构化投资决策流程封装成一个可复用的 Agent Skill。**

输入股票代码或公司名称，按“取数核验 → 规则估值 → 多空辩论 → 风控审计”三阶段执行，输出带五级评级、置信度和安全边际的完整投资结论。

## 简介

Phoenix+ 投委会是一套面向 A 股上市公司的多 Agent 决策编排包。它不直接提供行情数据，也不绑定某个运行时，而是用 `SKILL.md`、`interface.yaml` 和 `references/` 定义决策规则、子 Agent 分工、风控触发条件与输出格式，交由宿主平台执行。

适合希望把投资决策过程结构化、留痕、可审计的团队或个人。

## 核心能力

- **三阶段流程**：数据获取与出身证核验 → 规则锚定、估值与多空辩论 → 存档与输出。
- **多 Agent 分工**：投研秘书、基本面 / 量化分析师、合规官、多头 / 空头研究员、风控部、档案管理员。
- **风控机制**：R1 熔断、M1 暂停、ESG 一票否决、风控独立审计与最多 3 轮封驳。
- **结构化输出**：五级评级（强制买入 ~ 回避）、高 / 中 / 低置信度、操作建议、安全边际与交易点位、关键风险。
- **自然语言触发**：`分析一下`、`帮我看看`、`跑一下`、`评估`、`XX 能不能买` 等，附 14 条触发 / 不触发评测用例。
- **可适配的 Skill 包**：遵循 Agent Skills 中立格式，提供 OpenAI / Claude / VS Code / generic 的适配声明。

## 架构

```text
用户 → 投委会（决策中枢）
           │
           ├── spawn → 投研秘书 → spawn × 3（基本面 / 量化 / 合规）
           ├── spawn → 多头研究员 → 写文件 → exec(cat) 读
           ├── spawn → 空头研究员 → 写文件 → exec(cat) 读
           ├── sessions_send → 风控部（独立审计）
           └── spawn → 档案管理员（存档）
```

## 三阶段流程

| 阶段 | 内容 | 入口 | 出口 |
|---|---|---|---|
| 一 | 数据获取与出身证核验 | 用户请求 | 出身证摘要 |
| 二 | 规则锚定、估值、多空辩论 | 出身证摘要 | 结论草案（已审计） |
| 三 | 存档与输出 | 审计通过 | 最终结论 |

## 使用前提

这是 Skill 包，**不包含运行时和行情脚本**。使用前需要：

1. **数据脚本**：`scripts/stock_check_v614.py`（Python 3，依赖 `akshare`、`pandas`、`requests`）。该脚本未随仓库提供，需自行放入 `scripts/`。参考 [`references/data-script.md`](references/data-script.md)。
2. **宿主能力**：支持多 Agent 派发（`sessions_spawn`）、平级通信（`sessions_send`）、命令执行（`exec`）以及消息推送（默认飞书 `message` 工具）。
3. **装载 Skill**：把仓库目录作为 Skill 包放入宿主平台的 skills 目录，并按 [`manifest.json`](manifest.json) / [`agents/interface.yaml`](agents/interface.yaml) 完成适配。

触发示例：

> 分析一下 600519
>
> 帮我看看招商银行

## 兼容性

| 平台 | 适配声明 |
|---|---|
| OpenAI | metadata adapter |
| Claude | neutral source + adapter |
| VS Code | agent-skills source + VS Code notes |
| Generic | Agent Skills 中立包 |

以上为 `manifest.json` / `interface.yaml` 中的适配目标。实际可运行程度取决于宿主是否提供“使用前提”中的多 Agent 与消息工具。

## 目录结构

```text
├── SKILL.md                      # 主流程与触发条件
├── manifest.json                 # 包元数据与目标平台
├── agents/interface.yaml         # 平台适配与执行约束
├── references/
│   ├── agent-orchestration.md    # 子 Agent 分工与通信
│   ├── data-script.md            # 外部数据脚本约定
│   ├── output-format.md          # 输出与推送格式
│   ├── risk-control.md           # 熔断、否决与审计
│   ├── rules.md                  # 决策规则
│   └── valuation-models.md       # 估值模型
└── evals/trigger_cases.json      # 14 条触发评测用例
```

## 已知限制

- 仓库不含 `scripts/stock_check_v614.py`，也不含 `manifest.json` 中列出的 `reports/` 目录。
- 输出默认推送到飞书；其他渠道需要改写 `references/output-format.md`。
- 未提供各平台的逐步安装脚本，适配方式以 `interface.yaml` 声明为准。
- 本项目声明以 MIT 许可发布，但仓库当前未附 `LICENSE` 文件。
