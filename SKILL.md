# daf-prd-maker — PRD 全流程制作 Skill

> 本 Skill 引导完成从产品调研到原型部署的完整 PRD 制作流程。
> **每一步都使用 `AskUserQuestion` 工具展示可点击选项，等待用户选择后再推进，不跳步执行。**

---

## 触发方式

用户输入 `/daf-prd-maker` 时激活本 Skill，立即从 **Step 1** 开始。

---

## 预加载上下文（自动）

- 公司业务背景：已通过 `CLAUDE.md` 加载（CogFoundry / 胜算云双平台战略、核心术语定义）
- Vault 根目录：`/Users/daf/Documents/daf/`
- 默认 PRD 模板路径：`工作/模板/prd.md`
- 功能流程文档格式参考：`templates/flow-doc-format.md`（本 Skill 目录内）
- Schema 文档格式参考：`templates/schema-format.md`（本 Skill 目录内）

---

## Step 1 — 确认模板与项目

**动作 1-A：先用 `AskUserQuestion` 询问模板来源**

```
问题：使用哪个 PRD 模板？
选项：
  - 使用默认模板（工作/模板/prd.md）       ← 推荐
  - 我有自己的模板，稍后提供路径
```

**动作 1-B：再用 `AskUserQuestion` 询问项目归属**

先执行 `ls 工作/` 列出现有项目文件夹，将结果作为选项动态生成：

```
问题：这个 PRD 属于哪个项目？
选项（动态生成，示例）：
  - cogfoundry（已有项目）
  - 新建项目（请输入项目名）
```

**收到选择后：**
- 读取模板文件（默认或用户指定），加载其结构
- 告知用户：`PRD 将保存到 工作/[项目名]/prd/[YYYY-MM-DD]-[项目名]-需求.md`
- 进入 Step 2

---

## Step 2 — 加载项目参考信息

**动作：用 `AskUserQuestion` 询问参考来源**

```
问题：项目参考信息从哪里获取？
选项：
  - 自动读取项目 _context.md（工作/[项目名]/公司参考/_context.md）  ← 推荐
  - 我来提供文件路径或 Obsidian 链接
  - 直接跳过，先没有参考文档
```

**收到选择后：**

1. **自动读取**：直接 Read `工作/[项目名]/公司参考/_context.md`，若文件不存在告知用户
2. **用户提供**：等待用户输入路径或 obsidian:// 链接（自动 URL decode `file=` 参数后 Read）
3. **跳过**：记录「无参考文档」，继续

读取成功后反馈：「✅ 已读取 [文件名]，提取到以下背景信息：[产品定位 / 核心功能 / 目标用户 各一句话摘要]」

进入 Step 3

---

## Step 3 — 功能流程调研与文档生成

本步骤分三个子阶段：**3A 信息采集 → 3B 生成流程文档（需确认）→ 3C 自动生成 Schema**

### Step 3A — 信息采集

**动作：用 `AskUserQuestion` 询问调研素材类型**

```
问题：功能流程调研的素材来源是？（可多选）
选项（multiSelect: true）：
  - 提供网站域名，自动抓取
  - 上传截图 / 流程图（稍后发图）
  - 文字描述功能模块
  - 已有参考文档（Step 2 已读取，直接使用）
```

**根据选择执行：**

- **域名抓取**：等用户输入域名后，用 `WebFetch` 抓主页和核心子页（`/dashboard`、`/pricing`、`/docs`、`/features`）。若返回 403 / 空内容，提示「自动抓取受限，建议改用截图」并用 `AskUserQuestion` 再次询问
- **截图**：等用户上传后，用 Read 读取图片进行分析
- **文字描述**：等用户输入文字
- **已有参考文档**：直接使用 Step 2 已加载的内容

### Step 3B — 生成功能流程文档

基于采集到的信息，按 `templates/flow-doc-format.md` 格式生成功能流程文档，保存到：

```
工作/[项目名]/参考/[项目名]-功能流程文档.md
```

生成后，展示结构摘要（各模块名称 + 字段数量），然后用 `AskUserQuestion`：

```
问题：功能流程文档草稿已生成，请确认
选项：
  - 内容准确，生成 Schema ✅
  - 需要修改（请说明哪里不对）
  - 补充遗漏的页面或模块（请描述）
```

等用户确认无误后进入 3C；若需修改，更新后再次确认。

### Step 3C — 自动生成 Schema

基于已确认的功能流程文档，按 `templates/schema-format.md` 格式生成 Schema：

```
工作/[项目名]/参考/[项目名]-schema.md
```

Schema 包含：产品基本信息、架构总览、核心实体定义、每页字段结构、用户核心路径。

生成后用 `AskUserQuestion`：

```
问题：Schema 文档已生成，下一步？
选项：
  - 进入 Step 4，开始写 PRD ✅
  - 先查看 / 调整 Schema 内容
```

---

## Step 4 — PRD 制作

**动作：先用 `AskUserQuestion` 确认需求输入方式**

```
问题：改版需求如何提供？
选项：
  - 我来文字描述
  - 已有需求文档，提供路径
  - 直接基于功能流程文档生成完整 PRD（无额外需求）
```

**收到需求后，按 PRD 模板逐章节填写：**

- **一、版本信息**：自动填入当日日期、v0.1-draft
- **二、变更日志**：填入初稿记录
- **三、名词解释**：从 Schema + 公司参考信息中提取术语
- **四、需求背景**：基于参考文档填写；用户调研/竞品分析无则标"暂无"
- **五、需求范围**：整理需求，标注迭代分期
- **六、功能详细说明**：包含 Mermaid 流程图 + 各模块详细功能表
- **七、非功能需求**：从参考信息提取合规约束等
- **八、埋点**：暂无则标注
- **九、项目规划**：填入优先级和时间要求

