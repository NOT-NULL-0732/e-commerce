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

## 功能

- 用户注册、JWT 登录、Refresh Token
- 商品创建/列表/更新/删除
- 订单创建（事务：行锁扣库存 + 快照 + MQ 延迟超时）
- 钱包充值（DB 唯一键幂等）
- 全链路追踪 + 结构化日志 + 业务指标