---
name: frontend-workflow
description: >
  Design-first frontend development workflow with scene-based routing.
  Routes to the right flow based on project state: full pipeline for greenfield,
  streamlined for existing designs, minimal for single components.
  Use when: (1) starting any frontend feature, (2) user says /frontend-workflow,
  (3) user says "build the UI", "design the page", "implement the view",
  (4) user wants to create a new frontend component or page from scratch,
  (5) user says "从零开始做前端", "设计这个页面", "写这个组件".
  Make sure to use this skill whenever the user is about to start frontend work
  that involves design decisions — even if they jump straight to "write the code",
  check if they have a design spec first. If not, route them through design.
  Triggers on: "build frontend", "create the UI", "design the page",
  "implement the view", "write the component", "做前端", "设计页面",
  "新建组件", "从零开始".
user-invokable: true
args:
  - name: target
    description: What to build (component, page, feature description)
    required: false
  - name: scene
    description: "Force a specific scene: full | lite | mini"
    required: false
---

# Frontend Workflow

设计先行的前端开发流程。根据项目状态自动分流到合适的流程深度。

## Why Design First

直接写前端代码会导致"做完了但不好看"或"好看但不是用户想要的"。设计决策前置——用户在写代码之前就能看到并选择风格方向。

但不是每次都需要走完整流程。单个按钮组件不需要 design freeze；已有设计系统的项目不需要从零探索风格。

## Scene Detection

进入 workflow 后，先判断当前场景：

```
检查 1: 有 design-spec.md 吗？
  ├─ YES → 检查 2
  └─ NO  → 检查 3

检查 2: 本次要做的组件/页面在 spec 中有记录吗？
  ├─ YES → Scene C: Mini（直接实现）
  └─ NO  → Scene B: Lite（补充设计后实现）

检查 3: 有 .impeccable.md 吗？
  ├─ YES → Scene B: Lite（已有设计语言，探索+实现）
  └─ NO  → Scene A: Full（完整流程）
```

用户也可以通过 `scene` 参数强制指定场景。

## Scene A: Full Pipeline

**适用场景**：从零开始的项目，没有设计上下文，没有设计规范。

```
1. teach-impeccable  → 建立设计上下文（.impeccable.md）
2. 组件清单          → 列出所有需要设计的组件，与用户对齐
3. ui-cases          → 为每个组件生成 5+ 设计方案（可并行）
4. 用户选择          → 用户通过 index.html 对比选择
5. design-freeze     → 冻结设计规范（design-spec.md）
6. frontend-design   → 按 spec 实现代码
7. frontend-review   → 质量门控
```

### Step 1: Design Context

调用 `/teach-impeccable`，建立 `.impeccable.md`。这定义了项目的品牌调性、设计原则、目标受众。

### Step 2: Component List

与用户对齐需要设计的组件列表：

- 列出所有组件（名称、职责、所在页面、包含的状态）
- 确定风格体系数量（默认 1 套，用户要求时可追加）
- 确定 cases 输出目录

### Step 3: Generate Cases

调用 `/ui-cases` 为每个组件生成设计方案：

- 多个组件可并行 dispatch
- 每批完成后更新 index.html 对比工具
- 用户可随时追加风格体系

### Step 4: User Selection

引导用户通过 index.html 对比选择。记录每个组件的选择到 design-spec.md 草稿。

### Step 5: Design Freeze

调用 `/design-freeze`。Spec Auditor 审核完整性和一致性，用户确认后冻结。

### Step 6: Implementation

使用 `/frontend-design` skill 指导实现：

- 严格按 design-spec.md 实现
- 实现顺序：design tokens → 基础组件 → 复合组件 → 页面布局
- 技术困难需调整设计 → 先与用户沟通

### Step 7: Review

调用 `/frontend-review` 做质量门控。PASS 后完成。

---

## Scene B: Lite

**适用场景**：已有 `.impeccable.md`（设计语言已建立），但本次要做的组件还没有冻结的设计方案。

```
1. ui-cases          → 为新组件生成设计方案
2. 用户选择          → 对比选择
3. design-freeze     → 补充冻结（追加到现有 spec）
4. frontend-design   → 实现
5. frontend-review   → 门控
```

跳过 `teach-impeccable` 和组件清单对齐（已有上下文）。`design-freeze` 是追加模式——在现有 design-spec.md 中补充新组件的规范，而非重新生成。

---

## Scene C: Mini

**适用场景**：design-spec.md 中已有该组件的冻结设计方案，直接实现。

```
1. frontend-design   → 按 spec 实现
2. frontend-review   → 门控
```

最简流程。设计决策已经做过了，不需要再探索。

---

## Scene Override

用户可以在任何场景下跳步或回退：

| 用户说 | 动作 |
|--------|------|
| "跳过设计直接写" | 走 Scene C（但 review 时如果没有 spec 会标注） |
| "我想重新设计这个组件" | 即使有 spec，也走 Scene B 重新生成 cases |
| "再来一套 XX 风格" | 在当前场景中追加 ui-cases |
| "先不 freeze" | 跳过 design-freeze，但 review 时会缺少 spec 对照 |

## Anti-Patterns

| Anti-Pattern | What To Do Instead |
|---|---|
| 不检查场景直接走 Full | 先 detect scene，避免已有设计的项目重走全流程 |
| Lite 场景中重建 .impeccable.md | 已有就用，除非用户明确要求重建 |
| Mini 场景中跳过 review | 即使是最简流程，review 也不能省 |
| 强制用户走完每一步 | 用户说跳就跳，记录跳过的步骤即可 |
