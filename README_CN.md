# VibeGuard

**契约驱动的 AI 全栈开发工作流。**

[English](./README.md) | 中文

## 为什么做这个？

**Q: 在做 VibeGuard 之前，你是怎么用 AI 开发项目的？**

让 AI 生成代码，然后反复改。试过很多"神乎其神"的 skill——提示词模板、工作流插件、自我进化 agent，没一个靠谱。根本原因：AI 是概率模型。同样的 skill 这次触发对了，下次可能就失效了。你控制不了概率。

**Q: 那你改了什么？**

不再依赖 AI 的"悟性"，把意图编码成机械步骤。Skill 告诉 AI "你应该做这个"——AI 按概率理解和执行。VibeGuard 告诉 AI "执行这一步，输入是这个，输出写到那里，然后对照这个文件验证"——AI 按指令机械执行。Skill 假设 AI 有判断力，VibeGuard 假设它没有。

**Q: VibeGuard 实际上防住了哪些低级错误？**

每个项目都会积累的那种：
- 字段名前后端不一致 → `naming-convention.md` 强制机械转换
- 类型写了 `any` 或"格式不限" → 步骤1 要求精确、可判定真伪的类型定义
- Mock 数据跟接口契约脱节 → 步骤14.5 基于最终文档重新生成 mock
- 链接指向不存在的路由 → 步骤9-10 全量扫描所有链接对照路由表
- 前端能跑但后端还没实现 → MSW mock 让前端独立运行
- 数据库字段名跟 API 对不上 → mock-api-doc.md 的五列表格消除歧义

这些都是确定性错误。约束到位，100% 可消灭。

**Q: 功能多了还能撑住吗？**

VibeGuard 维护的是**接口层的秩序**，不是实现层的秩序。项目从 28 个接口增长到 100 个时，命名仍然一致，API 契约仍然同步，mock 仍然对齐。业务逻辑膨胀和架构腐烂仍然会发生——这是软件工程的本质问题，不是 AI 特有的，就算是顶尖大厂的真人团队也避免不了。

但 VibeGuard 保证的是：当你确实需要重构时，重构的成本更低。契约文档记录了每个接口边界，命名可以全局搜索替换，测试覆盖了每条路径。你始终有一张地图，即使地图上的 territory 变得凌乱了。

**Q: 新项目还是老项目更适合？**

老项目。把 VibeGuard 接入老项目时，会自动审计现有代码——提取类型、补全文档、暴露命名不一致。交叉校验步骤会抓出已有代码和新契约之间的真实冲突。新项目的约束是"防止犯错"，老项目的约束还能"发现已有的错"。

**Q: 能防住所有 bug 吗？**

不能。VibeGuard 拉的是下限，不是上限。它消灭的是低级错误（命名、类型、契约、路由）——这些也是人类团队经常犯的，"随便吧，能跑就行"就是技术债的起点。安全、并发、金融交易这些，人工还是得接入。框架设计上是可以扩展的：按项目需求逐步往安全检查清单里加约束项就行。

**Q: 那 VibeGuard 到底是什么定位？**

纪律框架，不是银弹。它对 AI 做的事，跟 code review、PR 检查、CI 流水线对人类团队做的是同一件事——不是消灭错误，而是让错误可检测、可修复。核心哲学：接受"会出问题"，保证"出了问题能快速定位和低成本修复"。

---

## 工作流程

```
人类："我要一个有文章、分类、评论的博客"
         │
         ▼
  /translate-requirement     ← 追问精炼 → 结构化需求说明
         │
         ├──► /frontend-standard   ← 15 步机械执行
         │         │
         │         ▼
         │    types.ts, api-contract.ts, mock-api-doc.md
         │    组件, 服务层, E2E 测试
         │
         ├──► /backend-assess       ← 写代码前的可行性审查
         │         │
         │         ▼
         │    backend-feasibility.md（通过 / 驳回）
         │
         └──► /backend-standard     ← 8 步后端实现
                   │
                   ▼
              ORM 模型, Pydantic 校验, API 路由,
              业务逻辑, 安全自检, 接口测试
```

