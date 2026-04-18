# 状态规则 (State Rules)

> 本规范定义了小说流项目中章节的生命周期状态及流转规则。
> 所有 skill 和 orchestrator 都必须遵守本规范。

---

## 双层状态模型

项目使用 **status + stage** 双层状态模型：

- **status**（状态）：章节在生命周期中的宏观位置
- **stage**（阶段）：当前正在执行哪个 skill 流程

---

## status 定义（6 个）

| status | 含义 | 文件证据 |
|--------|------|---------|
| `idea` | 项目有初步概念，还没开始写 | `project.md` 存在，`outline.md` 不存在 |
| `planned` | 当前卷章节已规划，可以开始写 | `outline.md` 中当前卷有章节列表 |
| `drafting` | 当前章正在写草稿 | `【书名】/第X卷/chapter-xxx.md` 有正文内容 |
| `reviewing` | 草稿完成，等待审查 | `章节/chapter-xxx.md` 的「修改意见」section 有内容 |
| `done` | 审查通过，定稿完成 | `章节/chapter-xxx.md` 的 frontmatter.status 为 `done` |
| `blocked` | 出问题，需要人工干预 | `project-state.md` 中 `blocked_reason` 非空 |

### status 与旧版的关系

- 旧版 `outlined` 已合并到 `idea`（outline 产出后直接进入 `planned`）
- 旧版 `idea`（章节已列出但未展开）已合并到 `planned`

---

## stage 定义（5 个）

| stage | 含义 | 当前执行的 skill |
|-------|------|-----------------|
| `brainstorm` | 正在收束项目概念 | `novel-brainstorm` |
| `outline` | 正在规划章节路线 | `novel-outline` |
| `draft` | 正在写草稿 | `novel-draft` |
| `review` | 正在审查草稿 | `novel-review` |
| `update` | 正在更新 canon 和 outline 状态 | `novel-update` |

stage 是 status 的子状态，表示"当前在做什么"。例如 `status=drafting, stage=draft` 表示正在写草稿。

---

## 状态流转规则

### 正常流程

```
idea → planned → drafting → reviewing → done → update
```

各阶段触发条件：

1. **idea → planned**
   - 触发者：outline skill
   - 条件：outline skill 完成当前卷的章节列表规划
   - 动作：更新 `outline.md`，各章节初始 status 为 `planned`

2. **planned → drafting**
   - 触发者：draft skill
   - 条件：orchestrator 选中该章作为当前章，draft skill 开始撰写
   - 动作：创建 `章节/chapter-xxx.md`，frontmatter.status 改为 `drafting`

3. **drafting → reviewing**
   - 触发者：draft skill
   - 条件：草稿撰写完成
   - 动作：更新 frontmatter.status 为 `reviewing`，通知 orchestrator

4. **reviewing → done**
   - 触发者：review skill
   - 条件：审查通过，定稿已确认
   - 动作：填写定稿，正文在 `【书名】/第X卷/chapter-xxx.md` 中，frontmatter.status 改为 `done`，然后调用 novel-update

5. **done → update**
   - 触发者：orchestrator
   - 条件：review 通过后自动触发
   - 动作：调用 novel-update skill，更新 `project.md` canon 变更日志、更新 `outline.md` 章节状态为 `done`、填写定稿摘要

### 审查 reject

6. **reviewing → drafting**
   - 触发者：review skill
   - 条件：审查未通过，判定为「小修」或「重写」
   - 动作：填写修改意见，frontmatter.status 回退为 `drafting`，draft skill 根据意见修改

7. **reviewing → blocked**
   - 触发者：review skill
   - 条件：审查判定为「reject」（方向性错误）
   - 动作：记录 `blocked_reason` 到 `project-state.md`，路由到 outline skill

### 异常流程

8. **任何阶段 → blocked**
   - 触发条件（任一）：
     - review 判定为「reject」（方向性错误）
     - canon 一致性严重冲突（review 发现与 `project.md` 硬规则矛盾）
     - 用户主动声明"停下来"
   - 动作：记录 `blocked_reason` 到 `project-state.md`

---

## blocked 的恢复流程

| blocked 原因 | 路由目标 | 恢复后状态 |
|-------------|---------|-----------|
| 方向错误（review reject） | `novel-outline`（重新规划当前章或当前卷） | `planned` |
| canon 冲突 | 人工确认后更新 `project.md` | 恢复到 blocked 前的状态 |
| 用户暂停 | 等待用户指令 | 恢复到 blocked 前的状态 |

恢复后清除 `project-state.md` 中的 `blocked_reason`。

---

## 状态记录

- 每个章节的 status 记录在 `章节/chapter-xxx.md` 的 frontmatter.status 中（**文件级真相**）
- `project-state.md` 记录项目级状态：`current_volume`、`current_chapter`、`project_status`、`blocked_reason`、`last_skill`
- 每次状态变更由 orchestrator 负责更新 `project-state.md`
- 当 `project-state.md` 与 frontmatter 冲突时，以 frontmatter 为准
