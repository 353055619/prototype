# prototype

[design](https://github.com/zai-org/zcode/tree/main/packages/plugins/design) 官方插件（`design@zcode-plugins-official` v0.7.2）的完整副本，用于插件能力研究与对比评审（2026-11：design vs godw-html，design 胜出后 godw 系 html/design 插件已退役卸载）。

## 内容

- `design-plugin/` — 插件原样拷贝（382 文件）：单一 `design` skill 工作流（CREATE / REDESIGN / design-system / check / interact），74 个设计骨架（含 30+ HTML deck 模板）、23 套设计系统风格参考、11 份 craft 指南、design-lint / import-design-system 等脚本。
- `dsh-superagent-prototype/` — DeepSeek Harness 官方 Super-Agent 原型（MIT），depth-1 克隆后移除 `.git` 作为行为参考（入口仅 GitHub + 飞书；1 个常驻 orchestrator session + 每 ticket 一个 worker session）。
- `demo-spec.md` — 「飞书里跟 Super-Agent 对话」模拟 demo 的共享规格：机制事实表 F1–F12 + 统一对话脚本 + 三方向（demo-a 仿真飞书 / demo-b 指挥中心 / demo-c 移动叙事）差异定义。三方向在分支 `demo-a/b/c`（各自 worktree）并行产出，成品在各分支 `原型/` 下。

## 许可

插件代码 Apache-2.0；设计资源含第三方开源内容（open-design MIT、html-ppt 系列 MIT 等），许可证文件随目录完整保留，见 `design-plugin/skills/design/design-resources/` 下各 LICENSE / NOTICE。设计系统仅作风格参考，不暗示任何品牌关联（见 BRAND-DISCLAIMER.md）。
