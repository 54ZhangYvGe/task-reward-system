# 任务悬赏系统

一个基于 Node.js + Express + SQLite 的任务悬赏平台，用户可以发布任务并设置悬赏，其他用户可以接单完成任务。

## 🚀 功能特性

- **任务管理**：创建、查看、接单、完成任务
- **用户系统**：用户注册、登录、等级管理
- **悬赏机制**：任务发布者设置悬赏金额
- **等级限制**：任务可设置最低等级要求
- **并发控制**：防止多人同时抢单的竞态条件

## 🛠️ 技术栈

- **后端**：Node.js + Express.js
- **数据库**：SQLite3
- **API**：RESTful API 设计

## 📁 项目结构

```
Task/
├── backend/                 # 后端代码
│   ├── routes/             
│   │   └── tasks.js        # 任务相关API路由
│   ├── db.js               # 数据库连接配置
│   ├── server.js           # 服务器入口文件
│   ├── init_db.js          # 数据库初始化脚本
│   └── insert_test_task2.js # 测试数据插入脚本
├── frontend/               # 前端代码（待开发）
├── docs/                   # 项目文档
│   └── er.md              # 数据库ER图
├── db.sqlite              # SQLite数据库文件
├── package.json           # 项目依赖配置
└── README.md              # 项目说明文档
```

## 🗄️ 数据库设计

### 用户表 (user)
- `id`: 用户唯一标识
- `username`: 用户名
- `password_hash`: 密码哈希
- `level`: 用户等级
- `created_at`: 创建时间

### 任务表 (task)
- `id`: 任务唯一标识
- `title`: 任务标题
- `description`: 任务描述
- `reward`: 悬赏金额
- `min_level`: 最低等级要求
- `status`: 任务状态（OPEN/TAKEN/COMPLETED）
- `publisher_id`: 发布者ID
- `taker_id`: 接单者ID
- `created_at`: 创建时间

## 🚀 快速开始

### 环境要求
- Node.js >= 14.0
- npm >= 6.0

### 安装依赖
```bash
npm install
```

### 初始化数据库
```bash
node backend/init_db.js
```

### 插入测试数据（可选）
```bash
node backend/insert_test_task2.js
```

### 启动服务器
```bash
node backend/server.js
```

服务器将在 `http://localhost:3000` 启动

## 📚 API 文档

### 基础URL
```
http://localhost:3000/api
```

### 任务相关接口

#### 1. 获取所有任务
```http
GET /api/tasks
```

**响应示例：**
```json
[
  {
    "id": 1,
    "title": "第二个测试任务",
    "description": "这是第二个测试任务",
    "reward": 200,
    "min_level": 1,
    "status": "OPEN",
    "publisher_id": 1,
    "taker_id": null,
    "created_at": "2024-01-01 10:00:00"
  }
]
```

#### 2. 获取单个任务详情
```http
GET /api/tasks/:id
```

**参数：**
- `id`: 任务ID

#### 3. 接单
```http
POST /api/tasks/:id/accept
```

**请求体：**
```json
{
  "taker_id": 2
}
```

**功能说明：**
- 使用事务确保并发安全
- 只有状态为 OPEN 的任务才能被接单
- 防止重复接单

#### 4. 完成任务
```http
POST /api/tasks/:id/finish
```

**请求体：**
```json
{
  "taker_id": 2
}
```

**功能说明：**
- 只有接单者可以标记任务完成
- 任务状态必须为 TAKEN

#### 5. 发布新任务
```http
POST /api/tasks
```

**请求体：**
```json
{
  "title": "任务标题",
  "description": "任务详细描述",
  "reward": 500,
  "min_level": 1,
  "publisher_id": 1
}
```

**响应示例：**
```json
{
  "message": "Task created successfully",
  "task": {
    "id": 3,
    "title": "任务标题",
    "description": "任务详细描述",
    "reward": 500,
    "min_level": 1,
    "status": "OPEN",
    "publisher_id": 1,
    "taker_id": null,
    "created_at": "2024-01-01 12:00:00"
  }
}
```

**功能说明：**
- 发布者可以创建新任务
- 必填字段：title, description, reward, publisher_id
- min_level 默认为1
- 新任务状态自动设为 OPEN

#### 6. 获取我接的任务
```http
GET /api/tasks/my/:takerId
```

**参数：**
- `takerId`: 接单者用户ID

**响应示例：**
```json
{
  "message": "My tasks retrieved successfully",
  "count": 2,
  "tasks": [
    {
      "id": 1,
      "title": "我接的任务1",
      "description": "任务描述",
      "reward": 200,
      "min_level": 1,
      "status": "TAKEN",
      "publisher_id": 1,
      "taker_id": 2,
      "created_at": "2024-01-01 10:00:00"
    }
  ]
}
```

**功能说明：**
- 查看指定用户接的所有任务
- 按创建时间倒序排列
- 包含任务数量统计

## 🔧 开发计划

### 已完成功能
- ✅ 基础数据库设计
- ✅ 任务查看接口 (GET /api/tasks, GET /api/tasks/:id)
- ✅ 任务接单功能 (POST /api/tasks/:id/accept)
- ✅ 任务完成功能 (POST /api/tasks/:id/finish)
- ✅ 任务发布接口 (POST /api/tasks)
- ✅ 我的任务查询 (GET /api/tasks/my/:takerId)
- ✅ 并发控制（接单时防止重复抢单）
- ✅ 基础参数校验和错误处理

### 待开发功能
- 🔲 用户注册/登录系统
- 🔲 JWT 认证中间件
- 🔲 用户等级系统
- 🔲 前端界面（任务列表、详情、我的任务页）
- 🔲 单元测试（API接口测试）
- 🔲 部署脚本（Node.js + Nginx）
- 🔲 悬赏金管理
- 🔲 任务评价系统

## 🤝 开发指南

### 代码规范
- 使用 2 空格缩进
- 函数和变量命名使用驼峰命名法
- 数据库字段使用下划线命名法
- 添加适当的错误处理

## 📝 注意事项

1. **数据库事务**：接单操作使用了数据库事务来防止并发问题
2. **错误处理**：所有API都包含了适当的错误处理和状态码
3. **数据验证**：需要在后续开发中添加更严格的数据验证
4. **安全性**：当前版本缺少身份验证，生产环境需要添加JWT等认证机制