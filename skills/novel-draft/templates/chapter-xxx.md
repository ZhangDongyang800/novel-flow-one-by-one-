---
id: chapter-001
volume: 1
status: drafting
review_round: 0
canon_changed: false
source_outline_version: 1
updated_at: YYYY-MM-DD
---

# 章节模板（chapter-xxx.md）

<!-- 本文件是每一章的工作文件，记录章节目标、review 意见和定稿摘要。
     由 novel-draft 在开始写作时创建，由 novel-review 填写修改意见，由 novel-update 维护状态。
     每章一个文件，保存在 `章节/` 文件夹下，文件名格式为 chapter-XXX.md。 -->

---

# Chapter [XXX]: [章节标题]

<!-- 必填。章节编号和标题。编号与 outline.md 中的章节列表对应。 -->

## 本章目标

<!-- 必填。从 outline.md 继承的任务说明 + 结构标记。
     写作前必须明确本章要完成什么，写作中始终围绕此目标推进。 -->

> 从 outline 继承的任务说明 + 结构标记

## 修改意见

<!-- 可选。由 novel-review skill 在审核后填写。
     review_status 取值：
       - pass：通过，无需修改
       - minor_fix：小修，文字层面的调整（错别字、措辞、节奏微调）
       - rewrite_required：需要重写，存在结构或逻辑问题
       - reject：打回，严重偏离 outline 或违反 project.md 约束
     review_severity 取值：
       - minor：问题不影响整体结构，局部修改即可
       - structural：问题涉及章节结构、角色行为逻辑或 canon 一致性
     判定依据和建议修改应具体、可操作，避免模糊描述。 -->

> review skill 填写：
> review_status: pass / minor_fix / rewrite_required / reject
> review_severity: minor / structural
> 判定依据 + 建议修改

## 定稿摘要

<!-- 可选。review 通过后由 novel-update skill 填写。
     用 2-3 句话概括本章关键事件，不含正文。
     用于后续章节回溯前情，维护 canon 一致性。 -->

> review 通过后填写：本章关键事件摘要（2-3 句话），不含正文

---

## 填写示例

```markdown
---
id: chapter-003
volume: 1
status: reviewing
review_round: 1
canon_changed: false
source_outline_version: 1
updated_at: 2026-04-19
---

# Chapter 003: 孢子潮

## 本章目标
> 三人逃离医院时遭遇孢子潮，赵铁生受伤，林晚暴露军医身份。结构标记：climax。

## 修改意见
> review_status: minor_fix
> review_severity: minor
> 判定依据：整体节奏和冲突设计符合 climax 要求，但孢子潮的描写段落中有两处使用了"仿佛"和"宛如"（违反冷白描风格禁止事项）。建议替换为具体画面描写。
> 建议修改：第 4 段"孢子仿佛活物一般涌来"改为"孢子贴着地面爬过来，碰到墙壁就往上攀"。

## 定稿摘要
> （等待 review 通过后填写）
```

---

## 注意事项

- **frontmatter 中的 status 必须及时更新**：drafting → reviewing → done，状态流转由对应 skill 负责
- **review_round 每次打回时递增**：用于追踪同一章被 review 了几轮
- **canon_changed 标记很重要**：如果本章引入了新的 canon 变更（新设定、角色状态变化等），标记为 true，novel-update 会据此更新 project.md 和角色卡
- **source_outline_version 记录来源**：如果 outline.md 更新了，需要对比版本号，确保本章目标与最新大纲一致
- **定稿摘要不含正文**：只记录"发生了什么"，不记录"怎么写的"，便于后续快速回溯
- **修改意见要具体可操作**：避免"写得不好""需要改进"等模糊描述，要指出具体位置和具体问题
