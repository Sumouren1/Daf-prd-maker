# daf-prd-maker

> **Claude Code Skill** — PRD 全流程制作，从产品调研到原型部署的一站式引导 Skill。

## 功能流程

```
/daf-prd-maker
    │
    ├── Step 1  确认 PRD 模板 + 项目归属
    ├── Step 2  加载项目参考信息（_context.md / 自定义 / 跳过）
    ├── Step 3A 功能流程调研（域名抓取 / 截图 / 文字描述）
    ├── Step 3B 生成功能流程文档（等确认）
    ├── Step 3C 自动生成 Schema 文档
    ├── Step 4  收集改版需求 → 生成 PRD
    ├── Step 5  原型部署（React + Vite + GitHub Pages 指引）
    └── 持续    代码改动后询问是否同步 Schema / PRD
```

全程选项式交互，每步使用 `AskUserQuestion` 展示可点击选项，无需手动输入指令。

---

## 安装方法

### 方式一：安装到 Obsidian Vault（推荐，项目级）

```bash
git clone git@github.com:Sumouren1/Daf-prd-maker.git \
  "/path/to/your/vault/.claude/skills/daf-prd-maker"
```

将 `/path/to/your/vault` 替换为你的 Obsidian Vault 根目录，例如：

```bash
git clone git@github.com:Sumouren1/Daf-prd-maker.git \
  "/Users/yourname/Documents/MyVault/.claude/skills/daf-prd-maker"
```

### 方式二：安装到用户级（全局可用）

```bash
git clone git@github.com:Sumouren1/Daf-prd-maker.git \
  ~/.claude/skills/daf-prd-maker
```

### 验证安装

打开 Claude Code，输入 `/daf-prd-maker`，如果 Skill 被正确识别，会立即弹出第一个选项问题。

---

## 前置要求

Skill 依赖以下 Claude Code 内置能力，无需额外安装：

| 依赖 | 用途 |
|------|------|
| `AskUserQuestion` 工具 | 选项式交互 |
| `WebFetch` 工具 | 自动抓取网站内容（可选） |
| `Read` / `Write` 工具 | 读写 Vault 文件 |
| `ui-ux-pro-max` Skill | 原型设计规范（Step 5，可选） |

> 如果没有安装 `ui-ux-pro-max`，Step 5 原型部署依然可以执行，只是设计规范部分需手动描述风格。

---

## 文件结构

```
daf-prd-maker/
├── README.md                     ← 本文件
├── SKILL.md                      ← 主流程脚本（Claude 读这个执行）
└── templates/
    ├── flow-doc-format.md        ← 功能流程文档写作规范
    └── schema-format.md          ← Schema 文档写作规范
```

---

## 输出产物

每次运行 Skill 会在你的 Vault 里生成以下文件：

```
工作/[项目名]/
├── 公司参考/
│   └── _context.md               ← 项目背景（如有）
├── 参考/
│   ├── [项目名]-功能流程文档.md   ← Step 3B 生成
│   └── [项目名]-schema.md        ← Step 3C 生成
└── prd/
    └── YYYY-MM-DD-[项目名]-需求.md ← Step 4 生成
```

---

## 示例输出

`examples/` 目录包含一个虚构测试项目（TaskFlow 任务管理 SaaS）的完整输出示例：

- `examples/flow-doc-sample.md` — 功能流程文档示例
- `examples/schema-sample.md` — Schema 文档示例

---

## CLAUDE.md 配置建议

为了让 Skill 自动获取公司业务背景，建议在你的 Vault 根目录或项目目录配置 `CLAUDE.md`：

```markdown
# 项目业务上下文

## 公司/产品定位
[一句话描述]

## 核心术语
[术语表]

## 不可妥协的约束
[合规要求、技术限制等]
```

Skill 启动时会自动读取 `CLAUDE.md` 作为最高优先级背景信息。

---

## 更新 Skill

```bash
cd ~/.claude/skills/daf-prd-maker  # 或 vault/.claude/skills/daf-prd-maker
git pull origin main
```
