# biao-spec

> 轻量的规格驱动开发（SDD）技能对：访谈澄清需求 → 落盘可审 spec → 拆可执行计划。
> 纯 Markdown，零脚本、零 hook、零状态机。为 Claude Code 与 codex 双平台设计。

**A minimal spec-driven-development skill pair for Claude Code / codex.** Interview to clarify requirements, land a reviewable spec, decompose into an executable plan — all in one file, no scripts, no hooks, no process machinery.

---

## 为什么是它

业界 SDD 工具（spec-kit、OpenSpec、superpowers、trellis、cc-sdd…）大多有一个共同代价：**流程装置越堆越重**——相位门、状态机、hook 注入、多文件产物，最后"审规格比审代码还累"。

biao-spec 走相反方向：只提炼各家里真正值钱的机制，其余全部砍掉。

- **访谈内核来自 grill-me**（Matt Pocock）：把需求当决策树，一次一问、每问必带推荐答案、能查代码就不问。三句话贡献了 80% 的价值。
- **spec 自审、No Placeholders 来自 superpowers**：廉价、可靠、立刻能做的质量底线。
- **单文件三段（PRD → Spec → Plan）**：一份 `docs/<日期>-<主题>-spec.md` 从头到尾，与"设计文档"惯例零迁移成本。
- **流程深度随任务伸缩**：由用户主动调用即分级——简单任务几句话，复杂任务才展开。机制归零,判断还给人。

设计取舍的完整调研见 [`docs/design-notes.md`](docs/design-notes.md)。

## 两个技能

| 技能 | 做什么 | 何时触发 |
|---|---|---|
| **biaospec-brainstorm** | 访谈式澄清需求 + 落盘可审 spec（PRD + 设计） | 需求不清、多方案可选、"帮我理需求 / 头脑风暴 / grill me" |
| **biaospec-plan** | 把已批准的 spec 拆成可勾选的实施计划 + 执行纪律 | spec 已获批、"拆任务 / 写实施计划 / 开始实施" |

两者只有一处单向移交（brainstorm 终态建议加载 plan），不做链式强制引用——进一个不会被拽进全家桶。

## 安装

技能就是「一个含 `SKILL.md` 的文件夹」，把 `skills/` 下的两个目录放到平台的技能目录即可。

**Claude Code**（项目级，随 git 同步给团队）：

```sh
cp -r skills/biaospec-brainstorm skills/biaospec-plan <你的项目>/.claude/skills/
```

用户级（对所有项目生效）：放到 `~/.claude/skills/` 即可。

**codex**：放到项目的 `.agents/skills/` 下。

> Claude Code 只从 `.claude/skills/` 加载项目技能，`.agents/skills/` 是 codex 的约定。两平台共用同一份 `SKILL.md`，双向复制即可。

## 用法

```
/biaospec-brainstorm   # 或直接描述一个模糊需求
```

典型流程：

1. **brainstorm** 先探索代码库，然后一次一问澄清目的 / 约束 / 成功标准，每问带推荐答案；
2. 收敛后把设计直接写成 `docs/<日期>-<主题>-spec.md`（状态=需求澄清中），你在文件上评审；
3. 获批后加载 **plan**，把计划追加到同一 spec 文件的「实施计划」节，每个 task 标注文件 / 内容 / 对应验收标准 / 验证方式；
4. 执行时逐 task 勾选、当场验证，实施中的坑回填进计划,完成后回填状态行。

## 设计原则

- **高分辨率输入胜过聪明的 AI**——好结果来自把需求问清楚，而非更强的模型。
- **对话会被压缩,文件不会**——决策、验收标准、实施中的发现全部落盘。
- **任意约定写死,判断只需点拨**——状态词表、AC 编号这类模型猜不出的约定明写；要不要展开某节这类判断交给模型。
- **设计获批前不写代码**——保留这道门,去掉"再小也要走全流程"的偏执。

## 致谢

站在这些工作的肩膀上：

- [grill-me](https://note.com/gadgettakayuki/n/n0726556196db) — Matt Pocock，访谈内核的源头
- [superpowers](https://github.com/obra/superpowers) — spec 自审、No Placeholders、零上下文工程师假设
- [trellis](https://github.com/) — 复杂度分级、ADR-lite、"persist everything" 的启发
- [OpenSpec](https://github.com/Fission-AI/OpenSpec) — spec-anchored 的方向（本项目二期候选）

## License

MIT — 见 [`LICENSE`](LICENSE)。
