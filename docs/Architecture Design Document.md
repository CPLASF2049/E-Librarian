<h1 align="center">系统架构设计文档</h1>

## 1. 引言

本文档旨在描述无人值守图书馆系统的架构设计，包括架构样式、参考模型、参考架构、质量场景、框架和设计模式。

## 2. 架构样式

### 2.1 包含边缘层的三层客户端-服务器样式 (C/S)
无人值守图书馆系统采用包含边缘层的三层C/S样式，将系统分为表示层、业务逻辑层、数据层，并引入边缘层以提高安全性和性能。

#### 2.1.1 表示层
- 组件：客户端用户界面，如Web页面、移动应用界面。
- 职责：处理用户输入，展示业务逻辑层处理后的结果。

#### 2.1.2 业务逻辑层
- 组件：应用服务器。
- 职责：处理业务逻辑，如账户管理、图书检索、借阅管理等。

#### 2.1.3 数据层
- 组件：数据库服务器。
- 职责：管理数据持久化，如用户数据、图书信息、借阅记录等。

#### 2.1.4 边缘层
- 组件：安全节点。
- 职责：在网络边缘部署，用于初步过滤流量，减少恶意访问到达核心服务器的可能性，快速识别和响应安全威胁。

### 2.2 多层分布式结构
系统采用多层分布式结构，使用Web服务器集群和应用服务器集群，支持动态负载平衡和容错机制，提高系统的稳定性和可用性。

### 2.3 管道-过滤器样式
- **应用说明**: 系统内部处理信息流程时，采用管道-过滤器样式，每个过滤器负责一部分处理逻辑，数据通过管道在过滤器间传递。
- **过滤器**: 完成特定处理任务，如日志记录、数据验证等。
- **管道**: 作为数据流的通道，负责在过滤器间传递数据。

## 3. 参考模型

### 3.1 ISO/OSI七层参考模型
在网络通信方面，系统遵循ISO/OSI七层模型来确保不同系统间有效的数据交换。

- **应用层**：提供用户接口服务，如Web页面与用户交互。
- **表示层**：确保数据在网络中的传输格式正确，如加密解密、压缩解压。
- **会话层**：建立、管理和终止表示层会话。
- **传输层**：确保数据的可靠传输，如TCP/IP协议。
- **网络层**：负责数据包从源到目的地的传输和路由选择。
- **数据链路层**：在相邻的网络节点之间传输数据帧。
- **物理层**：传输原始比特流，定义物理设备标准。

### 3.2 数据库管理系统的参考模型
系统的数据存储和访问遵循数据库管理系统的参考模型，以确保数据的一致性和完整性。

- **外模式**：用户视图，定义用户与数据库交互的方式。
- **概念模式**：全局逻辑视图，定义数据的逻辑结构。
- **内模式**：存储物理视图，定义数据的物理存储方式。
- **映射**：外模式到概念模式、概念模式到内模式的映射。

### 3.3 系统可用性参考模型
为确保系统的高可用性，定义以下参考模型：

- **冗余层**：确保关键组件的冗余，如数据库镜像、负载均衡。
- **故障转移层**：在组件故障时自动切换，如故障转移集群。
- **备份与恢复层**：定期备份数据，并确保能够快速恢复，如快照、备份策略。

### 4. 参考架构详细说明

#### 4.1 组件视图详细说明

**用户接口**：
- **自助服务终端**：提供触摸屏操作界面，用户可进行图书查询、借阅、归还等操作。
- **移动应用**：用户可通过手机应用访问图书馆服务，实现预约、续借等功能。
- **Web门户**：提供全面的图书馆服务，包括资源搜索、活动信息、新闻发布等。

**业务逻辑组件**：
- **账户管理服务**：负责用户账户的创建、更新、删除以及权限管理。
- **图书检索服务**：提供图书搜索功能，支持关键词、作者、ISBN等多条件查询。
- **借阅管理服务**：处理借阅请求，更新借阅状态，计算逾期费用等。
- **预约管理服务**：允许用户预约图书，管理预约队列。

