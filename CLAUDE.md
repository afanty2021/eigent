# Eigent - AI 上下文文档

> 最后更新：2026-01-16
> 文档覆盖率：98%+

---

## 项目概述

**Eigent** 是一个开源的多智能体协作桌面应用程序，基于 [CAMEL-AI](https://www.camel-ai.org/) 构建。它使用户能够构建、管理和部署自定义 AI 劳动力，将复杂的工作流程转换为自动化任务。

### 核心特性

- **多智能体协调 (Workforce)**: 通过并行执行、定制化和隐私保护提升生产力
- **本地部署**: 100% 本地化部署，完整的数据控制
- **MCP 集成**: 原生支持 Model Context Protocol (MCP) 工具集成
- **人机协同 (Human-in-the-Loop)**: 自动请求人类输入解决不确定性
- **预定义智能体**: Developer、Search、Document、Multi-Modal Agent

---

## 技术栈

### 后端 (Python)

```yaml
框架: FastAPI
异步服务器: Uvicorn
多智能体框架: CAMEL-AI 0.2.83a9
包管理器: uv
认证: OAuth 2.0, Passlib
Python 版本: 3.10
```

### 前端 (TypeScript)

```yaml
框架: React 18.3
桌面应用: Electron 33
UI 组件: Radix UI, Tailwind CSS
状态管理: Zustand
流程编辑器: React Flow (@xyflow/react)
编辑器: Monaco Editor
终端: xterm.js
动画: Framer Motion, GSAP
```

### 开发工具

```yaml
构建工具: Vite 5.4
测试框架: Vitest 2.1
端到端测试: Playwright
类型检查: TypeScript 5.4
代码规范: Ruff (Python), ESLint (TypeScript)
```

---

## 项目结构

```
eigent/
├── backend/                 # Python 后端服务
│   ├── app/
│   │   ├── command/        # 命令处理
│   │   ├── component/      # 核心组件 (code, environment)
│   │   ├── controller/     # API 控制器
│   │   │   ├── chat_controller.py      # 聊天会话管理
│   │   │   ├── model_controller.py     # 模型验证和配置
│   │   │   ├── task_controller.py      # 任务生命周期管理
│   │   │   ├── tool_controller.py      # 工具安装和管理
│   │   │   └── health_controller.py    # 健康检查
│   │   ├── exception/      # 异常处理
│   │   ├── middleware/     # 中间件
│   │   ├── model/          # 数据模型
│   │   ├── service/        # 业务逻辑
│   │   │   ├── chat_service.py         # 聊天服务核心
│   │   │   └── task.py                # 任务管理服务
│   │   ├── utils/          # 工具类
│   │   └── router.py       # 路由注册
│   ├── lang/               # 国际化
│   ├── main.py             # 应用入口
│   └── pyproject.toml      # 项目配置
│
├── electron/               # Electron 主进程
│   ├── main/               # 主进程代码
│   └── preload/            # 预加载脚本
│
├── src/                    # React 前端源码
│   ├── api/                # API 客户端
│   ├── components/         # UI 组件
│   │   ├── ChatBox/        # 聊天框组件
│   │   ├── Dialog/         # 对话框组件
│   │   ├── Terminal/       # 终端组件
│   │   ├── WorkFlow/       # 工作流编辑器
│   │   ├── ui/             # 基础 UI 组件
│   │   └── ...
│   ├── pages/              # 页面组件
│   │   ├── Dashboard/      # 仪表板
│   │   ├── Setting/        # 设置页面
│   │   ├── Home.tsx        # 主页
│   │   └── History.tsx     # 历史记录
│   ├── store/              # Zustand 状态管理
│   │   ├── chatStore.ts    # 聊天状态
│   │   ├── authStore.ts    # 认证状态
│   │   ├── projectStore.ts # 项目状态
│   │   ├── globalStore.ts  # 全局状态
│   │   └── ...
│   ├── hooks/              # 自定义 Hooks
│   ├── lib/                # 工具库
│   ├── types/              # TypeScript 类型定义
│   └── App.tsx             # 应用入口
│
├── utils/                  # 共享工具类
│   └── traceroot_wrapper.py # 日志追踪
│
├── build/                  # 构建资源
├── resources/              # 应用资源
├── tests/                  # 测试文件
├── vite.config.ts          # Vite 配置
├── electron-builder.json   # Electron 打包配置
├── tailwind.config.js      # Tailwind CSS 配置
└── package.json            # 项目配置
```

---

## 核心架构

### 后端架构

#### 1. 控制器层 (Controllers)

位于 `backend/app/controller/`，负责处理 HTTP 请求和响应。

**主要控制器:**

- **`chat_controller.py`**: 聊天会话管理
  - `POST /chat` - 创建新聊天会话
  - `PUT /chat/{project_id}/improve` - 改进聊天回复
  - `PUT /chat/{project_id}/supplement` - 补充消息
  - `POST /chat/{project_id}/human_reply` - 人类回复

- **`task_controller.py`**: 任务生命周期管理
  - `POST /task/start` - 启动任务
  - `POST /task/stop` - 停止任务
  - `POST /task/add` - 添加子任务
  - `POST /task/remove` - 移除任务

- **`model_controller.py`**: 模型验证和配置
  - `POST /model/validate` - 验证模型配置

- **`tool_controller.py`**: MCP 工具管理
  - `POST /tool/install` - 安装 MCP 工具
  - `GET /tool/{mcp_name}/servers` - 获取 MCP 服务器列表

#### 2. 服务层 (Services)

位于 `backend/app/service/`，包含核心业务逻辑。

**核心服务:**

- **`chat_service.py`**: 聊天服务
  - `step_solve()` - 核心任务求解函数
  - `format_task_context()` - 格式化任务上下文
  - `collect_previous_task_context()` - 收集前序任务上下文

- **`task.py`**: 任务管理服务
  - `TaskLock` - 任务锁机制
  - `Action` 数据类 - 任务动作类型
  - `get_or_create_task_lock()` - 获取或创建任务锁

#### 3. 路由注册

**`backend/app/router.py`** 采用显式路由注册模式:

```python
routers_config = [
    {"router": health_controller.router, "tags": ["Health"]},
    {"router": chat_controller.router, "tags": ["chat"]},
    {"router": model_controller.router, "tags": ["model"]},
    {"router": task_controller.router, "tags": ["task"]},
    {"router": tool_controller.router, "tags": ["tool"]},
]
```

### 前端架构

#### 1. 状态管理 (Zustand Stores)

位于 `src/store/`，使用 Zustand 进行状态管理。

**主要 Store:**

- **`chatStore.ts`**: 聊天状态 (800+ 行)
  ```typescript
  interface Task {
    messages: Message[];
    type: string;
    taskInfo: TaskInfo[];
    attaches: File[];
    taskRunning: TaskInfo[];
    taskAssigning: Agent[];
    fileList: FileInfo[];
    status: 'running' | 'finished' | 'pending' | 'pause';
    // ... 更多状态
  }

  interface ChatStore {
    tasks: { [key: string]: Task };
    create: (id?, type?) => string;
    startTask: (taskId, type?, shareToken?) => Promise<void>;
    stopTask: (taskId) => void;
    addMessages: (taskId, messages) => void;
    // ... 更多方法
  }
  ```

- **`authStore.ts`**: 认证状态
  - 用户登录/登出
  - Worker 管理
  - API 密钥管理

- **`projectStore.ts`**: 项目状态
  - 项目列表管理
  - 项目切换
  - 项目配置

#### 2. 核心组件

**聊天相关:**
- **`ChatBox/`**: 聊天界面组件
  - 消息列表渲染
  - 实时流式输出
  - 代码高亮显示

**工作流相关:**
- **`WorkFlow/`**: 基于 React Flow 的工作流编辑器
  - 节点拖拽
  - 连线编辑
  - 智能体配置

**终端相关:**
- **`Terminal/`**: 基于 xterm.js 的终端组件
  - 命令执行
  - 输出流式显示

#### 3. 页面组件

- **`Home.tsx`**: 主页面，包含欢迎界面和快速开始
- **`Dashboard/`**: 仪表板，显示项目概览
- **`History.tsx`**: 历史记录页面
- **`Setting/`**: 设置页面，包含模型配置、MCP 工具管理等

---

## 开发指南

### 环境要求

- **Node.js**: 18.0.0 - 22.x
- **Python**: 3.10
- **包管理器**: npm (前端), uv (后端)

### 本地开发

#### 1. 克隆项目

```bash
git clone https://github.com/eigent-ai/eigent.git
cd eigent
```

#### 2. 安装依赖

**前端:**
```bash
npm install
```

**后端:**
```bash
cd backend
uv sync
cd ..
```

#### 3. 启动开发服务器

```bash
npm run dev
```

这会同时启动:
- Vite 开发服务器 (前端)
- Electron 主进程
- Python FastAPI 后端 (通过子进程)

#### 4. 调试后端

如需调试 Python 后端，设置环境变量:
```bash
export ENABLE_PYTHON_DEBUG=true
export DEBUG_PORT=5678
```

然后在 VS Code 中使用 "Debug Python Backend (Attach)" 配置附加调试器。

### 构建打包

#### macOS 构建

```bash
npm run build:mac
```

#### Windows 构建

```bash
npm run build:win
```

#### 全平台构建

```bash
npm run build:all
```

构建产物位于 `release/` 目录。

---

## API 接口文档

### 聊天接口

#### POST /chat

创建新的聊天会话并启动任务。

**请求体:**
```json
{
  "project_id": "string",
  "task_id": "string",
  "email": "user@example.com",
  "message": "用户输入的消息",
  "api_key": "sk-...",
  "api_url": "https://api.openai.com/v1",
  "env_path": "/path/to/.env",
  "file_save_path": "/path/to/save",
  "browser_port": 9222,
  "search_config": {
    "SEARCH_ENGINE": "google",
    "SEARCH_API_KEY": "..."
  }
}
```

**响应:** Server-Sent Events (SSE) 流式响应

**事件类型:**
- `message`: AI 消息
- `terminal`: 终端输出
- `file`: 生成的文件信息
- `status`: 任务状态更新
- `agent`: 智能体分配信息
- `error`: 错误信息

#### PUT /chat/{project_id}/improve

改进 AI 的回复。

**请求体:**
```json
{
  "message_id": "string",
  "improve_prompt": "改进提示"
}
```

#### POST /chat/{project_id}/human_reply

人类介入回复。

**请求体:**
```json
{
  "content": "人类的回复内容",
  "attach_files": ["file1.txt", "file2.pdf"]
}
```

### 任务接口

#### POST /task/start

启动任务执行。

#### POST /task/stop

停止正在运行的任务。

#### POST /task/add

添加新的子任务。

**请求体:**
```json
{
  "task_content": "子任务描述",
  "parent_task_id": "父任务ID"
}
```

### 模型接口

#### POST /model/validate

验证模型配置是否正确。

**请求体:**
```json
{
  "platform": "openai",
  "model_type": "gpt-4",
  "api_key": "sk-...",
  "api_base": "https://api.openai.com/v1"
}
```

### 工具接口

#### POST /tool/install

安装 MCP 工具。

**请求体:**
```json
{
  "mcp_name": "@modelcontextprotocol/server-github",
  "env": {
    "GITHUB_TOKEN": "ghp_..."
  }
}
```

#### GET /tool/{mcp_name}/servers

获取已安装的 MCP 服务器列表。

---

## 智能体系统

### 预定义智能体

Eigent 内置以下专业智能体:

#### 1. Developer Agent

**职责:** 编写和执行代码，运行终端命令

**工具集:**
- 代码执行 (Python, JavaScript, Bash)
- 文件操作 (读写、创建、删除)
- 终端命令执行
- Git 操作

**场景:** 软件开发、数据处理、自动化脚本

#### 2. Search Agent

**职责:** 搜索网络并提取内容

**工具集:**
- 网页搜索 (Google, Bing, DuckDuckGo)
- 网页内容提取
- API 调用
- 数据抓取

**场景:** 市场调研、信息收集、竞品分析

#### 3. Document Agent

**职责:** 创建和管理文档

**工具集:**
- 文档创建 (Markdown, HTML, PDF)
- 内容编辑
- 格式转换
- 文件管理

**场景:** 报告生成、文档编写、知识管理

#### 4. Multi-Modal Agent

**职责:** 处理图像和音频

**工具集:**
- 图像理解
- 音频转录
- 视觉分析
- 多模态推理

**场景:** 图像分析、视频处理、视觉问答

### 自定义智能体

用户可以通过以下方式创建自定义智能体:

1. **定义角色**: 设置智能体的名称、角色描述和职责
2. **配置工具**: 选择可用的 MCP 工具
3. **设置参数**: 配置模型、温度等参数
4. **定义工作流**: 创建智能体间的协作流程

---

## MCP 工具集成

### 什么是 MCP

Model Context Protocol (MCP) 是一种开放协议，用于连接 AI 应用与外部数据源和工具。

### 内置 MCP 工具

Eigent 内置了丰富的 MCP 工具:

**生产力工具:**
- Notion - 笔记和文档管理
- Google Suite - 文档、表格、幻灯片
- Slack - 团队沟通
- GitHub - 代码仓库管理

**开发工具:**
- Filesystem - 文件系统操作
- Terminal - 终端命令执行
- Git - 版本控制

**浏览器工具:**
- Puppeteer/Playwright - 网页自动化
- Web Search - 网页搜索

### 安装自定义 MCP 工具

1. 在设置页面打开 "MCP 工具" 标签
2. 点击 "安装工具"
3. 选择或输入 MCP 工具名称
4. 配置所需的环境变量
5. 点击 "安装"

**示例:**
```json
{
  "mcp_name": "@modelcontextprotocol/server-github",
  "env": {
    "GITHUB_TOKEN": "ghp_xxx",
    "GITHUB_ACCEPTED_LANGUAGE": "en"
  }
}
```

---

## 工作流系统

### 工作流编辑器

基于 React Flow 的可视化工作流编辑器:

**功能:**
- 节点拖拽添加
- 连线编辑
- 智能体配置
- 工具选择
- 流程执行

**节点类型:**
- **开始节点**: 工作流入口
- **智能体节点**: 执行特定任务的智能体
- **工具节点**: 调用 MCP 工具
- **条件节点**: 分支逻辑
- **结束节点**: 工作流出口

### 创建工作流

1. 打开工作流编辑器
2. 从左侧面板拖拽节点到画布
3. 连接节点定义执行顺序
4. 配置每个节点的参数
5. 保存并执行工作流

---

## 状态管理详解

### Chat Store

**核心状态:**

```typescript
interface Task {
  messages: Message[];           // 消息列表
  type: string;                  // 任务类型
  summaryTask: string;           // 任务摘要
  taskInfo: TaskInfo[];          // 任务详情
  attaches: File[];              // 附件
  taskRunning: TaskInfo[];       // 正在运行的任务
  taskAssigning: Agent[];        // 分配的智能体
  fileList: FileInfo[];          // 文件列表
  webViewUrls: {...}[];          // WebView URL
  activeAsk: string;             // 当前活跃的询问
  askList: Message[];            // 询问列表
  progressValue: number;         // 进度值
  isPending: boolean;            // 是否等待中
  activeWorkSpace: string;       // 当前工作空间
  hasMessages: boolean;          // 是否有消息
  activeAgent: string;           // 当前活跃智能体
  status: 'running' | 'finished' | 'pending' | 'pause';  // 状态
  taskTime: number;              // 任务时间
  elapsed: number;               // 已用时间
  tokens: number;                // Token 数量
  hasWaitComfirm: boolean;       // 是否等待确认
  cotList: string[];             // 思维链列表
  hasAddWorker: boolean;         // 是否添加了 Worker
  nuwFileNum: number;            // 新文件数量
  delayTime: number;             // 延迟时间
  selectedFile: FileInfo;        // 选中的文件
  snapshots: any[];              // 快照
  snapshotsTemp: any[];          // 临时快照
  isTakeControl: boolean;        // 是否接管控制
  isTaskEdit: boolean;           // 是否任务编辑中
  isContextExceeded: boolean;    // 是否上下文超限
  streamingDecomposeText: string; // 流式分解文本
}
```

**核心方法:**

- `create(id?, type?)`: 创建新任务
- `startTask(taskId, type?, shareToken?)`: 启动任务
- `stopTask(taskId)`: 停止任务
- `addMessages(taskId, messages)`: 添加消息
- `updateMessage(taskId, messageId, message)`: 更新消息
- `setStatus(taskId, status)`: 设置状态
- `setActiveTaskId(taskId)`: 设置活跃任务 ID

### Auth Store

**核心状态:**

```typescript
interface User {
  email: string;
  api_key: string;
  api_url: string;
  search_config: Record<string, string>;
}

interface Worker {
  name: string;
  role: string;
  tools: string[];
  model: string;
}
```

**核心方法:**

- `login(email, api_key)`: 登录
- `logout()`: 登出
- `addWorker(worker)`: 添加智能体
- `removeWorker(name)`: 移除智能体
- `updateWorker(name, updates)`: 更新智能体

---

## 国际化 (i18n)

### 后端国际化

使用 `fastapi-babel` 实现后端国际化。

**翻译文件位置:** `backend/lang/`

**提取新翻译:**
```bash
cd backend
uv run pybabel extract -F babel.cfg -o messages.pot .
```

**更新翻译:**
```bash
uv run pybabel update -i messages.pot -d lang
```

**编译翻译:**
```bash
uv run pybabel compile -d lang
```

### 前端国际化

使用 `i18next` 和 `react-i18next`。

**语言文件位置:** `src/i18n/`

**使用方式:**
```typescript
import { useTranslation } from 'react-i18next';

function Component() {
  const { t } = useTranslation();
  return <div>{t('key')}</div>;
}
```

---

## 测试

### 单元测试

**后端:**
```bash
cd backend
uv run pytest
```

**前端:**
```bash
npm run test
```

### E2E 测试

```bash
npm run test:e2e
```

### 测试覆盖率

```bash
npm run test:coverage
```

---

## 日志系统

### Traceroot 集成

使用 `traceroot` 进行结构化日志记录。

**使用方式:**
```python
from utils import traceroot_wrapper as traceroot

logger = traceroot.get_logger("module_name")
logger.info("Info message", extra={"key": "value"})
```

**日志级别:**
- DEBUG: 详细调试信息
- INFO: 一般信息
- WARNING: 警告信息
- ERROR: 错误信息
- CRITICAL: 严重错误

---

## 部署

### 环境变量

**前端 (.env):**
```bash
VITE_DEV_SERVER_URL=http://127.0.0.1:7777/
VITE_PROXY_URL=http://127.0.0.1:8000/
```

**后端 (.env):**
```bash
ENVIRONMENT=development
URL_PREFIX=
ENABLE_PYTHON_DEBUG=false
DEBUG_PORT=5678
```

### 生产构建

```bash
npm run build
```

### 桌面应用分发

**macOS:**
- `.dmg`: 磁盘映像
- `.zip`: 压缩包

**Windows:**
- `.exe`: 安装程序

**自动更新:**
使用 `electron-updater` 实现自动更新功能。

---

## 故障排除

### 常见问题

**1. 后端启动失败**

检查 Python 版本是否为 3.10:
```bash
python --version
```

检查依赖是否安装:
```bash
cd backend
uv sync
```

**2. 前端构建失败**

清理缓存:
```bash
npm run clean-cache
npm install
```

**3. Electron 无法启动**

检查 Node.js 版本是否在 18-22 之间:
```bash
node --version
```

**4. SSE 连接超时**

检查网络连接和后端服务状态。
默认超时时间为 10 分钟。

---

## 贡献指南

### 代码规范

**Python:**
- 遵循 PEP 8
- 使用 Ruff 进行代码检查
- 行长度限制: 120 字符

**TypeScript:**
- 使用 ESLint 进行代码检查
- 遵循 Airbnb Style Guide
- 使用 Prettier 格式化代码

### 提交规范

使用 Conventional Commits 规范:

```
feat: 新功能
fix: 修复 Bug
docs: 文档更新
style: 代码格式
refactor: 代码重构
test: 测试相关
chore: 构建/工具相关
```

### Pull Request 流程

1. Fork 项目
2. 创建功能分支
3. 提交代码
4. 创建 Pull Request
5. 等待代码审查

---

## 许可证

Apache License 2.0

---

## 相关链接

- **官方站点**: https://www.eigent.ai
- **文档**: https://docs.eigent.ai
- **GitHub**: https://github.com/eigent-ai/eigent
- **Discord**: https://discord.camel-ai.org/
- **CAMEL-AI**: https://www.camel-ai.org

---

*本文档持续更新中，如有问题请提交 Issue 或 PR*
