# 个人网盘系统开发文档

## 1. 系统概述

个人网盘系统是一个基于Java Web的文件存储和管理平台，提供文件上传、下载、分享等功能。

### 1.1 技术栈
- 后端：Java, Servlet, MySQL
- 前端：HTML, CSS, JavaScript, jQuery
- 服务器：Tomcat
- 构建工具：Maven

### 1.2 安全特性
- CSRF防护
- 会话超时管理
- 密码加密存储
- 文件类型验证
- 请求频率限制
- 安全日志记录

## 2. 系统架构

### 2.1 目录结构
```
src/
├── main/
│   ├── java/
│   │   └── com/
│   │       └── personal/
│   │           └── disk/
│   │               ├── filter/      # 过滤器
│   │               ├── model/       # 数据模型
│   │               ├── servlet/     # 控制器
│   │               ├── service/     # 业务逻辑
│   │               └── util/        # 工具类
│   ├── resources/   # 配置文件
│   └── webapp/      # Web资源
│       ├── WEB-INF/
│       ├── js/      # JavaScript文件
│       └── css/     # 样式文件
└── test/            # 测试代码
```

### 2.2 核心组件

#### 2.2.1 过滤器
- `SessionFilter`: 处理会话超时和CSRF防护

#### 2.2.2 工具类
- `CsrfUtil`: CSRF令牌管理
- `SecurityUtil`: 密码加密和验证
- `LogUtil`: 日志记录
- `DatabaseUtil`: 数据库连接管理

#### 2.2.3 服务类
- `UserService`: 用户管理
- `FileService`: 文件管理
- `ShareService`: 文件分享

## 3. 安全机制

### 3.1 CSRF防护
- 使用随机生成的令牌
- 令牌存储在服务器端
- 通过HTTP头传递令牌
- 自动清理过期令牌

### 3.2 会话管理
- 30分钟会话超时
- 自动检测和处理过期会话
- 安全的Cookie配置

### 3.3 密码安全
- 使用SHA-256算法加密
- 随机盐值
- 密码验证机制

### 3.4 日志记录
- 安全事件记录
- 操作日志
- 错误日志

## 4. 数据库设计

### 4.1 用户表(users)
```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL UNIQUE,
    password VARCHAR(100) NOT NULL,
    salt VARCHAR(24) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### 4.2 文件表(files)
```sql
CREATE TABLE files (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    file_name VARCHAR(255) NOT NULL,
    file_path VARCHAR(500) NOT NULL,
    file_size BIGINT NOT NULL,
    file_type VARCHAR(50),
    parent_id INT DEFAULT NULL,
    is_folder BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id),
    FOREIGN KEY (parent_id) REFERENCES files(id)
);
```

### 4.3 分享表(shares)
```sql
CREATE TABLE shares (
    id INT PRIMARY KEY AUTO_INCREMENT,
    file_id INT NOT NULL,
    user_id INT NOT NULL,
    share_code VARCHAR(32) NOT NULL UNIQUE,
    expire_time TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (file_id) REFERENCES files(id),
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

## 5. API接口

### 5.1 用户相关
- POST /api/login - 用户登录
- POST /api/register - 用户注册

### 5.2 文件相关
- GET /api/files - 获取文件列表
- POST /api/files - 上传文件
- DELETE /api/files/{id} - 删除文件
- GET /api/files/{id} - 下载文件

### 5.3 分享相关
- POST /api/shares/{fileId} - 创建分享
- GET /api/shares/{code} - 获取分享信息
- DELETE /api/shares/{id} - 删除分享

## 6. 部署说明

### 6.1 环境要求
- JDK 1.8+
- MySQL 5.7+
- Tomcat 8.5+
- Maven 3.6+

### 6.2 部署步骤
1. 克隆代码库
2. 配置数据库连接
3. 执行数据库脚本
4. 构建项目：`mvn clean package`
5. 部署WAR文件到Tomcat

### 6.3 配置文件
- `database.properties`: 数据库连接配置
- `web.xml`: Web应用配置
- `pom.xml`: 项目依赖配置

## 7. 安全配置

### 7.1 HTTPS配置
- 在Tomcat中配置SSL证书
- 启用HTTPS重定向

### 7.2 文件上传限制
- 最大文件大小：10MB
- 允许的文件类型：配置在FileService中

### 7.3 会话配置
- 超时时间：30分钟
- Cookie安全属性：HttpOnly, Secure

## 8. 常见问题

### 8.1 部署问题
- 确保数据库连接配置正确
- 检查文件上传目录权限
- 验证HTTPS配置

### 8.2 安全问题
- 定期检查安全日志
- 及时更新依赖库
- 监控异常访问

## 9. 版本历史

### v1.0.0
- 初始版本
- 基本文件管理功能
- 用户认证系统
- 文件分享功能

### v1.1.0
- 添加安全特性
- 改进错误处理
- 优化性能

## 10. 联系方式

如有问题，请联系系统管理员。 