# 民法典生活伴侣 · Civil Code Companion

> 把《中华人民共和国民法典》变成一个能对话的技能：**生活纠纷一句话，民法典条文对号入座。**

一个 [Agent Skill](https://code.claude.com/docs/en/skills)。用户用大白话描述生活纠纷，它输出四段固定结构：涉及编章 → 精确条号与要义 → 大白话解读 → 下一步建议。

## 它能干什么

| 输入 | 输出 |
|---|---|
| "房东说房子卖了让我月底搬走，合同还剩8个月" | 第三编 · 第725条（买卖不破租赁）→ 新房东须继续履行 → 保存合同与转账记录 |
| "我爸去世没留遗嘱，房本是爸妈两人的名字" | 第六编 · 第1123/1127/1153/1161条 → 先析产一半归母亲，余下按第一顺序继承 |
| "邻居偷了我快递，能判几年？" | 超出民法典范围 → 建议报警；物权视角可主张返还原物（第235条） |

覆盖租房、婚姻、继承、侵权、消费、物业、邻里等高频生活场景。

## 目录结构

```
.
├── SKILL.md                     # 技能本体（YAML frontmatter + 知识骨架 + few-shot）
├── README.md                    # 本文件
├── mcp.example.json             # 可选的民法典全文 MCP 配置
└── LICENSE                      # MIT
```

## 安装

把 `SKILL.md` 放进 skills 目录即可：

```bash
# 用户级（所有项目可用）
mkdir -p ~/.workbuddy/skills/civil-code-companion
cp SKILL.md ~/.workbuddy/skills/civil-code-companion/
```

Claude Code 等支持 Agent Skills 的环境同理（放到对应 skills 目录）。

## 可选升级：接入民法典全文 MCP

`SKILL.md` 内置约 60 条高频条文骨架，**零依赖即可用**。若想让"条号零幻觉"更进一步，接入《民法典》全文 MCP，让技能直接查 1260 条原文：

```json
{
  "mcpServers": {
    "civil-code-of-china": {
      "command": "npx",
      "args": ["-y", "@jjfather/civil-code-of-china-mcp"]
    }
  }
}
```

见 `mcp.example.json`。该 MCP 提供两个工具：

- `get_structure(parent_node?)` —— 逐层定位（根 → 编 → 章 → 节）
- `get_article_content(article_path)` —— 取条文原文，路径须用上一级返回的**精确节点字符串**拼接

技能会优先调用 MCP 取回原文再解读；未接入时退回内置骨架，并提示"建议核实原文"。

## 设计要点

- **只做一件事**：场景 → 条文定位 + 通俗解读，不做长篇普法、不写文书、不预测判决。
- **禁止编造条号**：骨架中没有的条文明确说"需核实原文"。
- **有边界**：刑事、行政、诉讼程序问题直说超出范围，转介律师或 12348 法律援助热线。
- **不吓唬人**：语气平实通俗；案情重大时提示"不构成正式法律意见"。
- **纠偏常识**：内置 7 条常见误区（"净身出户"无法律依据、冷静期仅适用协议离婚、公证遗嘱不再当然优先、"父债子还"不绝对等）。

## 来源与致谢

- 条文依据：《中华人民共和国民法典》（2020年5月28日通过，2021年1月1日施行，7编1260条）
- 知识骨架结构参考 [THUYRan/Legal-Skills-Chinese](https://github.com/THUYRan/Legal-Skills-Chinese)、[CSlawyer1985/claude-for-legal-ZH](https://github.com/CSlawyer1985/claude-for-legal-ZH) 等开源法律技能库的组织思路
- 全文数据能力来自 [liuxc1024/civil-code-of-china-mcp](https://github.com/liuxc1024/civil-code-of-china-mcp)

## ⚠️ 免责声明

本项目是**普法辅助工具，不提供法律意见、不构成法律结论、不能替代律师**。输出仅作为理解条文的参考。

法律适用高度依赖具体事实与证据，且司法解释、地方规定可能更新。涉及金额较大、案情复杂或拟提起诉讼的，请咨询执业律师，或拨打 **12348** 法律援助热线。因使用本项目内容作出的任何决定，由使用者自行承担后果。

## License

MIT
