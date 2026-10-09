---
name: csharp-avalonia-codespec
description: Organize, normalize, or review C# and Avalonia AXAML/XAML code while preserving behavior and repository conventions. Use for code cleanup, style consistency, readability improvements, binding/resource safety, or focused refactoring in Avalonia projects. Do not use for feature work that merely happens to touch C# or AXAML.
---

# C# & Avalonia CodeSpec

Produce a small, reviewable cleanup that follows the target repository rather than imposing a universal house style.

## Establish the local standard

- Read applicable `AGENTS.md` files, `.editorconfig`, project files, and nearby examples before editing.
- Inspect `git status --short` and the target diff. Preserve user changes and avoid unrelated files.
- Treat repository instructions and generated formatting rules as authoritative. When they are silent, match neighboring code.
- Default to behavior-preserving cleanup. Do not rename public APIs, binding paths, resource keys, `x:Name` values, automation identifiers, or serialization members unless the user explicitly requests it.
- Avoid generated files, vendored code, migrations, and submodules unless they are explicitly in scope.

## Organize C# safely

- Remove only demonstrably unused imports and code. Account for reflection, dependency injection, serialization, XAML-generated references, and platform entry points.
- Prefer clear names, early returns, and simple control flow. Do not trade readability for compressed LINQ or clever expressions.
- Preserve nullable-reference semantics; do not silence uncertainty with `!`, empty catches, or arbitrary defaults.
- Keep asynchronous code cancellation-aware. Do not introduce `.Result`, `.Wait()`, or unowned fire-and-forget work.
- Keep event subscriptions, streams, clients, leases, timers, and cancellation sources paired with cleanup on success, failure, and cancellation paths.
- Keep business state and workflows in view models or services. Code-behind may retain platform, focus, pointer, drag/drop, window, and visual-lifecycle bridging.
- Preserve existing UI-thread dispatch patterns when updating observable state.

## Organize AXAML safely

- Match local indentation and attribute wrapping. Avoid whole-file reformatting when a focused edit is sufficient.
- Group root metadata coherently: `x:Class` and namespace declarations, design-time declarations, then ordinary properties. Follow nearby ordering for other attributes.
- Use one attribute per line for elements with complex bindings or many properties; keep genuinely short elements compact when that matches the file.
- Reuse existing theme resources, style classes, templates, spacing, and colors instead of creating a parallel visual system.
- Preserve binding mode, fallback values, converters, relative sources, and compiled-binding contracts. Do not convert bindings without confirming the data type and runtime behavior.
- Before removing a namespace, resource, style, or named element, check dynamic resources, selectors, templates, code-behind, tests, and cross-file references.
- Preserve layout constraints, scrolling, virtualization, proportional image sizing, and responsive behavior.

## Apply Avalonia lifecycle guards

Use these rules when the affected code has the corresponding pattern:

- Controls that can leave the logical tree, including list items and popup content, must not use `StaticResource` for values available only from a parent window theme dictionary.
- Hidden controls may still evaluate bindings. Bind SVG controls only to a real SVG file path, never a directory-valued artifact path.
- Preserve `handledEventsToo: true` on outer drag/drop routing where inner controls can mark events handled.
- Do not reintroduce transparent borderless window themes, custom native window templates, or platform template parts when `FramelessResizableWindow` is only a compatibility base.
- Reusable popup content must release visual-parent ownership and interaction callbacks when detached. Late detach from an old view must not clear a newer registration.
- Create expensive rich-content controls only while visible when the local implementation relies on that lifecycle; stop polling, animation, and subscriptions when detached.

These are repository-sensitive guards, not permission to redesign unrelated UI.

## Make a focused change

1. Identify concrete cleanup issues and separate formatting-only changes from semantic refactoring.
2. Prefer a narrow patch that preserves encoding, line endings, and untouched regions.
3. Do not globally reorder members, resources, attributes, or imports unless the repository has a deterministic rule that requires it.
4. If cleanup exposes a likely functional defect outside the requested scope, report it instead of silently expanding the task.
5. Review the final diff for behavioral changes and accidental formatting churn.

## Verify proportionally

- Run `git diff --check` and inspect the target diff.
- Build the smallest project that compiles the changed C# or AXAML. For this NPP repository, use:

```powershell
dotnet build Workbench/Npp.Desktop/Npp.Desktop.csproj -c Debug -m:1
```

- Run relevant tests when requested or required by repository instructions. If a license or environment gate prevents tests from starting, report them as not run rather than passed.
- For layout, resource lookup, popup, drag/drop, window-lifecycle, or runtime-binding changes, perform an application smoke test when available. Distinguish compilation from runtime verification.

Report changed files, the important conventions applied, verification results, and any deliberately deferred issue or remaining risk.
