# 智能客服系统

> 基于 OpenClaw + 飞书的智能客服解决方案，已完整实现企业级核心特性。

[![OpenClaw](https://img.shields.io/badge/OpenClaw-Framework-blue)](https://openclaw.ai)
[![Feishu](https://img.shields.io/badge/飞书-Bot-green)](https://www.feishu.cn)
[![OpenAI Compatible](https://img.shields.io/badge/Model-All_Compatible-orange)](#)

## 项目简介

本项目旨在构建一个面向企业/机构的智能客服系统，基于 OpenClaw 框架和 Python FastAPI 开发，通过飞书平台进行安全的长链消息交互，并在内部通过 Tool Calling 及 RAG 实现了 7×24 小时专业化客户服务。

## 已实现核心特性

- 🤖 **多轮对话与自主规划**：核心框架不绑定特定模型！不论是基础的 OpenAI GPT 系列，还是国内的 MiniMax、智谱 AI 等模型，只要兼容 OpenAI API 规范，只需修改环境变量即可无缝切换！
- 📚 **知识库本地 RAG**：内置基于局部 ChromaDB 向量引擎的 FAQ 自动索引系统。
- 🔌 **灵活扩展 Skills**：业务模块化，目前专注于**银行业务**，已内置“基础银行”、“信用卡业务”以及“贷款业务”等多套处理钩子。
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
│  OpenClaw   │  ← Agent + 大语言模型中枢 (支持泛用)
│   Agent     │
└──────┬──────┘
       │
┌──────▼──────┐     ┌─────────────┐
│   Skills    │────▶│ ChromaDB RAG│
│(基础/信用卡/贷款)|     └─────────────┘
└─────────────┘
```
## 完整搭建规划与核心流程

本项目从零到一的搭建与调优流程如下，旨在构建一个稳定、高效的企业级对话系统：

1. **环境与框架选型**
   - 核心使用了 Python 的 **FastAPI** 框架，对外提供高并发和快响应的 HTTP Webhook 服务。
   - 对接了大语言模型代理框架 **OpenClaw Agent**，实现了原生的 Tool Calling（函数调用）和上下文状态管理。
   
2. **飞书机器人接入与鉴权**
   - 在飞书开放平台创建应用并获取 `APP_ID` 和 `APP_SECRET`。
   - 在 `src/main.py` 中编写 Webhook 回调路由 `/webhook/feishu`。起初碰到了由于 LLM 响应大段回复较慢，导致飞书触发 3 秒超时重试（一条消息重复回复三次）的问题。
   - **优化解决**：巧妙地引入 FastAPI 的 `BackgroundTasks`，将消息提交流程放入后台异步执行，使主线程能立刻返回 `200 OK`，彻底解决了超时重发的 Bug。

3. **智能知识库与业务编排 (Skills)**
   - 确立了以**银行业务**为核心的垂直领域知识库系统。
   - 在 `openclaw/skills/` 目录下开发具体的银行受理逻辑，包含：
     - `bank_service.py`：基础银行业务（开户、理财、转账限额等）
     - `credit_card_service.py`：信用卡业务（申请办理、额度、年费、账单还款等）
     - `loan_service.py`：贷款业务（房贷、车贷、纯信用消费贷及利率咨询等）
   - 剔除了其他无关的税务与教务逻辑，确保大模型的系统提示词（System Prompt）纯碎且专注于银行业务。

4. **Agent 配置与模型调用**
   - 在 `src/agent.py` 中动态挂载最新的三项银行业务 Tools 到兼容 OpenAI 规范的 Client 中。
   - 优化 Prompt，对于无关银行业务的闲聊及无关咨询，要求 Agent 基于规则礼貌拒绝并引导用户。

5. **部署与内网穿透测试 (使用 ngrok)**
   - 飞书服务端要求 Webhook 地址必须为公网可外网访问的 HTTPS 域名，因此本地开发阶段我们采用了 **ngrok** 作为内网穿透工具。
   - 首先利用 Docker Compose 或是通过本地环境启动应用，服务将默认监听本机的 `8000` 端口。
   - 接着在终端执行 `ngrok http 8000` 将本地的 8000 端口映射并暴露到公网，得到一个如 `https://<hash>.ngrok-free.app` 的对外安全域名。
   - 最后，将拼接好路由的 Webhook 地址（如 `https://<hash>.ngrok-free.app/webhook/feishu`）填入飞书开放平台后台的“事件订阅”处。当飞书服务端发来 Challenge 请求且我们能在控制台看到 200 返回后，本地与飞书端的安全握手联调即宣告完成。

```
```
## 技术栈

| 组件 | 技术 |
|-----|------|
| Web 后端 | Python FastAPI |
| Agent 构建 | OpenClaw 风格 / OpenAI SDK |
| 大语言模型 | **任何兼容 OpenAI 规范的模型 (GPT, MiniMax, 等)** |
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
- **LLM_API_KEY**：您的模型提供商（如 Minimax、OpenAI 等）API 密钥
- **LLM_BASE_URL**：您的请求终端 URL，例如 `https://api.minimax.chat/v1`
- **LLM_MODEL**：模型名称，如 `MiniMax-Text-2.7` 或 `gpt-4o`
- **FEISHU_APP_ID / SECRET**：飞书后台获取

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
│   ├── skills/           # 包含基础银行、信用卡、贷款等 Python 函数工具
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
