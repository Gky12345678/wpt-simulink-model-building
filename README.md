# WPT Simulink 模型搭建

语言选择：简体中文 | [English](README.en.md)

这是一个用于无线电能传输（WPT）和双向无线电能传输（BWPT）系统的 Codex skill，面向 Simulink 或 Simscape Electrical 模型的搭建、整理和审查。

## 用途

该 skill 用于帮助 Codex 搭建结构清晰、器件选择明确、测量位置合理、辅助模块封装规范、参数初始化可复现、整体布局美观的 WPT/BWPT 电力电子仿真模型。

它关注的是模型搭建规范，而不是同步理论推导、PQ 法推导、专利理论撰写或控制算法证明。

## 文件结构

```text
wpt-simulink-model-building/
|-- README.md
|-- README.en.md
|-- SKILL.md
|-- agents/
|   `-- openai.yaml
`-- references/
    |-- model-building-rules.md
    |-- topology-templates.md
    `-- review-and-failures.md
```

## 主要能力

- 在正式搭建模型前进行方案对接，确认关键建模信息。
- 按支路数量、功率流向、补偿网络、接收端变换方式和模型抽象层级对 WPT/BWPT 拓扑进行分类。
- 主功率回路保持显式展示，同时允许控制、驱动、保护、诊断和工况模块封装为清晰的功能子系统。
- 应用横平竖直、少交叉、少压线的 Simulink 布局规则。
- 提供 SS 单向 WPT、LCC-LCC 双向 BWPT、2 对 2 全串联补偿 WPT 的模板化搭建参考。
- 审查模型中的连线、命名、参数、测量与仿真验证准备问题。

## 安装

将该文件夹放入 Codex 的 skills 目录：

```text
C:\Users\Administrator\.codex\skills\wpt-simulink-model-building
```

在其他电脑上使用时，可以将该仓库复制或克隆到对应的 Codex skills 目录中。

## 使用方式

在需要搭建或整理 WPT/BWPT Simulink 模型时，让 Codex 使用该 skill，例如：

```text
使用 wpt-simulink-model-building 搭建一个 SS 单向 WPT Simulink 模型。
```

```text
使用 wpt-simulink-model-building 审查并整理这个 BWPT Simscape 模型的布局。
```

该 skill 会优先确认功率流向、补偿拓扑、器件抽象层级、库类型、是否启用控制和驱动等辅助模块、测量输出、参数管理方式以及布局偏好，之后再开始正式建模或整理。

## 校验

该 skill 文件夹已通过 Codex 的 `quick_validate.py` skill 校验脚本。