**数据访问对象**：
- **用户数据访问对象**：与用户数据表交互，执行用户数据的CRUD操作。
- **图书数据访问对象**：与图书数据表交互，执行图书数据的CRUD操作。
- **借阅数据访问对象**：与借阅记录数据表交互，执行借阅记录的CRUD操作。

**数据库**：
- **用户数据库**：存储用户信息，包括个人资料、借阅历史、预约信息等。
- **图书数据库**：存储图书信息，包括书名、作者、ISBN、库存状态等。
- **借阅数据库**：存储借阅记录，包括借阅日期、归还日期、逾期状态等。

#### 4.2 数据流视图详细说明

1. **用户请求**：用户通过自助服务终端、移动应用或Web门户发起请求。
2. **请求分发**：请求被发送到应用服务器，根据请求类型分发到相应的业务逻辑组件。
3. **业务逻辑处理**：业务逻辑组件处理请求，可能需要调用多个数据访问对象。
4. **数据访问**：数据访问对象与数据库交互，执行必要的CRUD操作。
5. **结果生成**：处理结果被封装成响应消息，返回给用户接口。
6. **用户反馈**：用户接口展示处理结果，提供反馈给用户。

### 5. 质量场景详细说明

#### 5.1 性能场景详细说明

- **系统响应性**：系统应保证在高峰时段也能快速响应用户操作，如图书检索、借阅和归还等，确保操作流畅无卡顿。
- **缓存策略**：对于频繁访问的数据，如热门图书信息，系统应采用缓存机制，减少数据库访问次数，提高数据检索速度。
- **负载均衡**：系统应部署负载均衡器，合理分配用户请求到不同的服务器，避免单点过载，确保系统整体性能稳定。
- **性能监控**：系统应集成性能监控工具，实时监控系统运行状态，及时发现并解决性能瓶颈。

#### 5.2 可靠性场景详细说明

- **系统冗余**：关键服务如数据库和应用服务器应实现冗余部署，确保单点故障不会导致服务中断。
- **自动故障转移**：系统应具备自动故障转移机制，当主服务出现故障时，能够无缝切换到备用服务，保证服务的连续性。
- **定期维护**：系统应定期进行维护和更新，包括软件补丁、硬件检查等，以预防潜在的故障和安全风险。
- **数据完整性**：系统应实施数据校验和备份机制，确保数据在传输和存储过程中的完整性和一致性。

#### 5.3 安全性场景详细说明

- **访问控制**：系统应实施严格的访问控制策略，确保只有授权用户才能访问敏感数据。
- **数据加密**：敏感数据在传输和存储时必须进行加密处理，防止数据泄露。
- **入侵检测**：系统应具备入侵检测系统，及时发现并响应安全威胁。
- **用户注册和登录保护**：提供必要的人机检测，防止机器人系统入侵。对密码提供一定的强度要求和显示密码强度提示。要求用户提供手机、邮箱等方式便于找回密码，对连续5次输入错误密码的账户短暂冻结其登录权限。

#### 5.4 可用性场景详细说明

- **系统监控**：系统应实现全面的监控体系，包括硬件状态、服务运行情况、用户访问量等，以便及时发现并解决问题。
- **灾难恢复**：系统应制定详细的灾难恢复计划，包括数据备份、服务恢复流程等，确保在发生灾难时能够快速恢复服务。
- **用户支持**：系统应提供用户支持服务，包括在线帮助、FAQ、用户反馈渠道等，帮助用户解决使用中遇到的问题。

#### 5.5 可修改性场景详细说明

- **模块化设计**：系统应采用模块化设计，使得各个功能模块可以独立更新和维护，而不会影响到其他模块的正常运行。
- **配置管理**：系统应提供灵活的配置管理功能，允许管理员通过配置文件轻松调整系统参数和业务规则，而无需修改代码。
- **自动化测试**：系统开发过程中应实施自动化测试，确保每次更新后系统的功能和性能符合预期。

#### 5.6 可移植性场景详细说明

