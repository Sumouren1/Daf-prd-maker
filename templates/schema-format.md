# Schema 文档 — 格式规范

> 参考文件：`工作/cogfoundry/参考/cogfoundry-第一期schema.md`
> Schema 文档是面向开发者和 AI 的机器可读上下文，直接粘贴到对话开头即可让 AI 理解产品结构。
> 与功能流程文档的区别：流程文档 = 用户视角；Schema = 开发/AI 上下文视角。

---

## Frontmatter

```yaml
---
type: ai-context
title: [产品名] [阶段] 产品 Schema
usage: 将 schema 块粘贴到 AI 对话开头，AI 即可理解产品结构并辅助开发
phase: [1 / 2 / ...]
date: [YYYY-MM-DD]
updated: [YYYY-MM-DD]
---
```

---

## 文档结构

Schema 文档由以下几个部分组成，包裹在 `---schema---` 和 `---end---` 之间：

````markdown
---schema---

## Product: [产品名] ([阶段])

**Type**: [产品类型，一句话]
**Console URL**: [后台地址]
**API Base**: `[API 根地址]`
**Protocol**: [协议，如 OpenAI API compatible]
**Currency**: [计费单位]
**[特殊约束]**: [如 AWS Constraint、合规说明]

---

## Architecture

### Frontend（前台，如不变则注明）
- [页面列表]

### Backend Console
Left sidebar navigation:
```
[导航结构，缩进表示层级]
Dashboard
Credits
API Keys    ← NEW / MODIFIED / UNCHANGED
...
```

Top nav bar:
```
[顶部导航结构]
```

---

## Changes Summary

| Page | Status | What Changed |
|------|--------|--------------|
| [页面名] | NEW / MODIFIED / UNCHANGED | [改动描述] |

---

## Core Entities

[每个核心实体用代码块定义]

### [实体名]
```
entity: [实体名]
fields:
  [字段名]: [类型]    # [说明]
  [字段名]: [类型] | null   # null = [含义]
  [枚举字段]: [值1] | [值2] | [值3]
[关联逻辑]:
  [key]: [说明]
```

---

## Pages

[每个页面用代码块定义]

### [页面名]
```
page: [页面名]
url: /[路径]
tabs: [[tab1], [tab2]]   # 如有 tab 结构

section: [区域名]   # 如有多个区域
  fields:
    [字段名]: [类型/说明]
  actions: [[操作1], [操作2 → 跳转目标]]

tab: [tab名]
  filters: [[筛选条件1], [筛选条件2]]
  columns:
    - [字段名]: [类型/说明]
  empty_state: "[空状态文案]"

modal: [弹窗名]
  fields:
    [字段名]: [类型]
      [子选项]: [[选项列表]]
      default: [默认值]
  actions: [[Cancel], [Confirm]]
```

---

## User Journey（核心用户路径）

```
1. [步骤描述]
2. [步骤描述，含具体操作]
   [子步骤]
3. ...
```

---

## Key Differences（如有对标产品）

| Dimension | [对标产品A] | [本产品] |
|-----------|-----------|---------|
| [维度] | [A的做法] | [本产品的做法] |

---

## Out of Scope（本阶段不做）

- [排除项1]
- [排除项2（说明原因）]

---end---
````

---

## 写作要点

1. **实体定义**：字段类型用伪代码风格（`string`、`decimal`、`timestamp`、`string[]`、`enum1 | enum2`），加注释说明含义
2. **页面结构**：每个页面的每个功能区域都要定义，包括隐藏的弹窗（modal）
3. **约束条件**：平台特有约束（合规、AWS 限制等）放在 Product 基本信息里
4. **空状态**：有 empty_state 的地方要填，开发时需要处理
5. **Changes Summary**：标清楚 NEW / MODIFIED / UNCHANGED，让开发者快速定位改动范围
6. **Out of Scope**：明确写出本阶段不做的内容，避免开发理解偏差

---

## 示例片段

```
## Core Entities

### Credits
```
entity: Credits
fields:
  total_credits: decimal(USD)
  purchased_credits: decimal(USD)    # 实际购买，随消费减少
  bonus_credits: decimal(USD)        # 平台赠送
top_up:
  presets: [$10, $30/$31.5, $60/$66, $150/$172.5, enterprise]
  custom_range: $10 - $1500
  payment_methods: [stripe, paypal, wire_transfer, enterprise]
  wire_transfer_minimum: $500        # 低于此值禁用
```

### APIKey
```
entity: APIKey
fields:
  id: uuid
  name: string
  key: string                         # 展示时部分脱敏：sk-cf-xxxx...xxxx
  usage_usd: decimal
  spend_limit_usd: decimal | null     # null = unlimited
  created_at: timestamp
  expires_at: timestamp | null        # null = never
  model_permissions: string[] | null  # null = all models
  rpm: int (default: 100)
  tpm: int (default: 200000)
  is_default: boolean
```

## Pages

### Credits
```
page: Credits
url: /credits
tabs: [balance_topup, transaction_history, recharge_records]

tab: balance_topup
  summary_fields: [total_credits, purchased_credits, bonus_credits]
  top_up_flow:
    step1: select amount (presets or custom $10-$1500)
    step2: select payment method
    step3: complete payment → modal → balance updated

tab: transaction_history
  filters: [date_range, reset, search]
  columns:
    - tx_id: string
    - timestamp: UTC datetime
    - type: Debit | Credit
    - description: "API Usage" | "Agent Runtime" | "Top-up" | "Bonus Credit"
    - amount_usd: decimal (positive = credit, negative = debit)
    - balance_after: decimal
```
