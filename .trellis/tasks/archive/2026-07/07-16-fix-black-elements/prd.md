# Fix: 全黑元素问题 (Black Elements)

## Background

Brutal Gum 主题设置 `--background-modifier-border: var(--gumroad-black)` = `#000000`（纯黑）。
这导致两个问题：

1. **macOS 上级联到 `--interactive-normal`**：Obsidian app.css 有 `.mod-macos { --interactive-normal: var(--background-modifier-border); }`，主题未显式覆盖 `--interactive-normal`，导致交互元素背景纯黑。

2. **Syncthing Manager 插件 `.st-device-badge`** 用 `var(--background-modifier-border)` 作为背景色 + `var(--text-normal)` 作为文字色，黑底黑字形成不可见的纯黑方块。

## Scope

- 文件：`src/_base.scss` 和 `src/components/_obsidian.scss`
- 不改已编译的 `theme.css`
- 不改 Syncthing Manager 插件代码

## Changes

### 1. `src/_base.scss`

- 在 `body {}` 块中新增 `--interactive-normal` 和 `--interactive-hover`，防止 macOS 级联到纯黑
   - `--interactive-normal: var(--bg2);`
   - `--interactive-hover: var(--bg3);`

### 2. `src/components/_obsidian.scss`

- 在 `/* ==== 特定插件 ==== */` 部分新增 Syncthing Manager 兼容规则：
   - `.st-device-badge` 背景色改为 `var(--bg2)`，文字色改为 `var(--tx1)`
   - 用 `!important` 确保覆盖插件自己的样式

## Out of scope

- 不改 `--background-modifier-border` 的值（保持 Brutalist 黑色边框风格）
- 不改其他插件
- 不改已编译的 theme.css

## Acceptance Criteria

1. `npm run build` 编译无错误
2. 编译后的 `theme.css` 包含 `--interactive-normal` 和 `--interactive-hover` 定义
3. 编译后的 `theme.css` 包含 `.st-device-badge` 规则
4. 复制到测试 vault 的 themes 目录后可正常工作