保存到：`工作/[项目名]/prd/[YYYY-MM-DD]-[项目名]-需求.md`

**生成后用 `AskUserQuestion`：**

```
问题：PRD 初稿已生成，下一步？
选项：
  - 内容 OK，进行原型部署 ✅
  - 需要修改某个章节（请说明哪章）
  - 暂时不部署，先结束
```

---

## Step 5 — 原型部署

**动作：用 `AskUserQuestion` 询问部署方式**

```
问题：选择原型部署方式
选项：
  - React + Vite + Tailwind CSS + GitHub Pages（推荐）
  - 其他技术栈（请说明）
  - 暂不部署
```

**同时询问代码输出目录：**

```
问题：原型代码生成位置
选项：
  - 默认：工作/[项目名]/prototype/
  - 自定义路径（请输入）
```

**确认后执行：**

1. 调用 `ui-ux-pro-max` Skill 生成设计规范（品牌风格、颜色、字体）
2. 基于 Schema 和 PRD 生成原型代码
3. 提供完整 GitHub Pages 部署步骤（见下方详细指引）
4. 部署成功后，将 GitHub Pages 地址自动填入 PRD 的「交互原型图」章节

**GitHub Pages 部署完整指引（含 UI 操作说明）：**

```
═══ 第一步：创建 GitHub 仓库 ═══
1. 打开 https://github.com/new
2. 填写 Repository name（如 my-project-prototype）
3. 选择 Public（GitHub Pages 免费版需要 Public）
4. 不要勾选 Initialize README（本地已有代码）
5. 点击「Create repository」

═══ 第二步：本地代码配置 ═══
# 1. 进入原型目录
cd 工作/[项目名]/prototype

# 2. 配置 vite.config.js（必须，否则资源路径错误）
#    base: '/[仓库名]/'   ← 仓库名要和 GitHub 上一致

# 3. 配置 main.jsx 的 BrowserRouter（必须，否则刷新 404）
#    <BrowserRouter basename={import.meta.env.BASE_URL}>

# 4. 创建 public/404.html（SPA 路由兜底，防刷新丢失）
#    内容见下方代码块

# 5. 安装部署工具
npm install --save-dev gh-pages

# 6. package.json 添加 deploy 命令
"scripts": {
  "deploy": "npm run build && gh-pages -d dist"
}

═══ 第三步：推送代码 ═══
git init
git remote add origin git@github.com:[你的用户名]/[仓库名].git
git add .
git commit -m "init prototype"
git push -u origin main

═══ 第四步：执行部署 ═══
npm run deploy
# 成功后终端显示：Published

═══ 第五步：GitHub 网页端开启 Pages（重要！）═══
1. 打开你的 GitHub 仓库页面
2. 点击顶部菜单「Settings」（齿轮图标，在 Code/Issues/Pull requests 右侧）
3. 左侧边栏找到「Pages」（在 Code and automation 分组下）
4. 找到「Branch」设置项：
   - 点击下拉框，从「None」改为「gh-pages」
   - 右侧 folder 保持「/ (root)」
5. 点击「Save」按钮
6. 页面顶部会出现绿色横幅：
   「✅ Your site is live at https://[用户名].github.io/[仓库名]/」
7. 首次部署需等待 1-2 分钟，刷新页面即可看到网址

═══ public/404.html 内容 ═══
<!DOCTYPE html>
<html>
<head>
  <script>
    var l = window.location;
    l.replace(l.protocol + '//' + l.hostname +
      (l.port ? ':' + l.port : '') +
      l.pathname.split('/').slice(0, 2).join('/') +
      '/?/' + l.pathname.slice(1) +
      (l.search ? '&' + l.search.slice(1) : '') +
      l.hash);
  </script>
</head>
</html>
```

> 💡 **常见问题**：
> - 部署后页面空白 → 检查 `vite.config.js` 的 `base` 是否与仓库名一致
> - 刷新后 404 → 检查 `public/404.html` 是否存在，BrowserRouter 是否有 `basename`
> - Settings 里找不到 Pages → 确认仓库是 Public，不是 Private

---

## 持续同步（代码调整后触发）

**每当用户完成代码调整，主动用 `AskUserQuestion` 询问：**

```
问题：代码已更新，是否同步文档？
选项（multiSelect: true）：
  - 同步 Schema（页面结构 / 字段 / 实体有变化）
  - 同步 PRD（功能说明 / 交互逻辑有变化）
  - 暂不同步
```

**同步规则：**
- Schema：只修改变动的 page 定义部分，其余实体保持不变
- PRD：最小化修改对应功能章节，同时在变更日志表格自动追加一行记录
- 两者都改时，先改 Schema 再改 PRD（保持两者一致）

---

## 注意事项

1. **AskUserQuestion 优先**：所有分支决策点必须用选项，禁止纯文字询问
2. **等确认再推进**：每步完成后必须等用户选择，不自动连续执行
3. **文件路径相对化**：所有路径从 Vault 根目录 `/Users/daf/Documents/daf/` 开始，存储和展示均用相对路径
4. **obsidian:// 链接**：解析 `file=` 参数 URL decode 后直接作为相对路径 Read
5. **公司参考优先级**：CLAUDE.md（最高）> `公司参考/_context.md` > 用户提供文档
6. **AWS 约束**：CogFoundry 项目时，自动检查 PRD 是否违反「前台不展示大模型 API 售卖」约束
7. **原型设计**：调用 `ui-ux-pro-max` Skill，保持与 PRD 品牌风格一致
