# 古诗词平台

AI 辅助编程期末大作业 · 小组项目

> 面向古诗词学习者，提供诗词库管理、闯关练习、复习巩固与诗人生平地图浏览的一站式 Web 平台。
> 本句为初稿，最终以《需求文档》为准。

## 功能模块

| 编号 | 模块 | 核心内容 | 负责人 |
| --- | --- | --- | --- |
| M1 | 登录 / 注册 | 账号注册、登录、JWT 鉴权、登录态保持 | 待定 |
| M2 | 诗词库管理 | 诗词新增、删除、修改、查询，分类与标签 | 待定 |
| M3 | 闯关模式 | 关卡与题目生成、答题判分、进度与成绩记录 | 待定 |
| M4 | 复习模式 | 错题本 / 收藏、按计划复习、掌握度统计 | 待定 |
| M5 | 管理员 / 用户 | 角色权限区分、用户管理、内容维护 | 待定 |
| M6 | 诗人生平地图 | 诗人档案、生平时间线、足迹地图可视化 | 待定 |

## 目录规划（技术栈确定后补充）

```text
.
├── docs/          # 需求文档、技术规范、测试与安全说明、分工表
├── frontend/      # 前端工程
├── backend/       # 后端工程
└── README.md
```

## 协作规范

### 分支模型

| 分支 | 用途 | 规则 |
| --- | --- | --- |
| `main` | 稳定可演示版本 | 只允许从 `develop` 通过 PR 合入，不直接提交 |
| `develop` | 日常集成分支 | 功能分支合并到这里 |
| `feature/<模块>-<简述>` | 单个功能开发 | 例：`feature/auth-login`、`feature/poem-crud` |
| `fix/<简述>` | 缺陷修复 | 例：`fix/login-token-expired` |

### 提交信息规范

格式：`type(scope): 中文描述`

| type | 含义 |
| --- | --- |
| feat | 新功能 |
| fix | 修复缺陷 |
| docs | 文档 |
| style | 格式调整（不影响逻辑） |
| refactor | 重构 |
| test | 测试 |
| chore | 构建、依赖、配置 |

示例：

- `feat(auth): 实现注册接口与参数校验`
- `fix(poem): 修复删除后列表未刷新的问题`
- `docs(requirements): 补充验收标准`

要求：小步提交；禁止 `update`、`修改` 这类无信息量的提交信息；禁止把一周的工作压在最后一天提交。

### 开发流程

1. `git clone <仓库地址>`，然后 `git checkout develop`
2. 开始新功能前先 `git pull` 拉取最新代码
3. `git checkout -b feature/xxx` 新建功能分支
4. 开发过程中按功能点小步提交
5. 推送分支，并在 GitHub 发起 Pull Request 到 `develop`
6. 至少一名组员 Review 后合并，合并后删除功能分支

## 安全约定

- 数据库密码、JWT 密钥、第三方 API Key 一律通过环境变量注入；`.env` 已被忽略，不得入库。
- 提交前自查：暂存区中不得出现密钥、token、真实姓名学号。
- 演示数据必须脱敏。

## 远程仓库

GitHub 仓库创建后执行：

```bash
git remote add origin https://github.com/<你的用户名>/poetry-platform.git
git push -u origin main
git push -u origin develop
```
