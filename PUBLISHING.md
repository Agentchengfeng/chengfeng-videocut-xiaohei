# 发布范围

本文件与 bin/check-package.cjs 由工作台统一生成。业务 SKILL.md 面向使用者；发布步骤放在本文件，不维护第二套业务源码或包装逻辑。

## 本包的方向

保留独立分发方向；当前按 GitHub 源码预览发布。第三方素材、图标和 vendor 的来源与许可已在 `NOTICE.md` 列明；这不等于 npm 注册表发行、宿主加载或业务 E2E 已验收。

## 上传什么

- 唯一业务真源：.agents/skills/chengfeng-videocut-xiaohei/，含实际需要的 references、scripts、获许可 assets、公开样例与可复现测试。HTML、SVG、模板图片可以是必要资源。
- 包装文件：README.md、SKILL.md、package.json、skill-package.json、PUBLISHING.md、LICENSE、NOTICE.md、CITATION.cff、bin/install.cjs、bin/check-package.cjs，以及 .gitignore。公开使用方法需自包含，不能依赖私有产品记录。
- 保留 LICENSE / NOTICE / CITATION 以及第三方许可与出处；项目 LICENSE 不会重新授权别人的资源。

## 不上传什么

私有产品记录、历史/备份、内部复盘、用户媒体/作品/字幕稿/词典、凭据、安装记录、未脱敏日志、缓存、node_modules、本机快捷软链和旧源码。Runtime、Studio、FFmpeg、浏览器等公共基础设施归工作台发行，不在每个 Skill 重复打包。

## 怎么检查

在本包目录运行：

```sh
npm run check:package
npm run check:upload
```

check:package 检查源文件、内容摘要、npm pack 实际候选清单与本仓库 Git 索引；check:upload 额外检查独立发行方向及已知许可阻断。二者都不下载、不执行安装钩子、不暂存、不推送。安装后的纯 Skill 目录不是源码包装，不在其中运行这两个命令。

.gitignore 无法阻止已跟踪文件泄漏。只暂存审核过的公开文件，然后重新检查；索引与工作区不一致会阻断，确认内容后再暂存。当前阻断视频、音频、字幕等用户数据文件；确需新增公开 fixture 时先审查隐私/权利，并调整共享规则和测试，不能强行忽略检查。

检查不是完整隐私扫描：文字、图片、来源与许可须人工审查；既有 Git 历史不在扫描范围。通过不证明已发布、宿主加载或业务可用。按授权推送后，仍需固定 commit/产物摘要、真实安装、宿主加载和最小任务验收。
