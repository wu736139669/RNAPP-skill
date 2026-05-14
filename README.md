# RN Empty Client — AI-Ready React Native Project Skeleton

一个**零业务、纯工程骨架**的 React Native 初始化规范文档。把这份文档喂给 AI（Claude、Cursor、Copilot、Codex 等），它能直接生成一个结构清晰、分层合理、开箱即用的 RN 新项目。

## 解决了什么问题？

每次起一个 React Native 新项目，你大概率会从旧项目复制代码，然后删业务逻辑。删不干净 → 带着旧项目的品牌色、API 地址、SDK 配置上线 → 后续越滚越脏。

这个仓库提供了一份**结构化规范文档**，AI 读完就能帮你生成：

- 模块化 MVVM 架构（View → ViewModel → Service → Model）
- React Navigation 导航骨架（只有 Root + NotFound 两个壳页面）
- 空 API Client、StorageService、主题 token 等基础设施
- TypeScript 严格模式 + ESLint + Prettier + Jest
- 基础通用组件（Button、TextField、EmptyState）
- 完整的目录结构和模块边界规则

**没有任何业务代码、业务命名、真实 API 地址、第三方业务 SDK。**

## 快速开始

把 [`RN_EMPTY_CLIENT_INIT_GUIDE.md`](./RN_EMPTY_CLIENT_INIT_GUIDE.md) 的内容作为提示词，发给 AI 编程助手即可。文档末尾有一段精简版初始化指令，可以直接复制使用。

示例（Claude Code / Cursor / Copilot Chat）：

```text
请初始化一个 React Native + TypeScript 项目骨架，只保留工程架构，不包含任何业务。

要求：
1. 使用模块化 MVVM 架构。
2. 创建 App.tsx、src/app、src/navigation、src/modules/app-shell、
   src/components、src/services、src/models、src/theme、src/utils、
   src/hooks、src/contexts。
3. 导航只注册 Root 和 NotFound 两个无业务页面。
4. services 只提供空 ApiClient、StorageService、config 类型。
5. components 只提供无业务基础组件。
6. theme 只提供中性设计 token。
7. 不要创建任何业务模块。
8. TypeScript 开启 strict，并配置 src 路径别名。
```

## 生成的目录结构

```text
.
├── App.tsx
├── index.js
├── app.json
├── babel.config.js
├── metro.config.js
├── tsconfig.json
├── package.json
├── __tests__/
├── docs/
│   ├── ARCHITECTURE.md
│   ├── CODE_RULES.md
│   └── MODULE_RULES.md
└── src/
    ├── app/           # AppProviders + AppBootstrap
    ├── assets/        # 占位资源规范
    ├── components/    # 无业务通用 UI 组件
    ├── contexts/      # 全局 Context
    ├── hooks/         # 公共 Hooks
    ├── models/        # 跨模块共享类型
    ├── modules/       # 功能模块（初始仅 app-shell）
    ├── navigation/    # React Navigation 路由
    ├── services/      # ApiClient / Storage / Config
    ├── theme/         # 颜色、间距、字体 token
    └── utils/         # 工具函数
```

## 架构原则

| 原则 | 说明 |
|------|------|
| 依赖方向 | View → ViewModel → Service → Model（单向） |
| 模块隔离 | 模块内部实现不暴露，通过 `index.ts` 对外 |
| 全局≠业务 | 全局目录只放跨模块能力，业务内容留在模块内 |
| 空项目零业务 | 没有登录、首页、订单、支付、视频等任何业务概念 |
| 依赖最小化 | 初始依赖只有 RN 核心 + Navigation，不预装 Firebase/支付/分析 |

## 适用场景

- 用 AI 工具起新 RN 项目，想要一个干净、规范的起点
- 团队统一项目骨架，避免每次从旧项目复制
- 教学 / 团队新人了解模块化 RN 架构
- 作为项目模板规范，配合脚本自动生成

## 配合 AI 工具使用

此文档针对 AI 编程助手优化：

- **Claude Code** (`/init` 或直接粘贴)
- **Cursor** (Composer / Chat)
- **GitHub Copilot** (Chat / Workspace)
- **OpenAI Codex CLI**
- **Windsurf / Cody / 其他 AI IDE**

## License

MIT
