```markdown
# 项目命名规范 (naming-convention.md)

> 本文档是项目**前后端命名的唯一真相源**。  
> 前端阶段 (`frontend-standard.md`)、后端评估 (`backend-feasibility.md`)、后端实现 (`backend-standard.md`) 均**必须**读取本文档。  
> `translate-requirement` 在进行需求精炼时，基于本文档的规则推导新功能命名并查重冲突。  
> 人类有权在“契约提案”阶段否决或修改命名，但除此之外，**所有命名必须机械遵循本规则**。

## 规则与目录的优先级

- 第 1、2、4、6 章为**命名与转换规则**，具有最高优先级。  
- 第 3 章（实体与字段目录）和第 4.3 节（已有端点目录）为基于规则生成的**项目现状快照**，由 `frontend-standard.md` 自动维护。  
- 当目录中条目与规则冲突时，**以规则为准**。冲突条目视为待修正的遗留项，在 `frontend-standard.md` 下次执行步骤 1 时自动对齐。  
- 目录的作用是**查重**，而非定义规则。

---

## 1. 通用命名原则

### 1.1 大小写与分隔符

| 类别 | 规则 | 示例 |
|------|------|------|
| 实体名、Interface/Type 名 | PascalCase（大驼峰） | `ArticleSummary`, `DashboardStats` |
| 字段名、变量名 | camelCase（小驼峰） | `likeCount`, `createdAt`, `isApproved` |
| API 路径节 | kebab-case（连字符分隔，全小写） | `/api/site-settings`, `/api/admin/articles` |
| 路径参数 | camelCase | `:articleId`, `:slug` |
| 枚举值（字符串字面量） | 小写字母，词间用 `-` 分隔 | `"draft"`, `"published"`, `"archived"` |
| 常量/环境变量 | UPPER_SNAKE_CASE（全大写下划线） | `MAX_PAGE_SIZE` |
| React 组件文件 | 与组件名一致的 PascalCase | `ArticleCard.tsx`, `AdminSidebar.tsx` |
| 非组件文件（工具/类型/配置/Hook） | kebab-case | `api-contract.ts`, `types.ts`, `use-auth.ts` |

### 1.2 语义优先原则

- 命名必须**自解释**：看到名字不需要查字典就能理解含义。禁止缩写除非已被广泛接受（`Id`、`Url`、`Api` 可作为单词的一部分）。  
- 字段名应与该实体在业务上的“属性”语义一致，而非描述其“存储方式”或“传输方式”。  
- API 端点命名应使用**资源名词 + 可选操作谓词**，而非 CRUD 动词（`POST /articles` 而非 `/createArticle`）。  

---

## 2. 实体与字段命名规则

### 2.1 实体名

- 实体名使用 PascalCase，为单数名词或名词短语。禁止复数形式（`Article` 非 `Articles`，`Category` 非 `Categories`）。  
- 表现层变体（如列表项、详情）使用 `Summary`、`Detail` 后缀：`ArticleSummary`, `ArticleDetail`。  
- 纯请求体/表单数据使用 `FormData` 后缀：`ArticleFormData`, `CategoryFormData`。  
- 统计/聚合数据使用 `Stats` 后缀：`DashboardStats`。  
- **特例**：容器类对象（如 `SocialLinks`）和统计聚合对象（如 `DashboardStats`）允许复数形式。该类实体的名称本身表达“包含多个子项”，与资源列表的复数语义不同。新实体命名时如需引用此特例，必须在契约提案中注明理由。  

### 2.2 ID 字段

- 所有实体主键一律命名为 `id`，类型为 `string`，格式必须遵循 ID 前缀规则（见 3.1）。  
- 外键引用命名为 `{被引用实体名}Id`（camelCase），如 `categoryId`, `articleId`。前缀与该实体的主键前缀一致。  

### 2.3 布尔字段

- 布尔字段必须使用 `is` 或 `has` 前缀，明确其“是/否”语义：`isApproved`, `isPinned`, `hasChildren`。  
- 禁止使用不带前缀的形容词（`approved`, `pinned`），因为无法与名词字段区分。  

### 2.4 计数与数量字段

计数字段遵循以下两条独立规则（二选一，不必同时满足）：  
- **实体级计数字段**：使用 `Count` 后缀。如 `viewCount`、`likeCount`、`articleCount`。  
- **聚合统计总数**：使用 `total` 前缀，此时 `Count` 后缀可选且推荐不加。如 `totalArticles`、`totalViews`。  

当字段同时满足“实体级”和“聚合统计”条件时，优先使用 `total` 前缀规则。  

### 2.5 日期时间字段

- 表示创建时间：`createdAt`。  
- 表示更新时间：`updatedAt`。  
- 表示特定事件时间：`{事件}At`，如 `publishedAt`, `deletedAt`。  
- 禁止使用 `create_time`、`updateTime` 等混合风格。  

### 2.6 枚举字段

- 枚举字段的字符串字面量必须为全小写，词间用 `-` 分隔：`"draft"`, `"published"`, `"archived"`。  
- 枚举值的完整集合必须在 `types.ts` 中通过 TypeScript 联合类型定义，并在 `mock-api-doc.md` 中列出。  

---

## 3. ID 前缀与实体查重目录

### 3.1 前缀规则

| 实体 | 前缀 | 示例 |
|------|------|------|
| 文章 (Article) | `art-` | `"art-550e8400-e29b-41d4-a716-446655440000"` |
| 分类 (Category) | `cat-` | `"cat-660e8400-e29b-41d4-a716-446655440001"` |
| 标签 (Tag) | `tag-` | `"tag-770e8400-e29b-41d4-a716-446655440002"` |
| 用户 (User) | `user-` | `"user-880e8400-e29b-41d4-a716-446655440003"` |
| 评论 (Comment) | `cmt-` | `"cmt-990e8400-e29b-41d4-a716-446655440004"` |

### 3.2 实体与字段目录（仅用于查重，由 frontend-standard 自动更新）

> **机械执行规则：** 当需要新增实体或字段时，必须逐行扫描此表。如果新名称与任何已有实体名、字段名重复，立即标记为“命名冲突”并提请人类裁决。

#### Article / ArticleSummary / ArticleDetail
`id`, `title`, `slug`, `summary`, `content` (仅 Detail), `coverImage`, `categoryId`, `tagIds`, `status`, `isPinned`, `viewCount`, `likeCount`, `dislikeCount`, `createdAt`, `updatedAt`

#### Category
`id`, `name`, `slug`, `description`, `articleCount`, `sortOrder`

#### Tag
`id`, `name`, `slug`, `articleCount`

#### User
`id`, `username`, `nickname`, `avatar`, `email`, `bio`

#### Comment
`id`, `articleId`, `authorName`, `authorEmail`, `content`, `isApproved`, `createdAt`

#### SiteSettings
`siteName`, `siteDescription`, `logo`, `favicon`, `footerText`, `socialLinks` (github, twitter, email), `postsPerPage`

#### DashboardStats
`totalArticles`, `totalViews`, `totalComments`, `totalCategories`

#### ArchiveItem
`year`, `months` (month, articles)

---

## 4. API 路径命名规则

### 4.1 通用格式

```
/api/{resource}               资源列表 (GET)
/api/{resource}/:{param}      单个资源 (GET)
/api/admin/{resource}         管理端资源列表 (GET, POST)
/api/admin/{resource}/:{param} 管理端单个资源 (GET, PUT, DELETE)
```

- `{resource}` 为实体名的 kebab-case 复数形式：`articles`, `categories`, `tags`, `comments`。  
- 特殊情况：单例资源使用原词，如 `dashboard`, `settings`, `profile`, `password`, `about`, `site-settings`。  

### 4.2 子操作（针对资源的非 CRUD 动作）

```
/api/{resource}/:{param}/{action}
```

- `{action}` 为动词或动词短语，kebab-case：`approve`, `like`, `dislike`。  
- 示例：`PUT /api/admin/comments/:id/approve`, `POST /api/articles/:slug/like`。  

### 4.3 已有端点目录（仅用于查重，由 frontend-standard 自动更新）

**公开接口：**
- `GET /api/articles`
- `GET /api/articles/:slug`
- `GET /api/categories`
- `GET /api/tags`
- `GET /api/archive`
- `GET /api/site-settings`
- `GET /api/about`
- `POST /api/login`

**管理接口：**
- `GET /api/admin/dashboard`
- `GET /api/admin/articles`
- `GET /api/admin/articles/:id`
- `POST /api/admin/articles`
- `PUT /api/admin/articles/:id`
- `DELETE /api/admin/articles/:id`
- `POST /api/admin/categories`
- `PUT /api/admin/categories/:id`
- `DELETE /api/admin/categories/:id`
- `POST /api/admin/tags`
- `PUT /api/admin/tags/:id`
- `DELETE /api/admin/tags/:id`
- `GET /api/admin/comments`
- `PUT /api/admin/comments/:id/approve`
- `DELETE /api/admin/comments/:id`
- `GET /api/admin/settings`
- `PUT /api/admin/settings`
- `GET /api/admin/profile`
- `PUT /api/admin/profile`
- `PUT /api/admin/password`

---

## 5. 文件维护与更新规则

### 5.1 由 `frontend-standard.md` 自动执行

- **首次创建**：步骤 1 结束时，基于 `types.ts` 生成此文件的初版，包含第 2、3 章的所有实体、字段、前缀。  
- **增量更新**：每次执行步骤 1 修改 `types.ts` 后，必须在此文件中同步新增/修改/删除相应条目。查重目录必须与 `types.ts` 完全一致。  

### 5.2 由 `translate-requirement` 强制读取

- **启动时加载**：作为项目上下文的一部分读取。如果文件不存在（新建项目），则在追问时主动询问命名偏好，并在输出需求说明前生成此文件的草案版本。  
- **契约提案引用**：在输出命名提案时，必须显式说明新命名遵循了本文件的哪条规则，并附上查重结论。  

### 5.3 由 `backend-feasibility` 和 `backend-standard` 强制读取

- 后端评估和实现阶段，所有数据库表名、字段名、索引名必须严格遵循第 6 章的转换规则生成。  
- 任何偏离都必须作为“技术障碍”在评估报告中明确指出，并提请人类裁决。

---

## 6. 前后端命名转换规则（机械执行）

> **设计意图**：前端 TypeScript 代码、API JSON 传输、后端 Python/FastAPI 模型、数据库表结构是同一逻辑实体的不同表现层。  
> 本章定义一套**无歧义的、一一对应的命名转换规则**，使得后端在阅读 `mock-api-doc.md` 时，无需查看前端代码，即可直接推导出数据库字段名和表名。

### 6.1 大小写转换：驼峰 ↔ 蛇形

前端字段名（camelCase）与后端属性名、数据库字段名（snake_case）之间的转换遵循以下机械规则：

- **驼峰 → 蛇形**：将小写字母后紧跟的大写字母，转换为该大写字母前加下划线并改为小写。连续大写字母视为一个词（如 `FAQList` → `faq_list`）。  
- **蛇形 → 驼峰**：将下划线后的字母转为大写，并去掉下划线。

| 前端 / API JSON | 后端 (Python) | 数据库 |
|----------------|---------------|--------|
| `likeCount` | `like_count` | `like_count` |
| `articleId` | `article_id` | `article_id` |
| `isApproved` | `is_approved` | `is_approved` |
| `createdAt` | `created_at` | `created_at` |
| `totalArticles` | `total_articles` | `total_articles` |

**机械执行要求**：
- 前后端代码中**不得**使用混合风格（如 `like_Count`）。  
- 数据库迁移脚本、ORM 模型必须统一使用 snake_case 定义字段。  
- API 传输的 JSON 键名**必须**与前端字段名保持一致（camelCase）。后端框架（如 FastAPI）通过 Pydantic 的 `alias` 或模型配置自动完成转换。

### 6.2 实体名 → 表名转换

- **前端实体名** (PascalCase, 单数) → **数据库表名** (snake_case, 复数)。
- **转换步骤**：
  1. 将 PascalCase 实体名按驼峰规则拆分为单词，转换为 snake_case（不改变单词顺序）。
  2. 对**最后一个单词**进行复数化：
     - **规则优先级**：
       a. 如果该单词已是公认的复数形式（如 `stats`、`data`、`series`、`news`、`status`），则保持不变。
       b. 如果该单词以 `y` 结尾且前一个字母为辅音 → 变 `y` 为 `ies`（如 `Category` → `categories`）。
       c. 如果该单词以 `s`、`x`、`ch`、`sh` 结尾 → 加 `es`（如 `Address` → `addresses`）。
       d. 否则加 `s`（如 `Article` → `articles`）。
  3. **特例处理**：对于第 2.1 节中允许复数形式的实体名（容器类 `SocialLinks`、统计类 `DashboardStats`），其表名转换规则为：**将实体名整体转换为 snake_case（保持不变复数）**。例如：
     - `SocialLinks` → `social_links`
     - `DashboardStats` → `dashboard_stats`
  4. **后缀处理**：如果实体名带有 `Summary`、`Detail`、`Stats` 后缀，复数化只作用于核心名词部分，后缀按语义处理：
     - `ArticleDetail` → 核心名词 `Article` 变为 `articles`，后缀 `Detail` 保持单数 → `article_details`
     - `ArticleSummary` → `article_summaries`
     - `DashboardStats`（已按特例处理）→ `dashboard_stats`

| 实体名 (前端) | 数据库表名 |
|---------------|-----------|
| `ArticleDetail` | `article_details` |
| `ArticleSummary` | `article_summaries` |
| `Category` | `categories` |
| `Tag` | `tags` |
| `User` | `users` |
| `Comment` | `comments` |
| `SiteSettings` | `site_settings` |
| `DashboardStats` | `dashboard_stats` |
| `SocialLinks` | `social_links` |
| `ArticleFormData` | （不持久化，不映射） |

**特例**：  
- 不持久化的纯请求体/表单数据类型（如 `ArticleFormData`）不映射为表。  
- 单例设置表（如 `site_settings`）虽然逻辑上是单数，但遵循统一复数规则。

### 6.3 ID 前缀与主键处理

- 前端 `id` 字段的值为 `"art-550e8400-e29b-41d4-a716-446655440000"` 格式的字符串。
- **数据库主键**：所有实体主键统一使用 UUID（版本4），格式 `xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx`，**不存储前缀**。在 API 返回时由后端序列化层统一拼接实体对应前缀。
- 自增整数仅用于不需要对外暴露 ID 的内部关联表（如多对多关联表），且需在 mock-api-doc 中明确标注。
- 后端 ORM 或序列化器必须使用本规范 3.1 节定义的前缀表进行拼装。
- 外键在数据库中仍为 UUID，不携带前缀。

### 6.4 默认数据类型映射（TypeScript → 数据库）

> 此映射供 `backend-standard.md` 和 `backend-feasibility.md` 作为默认选择，人类可在评估阶段调整。

| TypeScript 类型 | 数据库类型 (SQL) | 说明 |
|----------------|------------------|------|
| `string` (短文本) | `VARCHAR(255)` | 如 `title`、`name`、`slug`、`username` |
| `string` (长文本) | `TEXT` | 如 `content`、`summary`、`bio`、`description` |
| `string` (URL/路径) | `TEXT` | 如 `coverImage`、`logo`、`favicon` |
| `string` (UUID/ID) | `VARCHAR(36)` | 统一使用 VARCHAR(36)，存储 UUID v4 |
| `number` (整数, 非负) | `INTEGER` / `BIGINT` | 如 `viewCount`, `likeCount` |
| `number` (小数) | `DECIMAL` 或 `FLOAT` | 视精度需求 |
| `boolean` | `BOOLEAN` | 如 `isApproved` |
| `string` (日期时间 ISO 8601) | `DATETIME` / `TIMESTAMP` | 存储不含时区，前端统一 UTC |
| `string[]` (数组) | 关联表或 `JSON` 字段 | 如 `tagIds` |
| `object` / 嵌套对象 | 关联表或 `JSON` 字段 | 如 `socialLinks` |

**VARCHAR vs TEXT 决策规则（优先级从高到低）**：

1. **字段语义优先级**：如果字段名包含 `content`、`summary`、`bio`、`description` 等长文本关键词 → 使用 `TEXT`。
2. **长度约束优先级**：如果在 mock-api-doc 的数据模型表格中，该字段的“约束”列标注了明确的长度限制（如“长度 1-200”）→ 使用 `VARCHAR(该长度)`。
3. **示例映射优先级**：如果字段在本节映射表中有明确示例（如 `title` → `VARCHAR(255)`）→ 直接使用示例中的类型。
4. **默认规则**：其他情况下，默认使用 `VARCHAR(255)`。

**空值映射表（确保前后端空值语义一致）**：

| 前端类型（可空） | 数据库定义 | 说明 |
|----------------|-----------|------|
| `string \| null` 或 `string \| undefined`（业务预期为空字符串） | `VARCHAR(...) DEFAULT ''` 或 `TEXT DEFAULT ''`，`nullable=False` | 业务层统一用空字符串，不用 NULL |
| `string \| null`（业务语义为“未提供”，如用户可选的 bio） | `VARCHAR(...) DEFAULT NULL`，`nullable=True` | 允许数据库 NULL，JSON 序列化时转为 `null` |
| `object \| null` | `JSON DEFAULT NULL`，`nullable=True` | JSON 字段默认 NULL，表示不存在 |
| `array \| null`（如 `tagIds`） | 关联表（不用 NULL）或 `JSON DEFAULT NULL` | 空数组用 `[]` 而不是 NULL |

**强制要求**：后端必须在 `backend-feasibility.md` 中确认或修改这些默认映射，并在 `backend-standard.md` 的实现中使用最终确认的类型。

### 6.5 一致性强制规则

- 任何后端代码中出现的字段名、表名，必须能通过反向转换（蛇形→驼峰）无歧义地还原为前端定义的字段名。  
- 数据库索引名、约束名由 `backend-standard.md` 约束，但其基础字段名部分仍须从本规则推导。  
- 所有转换规则均为**全局唯一**，不得在项目不同模块中使用不同风格（如部分表用驼峰）。  
- 如果因历史遗留存在风格不一致的数据库对象，`backend-feasibility.md` 必须列出并建议迁移方案，`backend-standard.md` 实施重构。
```