每个命令在独立的对话窗口执行，防止上下文污染。

## 四个命令

| 命令 | 角色 | 产出 |
|------|------|------|
| `/translate-requirement` | 需求分析师——追问直到需求精确 | 结构化需求说明 |
| `/frontend-standard` | 前端执行者——从类型定义到 E2E 测试的 15 步 | `types.ts`、`api-contract.ts`、mock handlers、组件、Playwright 测试 |
| `/backend-assess` | 技术审查员——写后端代码前的可行性评估 | `backend-feasibility.md`（或驳回时的 `REJECT_FEEDBACK.md`） |
| `/backend-standard` | 后端执行者——从 ORM 到安全自检的 8 步 | 模型、校验、路由、服务、pytest 测试套件 |

## 核心设计原则

**契约驱动。** 每个产物（`types.ts`、`api-contract.ts`、`mock-api-doc.md`、`naming-convention.md`）都是下游步骤必须机械遵循的契约。不允许自由发挥。

**交叉校验。** 每个步骤把自己的产出对照已有契约验证。不一致立即停止——AI 不能自行决定保留哪一版。

**命名集中治理。** `naming-convention.md` 是唯一真相源。实体名、字段名、ID 前缀、API 端点、数据库列名全部由它机械推导（camelCase ↔ snake_case，PascalCase → 表名，前缀映射）。

**AI 的局限性被显式声明。** 命令开头直接告诉 AI："你没有全局视野，不能信任你的自我检查。"每一步都强制 AI 进入刚性执行模式，附带必须完成的验证。

## 项目结构（执行完所有命令后）

```
your-project/
├── blog-frontend/
│   ├── src/
│   │   ├── types.ts              # 所有实体接口定义
│   │   ├── api-contract.ts       # API 端点类型契约
│   │   ├── services/index.ts     # 带类型的 API 客户端函数
│   │   ├── mocks/
│   │   │   ├── handlers.ts       # MSW mock handlers（覆盖全部端点）
│   │   │   └── browser.ts        # MSW worker 配置
│   │   ├── components/           # React 组件
│   │   └── main.tsx              # 入口文件（MSW 条件启动）
│   ├── e2e/                      # Playwright E2E 测试
│   ├── mock-api-doc.md           # 全局 API 契约（后端的唯一真相源）
│   └── .gitignore
├── backend/
│   ├── app/
│   │   ├── models/               # SQLAlchemy ORM 模型
│   │   ├── schemas/              # Pydantic 校验模型
│   │   ├── routers/              # API 路由处理器
│   │   └── services/             # 业务逻辑
│   ├── tests/                    # pytest 测试套件
│   ├── migrations/               # Alembic 迁移脚本
│   ├── backend-feasibility.md    # 实现前的可行性评估报告
│   ├── BACKEND_DELIVERY_LOG.md   # 实现复盘日志
│   └── .gitignore
└── naming-convention.md          # 项目全局命名规范（唯一真相源）
```

## 快速开始

1. 把四个命令文件复制到项目的 `.claude/commands/` 目录：
   ```
   your-project/.claude/commands/
   ├── translate-requirement.md
   ├── frontend-standard.md
   ├── backend-assess.md
   └── backend-standard.md
   ```

2. 把 `naming-convention.md` 复制到项目根目录（或者让 `/frontend-standard` 首次运行时自动生成）。

3. 从 `/translate-requirement` 开始，描述你想构建什么。

4. 按流程推进：需求精炼 → 前端实现 → 后端评估 → 后端实现。

## 默认技术栈

- **前端：** React 19 + TypeScript + Vite + Tailwind CSS + React Router
- **后端：** FastAPI + SQLAlchemy + Pydantic + Alembic + SQLite/PostgreSQL
- **Mock：** MSW (Mock Service Worker)——前端可独立于后端运行
- **测试：** Playwright（E2E）+ pytest（接口测试）

命令对技术栈有偏好，但契约驱动的方法论是可迁移的。

## 协议

MIT
