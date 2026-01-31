---
name: skill-doc-reader-router
description: 读取项目文档，并输出文档阅读计划、约束清单和可执行改动方案
---
# skill-doc-reader-router

> **Skill 名称**：skill-doc-reader-router  
> **定位**：文档阅读路由与约束提取器  
> **目标**：根据「用户需求 + 项目目录结构」，**按需、按优先级**阅读项目文档，输出  
> 1️⃣ 阅读计划  
> 2️⃣ 约束清单  
> 3️⃣ 可执行的改动方案  
>  
> 本 skill **不直接改代码**，只负责“读对文档 + 产出工程级决策输入”。

---

## 一、适用场景（什么时候该用）

- 新人接手项目，不知道**先看哪些文档**
- Claude Code 经常：
  - 文档读太多 / 乱读
  - 忽略 CLAUDE.md / 约束文件
  - 给方案但不说明**依据来自哪里**
- 需要在 **写代码 / 写新 skill / 架构调整之前**，先做一次**可审计的文档理解**

👉 **这是一个“写代码前”的前置 skill**

---

## 二、输入（Input）

```yaml
user_request: |
  用一句话描述当前要做的事。
  示例：
  - 给 FastAPI 项目新增一个用户模块
  - 修复部署失败的问题
  - 设计一个新的 CI 流水线

repo_tree:
  - README.md
  - CLAUDE.md
  - docs/
    - adr/
    - api/
    - db/
    - devops/
  - backend/
    - README.md
  - frontend/
    - README.md
  - plans/
    - phase-1.md
````

### 输入说明

* **user_request**

  * 必须是“任务级目标”，不是实现细节
* **repo_tree**

  * 只需要文件路径结构
  * ❌ 不需要文件内容

---

## 三、输出（Output）

### 输出必须严格包含 3 个部分（顺序不可变）

---

### 1️⃣ 文档阅读计划（doc_read_plan）

```yaml
doc_read_plan:
  - path: CLAUDE.md
    reason: 全局约束与强制规则
    priority: P0

  - path: README.md
    reason: 项目整体结构与运行方式
    priority: P1

  - path: backend/README.md
    reason: 与当前需求直接相关的后端模块说明
    priority: P1

  - path: docs/db/schema.md
    reason: 涉及数据结构变更
    priority: P1
```

**规则**

* 必须说明：**为什么要读**
* 必须标注优先级：`P0 / P1 / P2`
* 不允许“全读 / 扫一遍”

---

### 2️⃣ 约束与事实清单（extracted_constraints）

```yaml
extracted_constraints:
  must:
    - 所有新增模块必须放在 backend/modules 下
    - 数据库模型必须使用 SQLModel
    - CI 使用 uv 作为依赖管理工具

  must_not:
    - 不允许直接修改 production workflow
    - 不允许引入新前端框架

  interfaces:
    - API 基础路径：/api/v1
    - 数据库迁移方式：alembic

  invariants:
    - 用户模块必须支持 RBAC
```

**规则**

* 只提取“会影响决策/实现”的内容
* 不要复述说明性文字
* 有冲突时：

  * 以 **优先级高的文档**为准
  * 明确标注冲突来源

---

### 3️⃣ 可执行改动方案（action_plan）

```yaml
action_plan:
  scope:
    - backend
    - database

  steps:
    - step: 1
      action: 新增 user 模块目录
      path: backend/modules/user/
      based_on: backend/README.md

    - step: 2
      action: 设计 User 数据模型
      path: backend/modules/user/model.py
      based_on: docs/db/schema.md

    - step: 3
      action: 补充 API 路由
      path: backend/modules/user/api.py
      based_on: docs/api/*.md

  acceptance_criteria:
    - 项目可正常启动
    - CI 不失败
    - 未违反 CLAUDE.md 中的任何 must_not
```

**规则**

* 每一步必须能追溯到：

  * 哪份文档
  * 哪条约束
* 不写“示例代码”
* 这是 **下一步写代码的施工图**

---

## 四、执行逻辑（伪代码）

```pseudo
INPUT user_request, repo_tree

READ CLAUDE.md -> constraints (P0)

BUILD doc_index FROM repo_tree
  INCLUDE docs/, README*, plans/
  EXCLUDE .venv, node_modules, dist, build, .git

CLASSIFY user_request INTO intent
  IF contains "部署|CI" -> devops
  IF contains "模块|功能" -> backend/frontend
  IF contains "表|字段" -> database
  IF contains "架构|重构" -> adr

SELECT docs BY:
  priority
  intent relevance
  minimal sufficient set

FOR each selected doc IN priority order:
  READ doc
  EXTRACT:
    - must / must_not
    - interfaces
    - invariants

RESOLVE conflicts BY priority

OUTPUT:
  doc_read_plan
  extracted_constraints
  action_plan
```

---

## 五、使用建议（工程经验）

* ✅ **任何“我要开始写代码了”的场景，先跑一次这个 skill**
* ✅ 特别适合：

  * 项目初始化
  * 新人 onboarding
  * 大模型生成代码前的“刹车层”
* ❌ 不适合：

  * 纯算法题
  * 单文件小脚本

---