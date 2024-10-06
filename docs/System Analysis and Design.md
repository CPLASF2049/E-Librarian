<h1 align="center">系统分析与设计文档</h1>

## 1. 系统分析

### 1.1 架构概述
云端书舍系统的架构设计为微服务架构，以支持系统的可扩展性、可维护性和可靠性。

### 1.2 架构组件
- **前端界面**：用户交互的界面，包括网页和移动应用。
- **身份验证服务**：处理用户注册、登录和权限验证。
- **图书管理服务**：提供图书搜索、借阅、归还和续借功能。
- **电子资源服务**：提供电子书籍和期刊的访问。
- **通知服务**：发送系统通知，如借阅到期提醒。
- **反馈服务**：收集用户反馈和建议。
- **库存管理服务**：管理图书库存和采购。
- **报告服务**：生成和提供系统报告，如借阅统计。
- **系统监控服务**：监控系统性能和健康状态。

### 1.3 数据库设计
- **用户表**：存储用户信息，包括用户ID、姓名、邮箱、加密密码等。
- **图书表**：存储图书信息，包括图书ID、标题、作者、ISBN、库存数量等。
- **借阅记录表**：存储借阅信息，包括记录ID、用户ID、图书ID、借阅日期、归还日期等。

### 1.4 安全性设计
- **数据加密**：敏感数据如用户密码进行加密存储。
- **身份验证**：使用JWT（JSON Web Tokens）进行用户身份验证。
- **授权**：基于角色的访问控制，确保用户只能访问授权资源。

## 2. 组件设计

### 2.1 组件视图
云端书舍系统的主要组件包括：
 
 <div align = "center">
 <img src="https://github.com/CPLASF2049/E-Librarian/blob/main/pics/组件图.png" width="100%" />
</div>

- **用户界面**：提供用户交互的前端界面。
- **账户管理服务**：处理用户注册、登录和信息更新。
- **图书管理服务**：处理图书的搜索、借阅、归还和续借。
- **电子资源服务**：提供电子资源的访问和管理。
- **通知服务**：发送通知给用户，包括邮件和短信。
- **反馈服务**：收集和处理用户反馈。
- **库存管理服务**：管理图书库存。
- **报告服务**：生成借阅统计和库存报告。
- **系统监控服务**：监控系统状态和性能。

## 3. 组件接口设计

### 3.1 接口定义
以下是系统组件之间的主要接口：

- **用户界面 <-> 账户管理服务**：
  - `POST /api/auth/register`：用户注册。
  - `POST /api/auth/login`：用户登录。
  - `PUT /api/user/{id}`：更新用户信息。

- **用户界面 <-> 图书管理服务**：
  - `GET /api/books?query={searchQuery}`：搜索图书。
  - `POST /api/books/{id}/borrow`：借阅图书。
  - `POST /api/books/{id}/return`：归还图书。
  - `POST /api/books/{id}/renew`：续借图书。

- **用户界面 <-> 电子资源服务**：
  - `GET /api/e-resources`：获取电子资源列表。
  - `GET /api/e-resources/{id}`：获取电子资源内容。

- **账户管理服务 <-> 数据库**：
  - `INSERT INTO users (username, password, email) VALUES (:username, :password, :email)`：创建新用户。
  - `UPDATE users SET email = :newEmail WHERE user_id = :id`：更新用户信息。

- **图书管理服务 <-> 数据库**：
  - `INSERT INTO borrowing_records (user_id, book_id, borrow_date, return_date) VALUES (:userId, :bookId, CURRENT_DATE, NULL)`：创建借阅记录。
  - `UPDATE books SET stock = stock - 1 WHERE book_id = :bookId AND stock > 0`：更新图书库存。

- **通知服务 <-> 邮件服务器/短信网关**：
  - `SMTP`：发送邮件通知。
  - `SMS API`：发送短信通知。

## 4. 系统流程分析
 <div align = "center">
 <img src="https://github.com/CPLASF2049/E-Librarian/blob/main/pics/借书时序图.png" width="80%" />
</div>
 <div align = "center">
 <img src="https://github.com/CPLASF2049/E-Librarian/blob/main/pics/还书时序图.png" width="80%" />
</div>
 <div align = "center">
 <img src="https://github.com/CPLASF2049/E-Librarian/blob/main/pics/历史记录时序图.png" width="50%" />
</div>

### 4.1 用户注册流程
1. 用户通过用户界面提交注册信息（用户名、密码、邮箱）。
2. 用户界面通过 `POST /api/auth/register` 发送注册信息到账户管理服务。
3. 账户管理服务验证注册信息的有效性。
4. 若信息有效，账户管理服务通过 `INSERT INTO users` 将新用户信息存储到数据库。
5. 账户管理服务返回注册成功或失败的响应。

### 4.2 图书借阅流程
1. 用户通过用户界面搜索图书。
2. 用户界面通过 `GET /api/books?query={searchQuery}` 发送搜索请求到图书管理服务。
3. 图书管理服务查询数据库并返回图书列表。
4. 用户选择要借阅的图书。
5. 用户界面通过 `POST /api/books/{id}/borrow` 发送借阅请求到图书管理服务。
6. 图书管理服务检查图书的库存状态。
7. 若图书可借，图书管理服务通过 `INSERT INTO borrowing_records` 创建借阅记录，并通过 `UPDATE books SET` 更新库存。
8. 图书管理服务返回借阅成功或失败的响应。

### 4.3 用户反馈流程
1. 用户通过用户界面提交反馈。
2. 用户界面通过 `POST /api/feedback` 发送反馈请求到反馈服务。
3. 反馈服务记录反馈信息。
4. 若需要，反馈服务将反馈信息转发给管理员。
5. 反馈服务返回反馈提交成功的响应。

### 4.4 系统监控流程
1. 系统监控服务定期检查系统状态和性能。
2. 若发现异常，系统监控服务记录详细信息。
3. 系统监控服务通过 `SMTP` 或 `SMS API` 通知管理员或自动触发修复流程。
4. 系统监控服务持续监控直至系统恢复正常。

