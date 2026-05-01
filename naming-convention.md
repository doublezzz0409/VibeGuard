```markdown
# 项目命名规范 (naming-convention.md)

> 本文档是项目前后端命名的**唯一真相源**。
> `translate-requirement` 在进行需求精炼时，**必须**读取此文档，基于规则推导新功能命名，并查重冲突。
> `frontend-standard.md` 在步骤 1 生成或更新 `types.ts` 后，**必须**更新此文档。
> 人类有权在“契约提案”阶段否决或修改命名，但除此之外，**所有命名必须机械遵循本规则**。

## 规则与目录的优先级

- 第 1、2、4 章为**命名规则**，具有最高优先级。
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
| 文章 (Article) | `art-` | `"art-abc123"` |
| 分类 (Category) | `cat-` | `"cat-1"` |
| 标签 (Tag) | `tag-` | `"tag-uuid"` |
| 用户 (User) | `user-` | `"user-admin"` |
| 评论 (Comment) | `cmt-` | `"cmt-a1b2c3"` |

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
```