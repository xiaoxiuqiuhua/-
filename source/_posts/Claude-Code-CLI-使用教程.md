---
title: Claude Code CLI 使用教程
date: 2026-06-03 18:30:00
tags:
  - Claude Code
  - AI工具
  - 教程
categories:
  - 技术
---

> 基于 Claude Code v2.1.143+ (2026年5月)

---

## 速查表（可直接复制）

### 启动命令

```bash
claude                          # 启动 Claude Code
claude /path/to/project         # 指定项目目录启动
claude --continue              # 启动并继续最近会话
claude --new                   # 启动并创建全新会话
claude --dangerously-skip-permissions  # 跳过所有权限确认
claude --print "问题"          # 单次查询后退出
claude --version               # 查看版本号
```

### 最常用命令

```bash
/help        # 查看所有命令
/plan        # 生成执行计划（只分析不执行）
/edit        # 编辑代码
/read        # 读取文件
/write       # 写入文件
/clear       # 清除上下文
/compact     # 压缩上下文
/context     # 查看上下文使用情况
/exit        # 退出当前会话
/resume      # 查看历史记录
/rewind      # 回滚到之前对话节点
/approve     # 批准待执行的修改
/reject      # 拒绝待执行的修改
/stop        # 立即终止当前运行任务
```

### 常用命令

```bash
/model       # 切换模型 (opus/sonnet/haiku)
/fast        # 快速模式（用 Haiku）
/effort      # 切换思考等级 (low/medium/high/xhigh)
/diff        # 查看变更
/grep        # 搜索内容
/glob        # 搜索文件
/branch      # 分支管理
/review      # 代码审查
/commit      # 提交代码
/pr          # 创建 PR
/agents      # 打开智能体管理面板
/files       # 列出会话访问过的文件
```

### 系统与调试

```bash
/status      # 查看全面状态
/usage       # 查看用量
/cost        # 查看花费
/doctor      # 诊断问题
/hooks       # 管理 Hooks
/tools       # 列出工具
/cache       # 缓存管理
/session     # 会话管理
/export      # 导出会话
```

### 记忆系统

```bash
/memory      # 查看/管理记忆
/remember    # 记住偏好
/forget      # 删除记忆
/init        # 初始化项目 (创建 CLAUDE.md)
/ask         # 纯问答（不执行任何操作）
```

### 快捷键

```bash
# 导航
↑ / ↓        # 浏览历史命令
Tab          # 自动补全
Tab × 2      # 显示建议列表
Ctrl+G       # 全局搜索文件 / 以 txt 文本格式打开编辑
Ctrl+L       # 清屏
Ctrl+C       # 取消当前操作

# 输入
Shift+Enter  # 换行（多行输入）
Ctrl+J       # 插入换行
Ctrl+Enter   # 发送消息
Ctrl+U       # 清空当前输入
Ctrl+W       # 删除前一个单词
Ctrl+A       # 光标移到行首
Ctrl+E       # 光标移到行尾

# 命令
/            # 打开命令列表
//           # 搜索命令
Esc × 2      # 退出当前模式
Shift + Tab  # 切换自动编辑
!            # 切换终端模式
```

---

## 1. 安装与登录

### 安装

```bash
# Windows (PowerShell 管理员)
irm https://claude.ai/install.ps1 | iex

# npm 安装
npm install -g @anthropic-ai/claude-code

# 验证
claude --version
```

### 登录

```bash
claude login
```

### 卸载

```bash
npm uninstall -g @anthropic-ai/claude-code
```

---

## 2. 启动方式

### 基础启动

| 命令 | 说明 |
|------|------|
| `claude` | 当前目录启动新会话 |
| `claude /path/to/project` | 指定项目目录启动 |
| `claude -c` | 继续最近会话 |
| `claude -r` | 选择历史会话继续 |
| `claude --print "问题"` | 单次查询后退出 |

### CLI 参数

