# RN 空业务客户端初始化规则

本文档从当前客户端抽取可复用的工程骨架，用于初始化一个新的 React Native 项目。目标是保留架构、分层、导航、主题、基础设施和代码规范，不携带任何业务功能、业务命名、业务接口、业务图片或第三方业务 SDK。

## 当前客户端框架抽象

当前项目的客户端框架可以概括为：

- `App.tsx` 是应用组合根：挂载全局 Provider、状态栏、导航容器、全局反馈组件，并执行应用级初始化。
- `src/navigation/` 统一管理 React Navigation：包含 `NavigationContainer`、根 Stack、全局 `navigationRef` 和路由类型。
- `src/modules/` 按功能域组织页面，每个模块内部可拥有 `screens/`、`components/`、`viewmodels/`、`services/`、`models/`、`assets/`。
- `src/services/` 放跨模块基础服务，例如 API Client、Storage、环境配置、日志、监控。业务服务应在对应模块内优先落地，只有真正跨模块复用时才上移。
- `src/models/` 放跨模块共享类型。模块私有类型放模块内部。
- `src/viewmodels/` 是历史全局 ViewModel 区域；新项目建议优先使用模块内 `viewmodels/`，只把跨模块状态放全局。
- `src/components/` 是无业务通用 UI 组件；业务组件必须放模块内。
- `src/theme/` 统一管理颜色、字体、间距、阴影、圆角等设计 token。
- `src/utils/` 放无状态、无业务或弱业务的工具函数；业务工具放模块内部。
- `src/contexts/` 放全局上下文，例如应用设置、会话态、网络态。禁止把页面局部状态放进全局 Context。

新项目初始化时应复用以上骨架，但不要复制当前项目中的任何业务模块、业务服务、业务模型、业务资源和业务文案。

## 初始化原则

1. 空项目只提供工程能力，不提供业务能力。
2. 默认使用 TypeScript 严格模式。
3. 默认采用模块化 MVVM，但初始化时只保留空模块示例或 `app` 壳模块。
4. View 不直接调用 Service；依赖方向为 `View -> ViewModel -> Service -> Model`。
5. 全局目录只放跨模块能力；模块私有内容必须留在模块内部。
6. 导航只注册基础壳页面，例如 `Root`、`NotFound`、`DevEntry`。不要注册登录、首页、订单、视频、支付等业务路由。
7. API Client 可以初始化为空基础设施，但不得写任何真实业务 endpoint。
8. 主题可以有中性色、品牌占位色和基础尺寸，但不得包含旧项目品牌色、业务状态色或业务命名。
9. assets 只允许保留占位资源规范，不放旧项目图片、图标、启动页、业务素材。
10. 任何第三方 SDK 必须按基础设施价值引入；Firebase、CodePush、登录、支付、分享、视频等都不是空项目默认依赖。

## 推荐目录结构

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
│   └── App.test.tsx
├── docs/
│   ├── ARCHITECTURE.md
│   ├── CODE_RULES.md
│   └── MODULE_RULES.md
└── src/
    ├── app/
    │   ├── AppProviders.tsx
    │   └── AppBootstrap.ts
    ├── assets/
    │   └── index.ts
    ├── components/
    │   ├── Button.tsx
    │   ├── TextField.tsx
    │   ├── EmptyState.tsx
    │   └── index.ts
    ├── contexts/
    │   └── AppContext.tsx
    ├── hooks/
    │   └── index.ts
    ├── models/
    │   ├── Api.ts
    │   └── index.ts
    ├── modules/
    │   ├── app-shell/
    │   │   ├── screens/
    │   │   │   ├── RootScreen.tsx
    │   │   │   └── NotFoundScreen.tsx
    │   │   └── index.ts
    │   └── index.ts
    ├── navigation/
    │   ├── AppNavigator.tsx
    │   ├── Navigation.tsx
    │   ├── NavigationService.ts
    │   ├── routes.ts
    │   └── types.ts
    ├── services/
    │   ├── ApiClient.ts
    │   ├── StorageService.ts
    │   ├── config.ts
    │   └── index.ts
    ├── theme/
    │   ├── colors.ts
    │   ├── spacing.ts
    │   ├── typography.ts
    │   └── index.ts
    └── utils/
        └── index.ts
