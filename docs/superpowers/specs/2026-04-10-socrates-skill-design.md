# Socrates Skill -- Design Specification

**Date:** 2026-04-10
**Status:** Draft
**Skill Name:** socrates

---

## 1. Overview

Socrates 是一个辩证思辨引擎 Skill，采用 **苏格拉底诘问法 + 第一性原理 + 奥卡姆剃刀** 的三阶段循环，指导 Agent 进行无限深入的自我思辨。

**核心哲学模型：辩证综合式 (Dialectical Synthesis)**

每轮迭代产生 正题 -> 反题 -> 合题，合题成为下一轮正题，形成螺旋上升的思辨链。每轮必须产生质变（新理解 != 旧理解），防止原地踏步。

**设计原则：**
- 不解决特定问题，而是辅助思考
- 纯无限模式，用户主动中断
- 可见思维链，Agent 自我思辨展示
- 通用思考引擎 + 软件工程特化

---

## 2. Trigger Conditions

### 何时触发
- 用户直接调用 `/socrates`
- 用户要求"深度思考"、"质疑假设"、"从第一性原理分析"
- 面对复杂决策、架构权衡、多方案选择
- 其他 Skill 内部调用作为思考引擎

### 何时不触发
- 简单的信息查询（"X 是什么"）
- 明确的执行指令（"删掉这个文件"）
- 已有成熟方案的标准操作

---

## 3. Architecture

### 3.1 File Structure

```
skills/socrates/
├── SKILL.md                    # SOP: 辩证循环流程 + 纪律条款 (<500 lines)
└── reference/
    ├── socratic-questions.md   # L3: 五类追问详解与示例
    └── engineering.md          # L3: 软件工程领域特化追问
```

### 3.2 Progressive Disclosure

| Level | Content | Load Time | Tokens |
|-------|---------|-----------|--------|
| L1 | `name` + `description` | Always | ~100 |
| L2 | SKILL.md body | Skill triggered | ~3-4k |
| L3 | reference/socratic-questions.md | Agent needs question guidance | ~1-2k |
| L3 | reference/engineering.md | Problem is engineering domain | ~1k |

### 3.3 No Scripts

纯思维过程 Skill，无确定性计算需求，不设 `scripts/` 目录。

---

## 4. Core Loop: Dialectical Triad

### 4.1 Process Flow

```
┌─────────────────────────────────────────────────┐
│  Round N                                         │
│                                                  │
│  ┌───────────┐    ┌────────────┐    ┌─────────┐ │
│  │  Thesis   │───>│ Antithesis │───>│Synthesis│ │
│  │  正题      │    │  反题       │    │  合题    │ │
│  │(当前理解)  │    │(苏格拉底)   │    │(重建)   │ │
│  └───────────┘    └────────────┘    └─────────┘ │
│       ^                                  │       │
│       └──────────────────────────────────┘       │
│              合题 -> 下一轮正题                    │
└─────────────────────────────────────────────────┘
```

### 4.2 Thesis (正题)

**Round 1:** 用户输入经启动协议处理后的初始命题
**Round N>1:** 上一轮的合题

格式：一句话清晰陈述当前立场。

### 4.3 Antithesis (反题) -- 苏格拉底诘问

对正题发起追问，从五类中选择最有力的：

1. **澄清追问 (Clarification):** 这个概念到底意味着什么？
2. **假设追问 (Assumption Probing):** 这依赖了什么未经检验的前提？
3. **证据追问 (Evidence Probing):** 支撑证据是什么？可靠吗？
4. **视角追问 (Perspective Shifting):** 从对立面看会得出什么？
5. **后果追问 (Consequence Tracing):** 如果成立，逻辑上导致什么？

**硬性要求：** 每轮必须产出至少一个被动摇的假设或发现的盲区。

详细指南与示例：`reference/socratic-questions.md`

### 4.4 Synthesis (合题) -- 第一性原理 + 奥卡姆剃刀

**两步操作：**

1. **第一性原理还原：** 剥离类比、经验、权威引用，只保留不可否认的基本事实
2. **奥卡姆剃刀重建：** 从基本事实出发，用最简模型重新构建理解

**硬性要求：** 合题 != 正题。如果相同，触发质变守卫。

---

## 5. Startup Protocol

用户输入进入辩证循环前，先经过命题锚定：