| 参数 | 说明 |
|------|------|
| `--model <model>` | 指定模型 (opus/sonnet/haiku/MiniMax-M2.7) |
| `--permission-mode <mode>` | 权限模式 (auto/ask/plan) |
| `--dangerously-skip-permissions` | 跳过所有权限确认 |
| `--version` | 显示版本 |
| `--help` | 帮助 |

---

## 3. 核心斜杠命令

### 3.1 会话管理

#### `/help` - 帮助

```bash
/help          # 查看所有命令
/help <topic>  # 查看特定主题帮助
```

#### `/clear` - 清除上下文

```bash
/clear         # 清除当前会话历史，释放上下文
/reset         # 同上
/new           # 同上
```

#### `/compact` - 压缩上下文

```bash
/compact [instructions]
```

压缩对话上下文，节省 token。可选添加指令指定保留重点：

```bash
/compact 请保留所有关于登录逻辑的讨论
```

#### `/context` - 查看上下文使用

```bash
/context
```

可视化当前上下文使用情况，给出优化建议。显示：
- 当前 token 使用量
- 上下文窗口占用比例
- 优化建议

#### `/cost` - 查看花费

```bash
/cost
```

显示当前会话的 token 消耗情况。

#### `/exit` - 退出会话

```bash
/exit
```

#### `/resume` - 恢复会话

```bash
/resume
```

恢复最近中断的会话。

#### `/rewind` - 回滚对话

```bash
/rewind
```

回退到上一个对话节点。

#### `/stop` - 终止任务

```bash
/stop
```

立即终止当前运行任务。

---

### 3.2 模型控制

#### `/model` - 模型管理

```bash
/model           # 查看当前模型
/model <model>   # 切换模型
```

可用模型：
- `claude-opus-4-7` / `opus` - 最大模型，适合复杂任务
- `claude-sonnet-4-6` / `sonnet` - 中等模型，平衡性能
- `claude-haiku-4-5` / `haiku` - 快速模型，简单任务
- `MiniMax-M2.7` - MiniMax 模型

#### `/fast` - 快速模式

```bash
/fast
```

切换到快速模式，使用 Haiku 处理简单查询，节省时间和成本。

#### `/effort` - 思考等级

```bash
/effort low|medium|high|xhigh
```

设置模型思考和执行的深入程度：
- `low` - 快速响应，简单处理
- `medium` - 平衡
- `high` - 深入分析（默认）
- `xhigh` - 最高级别探索

---

### 3.3 规划与执行

#### `/plan` - 规划模式

```bash
/plan           # 进入计划模式（只分析不执行）
/plan <具体描述> # 直接指定任务，先规划再执行
```

适合：
- 重构大型代码库
- 多文件修改任务
- 需要先了解方案的任务

提交计划后，可以选择：
- 执行计划
- 修改计划
- 取消

#### `/execute` - 执行计划

```bash
/execute
```

在计划模式后执行已批准的计划。

#### `/approve` / `/reject` - 审批修改

```bash
/approve   # 批准待执行的修改
/reject    # 拒绝待执行的修改
```

#### `/review` - 代码审查

```bash
/review
/review <file_path>
```

审查代码质量问题、安全漏洞、逻辑错误等。

---

### 3.4 文件操作

#### `/edit` - 编辑代码

```bash
/edit <instruction>
```

对当前文件或选中代码进行编辑：

```bash
/edit 修复这个函数的 bug
/edit 添加参数验证
/edit 重构为 async/await
```

#### `/read` - 读取文件

```bash
/read <file_path>
/read <file_path>:<start_line>-<end_line>
```

读取文件内容，支持指定行号范围：

```bash
/read src/app.py
/read src/app.py:10-50
```

#### `/write` - 写入文件

```bash
/write <file_path>
```

创建新文件或覆盖现有文件。

#### `/diff` - 查看变更

```bash
/diff
/diff <file_path>
```

查看当前会话中文件的变更内容。

#### `/glob` - 搜索文件