```

## 最小依赖建议

`package.json` 初始依赖只放通用客户端能力：

```json
{
  "dependencies": {
    "@react-native-async-storage/async-storage": "latest",
    "@react-navigation/native": "latest",
    "@react-navigation/native-stack": "latest",
    "react": "matching-react-native-version",
    "react-native": "selected-version",
    "react-native-gesture-handler": "latest",
    "react-native-safe-area-context": "latest",
    "react-native-screens": "latest"
  },
  "devDependencies": {
    "@react-native/babel-preset": "matching-react-native-version",
    "@react-native/eslint-config": "matching-react-native-version",
    "@react-native/metro-config": "matching-react-native-version",
    "@react-native/typescript-config": "matching-react-native-version",
    "@types/jest": "latest",
    "@types/react": "latest",
    "eslint": "latest",
    "jest": "latest",
    "prettier": "latest",
    "typescript": "latest"
  }
}
```

不要默认加入：

- 登录相关 SDK
- Firebase / Crashlytics / Analytics
- CodePush
- 支付 SDK
- WebView
- 视频播放、视频编辑、图片选择器
- 图表库
- 业务图标库
- 任何旧项目私有包

这些依赖必须等真实业务需求出现后再引入。

## 根入口规则

`App.tsx` 只负责组合，不写业务判断。

```tsx
import React from 'react';
import { StatusBar, useColorScheme } from 'react-native';
import { SafeAreaProvider } from 'react-native-safe-area-context';

import { AppProviders } from './src/app/AppProviders';
import { Navigation } from './src/navigation/Navigation';

function App() {
  const isDarkMode = useColorScheme() === 'dark';

  return (
    <SafeAreaProvider>
      <AppProviders>
        <StatusBar barStyle={isDarkMode ? 'light-content' : 'dark-content'} />
        <Navigation />
      </AppProviders>
    </SafeAreaProvider>
  );
}

export default App;
```

`src/app/AppProviders.tsx` 只挂载基础 Provider：

```tsx
import React, { type ReactNode } from 'react';

interface AppProvidersProps {
  children: ReactNode;
}

export function AppProviders({ children }: AppProvidersProps) {
  return <>{children}</>;
}
```

如果未来接入全局弹窗、Toast、Query Client、状态管理库，应统一从这里进入。

## 导航初始化规则

初始化时只保留根壳页面和兜底页面。

```tsx
// src/navigation/routes.ts
export const ROUTES = {
  ROOT: 'Root',
  NOT_FOUND: 'NotFound',
} as const;
```

```ts
// src/navigation/types.ts
import { ROUTES } from './routes';

export type RootStackParamList = {
  [ROUTES.ROOT]: undefined;
  [ROUTES.NOT_FOUND]: undefined;
};
```

```tsx
// src/navigation/Navigation.tsx
import 'react-native-gesture-handler';
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';

import { AppNavigator } from './AppNavigator';
import { navigationRef } from './NavigationService';

export function Navigation() {
  return (
    <NavigationContainer ref={navigationRef}>
      <AppNavigator />
    </NavigationContainer>
  );
}
```

```tsx
// src/navigation/AppNavigator.tsx
import React from 'react';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

import { RootScreen, NotFoundScreen } from '../modules/app-shell';
import { ROUTES } from './routes';
import type { RootStackParamList } from './types';

const Stack = createNativeStackNavigator<RootStackParamList>();

export function AppNavigator() {
  return (
    <Stack.Navigator screenOptions={{ headerShown: false }}>
      <Stack.Screen name={ROUTES.ROOT} component={RootScreen} />
      <Stack.Screen name={ROUTES.NOT_FOUND} component={NotFoundScreen} />
    </Stack.Navigator>
  );
}
```

## 模块规则

模块是业务或功能的最小组织单位。初始化项目只放 `app-shell`，用于承载空壳页面。

每个模块最多包含：

```text
module-name/
├── screens/
├── components/
├── viewmodels/
├── services/
├── models/
├── hooks/
├── utils/
├── assets/
└── index.ts
```

模块边界规则：

- 模块对外只通过 `index.ts` 暴露必要内容。
- 跨模块不要互相读取内部目录。
- 模块内部可以调用全局基础服务。
- 全局基础层不能反向依赖具体模块。
- 新业务出现时，新建模块，不要把页面堆进 `src/screens/`。

## MVVM 分层规则

```text
Screen / Component
  -> ViewModel / Hook
    -> Service
      -> Model / DTO
```

职责边界：

- `screens/`：页面布局、用户交互、导航调用。
- `components/`：展示组件，只接收 props，不知道业务流程。
- `viewmodels/`：页面状态、交互流程、输入校验、数据转换。
- `services/`：网络、缓存、系统能力封装。
- `models/`：类型定义，不写逻辑。

禁止：

- Screen 直接请求接口。
- Service 直接操作导航。
- Model 引入 React。
- 通用组件读取业务 Context。
- ViewModel 写样式或返回 JSX。

## 基础服务规则

初始化可保留空 API Client：

```ts
// src/services/config.ts
export interface ApiResponse<T> {
  data: T;
  message?: string;
}

export class ApiError extends Error {
  constructor(message: string, public statusCode?: number, public data?: unknown) {
    super(message);
    this.name = 'ApiError';
  }
}
```

```ts
// src/services/ApiClient.ts
import { ApiError, type ApiResponse } from './config';