```
输入分类：
├── 明确命题 ("X是对的")      -> 直接作为 Round 1 正题
├── 开放问题 ("为什么X?")     -> 提炼为可辩证的命题
└── 模糊意图 ("想想X")        -> 提炼核心张力，构造初始命题
```

无论哪种，Round 1 开始前必须输出：

```
命题锚定：[初始正题陈述]
```

---

## 6. Mutation Guard (质变守卫)

防止思辨退化为复述的关键机制：

```
每轮合题产出后：
  if 合题 ~= 正题:
    显式声明："本轮未产生质变"
    自救策略：
      1. 切换追问类型（如从假设追问换到视角追问）
      2. 挑战更深层假设
      3. 引入对立范式或极端场景
    if 连续两轮无质变:
      声明："已触及当前认知边界"
      （不自动终止 -- 用户可选择继续或转向）
```

---

## 7. Output Template

### 7.1 Independent Mode (独立模式)

每轮迭代输出：

```markdown
## Round N

### Thesis
> [一句话陈述当前理解]

### Antithesis
**追问类型**: [澄清/假设/证据/视角/后果]

[展开追问过程]

**动摇的假设**: [明确列出]

### Synthesis
**不可否认的事实**:
- [事实 1]
- [事实 2]

**最简重建**:
> [新理解 -- 下一轮正题]

**质变判定**: [yes: 变化了什么 / no: 触发质变守卫]

---
Continuing...
```

### 7.2 Engine Mode (引擎模式)

被其他 Skill 调用时：
- 静默运行，不向用户输出中间过程
- 只返回最终合题 + 关键推导路径
- 调用方可通过配置块控制行为：

```
[socrates-config]
max_rounds: 3
focus: "架构决策"
return: "final_synthesis"  # 或 "full_chain"
[/socrates-config]
```

---

## 8. Engineering Specialization

当检测到问题属于软件工程领域时，三拍循环注入额外追问维度：

| Stage | Generic | Engineering Overlay |
|-------|---------|-------------------|
| Antithesis | 假设是否成立？ | 这个架构假设在 10x 负载下还成立吗？ |
| Antithesis | 有什么反例？ | 哪些已知系统因相同假设而失败？ |
| Synthesis-还原 | 不可否认的事实？ | 不可绕过的技术约束？(CAP, latency, consistency) |
| Synthesis-重建 | 最简模型？ | 从零开始只用标准库，你会怎么构建？ |

工程特化**叠加**在通用流程之上，不替换。

详细指南：`reference/engineering.md`

---

## 9. Hard Rules (纪律条款)

1. **禁止跳过反题** -- 不允许"显然正确"直接进入合题
2. **禁止重述伪装为质变** -- 换说法不算新理解
3. **禁止诉诸权威** -- "因为 Google 这样做"不构成第一性原理
4. **禁止过早收敛** -- 除非连续两轮质变守卫触发
5. **每轮必须暴露至少一个假设** -- 没有假设说明追问不够深

---

## 10. Red Flags -- Agent Self-Check

以下想法出现时，说明你正在偷懒：

| Thought | Reality |
|---------|---------|
| "这个结论很明显" | 明显 = 未被检验。追问它。 |
| "已经想得够深了" | 你不是判断者，用户是。继续。 |
| "这一轮和上一轮差不多" | 触发质变守卫，切换追问策略。 |
| "没有更多假设了" | 换一类追问，总有隐藏假设。 |
| "这只是语义问题" | 语义模糊是最深的假设陷阱。 |
| "实际中这不重要" | 第一性原理不关心实用性，只关心真理。 |

---

## 11. Dual Mode Summary

| Dimension | Independent Mode | Engine Mode |
|-----------|-----------------|-------------|
| Trigger | /socrates or description match | Other Skill internal call |
| Output | Full render per round, user visible | Silent, returns final synthesis |
| Interaction | Prompt to continue/interrupt each round | Caller sets max_rounds |
| Verbosity | Full dialectical process | Conclusion + key derivation path |
| Termination | User interrupt only | max_rounds or caller stops |

---

## 12. Success Criteria

1. **质变率 > 80%** -- 至少 80% 的轮次产生质变
2. **假设暴露** -- 每轮至少暴露一个未经检验的假设
3. **无权威引用** -- 合题中不出现"因为 X 权威这样说"
4. **深度感** -- 用户在 3 轮后能感知到思考层次的明显深入
5. **双模式可用** -- 独立调用和被其他 Skill 调用均正常工作