```bash
/glob <pattern>
```

搜索匹配模式的文件：

```bash
/glob **/*.ts
/glob src/**/*.js
```

#### `/grep` - 搜索内容

```bash
/grep <pattern>
/grep <pattern> <file_path>
```

在项目中搜索文本内容：

```bash
/grep "function login"
/grep "TODO" src/
```

#### `/files` - 列出访问过的文件

```bash
/files
```

列出会话中访问过的所有文件。

---

### 3.5 Git 操作

#### `/commit` - 提交

```bash
/commit                    # 提交当前更改
/commit <message>          # 提交并指定消息
```

自动分析更改并生成有意义的 commit message。

#### `/pr` - 创建 PR

```bash
/pr
/pr <title>
```

创建 Pull Request，需要在 git 分支上。

#### `/wcommit` - 辅助提交

```bash
/wcommit
```

查看当前更改并辅助编写 commit message。

#### `/branch` - 分支管理

```bash
/branch              # 查看当前分支
/branch <name>      # 创建并切换分支
```

---

### 3.6 权限管理

#### `/permissions` - 权限设置

```bash
/permissions         # 查看当前权限配置
/permissions allow   # 允许所有操作
/permissions ask     # 所有操作需确认
/permissions deny    # 禁止某些操作
```

#### `/allow` / `/deny` - 允许/禁止操作

```bash
/allow <operation>
/deny <operation>
```

---

### 3.7 智能体管理

#### `/agents` - 智能体管理面板

```bash
/agents
```

打开智能体管理面板。

---

### 3.8 记忆系统

#### `/memory` - 记忆管理

```bash
/memory                    # 查看当前记忆
/memory list               # 列出所有记忆
/memory add <content>      # 添加记忆
/memory edit <id> <content> # 编辑记忆
/memory delete <id>        # 删除记忆
```

#### `/remember` - 记住偏好

```bash
/remember <内容>
```

让 Claude 记住你的偏好和习惯：

```bash
/remember 我的项目使用 Python + FastAPI
/remember 每次重构前先问我确认
```

#### `/forget` - 忘记信息

```bash
/forget <id>
```

删除特定记忆。

---

### 3.9 系统状态

#### `/status` - 状态查看

```bash
/status
```

显示 Claude Code 的全面状态：
- 版本信息
- 当前模型
- 账号状态
- API 连接状态
- 工具状态
- 工作目录

#### `/usage` - 用量查看

```bash
/usage
```

显示：
- 当前套餐用量限制
- 本月已使用量
- 剩余额度
- 重置日期

#### `/doctor` - 诊断问题

```bash
/doctor
```

诊断和验证 Claude Code 的安装和配置：
- Node.js 版本
- API 连接
- 权限配置
- 环境变量

---

### 3.10 项目初始化

#### `/init` - 初始化项目

```bash
/init
```

创建 CLAUDE.md 文件，让 Claude 了解你的项目：
- 项目结构
- 代码规范
- 技术栈
- 特殊要求

#### `/ask` - 纯问答

```bash
/ask <问题>
```

不执行任何操作，仅回答问题。适合：
- 解释概念
- 分析代码
- 技术咨询

---

### 3.11 其他命令

#### `/tools` - 列出工具

```bash
/tools
```

显示所有可用工具及其当前状态。

#### `/hooks` - 管理 Hooks

```bash
/hooks              # 查看所有 hooks
/hooks list        # 列出已配置的 hooks
/hooks edit <name> # 编辑 hook
/hooks delete <name> # 删除 hook
```

#### `/cache` - 缓存管理

```bash
/cache clear       # 清除缓存
/cache status      # 查看缓存状态
```

#### `/session` - 会话管理

```bash
/session save <name>   # 保存当前会话
/session list          # 列出所有会话
/session load <id>     # 加载会话
```

#### `/export` - 导出会话

```bash
/export                  # 导出当前会话为 Markdown
/export <file>.md        # 导出到指定文件
```

