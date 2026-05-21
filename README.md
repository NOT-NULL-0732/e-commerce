# 个人练习 - 电商项目

## 技术栈

- **后端**: Go + Gin + GORM
- **数据库**: PostgreSQL + Redis
- **消息队列**: RabbitMQ
- **观测性**: OpenTelemetry + Prometheus + Tempo + Loki + Grafana

## 项目结构

```text
.
├── cmd/                    # 程序入口
├── internal/
│   ├── model/             # GORM 数据库模型
│   ├── auth/              # 认证模块 (JWT + Redis Session)
│   ├── user/              # 用户注册
│   ├── product/           # 商品 CRUD
│   ├── order/             # 订单 (事务内锁库存 + MQ 延迟超时)
│   ├── wallet/            # 钱包 (幂等充值)
│   └── config/            # 应用配置
├── pkg/                   # 公共组件
├── tests/                 # 集成测试 (Ginkgo)
└── .gitea/workflows/      # CI 配置
```

## 快速开始

```bash
# 启动基础设施
docker compose up -d

# 运行服务
go run cmd/main.go -c configs/config.yaml
```

## Git 提交规范

```
<type>(<scope>): <简短描述>

<详细说明>
```

**type 必选：**
- `feat` — 新功能
- `fix` — Bug 修复
- `refactor` — 重构（不改变外部行为）
- `chore` — 杂项（构建、依赖、配置）
- `docs` — 文档
- `test` — 测试
- `perf` — 性能优化

**scope 可选：** 模块名，如 `tests`、`config`、`order`、`ci`、`deps`

**body 可选：** 说明为什么要改、怎么改。有 body 时**第一行后空一行再写**。

**示例：**
```
fix(tests): 修复 AutoMigrate、库存不足、幂等键和订单列表测试

- tests_suite_test.go: AutoMigrate 补上 UserWallet / WalletLog
- order_api_test.go: lowStockProduct 在声明时初始化
  幂等键重复改为断言成功（与业务语义一致）
- 订单列表测试前清理历史订单
```

```
chore: 添加 IMAGE_REPOSITORY 环境变量支持 registry 切换

- docker-compose: 所有 image 改为 ${IMAGE_REPOSITORY} 变量
- config: 新增 RegistrySection 和 ImageRef() 方法
- test: testcontainers 镜像通过 config.ImageRef() 拼接前缀
```

## 功能

- 用户注册、JWT 登录、Refresh Token
- 商品创建/列表/更新/删除
- 订单创建（事务：行锁扣库存 + 快照 + MQ 延迟超时）
- 钱包充值（DB 唯一键幂等）
- 全链路追踪 + 结构化日志 + 业务指标