class ApiClient {
  async get<T>(url: string, init?: RequestInit): Promise<ApiResponse<T>> {
    return this.request<T>(url, { ...init, method: 'GET' });
  }

  async post<T>(url: string, body?: unknown, init?: RequestInit): Promise<ApiResponse<T>> {
    return this.request<T>(url, {
      ...init,
      method: 'POST',
      body: body == null ? undefined : JSON.stringify(body),
    });
  }

  private async request<T>(url: string, init: RequestInit): Promise<ApiResponse<T>> {
    const response = await fetch(url, {
      ...init,
      headers: {
        'Content-Type': 'application/json',
        ...init.headers,
      },
    });

    const data = await response.json().catch(() => undefined);

    if (!response.ok) {
      throw new ApiError('Request failed', response.status, data);
    }

    return data as ApiResponse<T>;
  }
}

export const apiClient = new ApiClient();
```

注意：空项目不要配置真实 baseURL、token 刷新、401 跳转、业务错误码、埋点字段。等业务协议确定后再加。

## 主题规则

主题只放基础设计 token：

```ts
// src/theme/colors.ts
export const colors = {
  background: '#FFFFFF',
  surface: '#F7F8FA',
  textPrimary: '#111827',
  textSecondary: '#6B7280',
  border: '#E5E7EB',
  primary: '#2563EB',
  danger: '#DC2626',
  success: '#16A34A',
};
```

```ts
// src/theme/spacing.ts
export const spacing = {
  xs: 4,
  sm: 8,
  md: 12,
  lg: 16,
  xl: 24,
  xxl: 32,
};
```

不要出现旧项目品牌名、旧项目页面色、旧项目业务状态色。

## 代码规范

- 文件命名：组件和页面使用 `PascalCase.tsx`，Hooks 使用 `useXxx.ts`，工具函数使用 `camelCase.ts`。
- 组件导出：优先命名导出，避免默认导出散落。
- 类型：禁止 `any`，不确定时使用 `unknown`。
- 样式：同文件 `StyleSheet.create`，共享 token 从 `src/theme` 读取。
- 导入顺序：React、React Native、第三方、项目内绝对路径、相对路径、类型。
- 注释：只解释复杂决策，不解释显而易见的代码。
- Barrel 文件：每个模块和全局目录可以有 `index.ts`，但不要导出内部实现细节。

## 初始化检查清单

新项目初始化完成后，必须确认：

- `npm run lint` 可运行。
- `npm run type-check` 可运行。
- `npm test` 至少有一个 App 渲染测试。
- App 启动后只进入空壳页面。
- `src/modules/` 只有 `app-shell` 或等价空壳模块。
- 没有登录、支付、视频、商品、用户、订单等业务命名。
- 没有真实 API 地址、密钥、token、业务错误码。
- 没有复制旧项目图片、图标、文案和私有包。
- `App.tsx` 没有业务初始化。
- 导航没有业务路由。

## 禁止从当前项目复制的内容

不要复制以下类别：

- `src/modules/auth`
- `src/modules/video`
- `src/modules/mainpage`
- `src/modules/marketing`
- `src/modules/profile`
- `src/modules/dataanalysis`
- `src/modules/TrainingQuiz`
- 任何具体业务 Service、Model、ViewModel
- Firebase、CodePush、TikTok、Apple/Google 登录、支付、推送、Universal Link 的现有实现
- `src/assets` 和各模块 `assets` 下的业务资源
- 旧项目 README 中的业务说明

可以借鉴但需要重写为无业务版本：

- MVVM 分层规范
- 模块化目录规则
- 导航服务模式
- 主题 token 组织方式
- API Client 基础封装模式
- lint、format、type-check、validate 脚本

## 给 AI 或团队的初始化指令

可以直接把下面这段作为新项目初始化提示词：

```text
请初始化一个 React Native + TypeScript 项目骨架，只保留工程架构，不包含任何业务。

要求：
1. 使用模块化 MVVM 架构。
2. 创建 App.tsx、src/app、src/navigation、src/modules/app-shell、src/components、src/services、src/models、src/theme、src/utils、src/hooks、src/contexts。
3. 导航只注册 Root 和 NotFound 两个无业务页面。
4. services 只提供空 ApiClient、StorageService、config 类型，不写真实接口、登录、token 或业务错误码。
5. components 只提供无业务基础组件，如 Button、TextField、EmptyState。
6. theme 只提供中性设计 token。
7. 不要创建登录、首页、商品、订单、视频、支付、用户中心等任何业务模块。
8. 不要复制任何旧项目图片、文案、品牌色、SDK 初始化或业务依赖。
9. package scripts 至少包含 start、ios、android、test、lint、format、type-check、validate。
10. TypeScript 开启 strict，并配置 src 路径别名。
```
