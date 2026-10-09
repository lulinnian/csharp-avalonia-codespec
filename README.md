# C# & Avalonia CodeSpec

一个面向 Codex 的代码整理技能，用于在保持行为不变的前提下，规范、审查和局部重构 C# 与 Avalonia AXAML/XAML 代码。

技能不会向项目强加一套固定的个人风格。它会优先读取仓库中的 `AGENTS.md`、`.editorconfig`、项目配置和相邻代码，再按当前项目的实际约定执行整理。

## 适用场景

- 整理 C# 文件结构和局部格式
- 改善命名、控制流和可读性
- 检查异步、取消、事件订阅和资源释放
- 规范 Avalonia AXAML 的属性排版、绑定和资源使用
- 检查 View、ViewModel 与 code-behind 的职责边界
- 审查 Popup、拖放、SVG、视觉树和富内容控件的生命周期
- 在不扩大任务范围的前提下执行聚焦重构

它不适合仅仅因为功能代码使用了 C# 或 AXAML 就自动触发；普通功能开发仍应按功能任务本身处理。

## 核心原则

1. 仓库规范优先于技能默认规则。
2. 默认执行行为保持型整理。
3. 保护用户已有修改，不顺手重构无关文件。
4. 不擅自修改公开 API、绑定路径、资源键、`x:Name`、自动化标识或序列化契约。
5. 避免全文件格式化和无意义 diff。
6. 构建成功与运行时验证必须明确区分。

## C# 关注点

- 只删除能够确认未被反射、DI、序列化、XAML 或平台入口使用的代码。
- 优先清晰命名、早返回和简单控制流，避免过度压缩的 LINQ 或技巧性表达式。
- 保留真实的可空语义，不用 `!`、空捕获或任意默认值掩盖问题。
- 不引入 `.Result`、`.Wait()` 或无人管理的 fire-and-forget。
- `CancellationToken`、事件订阅、流、Client、Lease、Timer 等需要完整的生命周期管理。
- 业务状态和流程优先放在 ViewModel 或服务；平台与视觉交互可以保留在 View 层。

## Avalonia 关注点

- AXAML 排版遵循当前项目风格，只整理必要区域。
- 保持绑定模式、转换器、回退值、相对源和编译绑定契约。
- 优先复用已有 Theme、Style、资源键、间距和颜色。
- 删除资源、命名元素或命名空间前，检查模板、选择器、动态资源、code-behind 和跨文件引用。
- 保持布局约束、滚动、虚拟化、响应式行为和图片等比缩放。

技能还包含针对复杂 Avalonia 项目的保护规则，例如：

- 脱离逻辑树的控件不能依赖只存在于父窗口中的 `StaticResource`。
- 隐藏控件仍可能求值绑定，SVG 路径必须指向真实文件。
- 外层拖放监听需要时应保留 `handledEventsToo: true`。
- Popup 或复用 View 离树时必须释放视觉父级和交互回调。
- WebView、轮询、动画等昂贵资源应随视觉生命周期启动和停止。

## 安装

将整个 `csharp-avalonia-codespec` 文件夹复制到个人 Codex 技能目录：

```text
~/.codex/skills/csharp-avalonia-codespec/
```

Windows 通常对应：

```text
%USERPROFILE%\.codex\skills\csharp-avalonia-codespec\
```

安装后重新打开聊天。如果技能未自动触发，可以显式调用：

```text
$csharp-avalonia-codespec
```

## 使用示例

```text
使用 $csharp-avalonia-codespec 整理 Views/SettingsView.axaml 及其 code-behind，保持界面行为不变。
```

```text
使用 $csharp-avalonia-codespec 检查这个 ViewModel 的异步取消、事件释放和 UI 线程更新。
```

```text
使用 $csharp-avalonia-codespec 整理本次 Git diff，只修改本次已经变更的 C# 和 AXAML 文件。
```

## 文件结构

```text
csharp-avalonia-codespec/
├── SKILL.md
├── README.md
└── agents/
    └── openai.yaml
```

- `SKILL.md`：技能的实际执行规则。
- `agents/openai.yaml`：Codex 中显示名称、简介和默认提示词。
- `README.md`：GitHub 项目介绍、安装和使用说明。

## 验证策略

技能要求先检查最终 diff，再构建能够覆盖修改的最小项目。对于资源解析、Popup、拖放、窗口行为、运行时绑定或布局修改，还应在条件允许时执行桌面端冒烟测试。

在 NPP 仓库中，默认构建命令是：

```powershell
dotnet build Workbench/Npp.Desktop/Npp.Desktop.csproj -c Debug -m:1
```
