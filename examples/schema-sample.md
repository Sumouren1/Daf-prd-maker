---
type: ai-context
title: TaskFlow 产品 Schema（测试）
usage: 将 schema 块粘贴到 AI 对话开头，AI 即可理解 TaskFlow 产品结构并辅助开发
phase: 1
date: 2026-04-14
updated: 2026-04-14
---

# 使用方式

> 复制 `---schema---` 至 `---end---` 之间的全部内容，粘贴到新对话开头，告诉 AI：
> 「以下是 TaskFlow 产品结构，请帮我实现……」

---schema---

## Product: TaskFlow

**Type**: 轻量级团队任务管理 SaaS
**Console URL**: app.taskflow.app
**Landing URL**: taskflow.app
**Currency**: USD（月/年订阅）
**Auth**: Email 注册 + 邀请制团队

---

## Architecture

### Frontend Landing（前台落地页）
- Hero（口号 + Get Started Free CTA）
- Features（3个核心功能）
- Pricing（Free / Pro $12/mo / Team $29/mo）
- Testimonials
- Footer

### Backend Console（app.taskflow.app）
Left sidebar navigation:
```
Dashboard
Tasks
  ├── My Tasks
  └── Team Tasks     ← 仅 Admin / Manager 可见
Projects
Members              ← 仅 Admin 可操作
Settings
  ├── Profile
  ├── Team
  ├── Billing
  └── Notifications
```

---

## Changes Summary

| Page | Status | Notes |
|------|--------|-------|
| Dashboard | NEW | 数据概览 + 快速创建入口 |
| My Tasks | NEW | 个人任务列表，含筛选 |
| Team Tasks | NEW | 全团队视图，权限限制 |
| Projects | NEW | 项目管理，任务挂载 |
| Members | NEW | 邀请 + 角色管理 |
| Settings | NEW | Profile / Team / Billing / Notifications |

---

## Core Entities

### User
```
entity: User
fields:
  id: uuid
  name: string
  email: string
  avatar_url: string | null
  role: Admin | Manager | Member
  status: Active | Invited | Suspended
  joined_at: timestamp
```

### Task
```
entity: Task
fields:
  id: uuid
  title: string
  description: text | null
  project_id: uuid | null
  assignee_id: uuid | null       # null = 未指派
  created_by: uuid
  priority: High | Medium | Low  # default: Medium
  status: Open | In Progress | Done | Cancelled
  due_date: date | null
  created_at: timestamp
  updated_at: timestamp
```

### Project
```
entity: Project
fields:
  id: uuid
  name: string
  status: Active | Archived
  member_ids: uuid[]
  created_at: timestamp
computed:
  task_total: int
  task_completed: int
```

### TeamInvite
```
entity: TeamInvite
fields:
  id: uuid
  email: string
  role: Manager | Member          # Admin 不可邀请
  status: Pending | Accepted | Expired
  invited_at: timestamp
  expires_at: timestamp           # 邀请链接 72h 有效
```

---

## Pages

### Dashboard
```
page: Dashboard
url: /dashboard

widgets:
  stat_cards:
    - my_open_tasks: int
    - due_today: int
    - team_activity_today: int
    - completed_this_week: int
  recent_tasks:
    type: list (last 5)
    fields: [title, status, priority, due_date]
    action: view_all → /tasks/my
  quick_actions:
    - "+ New Task" → create_task_modal
```

### My Tasks
```
page: MyTasks
url: /tasks/my

filters: [status_dropdown, priority_dropdown, date_range, reset]
columns:
  - title: string (clickable → task detail)
  - project: project_name tag
  - priority: High | Medium | Low (color badge)
  - assignee: avatar
  - due_date: date (red if overdue)
  - status: Open | In Progress | Done | Cancelled (badge)
  - actions: [edit, delete]
cta: "+ New Task" → create_task_modal
empty_state: "No tasks yet. Create your first task!"
```

### Team Tasks
```
page: TeamTasks
url: /tasks/team
access: Admin | Manager only

filters: [status_dropdown, priority_dropdown, assignee_dropdown, date_range, reset]
columns:
  - title: string
  - project: project_name tag
  - priority: High | Medium | Low
  - assignee: name + avatar
  - created_by: name
  - due_date: date
  - status: badge
  - actions: [edit, delete]
cta: "+ New Task" → create_task_modal
```

### Create Task Modal
```
modal: create_task_modal
trigger: "+ New Task" button (Dashboard / My Tasks / Team Tasks)
fields:
  title: text input (required)
  description: rich text (optional)
  project: dropdown (existing projects, optional)
  assignee: dropdown (team members, default: self)
  priority:
    options: [High, Medium, Low]
    default: Medium
  due_date: date picker (optional)
  status: Open (fixed on create)
actions: [Cancel, Create Task]
```

### Projects
```
page: Projects
url: /projects

columns:
  - name: string (clickable → project detail)
  - tasks: "{completed}/{total}" progress display
  - members: avatar group (max 5 shown)
  - status: Active | Archived (badge)
  - created_at: date
  - actions: [edit, archive, delete]
cta: "+ New Project"
empty_state: "No projects yet. Create one to organize your tasks."
```

### Members
```
page: Members
url: /members
access: Admin only

columns:
  - name: string
  - email: string
  - role: Admin | Manager | Member (badge)
  - status: Active | Invited | Suspended (badge)
  - joined_at: date
  - actions: [change_role, suspend, remove]
cta: "Invite Member" → invite_modal

modal: invite_modal
fields:
  email: text input (required)
  role:
    options: [Manager, Member]
    default: Member
actions: [Cancel, Send Invite]
```

### Settings
```
page: Settings
url: /settings
sub_pages: [profile, team, billing, notifications]

sub_page: profile
  fields:
    name: text input
    avatar: image upload
    password: change_password_flow (current + new + confirm)

sub_page: team
  fields:
    team_name: text input
    logo: image upload
    timezone: dropdown

sub_page: billing
  sections:
    current_plan: Free | Pro | Team (with upgrade CTA)
    payment_method: card info + edit
    invoice_history: table [date, amount, status, download]

sub_page: notifications
  toggles:
    task_assigned: boolean (default: true)
    due_date_reminder: boolean (default: true)
    comment_mention: boolean (default: true)
    weekly_summary: boolean (default: false)
```

---

## User Journey（核心路径）

```
1. 访问 taskflow.app → 点击 Get Started Free → 注册账号
2. 首次登录 → Dashboard（数据全空状态）
3. Projects → 创建第一个项目（如 "Website Redesign"）
4. My Tasks → + New Task → 填写标题/项目/优先级/截止日
5. Dashboard → 看到任务计数更新，today due 出现任务
6. Members → Invite Member → 填写同事邮箱 → 发送邀请
7. 同事接受邀请 → Team Tasks 可见团队任务
8. 任务完成 → 更新 Status → Done → Dashboard completed_this_week +1
```

---

## Pricing

| Plan | Price | Limits |
|------|-------|--------|
| Free | $0 | 3 projects, 5 members, unlimited tasks |
| Pro | $12/mo | Unlimited projects, 15 members |
| Team | $29/mo | Unlimited everything + priority support |

---

## Out of Scope（当前版本不做）

- 任务评论 / 附件上传
- 甘特图 / 看板视图（仅列表）
- API 开放接入
- 移动端 App
- 第三方集成（Slack / GitHub 等）

---end---
