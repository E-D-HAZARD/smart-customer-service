# 智能客服系统

> 基于 OpenClaw + 飞书的智能客服解决方案，已完整实现企业级核心特性。

[![OpenClaw](https://img.shields.io/badge/OpenClaw-Framework-blue)](https://openclaw.ai)
[![Feishu](https://img.shields.io/badge/飞书-Bot-green)](https://www.feishu.cn)
[![MiniMax](https://img.shields.io/badge/Model-MiniMax_M2.7-orange)](https://www.minimax.com)

## 项目简介

本项目旨在构建一个面向企业/机构的智能客服系统，基于 OpenClaw 框架和 Python FastAPI 开发，通过飞书平台进行安全的长链消息交互，并在内部通过 Tool Calling 及 RAG 实现了 7×24 小时专业化客户服务。

## 已实现核心特性

- 🤖 **多轮对话与自主规划**：基于 MiniMax 编程大模型（如 m2.7 系列）与 OpenAI 兼容生态开发，Agent 能够自主识别意图并路由至特定 Tool 处理。
- 📚 **知识库本地 RAG**：内置基于局部 ChromaDB 向量引擎的 FAQ 自动索引系统。
- 🔌 **灵活扩展 Skills**：业务模块化，已内置“银行业务”、“税务业务”以及“教务服务”多套处理钩子。
- 📱 **多端接入**：针对飞书平台进行了全闭环的 Webhook 和 Challenge 安全握手校验集成。
- 🖥️ **一键私有部署**：提供带有自动服务拉起的 Docker Compose 完整部署链。

## 技术架构

```
┌─────────────┐
│   飞书 IM   │  ← 用户端发问
└──────┬──────┘
       │
┌──────▼──────┐
│ FastAPI 后台 │  ← 消息接入与验证
└──────┬──────┘
       │
┌──────▼──────┐
│  OpenClaw   │  ← Agent + M2.7 大模型中枢
│   Agent     │
└──────┬──────┘
       │
┌──────▼──────┐     ┌─────────────┐
│   Skills    │────▶│ ChromaDB RAG│
│  (银/税/教) │     └─────────────┘
└─────────────┘
```

## 技术栈

| 组件 | 技术 |
|-----|------|
| Web 后端 | Python FastAPI |
| Agent 构建 | OpenClaw 风格 / OpenAI Node |
| 大语言模型 | **MiniMax (M2.7)** / GPT |
| 消息平台 | 飞书 Webhook 订阅机制 |
| 部署环境 | **Docker Compose** |
| 向量数据库 | **ChromaDB** |

## 快速开始

### 1. 部署项目

```bash
# 克隆项目
git clone https://github.com/E-D-HAZARD/-.git
cd -/智能客服
```

### 2. 配置环境变量与模型

首先从模板复制 `.env` 文件：

```bash
cp .env.example .env
```

您需要在 `.env` 中填入：
- **MINIMAX_API_KEY**：您的 Minimax API 密钥
- **FEISHU_APP_ID / SECRET**：飞书后台获取
- 确认模型参数默认（`LLM_MODEL=MiniMax-Text-2.7`）或依照官方填写。

### 3. 启动服务与飞书验证

```bash
# 使用 Docker Compose 一键启动并后台运行
docker compose -p smart-customer-service up -d --build

# 查看运行日志确认 RAG 挂载
docker compose -p smart-customer-service logs -f
```

容器监听本机的 `8000` 端口。启动后，将您的内网穿透公网地址 `http(s)://<域名>:8000/webhook/feishu` 填入飞书的事件订阅处即可完成即开即用的系统搭建！

## 项目结构

```
├── docker-compose.yml    # Docker 服务编排配置
├── Dockerfile            # Python 应用镜像构建
├── requirements.txt      # 依赖管理 (FastAPI, lark-oapi, chromadb 等)
├── .env.example          # 环境配置模板
├── src/                  
│   ├── main.py           # FastAPI 入口及飞书回调处理
│   └── agent.py          # 核心智能 Agent 推理架构
├── openclaw/             
│   ├── skills/           # 包含银行、税务、教务等 Python 函数工具
│   └── knowledge/        # 包含 faq.json 以及 RAG ChromaDB 初始化服务
└── README.md             # 部署说明
```

## 后续规划情况

- [x] 完成基础问答功能
- [x] 接入 RAG 知识库
- [x] 开发更多业务技能
- [ ] 添加语音交互能力 (待实现)
- [x] 性能优化与 Docker 测试

## 注意事项

- 请妥善保管您的 `.env` 密钥配置文件，切勿提交至代码仓库。
- 若部署服务器在海外，可能会受到网络连通性限制，请以最新日志排错为准。

## 相关资源

- [MiniMax 开放平台](https://www.minimax.com)
- [飞书开放平台](https://open.feishu.cn)
- [OpenClaw / ClawHub](https://clawhub.com)
