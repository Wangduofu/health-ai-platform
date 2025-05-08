# 智医通快速部署指南

本文档提供了智医通项目的快速部署方法概述，帮助您快速将应用部署到服务器上并通过公网访问。详细步骤请参考相应的完整部署文档。

## 部署方式选择

智医通项目支持两种主要部署方式：

1. **传统部署**：分别部署前端、后端和AI模型组件
2. **Docker容器化部署**：使用Docker Compose一键部署整个应用栈

对于大多数用户，我们推荐使用Docker容器化部署，它简化了环境配置和应用管理。

## 服务器要求

- 操作系统：推荐Ubuntu 20.04 LTS或更高版本
- CPU：至少4核
- 内存：至少8GB（推荐16GB以上）
- 存储：至少50GB可用空间
- 网络：具有公网IP地址或域名

## Docker容器化部署（推荐）

### 1. 安装Docker和Docker Compose

```bash
# 更新软件包索引
sudo apt update

# 安装必要的依赖
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common

# 添加Docker官方GPG密钥
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# 添加Docker仓库
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# 更新软件包索引
sudo apt update

# 安装Docker CE
sudo apt install -y docker-ce docker-ce-cli containerd.io

# 安装Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.18.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### 2. 克隆代码仓库

```bash
# 克隆代码仓库
git clone https://github.com/twilight-l-wxy/health-ai-platform.git
cd health-ai-platform
```

### 3. 启动应用

```bash
# 构建并启动所有服务
docker-compose up -d

# 查看运行状态
docker-compose ps
```

### 4. 配置公网访问

#### 域名配置（推荐）

1. 在域名注册商处购买域名
2. 将域名解析到您服务器的公网IP地址

#### HTTPS配置（推荐）

```bash
# 安装certbot
sudo apt install -y certbot python3-certbot-nginx

# 获取证书并自动配置Nginx
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

## 传统部署方式

如果您希望更灵活地控制各组件的部署，可以参考以下步骤：

### 1. 前端部署

```bash
# 进入前端目录
cd frontend

# 安装依赖
npm install

# 构建生产版本
npm run build

# 将dist目录部署到Web服务器（如Nginx）
```

### 2. 后端部署

```bash
# 进入后端目录
cd backend

# 创建虚拟环境
python -m venv venv

# 激活虚拟环境
source venv/bin/activate

# 安装依赖
pip install -r requirements.txt

# 启动Gunicorn服务器
gunicorn -w 4 -b 0.0.0.0:5000 app:app
```

### 3. AI模型部署

```bash
# 进入AI模型目录
cd ai_model

# 安装依赖
pip install -r requirements.txt

# 启动模型服务
python model_server.py
```

## 详细文档

- [完整部署指南](./deployment_guide.md)
- [Docker部署指南](./docker_deployment.md)

## 常见问题

### 应用无法访问

1. 检查服务器防火墙设置
2. 确认Docker容器正在运行
3. 检查Nginx配置
4. 验证域名DNS解析是否正确

### 数据库连接问题

```bash
# Docker部署时检查数据库容器
docker-compose logs mysql

# 传统部署时检查数据库服务
sudo systemctl status mysql
```

## 联系与支持

如有任何部署问题，请联系项目维护团队：

- 邮箱：support@zhiyitong.example.com
- 问题追踪：https://github.com/twilight-l-wxy/health-ai-platform/issues

---

本快速部署指南最后更新于：2024年5月