---

## 4. 环境变量配置

### 设置文件位置

| 文件 | 作用域 | Git |
|------|--------|-----|
| `~/.claude/settings.json` | 全局 | 否 |
| `.claude/settings.json` | 项目 | 是 |
| `.claude/settings.local.json` | 项目本地 | 否 |

### 常用配置项

```json
{
  "env": {
    "TAVILY_API_KEY": "your-key-here"
  },
  "model": "claude-opus-4-7",
  "effortLevel": "high",
  "theme": "dark",
  "permissions": {
    "allow": ["Bash(git *)", "Read"],
    "deny": ["Bash(rm -rf *)"]
  }
}
```

---

## 5. 权限规则语法

### 基本语法

| 规则 | 说明 |
|------|------|
| `"Bash(npm *)"` | 允许所有 npm 命令 |
| `"Read"` | 允许所有读取操作 |
| `"Write(/tmp/*)"` | 允许写入 /tmp 目录 |
| `"Edit(.claude)"` | 允许编辑 .claude 目录 |

### 权限模式

| 模式 | 说明 |
|------|------|
| `auto` | 自动允许安全操作 |
| `ask` | 所有操作需确认 |
| `plan` | 复杂操作需计划审批 |
| `acceptEdits` | 自动接受编辑 |

---

## 6. Hooks 配置

### Hook 事件

| 事件 | 触发时机 |
|------|----------|
| `PreToolUse` | 工具执行前 |
| `PostToolUse` | 工具执行后 |
| `PreCompact` | 压缩上下文前 |
| `PostCompact` | 压缩上下文后 |
| `Stop` | 会话结束时 |
| `SessionStart` | 会话开始时 |

### 配置示例

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": "Write|Edit",
      "hooks": [{
        "type": "command",
        "command": "prettier --write $FILE"
      }]
    }]
  }
}
```

---

## 7. MCP 服务器配置

### .mcp.json 格式

```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "@package/name"]
    }
  }
}
```

### 启用/禁用服务器

```bash
# 在设置中配置
{
  "enabledMcpjsonServers": ["server1"],
  "disabledMcpjsonServers": ["server2"]
}
```

---

## 8. Agent 任务

### 使用 Agent

```javascript
Agent({
  description: "分析前端结构",
  prompt: "搜索 src/frontend 目录，找出所有 React 组件",
  subagent_type: "Explore"
})
```

### 内置 Agent 类型

| Agent | 用途 |
|-------|------|
| `Explore` | 搜索和分析代码库 |
| `general-purpose` | 通用任务 |
| `Plan` | 复杂任务规划 |

---

## 9. 高效使用技巧

### 1. 提供具体上下文

❌ 不好：`修复这个 bug`
✅ 好：`修复 src/auth/login.py:45 的 token 过期问题`

### 2. 使用规划模式处理复杂任务

```bash
/plan 重构整个用户模块
```

### 3. 利用记忆系统

```bash
/remember 我的项目使用 TypeScript + React
/remember 总是先运行测试再提交
```

### 4. 善用文件操作

```bash
/read src/app.py:100-150
/edit 简化这个复杂的 switch 语句
```

### 5. Git 集成

```bash
/commit                    # 自动生成有意义的 commit
/pr 创建 OAuth 登录功能   # 创建 PR
```

---

## 10. 常见问题排查

### `/doctor` 诊断清单

1. API 连接是否正常
2. 权限配置是否正确
3. 环境变量是否设置
4. Node.js 版本是否兼容

### 常见错误处理

| 错误 | 解决方案 |
|------|----------|
| `Permission denied` | 使用 `/permissions` 检查设置 |
| `Context overflow` | 使用 `/compact` 压缩上下文 |
| `Model unavailable` | 使用 `/model` 切换到可用模型 |
| `API timeout` | 检查网络连接或增加 timeout |

---

**最后更新**: 2026年5月31日
**版本**: Claude Code v2.1.143+
