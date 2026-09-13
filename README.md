# chengfeng-videocut-xiaohei

小黑动画：制作可编辑的 Ian 小黑风格 HTML/SVG 动画。

独立维护与分发的 chengfeng-videocut Skill，共用 Runtime CLI；不包含 Runtime、Studio 或媒体工具。

## 本地候选安装

当前版本 **0.1.0-beta.1**，先以 GitHub 源码预览方式发布；这不等于 npm 注册表发行或宿主加载已验收。已有 Node 18+ 时，在本仓库目录运行：

```sh
node bin/install.cjs help
node bin/install.cjs install --host codex --target-root <已存在的隔离用户目录>
node bin/install.cjs doctor --host codex --target-root <同一目录>
```

真实安装须获得授权，并把目标设为真实用户目录；不指定目标时使用当前用户。默认 help 不写入。安装只写中立 .agents/skills/chengfeng-videocut-xiaohei，codex 模式另建精确入口；默认 agents 模式不改宿主配置。不重建 Claude 整目录布局，不迁移现有 Plugin。

相同 ID、版本和文件摘要复用；冲突拒绝，保留已有包，不提供自动升级/卸载。未验证 Windows 安装与当前任务加载。宿主文件安装、实际加载、Runtime 能力和业务验收分开报告。

## 使用

安装并确认宿主可读后，使用 $chengfeng-videocut-xiaohei。完整方法见 [SKILL.md](.agents/skills/chengfeng-videocut-xiaohei/SKILL.md)。需要工程操作时核对 Runtime >=0.5.9 及实际 workbench commands；不只看版本号，不默认连接某个端口。

## 整套安装与维护

整套说明见 [主仓 INSTALL](https://github.com/Agentchengfeng/chengfeng-videocut/blob/main/INSTALL.md)。已公开六包源码预览，但本候选和远端完整组合不因此视为已发布。本包只包含自己的方法与资源，其他 Skills 分别维护。计划仓库名为 Agentchengfeng/chengfeng-videocut-xiaohei。候选状态见 skill-package.json；远端身份须另核对固定提交和发布证据。

源码唯一真源是 .agents/skills/chengfeng-videocut-xiaohei/。references/shared 是公共 CLI 合同的带摘要快照；由工作台 scripts/build-independent-skills.cjs 显式更新。私有产品记录与旧源码不进 Git 或 npm pack。

## 风格来源、原创边界与许可

由成峰 / AI产品自由维护。本仓库的视觉方向**借鉴 Ian Xiaohei 的公开作品与解释型手绘语言**：黑色角色、少字批注、留白构图和“一个动作讲清一个概念”的表达方式。这里是方法和实现层面的风格参考，不是 Ian Xiaohei 的官方项目，不代表合作、授权或背书；仓库不复制其原图、原始角色文件或未获授权的素材。

本仓库的 HTML/SVG 模板、动画编排和说明由本项目独立实现。请在再分发时保留本仓库的署名和本节来源说明。第三方运行时和图标不由 Apache-2.0 重新授权：GSAP 3.15.0 按 [GSAP Standard License](https://gsap.com/community/standard-license/) 使用，Rough.js 按 [MIT License](https://github.com/rough-stuff/rough/blob/master/LICENSE)，LobeHub 图标按其 [MIT License](https://github.com/lobehub/lobe-icons/blob/master/LICENSE)，Lucide 图标按 [ISC License](https://github.com/lucide-icons/lucide/blob/main/LICENSE)。完整清单见 [NOTICE.md](NOTICE.md)。

## 维护者上传边界

源码、安装包与本地资料的范围见 [PUBLISHING.md](PUBLISHING.md)。上传前运行 `npm run check:package` 和 `npm run check:upload`；检查不执行 GitHub 推送，也不代替人工许可/隐私审查。
