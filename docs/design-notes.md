# 设计说明：biao-spec 从哪来、砍了什么

本文记录 biao-spec 的设计取舍——它抄了业界哪些机制、又刻意拒绝了什么。核心信念一句话：**好结果来自高分辨率的输入，而非更聪明的 AI；技能的价值密度和它的长度不成正比。**

## SDD 的三层成熟度

规格驱动开发（Spec-Driven Development）指在让 AI 写代码前先写规格，让规格成为人与 AI 的共同事实来源。业界大致分三层：

| 层级 | 含义 | 代表 |
|---|---|---|
| **Spec-first** | 先写规格再开发，任务完成后规格即弃 | spec-kit、superpowers、多数工具 |
| **Spec-anchored** | 规格长期保留，随功能演进持续维护 | OpenSpec、Kiro steering |
| **Spec-as-source** | 规格是主源文件，人只改规格，代码全部生成 | Tessl（实验阶段） |

biao-spec 目前是 **spec-first**。spec-anchored（现状规格与变更提案分离、归档时合并）列为二期候选。

## 对 SDD 的主要批评（我们要规避的）

来自 martinfowler.com 的实测，同样值得自研时警惕：

1. **流程尺度不匹配**——固定重流程对小任务过度设计（一个简单 bug 被展开成 4 个用户故事 + 16 条验收标准）。
2. **审查负担**——spec-kit 式工具生成大量冗长重复的 markdown，审规格比审代码还累。
3. **虚假控制感**——规格写得再细，AI 仍会忽视指令或过度执行。

> 第一设计约束由此而来：**流程深度必须随任务复杂度伸缩。**

## 值得抄的机制

| 机制 | 来源 | 为什么值得 |
|---|---|---|
| 访谈式核心指令（决策树 + 一次一问 + 带推荐答案） | grill-me | 三句话 = 80% 价值 |
| 能查代码就不问 | grill-me / trellis | 省用户时间，把"验证"变得比"生成"便宜 |
| Research-first（选型问题先调研再给带 trade-off 的选项） | trellis / superpowers | 不让用户凭空决策 |
| spec 自审（placeholder / 自洽 / 范围 / 歧义 / 覆盖面） | superpowers | 廉价、可靠、立刻做 |
| No Placeholders 铁律 | superpowers | 计划质量的底线 |
| 零上下文工程师假设 | superpowers / BMAD | 任务分解质量的核心 |
| 设计获批前不动代码的门 | superpowers | 保留门，去掉"再小也走全流程"的偏执 |
| 产物即时落盘，"对话会压缩，文件不会" | trellis | 跨 session 的根本保障 |
| 验收标准可测试 + 编号回指 | Kiro / spec-kit | spec 与验证闭环，计划可机械核对覆盖率 |
| 经验回流（实施中的发现写回计划） | trellis / cc-sdd | 后续 task 不重复踩坑 |

## 明确不抄的

- **脚本 / hooks / 状态机 / jsonl 注入**（trellis）——单人场景收益不抵维护成本。
- **每 task 多 subagent 的执行编排、强制 worktree、强制 TDD bite-size**（superpowers）——交给宿主原生能力（plan mode、任务系统、code review）。
- **七段相位门、constitution 独立机制**（spec-kit）——`CLAUDE.md` / `AGENTS.md` 已承担项目宪法角色。
- **多角色管线**（BMAD 的 Analyst→PM→Architect）——团队级收益，个人开发过重。
- **FR/SC 分离、P1/P2/P3 优先级、MVP 切片**（spec-kit）——团队级仪式。

## 关键决策记录（ADR-lite）

| 问题 | 决策 | 理由 |
|---|---|---|
| 覆盖范围 | Brainstorm + Plan + 轻量执行纪律 | 不做 subagent 编排；执行交宿主 |
| 产物形态 | 单文件 `.biaospec/<日期>-<主题>/spec.md`，三段分节，任务级目录隔离 | 与项目 `docs/` 分离，不与手写设计文档混杂；一任务一目录，日后同任务下的其他产物也有地方放 |
| 技能拆分 | 拆两个：brainstorm + plan | grill 可独立用；plan 承接获批的 spec |
| 小任务过度设计的解法 | 靠触发方式本身——用户主动调用即分级 | 机制归零，判断还给人 |
| 富文档 vs 极简 | 任意约定写死，富结构靠点拨 + 范例涌现 | 富结构是判断不是约定；模板会制造填空压力 → 重回 spec-kit 病 |

## 访谈派 vs 模板派

产出 PRD / Spec / Plan，业界分两派：

| | 访谈派 | 模板派 |
|---|---|---|
| 代表 | biao-spec、superpowers、trellis | spec-kit、Kiro、BMAD |
| 消歧时机 | **落盘前**——对话中逐个消掉，spec 出生即无歧义 | **落盘后**——先按模板铺满，再靠标记 / 审阅消歧 |
| 代价 | 用用户时间换前置确定性 | 用审阅时间换吞吐 |
| 适合 | 需求真模糊、方案有分叉 | 需求已明确、要快速铺开 |

biao-spec 是访谈派纯血。需求已明确时的快速路径就是——不调用技能，直接干。

## 参考

- [Understanding Spec-Driven Development — martinfowler.com](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)
- [GitHub spec-kit](https://github.com/github/spec-kit)
- [Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec)
- [grill-me 解析](https://note.com/gadgettakayuki/n/n0726556196db)
