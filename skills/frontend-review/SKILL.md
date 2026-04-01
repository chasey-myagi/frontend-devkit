---
name: frontend-review
description: >
  Frontend quality gate with scored dimensions and binary pass/fail checks.
  Reviews visual design, typography, interaction, accessibility, performance,
  and code architecture. Dispatches an independent reviewer agent.
  Use when: (1) frontend implementation is complete, (2) user says /frontend-review,
  (3) before merging frontend code, (4) after impeccable:frontend-design implementation.
  Triggers on: "review the frontend", "check the UI", "frontend quality gate",
  "审查前端", "前端质量".
user-invokable: true
args:
  - name: target
    description: File path, directory, or git diff range to review
    required: false
  - name: spec
    description: Path to design-spec.md for spec conformance checking
    required: false
---

# Frontend Review

前端质量门控。基于 impeccable 维度体系的 6 维评分 + 6 项 Binary Gate，独立 agent 审查。

## Why This Exists

code-review 审的是代码逻辑和安全。但前端代码还有另一半质量维度——视觉设计、交互体验、可访问性、响应式、性能——这些 code-review 不覆盖。frontend-review 补上这个缺口。

**两个 review 不互斥**：前端代码应该同时跑 code-review（审代码）和 frontend-review（审体验）。

## Flow

```
1. Determine review scope
2. Gather context (.impeccable.md, design-spec.md)
3. Dispatch frontend-reviewer agent
4. Present report + gate decision
5. If FAIL → fix → re-review
```

## Step 1: Determine Review Scope

解析 target 参数：

| Input | Scope |
|-------|-------|
| 文件路径 | 审该文件 |
| 目录路径 | 审该目录下所有前端文件 |
| git diff range | 审 diff 中的前端文件变更 |
| 无参数 | 审当前分支相对 main 的所有前端变更 |

**前端文件**：`.html`, `.css`, `.scss`, `.jsx`, `.tsx`, `.vue`, `.svelte` 及它们的相关文件。

## Step 2: Gather Context

1. 读取 `.impeccable.md` — 项目设计上下文（品牌、调性、设计原则）
2. 读取 `design-spec.md` — 冻结的设计规范（如果有，用于 spec 一致性检查）
3. 读取变更文件的完整上下文（不只是 diff）

如果 `.impeccable.md` 不存在，reviewer 仍然运行，但会在报告中标注"无设计上下文，评分基于通用前端标准"。

## Step 3: Dispatch Frontend Reviewer Agent

Spawn 独立 agent，读取 `frontend-reviewer.md` 构建 prompt：

```
Agent(
  description: "frontend-review: {scope}",
  prompt: [frontend-reviewer.md 内容 + review 上下文],
  mode: "auto"
)
```

Prompt 结构：
```
你是一个独立的前端质量审核专家。请按照以下规范审查前端代码：

[frontend-reviewer.md 内容]

## 本次审查

### 审查范围
{文件列表 + diff}

### 设计上下文
{.impeccable.md 内容，或 "无"}

### 设计规范
{design-spec.md 内容，或 "无"}

### 完整文件内容
{被审文件的完整内容}
```

## Step 4: Present Report

Reviewer agent 返回报告后，直接展示给用户。报告包含：

1. **Binary Gates** — 6 项 YES/NO 判定（置顶，最重要）
2. **Dimension Scores** — 6 维评分表
3. **Gate Decision** — PASS / CONDITIONAL PASS / FAIL
4. **Issues** — 按严重度排序的具体问题
5. **Strengths** — 做得好的地方

## Step 5: Handle Result

| Result | Action |
|--------|--------|
| **PASS** ✅ | 前端质量达标，可以合并 |
| **CONDITIONAL PASS** ⚠️ | 有 Gate 未通过但可快速修复，修复后无需 re-review |
| **FAIL** ❌ | 有严重问题，修复后必须 re-review |

## Anti-Patterns

| Anti-Pattern | What To Do Instead |
|---|---|
| 跳过 review 直接合并 | 前端代码必须过 frontend-review |
| 只跑 code-review 不跑 frontend-review | 两个都跑，维度不同 |
| 对 Gate FAIL 的项目说"下次再修" | Gate 是硬性标准，不通过就不合并 |
| 审查 cases.html（设计探索文件） | Cases 是探索用的，只审正式实现代码 |
| 没有 .impeccable.md 就跳过 | 仍然审，标注无设计上下文 |
