# 智医通 - 基于人工智能的症状自查平台

## 项目概述

智医通是一个基于人工智能的症状自查平台，旨在提高基层医疗机构的问诊效率和准确率。本项目基于Llama-3模型进行轻量化微调，实现常见病症状识别与初步分诊建议。用户输入体征数据后，系统通过结构化问诊流程生成风险评估报告，提供就诊科室推荐。

## 项目特点

- **一体化解决方案**：整合前端、后端和AI模型于一个仓库中
- **轻量级AI模型**：基于Llama-3-8B进行微调，支持移动端部署
- **结构化问诊流程**：基于状态机模型的交互式问诊
- **风险评估**：基于贝叶斯网络的疾病概率计算
- **分诊建议**：推荐就诊科室和就医级别

## 系统架构

本项目采用前后端分离的架构设计，包括以下组件：

### 前端（Frontend）
- 用户端：症状自查、健康档案、就医指南
- 医生端：患者管理、问诊辅助、数据分析
- 管理端：系统监控、数据管理、用户管理

### 后端（Backend）
- API服务：RESTful API接口
- AI模型服务：症状识别、问诊路径生成、风险评估
- 数据处理服务：数据清洗、特征提取、模型训练

### AI模型（AI Model）
- 症状识别模块：识别用户描述的症状
- 风险评估模块：评估疾病风险
- 模型训练流程：数据处理、模型微调、模型优化

## 技术栈

### 前端技术
- 框架：React.js + Vite
- UI组件库：Ant Design
- 状态管理：Redux
- 路由：React Router

### 后端技术
- 框架：Flask (Python)
- API文档：Swagger
- 身份认证：JWT

### 数据库
- 关系型数据库：MySQL
- 非关系型数据库：MongoDB（存储非结构化医疗数据）

### AI模型
- 基础模型：Llama-3-8B
- 模型优化：知识蒸馏、量化、剪枝
- 部署方案：ONNX Runtime

## 项目结构

```
/health-ai-platform
├── frontend/                # 前端代码
│   ├── public/              # 静态资源
│   ├── src/                 # 源代码
│   │   ├── components/      # 组件
│   │   ├── pages/           # 页面
│   │   ├── services/        # API服务
│   │   ├── utils/           # 工具函数
│   │   ├── store/           # Redux状态管理
│   │   ├── App.jsx          # 主应用
│   │   └── main.jsx         # 入口文件
│   ├── package.json         # 依赖配置
│   └── vite.config.js       # Vite配置
├── backend/                 # 后端代码
│   ├── api/                 # API接口
│   ├── models/              # 数据模型
│   ├── services/            # 业务逻辑
│   ├── utils/               # 工具函数
│   ├── app.py               # 主应用
│   ├── config.py            # 配置文件
│   └── requirements.txt     # 依赖配置
├── ai_model/                # AI模型
│   ├── data/                # 训练数据
│   ├── models/              # 模型文件
│   ├── training/            # 训练脚本
│   │   ├── finetune.py      # 模型微调主脚本
│   │   ├── data_processor.py # 数据处理模块
│   │   ├── model_optimizer.py # 模型优化模块
│   │   ├── run_training.py  # 训练流程主脚本
│   │   └── config.json      # 训练配置文件
│   ├── inference/           # 推理脚本
│   │   ├── symptom_recognition.py # 症状识别模块
│   │   ├── risk_assessment.py # 风险评估模块
│   │   └── main.py          # 推理测试主程序
│   └── requirements.txt     # 依赖项
└── docs/                    # 文档
    ├── api/                 # API文档
    ├── database/            # 数据库文档
    └── deployment/          # 部署文档
```

## 安装与运行

### 前端

```bash
# 进入前端目录
cd frontend

# 安装依赖
npm install

# 启动开发服务器
npm run dev
```

### 后端

```bash
# 进入后端目录
cd backend

# 创建虚拟环境
python -m venv venv

# 激活虚拟环境
# Windows
venv\Scripts\activate
# Linux/Mac
source venv/bin/activate

# 安装依赖
pip install -r requirements.txt

# 启动服务器
python app.py
```

### AI模型

```bash
# 进入AI模型目录
cd ai_model

# 安装依赖
pip install -r requirements.txt

# 运行推理测试
python inference/main.py
```

## 贡献指南

1. Fork 本仓库
2. 创建您的特性分支 (`git checkout -b feature/amazing-feature`)
3. 提交您的更改 (`git commit -m 'Add some amazing feature'`)
4. 推送到分支 (`git push origin feature/amazing-feature`)
5. 打开一个 Pull Request

## 许可证

本项目采用 MIT 许可证 - 详情请参阅 [LICENSE](LICENSE) 文件

## 联系方式

项目维护者 - 邮箱地址

---

**注意**：本项目中的AI模型部分需要下载预训练模型才能运行。由于模型文件较大，未包含在代码仓库中，请参考`ai_model/README.md`获取详细信息。