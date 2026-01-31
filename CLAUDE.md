# Claude 项目说明（Claude.md）
 
这是 [项目名称] 的 Claude Code 配置文件。
 
## 项目概述
 
- **技术栈**：React + TypeScript + Vite
- **包管理器**：pnpm
- **代码规范**：ESLint + Prettier

## 环境配置
 
### 开发环境
- Node.js 18+
- pnpm 8+
- VS Code + 推荐扩展

### 前端技术栈
- Next.js + TypeScript + Tailwind CSS
- ESLint + Prettier

### 后端技术栈
- Python3.11+ + FastAPI+ Uvicorn+sqlmodel
- sqlite
- Redis

### 环境变量
```bash
VITE_API_URL=http://localhost:3001
VITE_APP_NAME=MyApp

## 常用命令
 
### 开发命令
- `pnpm dev` - 启动开发服务器
- `pnpm build` - 构建生产版本
- `pnpm test` - 运行测试
 
### 代码质量
- `pnpm lint` - 代码检查
- `pnpm lint:fix` - 自动修复
- `pnpm type-check` - 类型检查
 
## 代码风格
 
### TypeScript
- 使用严格模式
- 优先使用接口定义类型
- 避免使用 `any` 类型
 
### React
- 优先使用函数组件和 Hooks
- 使用 JSX 语法
- 组件名使用 PascalCase
 
### 文件命名
- 组件文件：`ComponentName.tsx`
- 工具函数：`utilName.ts`
- 样式文件：`styles.module.css`
 
## 项目结构

部署流程
本地测试通过
代码推送到 feature 分支
创建 Pull Request
代码审查通过后合并到 main
自动部署到生产环境
 

 # GitHub 工作流程
 
## 常用命令
- `gh issue list --assignee @me` - 查看分配给我的 issues
- `gh pr status` - 查看 PR 状态
- `gh repo view` - 查看仓库信息
 
## 发布流程
1. 更新版本号
2. 运行测试
3. 创建 tag
4. 生成 release notes
5. 发布 release