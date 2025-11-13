# 🚀 MCP MySQL Server v3.0.0 - 企业级数据库管理新体验

> **安全、灵活、强大的 MySQL 数据库管理工具，专为多环境协作而生**



**🔗 快速链接：**
- 📦 [NPM 包](https://www.npmjs.com/package/@liangshanli/mcp-server-mysql)
- 💻 [GitHub 仓库](https://github.com/liliangshan/mcp-server-mysql)

---

## 📖 项目简介

**MCP MySQL Server v3.0.0** 是一款专为企业级多环境数据库管理而设计的强大工具。它完美解决了开发、测试、生产环境数据库管理的核心痛点，通过创新的权限隔离和多实例支持，让数据库操作既安全又高效。

在生产环境中，只需启用只读模式（`READONLY=true`），系统将以最高优先级拦截所有非查询操作，确保生产数据绝对安全。同时，通过 `TOOL_PREFIX` 和 `PROJECT_NAME` 配置，您可以轻松管理多个数据库实例，每个实例拥有独立的工具名称和配置目录，实现完美的环境隔离。

更令人惊喜的是，配合 AI 助手使用，只需用自然语言发出指令，如"帮我查看下本地库和测试库的差异并生成文档和命令语句"，系统就能自动调用相应的工具，智能比对数据库结构差异，生成详细的差异文档和同步 SQL 脚本。所有 DDL 操作都会自动记录到独立的日志文件中，便于审计和追踪。

无论是企业级应用的数据库管理，还是开发团队的日常协作，MCP MySQL Server v3.0.0 都能为您提供安全、灵活、强大的数据库管理体验，让数据库操作变得简单而安全。

---

## ✨ v3.0.0 核心优势

### 🔒 **生产环境只读保护 - 最高优先级安全机制**

**问题场景：** 生产环境误操作导致数据丢失或服务中断

**解决方案：** 启用 `READONLY=true`，系统将**最高优先级**拦截所有非查询操作

```bash
# 生产环境配置 - 绝对安全
export READONLY=true
```

**效果：**
- ✅ 只允许 `SELECT` 和 `SHOW` 命令
- ✅ 自动拦截所有 DDL、DML、DROP、DELETE 操作
- ✅ 优先级高于所有其他权限设置
- ✅ 零配置，一键启用

**使用场景：**
- 生产环境数据库查询和分析
- 只读报表生成
- 数据审计和检查
- 防止误操作的安全屏障

---

### 🎯 **多环境权限隔离 - 开发/测试/生产完美分离**

**问题场景：** 不同环境需要不同权限级别，传统方案配置复杂且容易出错

**解决方案：** 通过环境变量灵活配置，每个环境独立权限策略

#### 📊 权限配置矩阵

| 环境 | READONLY | ALLOW_DDL | ALLOW_DROP | ALLOW_DELETE | 适用场景 |
|------|----------|-----------|------------|--------------|----------|
| **生产环境** | ✅ true | ❌ false | ❌ false | ❌ false | 只读查询，绝对安全 |
| **测试环境** | ❌ false | ✅ true | ✅ true | ✅ true | 完整测试，包括删除 |
| **开发环境** | ❌ false | ✅ true | ✅ true | ❌ false | 开发调试，禁止删除 |

#### 🔧 配置示例

**生产环境（只读）：**
```bash
export READONLY=true
export ALLOW_DDL=false
export ALLOW_DROP=false
export ALLOW_DELETE=false
```

**测试环境（完整权限）：**
```bash
export READONLY=false
export ALLOW_DDL=true
export ALLOW_DROP=true
export ALLOW_DELETE=true
```

**开发环境（安全开发）：**
```bash
export READONLY=false
export ALLOW_DDL=true
export ALLOW_DROP=true
export ALLOW_DELETE=false  # 防止误删
```

---

### 🔀 **多工具实例支持 - 快速比对数据库差异**

**问题场景：** 
- 需要同时连接多个数据库（本地、测试、生产）
- 需要快速比对不同环境的数据库结构差异
- 需要生成同步脚本或迁移文档

**解决方案：** 通过 `TOOL_PREFIX` 实现多实例隔离，每个实例独立工具和配置

#### 🎨 多实例配置示例

创建 `.cursor/mcp.json` 文件：

```json
{
  "mcpServers": {
    "local-mysql": {
      "disabled": false,
      "timeout": 60,
      "command": "npx",
      "args": ["@liangshanli/mcp-server-mysql"],
      "env": {
        "MYSQL_HOST": "localhost",
        "MYSQL_PORT": "3306",
        "MYSQL_USER": "dev_user",
        "MYSQL_PASSWORD": "dev_password",
        "MYSQL_DATABASE": "myapp_dev",
        "ALLOW_DDL": "true",
        "ALLOW_DROP": "true",
        "ALLOW_DELETE": "false",
        "TOOL_PREFIX": "local",
        "PROJECT_NAME": "本地开发环境"
      }
    },
    "test-mysql": {
      "disabled": false,
      "timeout": 60,
      "command": "npx",
      "args": ["@liangshanli/mcp-server-mysql"],
      "env": {
        "MYSQL_HOST": "test.example.com",
        "MYSQL_PORT": "3306",
        "MYSQL_USER": "test_user",
        "MYSQL_PASSWORD": "test_password",
        "MYSQL_DATABASE": "myapp_test",
        "READONLY": "false",
        "ALLOW_DDL": "true",
        "ALLOW_DROP": "true",
        "ALLOW_DELETE": "true",
        "TOOL_PREFIX": "test",
        "PROJECT_NAME": "测试环境"
      }
    },
    "prod-mysql": {
      "disabled": false,
      "timeout": 60,
      "command": "npx",
      "args": ["@liangshanli/mcp-server-mysql"],
      "env": {
        "MYSQL_HOST": "prod.example.com",
        "MYSQL_PORT": "3306",
        "MYSQL_USER": "prod_user",
        "MYSQL_PASSWORD": "prod_password",
        "MYSQL_DATABASE": "myapp_prod",
        "READONLY": "true",
        "ALLOW_DDL": "false",
        "ALLOW_DROP": "false",
        "ALLOW_DELETE": "false",
        "TOOL_PREFIX": "prod",
        "PROJECT_NAME": "生产环境"
      }
    }
  }
}
```

#### 🚀 多实例使用场景

**1. 快速比对数据库结构差异**

```bash
# 直接向 AI 助手发出指令，自动比对并生成结果
"帮我查看下本地库和测试库的差异并生成文档和命令语句"
```

**效果：**
- AI 助手自动调用 `local_get_database_info` 和 `test_get_database_info`
- 智能比对表结构、字段、索引等差异
- 自动生成差异文档（Markdown 格式）
- 自动生成同步命令语句（DDL SQL）
- 一键完成，无需手动操作

**2. 生成数据库同步脚本**

只需告诉 AI 助手：
```
"帮我生成从测试环境同步到生产环境的 DDL 语句"
```

**自动完成：**
- AI 助手调用 `test_sql_query` 查询测试数据库结构
- AI 助手调用 `prod_get_database_info` 获取生产环境信息
- 智能比对差异并生成同步 SQL 脚本
- 所有 DDL 操作自动记录到 `./.setting.test/ddl.sql`

**3. 生成数据库文档**

只需告诉 AI 助手：
```
"帮我生成生产环境的数据库结构文档"
```

**自动完成：**
- AI 助手调用 `prod_get_database_info` 获取生产环境信息
- 自动生成完整的数据库结构文档（Markdown 格式）
- 包含表结构、字段类型、索引、外键、权限等信息
- 支持导出为文档文件，便于团队共享

#### 💡 多实例核心优势

| 特性 | 说明 | 价值 |
|------|------|------|
| **工具隔离** | 每个实例独立工具名称（`local_sql_query`, `test_sql_query`, `prod_sql_query`） | 避免混淆，清晰区分环境 |
| **配置隔离** | 日志和 DDL 文件独立存储（`./.setting.local/`, `./.setting.test/`, `./.setting.prod/`） | 便于管理和审计 |
| **权限隔离** | 每个实例独立权限配置 | 生产环境绝对安全，开发环境灵活调试 |
| **项目品牌** | 每个实例独立项目名称 | 便于识别和管理 |

---

## 🎁 v3.0.0 完整功能清单

### 🔐 安全特性
- ✅ **只读模式**：生产环境只读保护，最高优先级拦截
- ✅ **权限控制**：DDL、DROP、DELETE 独立控制
- ✅ **操作审计**：完整的操作日志记录
- ✅ **DDL 追踪**：自动记录所有 DDL 操作到 SQL 文件

### 🛠️ 开发特性
- ✅ **工具前缀**：多实例支持，工具名称隔离
- ✅ **项目品牌**：自定义工具描述和标识
- ✅ **配置隔离**：每个实例独立日志和配置目录
- ✅ **权限检查**：详细的权限状态查询工具

### 📊 管理特性
- ✅ **操作日志**：完整的请求和响应日志
- ✅ **DDL 日志**：可执行的 DDL 操作历史
- ✅ **数据库信息**：快速获取数据库和表信息
- ✅ **健康检查**：自动连接池健康监控

---

## 🚀 快速开始

### 安装

```bash
# 全局安装
npm install -g @liangshanli/mcp-server-mysql

# 或使用 npx（推荐）
npx @liangshanli/mcp-server-mysql
```

### 基础配置

```bash
export MYSQL_HOST=localhost
export MYSQL_PORT=3306
export MYSQL_USER=root
export MYSQL_PASSWORD=your_password
export MYSQL_DATABASE=your_database
export READONLY=false
export TOOL_PREFIX=myproject
export PROJECT_NAME=我的项目
```

### 编辑器集成（Cursor）

创建 `.cursor/mcp.json`：

```json
{
  "mcpServers": {
    "mysql": {
      "command": "npx",
      "args": ["@liangshanli/mcp-server-mysql"],
      "env": {
        "MYSQL_HOST": "localhost",
        "MYSQL_PORT": "3306",
        "MYSQL_USER": "root",
        "MYSQL_PASSWORD": "your_password",
        "MYSQL_DATABASE": "your_database",
        "READONLY": "false",
        "ALLOW_DDL": "true",
        "ALLOW_DROP": "false",
        "ALLOW_DELETE": "false",
        "TOOL_PREFIX": "myproject",
        "PROJECT_NAME": "我的项目"
      }
    }
  }
}
```

---

## 📚 相关资源

- 📦 [NPM 包](https://www.npmjs.com/package/@liangshanli/mcp-server-mysql)
- 🐛 [问题反馈](https://github.com/liliangshan/mcp-server-mysql/issues)
- 💻 [GitHub 仓库](https://github.com/liliangshan/mcp-server-mysql)

---

## 🎯 适用场景

### 企业级应用
- ✅ 多环境数据库管理（开发/测试/生产）
- ✅ 生产环境只读查询和分析
- ✅ 数据库结构变更追踪和审计
- ✅ 跨环境数据库同步

### 开发团队
- ✅ 快速比对数据库差异
- ✅ 生成数据库同步脚本
- ✅ 自动生成数据库文档
- ✅ 安全的数据库操作实践

### 运维团队
- ✅ 生产环境安全查询
- ✅ 操作日志审计
- ✅ DDL 变更追踪
- ✅ 数据库健康监控

---

## 💬 用户反馈

> "v3.0.0 的多实例功能让我们可以同时管理多个环境的数据库，生产环境的只读模式让我们放心使用，再也不用担心误操作了！" - 某企业开发团队

> "权限隔离功能非常实用，开发环境可以自由调试，生产环境绝对安全，完美解决了我们的痛点。" - 某互联网公司 DBA

---

## 📝 版本历史

### v3.0.0 (当前版本)
- 🆕 只读模式支持（最高优先级）
- 🆕 工具前缀和项目品牌
- 🆕 多实例支持
- 🆕 增强的权限检查
- 🆕 改进的日志路径配置

### v2.0.1
- ✅ DDL SQL 日志记录
- ✅ 成功操作追踪
- ✅ 时间戳条目

---

## 📄 许可证

MIT License - 自由使用，商业友好

---

**立即体验 v3.0.0，开启安全高效的数据库管理之旅！** 🚀

```bash
npm install -g @liangshanli/mcp-server-mysql@3.0.0
```

---

# 🚀 MCP MySQL Server v3.0.0 - Enterprise-Grade Database Management Experience

> **Secure, Flexible, and Powerful MySQL Database Management Tool for Multi-Environment Collaboration**

**🔗 Quick Links:**
- 📦 [NPM Package](https://www.npmjs.com/package/@liangshanli/mcp-server-mysql)
- 💻 [GitHub Repository](https://github.com/liliangshan/mcp-server-mysql)

---

## 📖 Project Overview

**MCP MySQL Server v3.0.0** is a powerful tool designed for enterprise-grade multi-environment database management. It perfectly addresses the core pain points of database management across development, testing, and production environments. Through innovative permission isolation and multi-instance support, it makes database operations both secure and efficient.

In production environments, simply enable read-only mode (`READONLY=true`), and the system will intercept all non-query operations with the highest priority, ensuring absolute security of production data. Meanwhile, through `TOOL_PREFIX` and `PROJECT_NAME` configuration, you can easily manage multiple database instances, each with independent tool names and configuration directories, achieving perfect environment isolation.

What's even more impressive is that when used with AI assistants, you only need to issue natural language commands, such as "Help me check the differences between the local and test databases and generate documentation and command statements." The system will automatically call the corresponding tools, intelligently compare database structure differences, and generate detailed difference documentation and synchronization SQL scripts. All DDL operations are automatically recorded in independent log files for easy auditing and tracking.

Whether for enterprise-level application database management or daily collaboration within development teams, MCP MySQL Server v3.0.0 provides a secure, flexible, and powerful database management experience, making database operations simple and safe.

---

## ✨ v3.0.0 Core Advantages

### 🔒 **Production Read-Only Protection - Highest Priority Security Mechanism**

**Problem Scenario:** Production environment misoperations leading to data loss or service interruption

**Solution:** Enable `READONLY=true`, and the system will intercept all non-query operations with **highest priority**

```bash
# Production environment configuration - absolutely secure
export READONLY=true
```

**Effects:**
- ✅ Only `SELECT` and `SHOW` commands are allowed
- ✅ Automatically intercepts all DDL, DML, DROP, DELETE operations
- ✅ Priority higher than all other permission settings
- ✅ Zero configuration, one-click enablement

**Use Cases:**
- Production environment database queries and analysis
- Read-only report generation
- Data auditing and inspection
- Security barrier against misoperations

---

### 🎯 **Multi-Environment Permission Isolation - Perfect Separation of Dev/Test/Prod**

**Problem Scenario:** Different environments require different permission levels, traditional solutions are complex and error-prone

**Solution:** Flexible configuration through environment variables, independent permission policies for each environment

#### 📊 Permission Configuration Matrix

| Environment | READONLY | ALLOW_DDL | ALLOW_DROP | ALLOW_DELETE | Use Case |
|-------------|----------|-----------|------------|--------------|----------|
| **Production** | ✅ true | ❌ false | ❌ false | ❌ false | Read-only queries, absolutely secure |
| **Testing** | ❌ false | ✅ true | ✅ true | ✅ true | Complete testing, including deletion |
| **Development** | ❌ false | ✅ true | ✅ true | ❌ false | Development debugging, deletion prohibited |

#### 🔧 Configuration Examples

**Production Environment (Read-Only):**
```bash
export READONLY=true
export ALLOW_DDL=false
export ALLOW_DROP=false
export ALLOW_DELETE=false
```

**Testing Environment (Full Permissions):**
```bash
export READONLY=false
export ALLOW_DDL=true
export ALLOW_DROP=true
export ALLOW_DELETE=true
```

**Development Environment (Safe Development):**
```bash
export READONLY=false
export ALLOW_DDL=true
export ALLOW_DROP=true
export ALLOW_DELETE=false  # Prevent accidental deletion
```

---

### 🔀 **Multi-Tool Instance Support - Rapid Database Difference Comparison**

**Problem Scenarios:**
- Need to connect to multiple databases simultaneously (local, test, production)
- Need to quickly compare database structure differences across environments
- Need to generate synchronization scripts or migration documentation

**Solution:** Multi-instance isolation through `TOOL_PREFIX`, each instance with independent tools and configurations

#### 🎨 Multi-Instance Configuration Example

Create `.cursor/mcp.json` file:

```json
{
  "mcpServers": {
    "local-mysql": {
      "disabled": false,
      "timeout": 60,
      "command": "npx",
      "args": ["@liangshanli/mcp-server-mysql"],
      "env": {
        "MYSQL_HOST": "localhost",
        "MYSQL_PORT": "3306",
        "MYSQL_USER": "dev_user",
        "MYSQL_PASSWORD": "dev_password",
        "MYSQL_DATABASE": "myapp_dev",
        "ALLOW_DDL": "true",
        "ALLOW_DROP": "true",
        "ALLOW_DELETE": "false",
        "TOOL_PREFIX": "local",
        "PROJECT_NAME": "Local Development Environment"
      }
    },
    "test-mysql": {
      "disabled": false,
      "timeout": 60,
      "command": "npx",
      "args": ["@liangshanli/mcp-server-mysql"],
      "env": {
        "MYSQL_HOST": "test.example.com",
        "MYSQL_PORT": "3306",
        "MYSQL_USER": "test_user",
        "MYSQL_PASSWORD": "test_password",
        "MYSQL_DATABASE": "myapp_test",
        "READONLY": "false",
        "ALLOW_DDL": "true",
        "ALLOW_DROP": "true",
        "ALLOW_DELETE": "true",
        "TOOL_PREFIX": "test",
        "PROJECT_NAME": "Testing Environment"
      }
    },
    "prod-mysql": {
      "disabled": false,
      "timeout": 60,
      "command": "npx",
      "args": ["@liangshanli/mcp-server-mysql"],
      "env": {
        "MYSQL_HOST": "prod.example.com",
        "MYSQL_PORT": "3306",
        "MYSQL_USER": "prod_user",
        "MYSQL_PASSWORD": "prod_password",
        "MYSQL_DATABASE": "myapp_prod",
        "READONLY": "true",
        "ALLOW_DDL": "false",
        "ALLOW_DROP": "false",
        "ALLOW_DELETE": "false",
        "TOOL_PREFIX": "prod",
        "PROJECT_NAME": "Production Environment"
      }
    }
  }
}
```

#### 🚀 Multi-Instance Use Cases

**1. Rapid Database Structure Difference Comparison**

```bash
# Issue commands directly to AI assistant, automatically compare and generate results
"Help me check the differences between the local and test databases and generate documentation and command statements"
```

**Effects:**
- AI assistant automatically calls `local_get_database_info` and `test_get_database_info`
- Intelligently compares table structures, fields, indexes, etc.
- Automatically generates difference documentation (Markdown format)
- Automatically generates synchronization command statements (DDL SQL)
- One-click completion, no manual operation required

**2. Generate Database Synchronization Scripts**

Just tell the AI assistant:
```
"Help me generate DDL statements to synchronize from test environment to production environment"
```

**Automatically Completed:**
- AI assistant calls `test_sql_query` to query test database structure
- AI assistant calls `prod_get_database_info` to get production environment information
- Intelligently compares differences and generates synchronization SQL scripts
- All DDL operations automatically recorded to `./.setting.test/ddl.sql`

**3. Generate Database Documentation**

Just tell the AI assistant:
```
"Help me generate database structure documentation for the production environment"
```

**Automatically Completed:**
- AI assistant calls `prod_get_database_info` to get production environment information
- Automatically generates complete database structure documentation (Markdown format)
- Includes table structures, field types, indexes, foreign keys, permissions, etc.
- Supports export as documentation files for team sharing

#### 💡 Multi-Instance Core Advantages

| Feature | Description | Value |
|---------|-------------|-------|
| **Tool Isolation** | Each instance has independent tool names (`local_sql_query`, `test_sql_query`, `prod_sql_query`) | Avoid confusion, clear environment distinction |
| **Config Isolation** | Logs and DDL files stored independently (`./.setting.local/`, `./.setting.test/`, `./.setting.prod/`) | Easy management and auditing |
| **Permission Isolation** | Each instance has independent permission configuration | Production absolutely secure, development flexible debugging |
| **Project Branding** | Each instance has independent project name | Easy identification and management |

---

## 🎁 v3.0.0 Complete Feature List

### 🔐 Security Features
- ✅ **Read-Only Mode**: Production read-only protection, highest priority interception
- ✅ **Permission Control**: Independent control of DDL, DROP, DELETE
- ✅ **Operation Auditing**: Complete operation log recording
- ✅ **DDL Tracking**: Automatically records all DDL operations to SQL files

### 🛠️ Development Features
- ✅ **Tool Prefix**: Multi-instance support, tool name isolation
- ✅ **Project Branding**: Custom tool descriptions and identification
- ✅ **Config Isolation**: Each instance has independent logs and configuration directories
- ✅ **Permission Check**: Detailed permission status query tool

### 📊 Management Features
- ✅ **Operation Logs**: Complete request and response logs
- ✅ **DDL Logs**: Executable DDL operation history
- ✅ **Database Info**: Quick access to database and table information
- ✅ **Health Check**: Automatic connection pool health monitoring

---

## 🚀 Quick Start

### Installation

```bash
# Global installation
npm install -g @liangshanli/mcp-server-mysql

# Or use npx (recommended)
npx @liangshanli/mcp-server-mysql
```

### Basic Configuration

```bash
export MYSQL_HOST=localhost
export MYSQL_PORT=3306
export MYSQL_USER=root
export MYSQL_PASSWORD=your_password
export MYSQL_DATABASE=your_database
export READONLY=false
export TOOL_PREFIX=myproject
export PROJECT_NAME=My Project
```

### Editor Integration (Cursor)

Create `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "mysql": {
      "command": "npx",
      "args": ["@liangshanli/mcp-server-mysql"],
      "env": {
        "MYSQL_HOST": "localhost",
        "MYSQL_PORT": "3306",
        "MYSQL_USER": "root",
        "MYSQL_PASSWORD": "your_password",
        "MYSQL_DATABASE": "your_database",
        "READONLY": "false",
        "ALLOW_DDL": "true",
        "ALLOW_DROP": "false",
        "ALLOW_DELETE": "false",
        "TOOL_PREFIX": "myproject",
        "PROJECT_NAME": "My Project"
      }
    }
  }
}
```

---

## 📚 Related Resources

- 📦 [NPM Package](https://www.npmjs.com/package/@liangshanli/mcp-server-mysql)
- 🐛 [Issue Tracker](https://github.com/liliangshan/mcp-server-mysql/issues)
- 💻 [GitHub Repository](https://github.com/liliangshan/mcp-server-mysql)

---

## 🎯 Use Cases

### Enterprise Applications
- ✅ Multi-environment database management (dev/test/prod)
- ✅ Production read-only queries and analysis
- ✅ Database structure change tracking and auditing
- ✅ Cross-environment database synchronization

### Development Teams
- ✅ Rapid database difference comparison
- ✅ Generate database synchronization scripts
- ✅ Automatically generate database documentation
- ✅ Safe database operation practices

### Operations Teams
- ✅ Production environment secure queries
- ✅ Operation log auditing
- ✅ DDL change tracking
- ✅ Database health monitoring

---

## 💬 User Feedback

> "The multi-instance feature in v3.0.0 allows us to manage databases across multiple environments simultaneously. The read-only mode for production gives us peace of mind, and we no longer worry about misoperations!" - Enterprise Development Team

> "The permission isolation feature is very practical. Development environments can debug freely, while production is absolutely secure. It perfectly solves our pain points." - Internet Company DBA

---

## 📝 Version History

### v3.0.0 (Current Version)
- 🆕 Read-only mode support (highest priority)
- 🆕 Tool prefix and project branding
- 🆕 Multi-instance support
- 🆕 Enhanced permission checking
- 🆕 Improved log path configuration

### v2.0.1
- ✅ DDL SQL logging
- ✅ Success operation tracking
- ✅ Timestamp entries

---

## 📄 License

MIT License - Free to use, business-friendly

---

**Experience v3.0.0 now and start your secure and efficient database management journey!** 🚀

```bash
npm install -g @liangshanli/mcp-server-mysql@3.0.0
```

