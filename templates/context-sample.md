---
type: ai-context
project: CogFoundry
updated: 2026-04-12
source: CLAUDE.md v1.0
---

# CogFoundry AI 上下文

> 这个文件是此项目的完整背景信息，供 AI 快速理解项目全貌，帮我高效生成文档。
> 每次有新的重要信息，更新这里。原始完整约束文件见 `/Users/daf/CLAUDE.md`。

---

## 一、公司基础

| 项 | 内容 |
|----|------|
| **全球品牌** | CogFoundry — The Global Cognitive Factory for AI Workloads |
| **中国品牌** | 胜算云 |
| **一句话定位** | AI 工作负载生产系统，提供"生产能力"而非仅仅是"模型能力" |
| **核心使命** | 让 AI 从工具变成执行系统，承担软件开发、内容生产、自动化执行等生产级任务 |

**核心价值观：**

| 价值观 | 含义 |
|-------|------|
| Production First | 竞争生产能力，而非模型能力 |
| Massive Scale | 支持 1K / 1M / 1B workloads 的大规模并行 |
| Developer Centric | 以开发者为最终用户和增长杠杆 |
| Transparent Infrastructure | 基础设施透明、可观测、可控 |

**不可妥协的红线：**
- 不做没有监管的 AI 系统
- 不在客户不知情情况下使用客户数据进行模型训练
- 不夸大技术能力（不对客户承诺做不到的事）

---

## 二、产品架构

```
CogFoundry / 胜算云
└── AI SuperFoundry（品牌）
    └── AI Factory OS（核心系统）
        ├── Model Supply Network        # 汇聚全球 LLM API 供给
        ├── Workload Execution Engine   # 执行 API 任务与调度
        ├── Workflow Engine             # 持久化多步骤工作流编排
        ├── Serverless Container Engine # 统一执行运行环境，动态伸缩
        └── Shared Knowledge Network    # 跨用户共享执行记忆与缓存
```

**Container 三种运行模式：**
1. **Agent Runtime** — AI Agent 的专用执行环境
2. **User Software Runtime** — 用户自有软件的运行支持
3. **Batch Inference** — GPU 批量 LLM 推理集群

**核心执行组件：**

| 组件 | 功能 | 适用场景 |
|------|------|---------|
| API Workload Execution Engine | 执行 API 任务 | 快速 API 调用场景 |
| API Workflow Engine | 持久化工作流编排 | 复杂多步骤业务流程 |
| Serverless Container Engine | 统一执行运行环境 | 需要灵活扩展的场景 |
| Batch Inference GPU Cluster | 批量 LLM 推理 | 大规模推理优化 |

**对外沟通平台优势（标准版本）：** 效率（速度+价格）、可靠性、规模、安全

---

## 三、商业模式

**收入来源：**

| 收入项 | 模式 | 说明 |
|-------|------|------|
| API Usage | 按 token 收费 | 模型调用量计费 |
| Compute Runtime | 容器执行收费 | 按运行时长/资源计费 |
| Enterprise Subscription | 企业网关订阅 | 年度订阅制 |
| Workflow Platform | 企业 workflow 平台收费 | SaaS 化工作流平台 |

**双平台战略：**

| 平台 | 覆盖区域 | 说明 |
|-----|---------|------|
| 胜算云 | 中国大陆 | 国内合规化部署 |
| CogFoundry | 全球（含港澳台、海外） | 全球化品牌 |

**定价原则：** 开发者友好（免费 tier）、企业级溢价（SLA+专属支持）、透明计费（用多少收多少）

---

## 四、GTM 策略

**当前切入点：** AI Coding Infrastructure（最大 AI workload 来源）

**核心目标用户：**
- 使用 Claude Code、Cursor、Cline 等 AI Coding 工具的开发者
- 需要 AI 编程能力的企业研发团队

**北极星指标：** AI Workloads Executed（已执行的 AI 工作负载数量）
> 所有业务决策优先考虑对此指标的正面影响。

