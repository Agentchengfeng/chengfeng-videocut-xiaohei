# 小黑动画 → ChatCut 适配层

## 目的与边界

小黑 Skill 和 ChatCut 解决的是两件事：小黑负责把认知动作设计成可控的 HTML/SVG/GSAP，ChatCut 负责把可编辑动画资产放进视频工程。适配层只负责格式和生命周期转换，不把 ChatCut 变成小黑的运行时依赖，也不改变小黑的视觉规则。

当前已在 ChatCut Plugin `1.10.12` 的 Hosted MCP 路径验证过这条链路；版本号只是验证基线，不是永久兼容承诺。每次执行仍要读取 ChatCut 当前 Skill 和工具 schema。

## 两种输出模式

| 模式 | 交付物 | 是否需要 ChatCut |
| --- | --- | --- |
| 独立动画 | `index.html`、内联 SVG、GSAP、静态/动帧复核 | 否 |
| 剪辑工程动画 | ChatCut inline React/JSX Motion Graphic、资产属性、时间线实例 | 是 |

不要把独立模式的 `index.html`、SVG 文件路径或 GSAP 页面直接上传给 ChatCut。ChatCut 当前的 Motion Graphic 接口接收 inline JSX，不读取本地 Skill、仓库文件或本地 HTML。

## 转换原则

先保留一个与宿主无关的场景意图，再生成目标格式。推荐的中间描述至少包含：

```text
SceneSpec
├── canvas: width, height, fps
├── layers: 语义图层与共享坐标系
├── content: 文案、颜色、字体、资源引用
├── motion: 起止帧、关键帧、easing、层级顺序
└── editable: 允许时间线实例覆盖的属性
```

转换步骤：

1. 按小黑规则完成认知锚点、物理隐喻、图层和动作设计；独立 HTML/SVG 仍可作为设计预览。
2. 将每个语义图层映射到 JSX 中的单一组件/共享坐标系；不要把一堆绝对定位的 DOM 片段机械翻译成互相脱节的元素。
3. 用 ChatCut 的 Motion Graphic 合同生成 inline JSX：纯 JavaScript JSX、无 `import`/`export`，根节点为 `<div style={rootStyle}>`，使用 `useCurrentFrame()`，从 `item.props` 读取可编辑值。
4. 将可变文案、主色、强调色和资源 URL 声明为匹配的 editable properties；不在 JSX 中写死本地文件路径或未声明的默认值。
5. 使用当前 ChatCut 工具 schema 调用 `create_motion_graphic_from_code` 创建资产；工具字段必须以当前说明为准，不能猜字段名。
6. 创建后读取资产，确认状态为 `ready`、代码存在、属性 schema 完整且没有 errors/warnings；再用 `edit_item` 放入指定项目/时间线。
7. 回读 timeline/item，并用 `preview_timeline` 检查中间帧和 settled frame。云端渲染返回链接只证明渲染任务生成；没有像素访问时，必须把目视复核标为未完成。

## 画布适配与黑边

“适配视图”有两层含义，不能混用：

- **编辑器视口适配**：ChatCut 工具栏的“适配视图”只改变编辑器摄像机/缩放，让用户看清画布；它不会改变时间线素材实例的宽、高或位置。
- **素材实例适配**：把 Motion Graphic 放入时间线时，必须读取当前项目的 `canvas.width`、`canvas.height` 和画幅比例，并把全画幅小黑场景实例设置为同一尺寸，再水平、垂直居中（以画布中心为锚点）。不能依赖用户之后手动点“适配视图”。

对全画幅解释型小黑场景，默认合同是：

```text
instance.width  = canvas.width
instance.height = canvas.height
instance.x/y    = canvas center (aligned horizontally and vertically)
```

源场景的 SVG/HTML 根画布也应使用同一宽高和 `viewBox`。如果源画幅与项目不一致，必须显式选择策略：`contain` 保留完整画面但允许有意留边，`cover` 铺满画布但会裁切；不得把比例不匹配造成的黑边误报为“适配视图已完成”。

接入验收至少回读三项：实例宽高与画布尺寸、实例中心位置、预览中间帧/settled frame。全画幅场景的验收标准是没有非预期黑边（允许用户明确选择 `contain` 的留边），并且主体没有被裁掉；只看到“适配视图”按钮或 `ready` 状态都不算通过。

## ChatCut 侧前置检查

只在用户选择 ChatCut 或当前项目已绑定 ChatCut 时执行：

- 先用 ChatCut 的只读项目发现/连接接口确认项目身份和访问权限；不要从项目名称猜 `projectId`。
- 读取 `chatcut-plugin-basics` 与 `create-motion-graphics` 的当前版本；Hosted MCP 与 Desktop/ACP 的工具表不能混用。
- 若 MCP 只显示 `enabled`，仍需实际只读调用证明认证和项目访问；注册状态不是业务能力证明。
- 没有可用的 `create_motion_graphic_from_code` 或等价受支持接口时，停止工程接入，只交付独立 HTML/SVG，并明确“尚未集成”。

## 验收分层

```text
Skill 可读
→ ChatCut MCP/插件可调用
→ 临时或用户指定项目身份确认
→ JSX 资产创建并回读
→ 时间线实例放置并回读
→ 云渲染返回
→ 像素级静帧/动帧目视检查
```

前一层不能替代后一层。尤其是：

- 本地 Skill 文件存在 ≠ ChatCut 已加载小黑；
- MCP `enabled` ≠ OAuth、项目权限或真实剪辑能力已验证；
- JSX 资产 `ready` ≠ 画面构图和动作质量已通过；
- 预览资源 URL 返回 ≠ 当前环境已经完成像素目视检查。

## 失败处理

- **直接导入 HTML/SVG 失败**：不是重写小黑 Skill，而是改走 inline JSX 适配层。
- **工具接口缺失或 schema 不一致**：停止，不猜 API、不发送 chengfeng-videocut 的 CLI 命令给 ChatCut。
- **认证/项目权限失败**：报告准确错误，按 ChatCut 的登录与项目访问流程处理；不改账号设置、不迁移项目。
- **浏览器或渲染资源不可访问**：保留已创建的隔离资产，区分“结构/渲染已验证”和“像素目视未验证”，不要重复创建资产。

适配层的目标是让小黑内容进入 ChatCut 的可编辑工程，而不是把两个仓库合并。小黑仍以独立 Skill 维护、版本化和分发；ChatCut 只是一个可选宿主适配器。
