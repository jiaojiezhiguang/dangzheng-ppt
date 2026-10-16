# dangzheng-ppt

**党政风格 PPT 制作 Skill** —— 让 Codex（也兼容 Claude Code 等支持 SKILL.md 标准的 Agent）按「中国红 ＋ 金」的党政版式制作和改造中文演示文稿，输出**可编辑的原生 .pptx**。

适用于团日活动、党建、思政、评优与工作汇报类答辩。商务、科技、学术风格不适用。

## 它解决什么

AI 做党政风 PPT 常踩的坑，这份 skill 把它变成了可检查的规矩：

- 字体不统一：只改正文不改主题字体，到 Windows 上出现中英文两套字体
- 图片被拉伸、行距不齐、越出页面
- 装饰堆得太多压住正文，或者留白太多显寒酸
- 改稿时覆盖了用户手改过的版本

## 包含什么

```
dangzheng-ppt/
├── SKILL.md                        入口：什么时候用、三条硬性规范、工作流
└── references/
    ├── style-spec.md               精确到 0.01″ 的坐标、色值、字号
    ├── layout-rules.md             图片四条硬规则、文字容量优先级、装饰放置
    ├── toolchain.md                运行时路径、渲染命令、七项验收清单、八个踩过的坑
    └── materials.md                素材库结构，以及「接手前先扫目录找新增」的流程
```

## 安装

把整个 `dangzheng-ppt` 文件夹放进 Agent 的 skills 目录：

| Agent | 目录 |
| --- | --- |
| Codex | `~/.codex/skills/dangzheng-ppt` |
| Claude Code | `~/.claude/skills/dangzheng-ppt` |
| 其他支持开放标准的 Agent | `~/.agents/skills/dangzheng-ppt` |

## 用法

装好后直接用自然语言：

> 用党政风格做一份团日活动成果汇报 PPT，12 页左右

> 按党政风格把这版 PPT 的装饰重做一遍，底部角标看着戳到页脚了

## 依赖

- Python 3 + `python-pptx`（改 .pptx）
- LibreOffice（转 PDF 看版式）；缺了也能做，只是没法逐页渲染核对
- 第一次使用时按 `references/toolchain.md` 核对本机路径

## 说明

版式规范来自一份真实交付的 28 页答辩 PPT（西安交通大学第四十一届「最佳团日」· 计算机2604团支部），逐条都是从实际被打回的反馈里提炼的。