**增长飞轮：**
```
Workloads → Knowledge → Better Execution → More Workloads
工作负载  →  知识积累  →    执行优化     →   更多工作负载
```

**竞争策略：**

| 维度 | 策略 | 禁止行为 |
|-----|------|---------|
| 定价 | 效率驱动（速度+价格优势） | 不打价格战 |
| 可靠性 | SLA 保障，99.9% 可用性 | 不承诺未经实测的指标 |
| 规模 | 支持大规模并行 | 不夸大规模能力 |
| 安全 | 合规优先，数据隔离 | 不妥协数据安全 |

---

## 五、关键术语（禁止混用）

**核心产品术语：**

| 中文 | 英文 | 禁止用 |
|-----|------|-------|
| AI 工作负载 | AI Workload | 任务、作业、Job、Task |
| 超级工厂 | SuperFoundry | 平台、Platform、系统、System |
| AI 工厂操作系统 | AI Factory OS | 底层架构、Backend |
| 知识网络 | Knowledge Network | 知识库、Knowledge Base |
| 批量推理 | Batch Inference | 批量处理、Batch Processing |
| Agent 运行时 | Agent Runtime | — |
| 无服务器容器引擎 | Serverless Container Engine | — |

**GTM 术语：**

| 中文 | 英文 | 禁止用 |
|-----|------|-------|
| 北极星指标 | North Star Metric | 核心指标、DAU |
| 知识网络飞轮 | Knowledge Network Effect | — |
| 企业订阅 | Enterprise Subscription | 企业套餐、Enterprise Plan |

**AI 产业阶段：**

| 阶段 | 英文 | 竞争焦点 |
|-----|------|---------|
| 模型时代 | Model Era | 模型效果 |
| 应用时代 | Application Era | 产品体验 |
| **生产时代（当前）** | **Production Era** | **生产能力** |

**混用规则：** 同一文档内中英文必须统一，不能在同一段落内混用"AI Workload"和"工作负载"。

---

## 六、沟通规范

**内部文档生成要求：**
- 必须包含：背景、目标、关键信息、行动项
- 必须明确：负责人、截止时间、依赖关系
- 必须避免：空洞形容词（"很好"、"非常重要"）

**内部沟通风格：**

| 场景 | 语气 | 格式 |
|-----|------|------|
| 日常沟通 | 直接、简洁 | 结论先行 |
| 方案讨论 | 理性、建设性 | 背景→问题→方案→风险 |
| 会议纪要 | 客观、完整 | 要点 + 行动项 + 负责人 |
| 周报/月报 | 数据驱动 | KPI 先行，进度透明 |

**对外沟通原则：** 诚实、专业、克制、一致（不夸大、不贬低竞品、不混用术语）

**对外禁止内容：** 未经实测的性能数据、竞品贬低性描述、客户非公开信息、虚假宣传

**客户沟通标准结构：**
```
【背景】客户面临的问题或需求
【我们的理解】对问题的分析
【解决方案】我们的建议，量化收益
【下一步】明确的行动项和时间线
```

---

## 七、合规与安全

| 红线 | 说明 |
|-----|------|
| 数据隔离 | 不同客户数据必须隔离，零容忍 |
| 客户数据保护 | 未经授权不使用客户数据训练，零容忍 |
| 访问控制 | 生产环境访问必须审批 |
| 敏感信息 | 禁止在非加密渠道传递 |

**合规要求：** 中国大陆遵守《生成式人工智能服务管理暂行办法》；海外遵守 GDPR、CCPA 等适用法规。

---

## 八、我的角色与工作习惯

- **角色**：产品经理，刚接手 CogFoundry 海外项目
- **当前任务**：梳理产品现状 → 竞品分析 → PRD
- **工作流**：先导入参考文档（参考/）→ 做竞品分析（竞品/）→ 写 PRD（prd/）
- **文档习惯**：模板驱动，随笔写完再让 AI 整理结构，项目内容不跨文件夹混放