- **跨平台兼容性**：系统应设计为跨平台兼容，能够在不同的操作系统和硬件环境中运行，减少对特定环境的依赖。
- **标准化接口**：系统应使用标准化的接口和协议，如RESTful API，以提高与其他系统的互操作性和未来的扩展性。
- **文档和示例**：系统应提供详细的开发文档和示例代码，帮助开发者理解和使用系统的接口，促进系统的移植和集成。

#### 5.7 易用性场景详细说明

- **用户研究**：通过用户研究了解用户需求和使用习惯，设计符合用户期望的界面。
- **界面设计**：界面设计应简洁直观，提供清晰的导航和操作提示，减少用户学习成本。
- **特殊用户支持**：系统在用户注册、登录界面，提供大字简易版、英语版本供特殊读者选择，并将对应界面用于后续的显示中。对于新注册的特殊读者，在系统内给予它们一个身份标识，在下次使用时自动识别切换。

### 6. 框架

#### 6.1 后端框架
- **Spring Boot**：用于快速开发基于Spring框架的独立、生产级的基于Java的基于MVC的web应用。它提供自动配置、嵌入式服务器支持等。
- **Spring Security**：用于保护Web应用的安全，提供认证、授权、防止CSRF等安全控制。
- **Spring Data JPA**：简化数据库操作，提供Repository抽象，支持CRUD操作的声明式风格。

#### 6.2 前端框架
- **Angular**：用于动态Web应用的构建，提供数据绑定、依赖注入、模块化等特性。
- **Bootstrap**：用于快速开发响应式布局的前端界面，提供预定义的组件和样式。
- **NPM/Webpack**：用于前端项目的依赖管理和打包工具，支持模块化开发和资源优化。

### 7. 设计模式

#### 7.1 单例模式（Singleton Pattern）

**应用场景**：配置管理器

**描述**：
在无人值守图书馆系统中，配置管理器负责存储和提供全局配置信息，如数据库连接字符串、API密钥等。使用单例模式确保整个应用中只有一个配置管理器实例，从而保证配置信息的一致性和线程安全。

#### 7.2 工厂模式（Factory Pattern）

**应用场景**：用户会话和数据库连接的创建

**描述**：
用户会话和数据库连接是系统中频繁创建和管理的资源。工厂模式允许系统通过一个统一的接口来创建不同类型的用户会话和数据库连接，而无需关心具体的实现细节。这有助于解耦对象的创建逻辑和使用逻辑，提高系统的灵活性和可维护性。

#### 7.3 策略模式（Strategy Pattern）

**应用场景**：动态改变借阅规则

**描述**：
图书馆的借阅规则可能会根据政策调整或促销活动而变化。策略模式允许系统定义一系列的借阅策略，并且可以在运行时根据需要选择合适的策略。例如，可以有普通借阅策略、会员借阅策略、节假日借阅策略等，系统可以根据用户类型和当前日期来选择适用的策略。

#### 7.4 观察者模式（Observer Pattern）

**应用场景**：用户通知

**描述**：
在无人值守图书馆系统中，当用户的借阅状态发生变化，如图书到期、预约的图书到库等，需要及时通知用户。观察者模式允许系统定义一个通知机制，当有状态变化时，所有注册的观察者（如短信服务、邮件服务、App推送服务）都会收到通知并执行相应的动作。

#### 7.5 适配器模式（Adapter Pattern）

**应用场景**：系统与不同数据库之间的兼容

**描述**：
随着技术的发展，图书馆系统可能会迁移到不同的数据库平台。适配器模式允许系统定义一个统一的数据库访问接口，而具体的数据库操作则由适配器类来实现。这样，如果未来需要更换数据库，只需要增加一个新的适配器类，而不需要修改现有的业务逻辑代码，从而保护了现有投资并降低了迁移成本。

## 8. 结论

本架构设计文档提供了无人值守图书馆系统的架构样式、参考模型、参考架构、质量场景、框架和设计模式的详细描述，以指导系统的开发和实现。
