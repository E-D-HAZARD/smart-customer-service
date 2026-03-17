# 智能客服系统

> 基于 OpenClaw + 飞书的智能客服解决方案

[![OpenClaw](https://img.shields.io/badge/OpenClaw-Framework-blue)](https://openclaw.ai)
[![Feishu](https://img.shields.io/badge/飞书-Bot-green)](https://www.feishu.cn)

## 项目简介

本项目旨在构建一个面向企业/机构的智能客服系统，基于 OpenClaw 框架开发，通过飞书平台进行消息交互，实现 7×24 小时智能化客户服务。

## 核心特性

- 🤖 **多轮对话**：支持复杂上下文理解，意图识别
- 📚 **知识库问答**：基于 RAG 技术，支持结构化/非结构化文档
- 🔌 **灵活扩展**：Skill 模块化设计，按需添加功能
- 📱 **多端接入**：飞书平台，支持文本/语音交互
- 🖥️ **私有部署**：支持阿里云 ECS 私有化部署

## 技术架构

```
┌─────────────┐
│   飞书 IM   │  ← 用户入口
└──────┬──────┘
       │
┌──────▼──────┐
│  飞书 Bot   │  ← 消息接入
└──────┬──────┘
       │
┌──────▼──────┐
│  OpenClaw   │  ← Agent 核心
│   Agent     │
└──────┬──────┘
       │
┌──────▼──────┐     ┌─────────────┐
│   Skills    │────▶│  知识库 RAG │
│  (业务技能)  │     └─────────────┘
└─────────────┘
```

## 技术栈

| 组件 | 技术 |
|-----|------|
| Agent 框架 | OpenClaw |
| 大模型 | MiniMax / GPT |
| 消息平台 | 飞书 |
| 部署 | Docker + 阿里云 ECS |
| 向量数据库 | 待定 |

## 应用场景

- 🏦 **银行业务咨询**：开户、转账、理财
- 🏛️ **政务服务**：税务、社保、办事指南
- 🏫 **高校服务**：教务咨询、设备报修
- 🏢 **企业内部**：IT 支持、HR 问答

## 快速开始

### 1. 部署 OpenClaw

```bash
# 克隆项目
git clone https://github.com/your-repo/smart-customer-service.git
cd smart-customer-service

# 使用 Docker 部署
docker-compose up -d
```

### 2. 配置飞书 Bot

1. 在[飞书开放平台](https://open.feishu.cn)创建企业应用
2. 获取 App ID 和 App Secret
3. 配置事件订阅和权限

### 3. 配置模型

```bash
# 配置 MiniMax API
export MINIMAX_API_KEY="your-api-key"
```

### 4. 启动服务

```bash
# 启动
docker-compose up -d

# 查看日志
docker-compose logs -f
```

## 项目结构

```
├── docker-compose.yml    # Docker 配置
├── Dockerfile           # 镜像构建
├── openclaw/            # OpenClaw 配置
│   ├── skills/          # 业务技能
│   └── knowledge/       # 知识库
├── docs/                # 文档
└── README.md            # 本文件
```

## 技能列表

| 技能 | 功能 |
|-----|------|
| bank-service | 银行业务问答 |
| tax-service | 税务咨询 |
| edu-service | 教务问答 |
| faq | 常见问题 |

## 知识库格式

```json
{
  "question": "如何办理银行卡？",
  "answer": "办理银行卡需要...",
  "category": "banking"
}
```

## 后续规划

- [ ] 完成基础问答功能
- [ ] 接入 RAG 知识库
- [ ] 开发更多业务技能
- [ ] 添加语音交互能力
- [ ] 性能优化与测试

## 注意事项

- 本项目为实验性项目，生产环境使用请评估安全性
- 请妥善保管 API Key 等敏感信息
- 遵守飞书平台开发规范

## 相关资源

- [OpenClaw 官方文档](https://docs.openclaw.ai)
- [飞书开放平台](https://open.feishu.cn)
- [ClawHub](https://clawhub.com)

---

*本项目为个人学习研究项目*
