# Frontend DevKit

前端质量工具包。集成 impeccable 设计 skill 体系 + UI 设计工作流 + 前端质量门控。

## Architecture

- `skills/` — 所有 skill 源文件，plugin 直接读取
- 无 build 步骤

## Skills Overview

### 流程编排

| Skill | 类型 | 说明 |
|-------|------|------|
| `frontend-workflow` | SOP | 设计先行开发流程，按场景分流（Full / Lite / Mini） |
| `frontend-review` | Gate | 前端质量门控（6 维评分 + 6 项 Binary Gate） |

### 设计工作流

| Skill | 类型 | 说明 |
|-------|------|------|
| `ui-cases` | Tool | 组件设计方案生成（5+ variant 对比） |
| `design-freeze` | Tool | 冻结设计规范 |
| `teach-impeccable` | Setup | 建立项目设计上下文（.impeccable.md） |

### 设计实现（impeccable 核心）

| Skill | 说明 |
|-------|------|
| `frontend-design` | 生产级前端实现，反 AI slop 美学（含 reference/ 子目录） |
| `colorize` | 策略性配色（60/30/10 规则） |
| `typeset` | 排版设计（字体选择、层级、节奏） |
| `arrange` | 布局和空间（间距 scale、视觉节奏） |
| `animate` | 有目的的动效（easing、时长、reduced-motion） |
| `polish` | 最终细节打磨（对齐、间距、一致性） |

### 设计调整

| Skill | 说明 |
|-------|------|
| `bolder` | 放大视觉冲击力 |
| `quieter` | 降低视觉强度 |
| `distill` | 简化至本质 |
| `delight` | 增加惊喜和个性 |
| `overdrive` | 技术极限效果（shader、物理引擎、60fps） |
| `onboard` | 引导流程和首次体验设计 |

### 质量审计

| Skill | 说明 |
|-------|------|
| `audit` | 综合审计（A11y、性能、主题、响应式） |
| `critique` | UX 设计评估（层级、架构、情感） |
| `harden` | 健壮性（错误处理、i18n、文本溢出） |
| `adapt` | 跨设备适配 |
| `optimize` | 性能优化 |
| `clarify` | UX 文案改进 |

### 代码质量

| Skill | 说明 |
|-------|------|
| `normalize` | Design system 对齐 |
| `extract` | 组件抽取和复用 |

## 典型工作流

### 从零开始（Scene A: Full）

```
/frontend-workflow → teach-impeccable → ui-cases → design-freeze → frontend-design → frontend-review
```

### 已有设计语言，新增组件（Scene B: Lite）

```
/frontend-workflow → ui-cases → design-freeze → frontend-design → frontend-review
```

### 已有冻结 spec（Scene C: Mini）

```
/frontend-workflow → frontend-design → frontend-review
```

### 已有代码的质量把关

```
/frontend-review → 根据 Issues 中的 Skill 建议逐个修复 → re-review
```

### 与 devkit 配合

前端代码应同时跑两个 review：
- `devkit:code-review` — 审代码逻辑和安全
- `frontend-devkit:frontend-review` — 审视觉设计和前端体验
