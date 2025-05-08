# 智医通 Docker 部署指南

本文档提供了使用 Docker 容器化部署智医通项目的完整流程，帮助您快速将应用部署到服务器上并通过公网访问。

## 1. 环境准备

### 1.1 服务器要求

- 操作系统：推荐 Ubuntu 20.04 LTS 或更高版本
- CPU：至少 4 核
- 内存：至少 8GB（推荐 16GB 以上）
- 存储：至少 50GB 可用空间
- 网络：具有公网 IP 地址或域名

### 1.2 安装 Docker 和 Docker Compose

```bash
# 更新软件包索引
sudo apt update

# 安装必要的依赖
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common

# 添加 Docker 官方 GPG 密钥
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# 添加 Docker 仓库
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# 更新软件包索引
sudo apt update

# 安装 Docker CE
sudo apt install -y docker-ce docker-ce-cli containerd.io

# 安装 Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.18.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# 将当前用户添加到 docker 组（可选，避免每次使用 docker 命令都需要 sudo）
sudo usermod -aG docker $USER

# 重新登录以应用组更改
# 或者运行以下命令立即应用（不推荐在生产环境中使用）
# newgrp docker
```

## 2. 项目容器化

### 2.1 克隆代码仓库

```bash
# 克隆代码仓库
git clone https://github.com/twilight-l-wxy/health-ai-platform.git
cd health-ai-platform
```

### 2.2 创建 Docker 配置文件

在项目根目录创建 `docker-compose.yml` 文件：

```yaml
version: '3.8'

services:
  # 前端服务
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "80:80"
    depends_on:
      - backend
    restart: always
    networks:
      - zhiyitong-network

  # 后端服务
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "5000:5000"
    depends_on:
      - mysql
      - ai-model
    environment:
      - MYSQL_HOST=mysql
      - MYSQL_USER=zhiyitong_user
      - MYSQL_PASSWORD=your_secure_password
      - MYSQL_DATABASE=zhiyitong
      - AI_MODEL_URL=http://ai-model:8000
    restart: always
    networks:
      - zhiyitong-network

  # AI 模型服务
  ai-model:
    build:
      context: ./ai_model
      dockerfile: Dockerfile
    ports:
      - "8000:8000"
    restart: always
    networks:
      - zhiyitong-network

  # MySQL 数据库
  mysql:
    image: mysql:8.0
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=your_root_password
      - MYSQL_DATABASE=zhiyitong
      - MYSQL_USER=zhiyitong_user
      - MYSQL_PASSWORD=your_secure_password
    volumes:
      - mysql-data:/var/lib/mysql
    restart: always
    networks:
      - zhiyitong-network

networks:
  zhiyitong-network:
    driver: bridge

volumes:
  mysql-data:
```

### 2.3 创建前端 Dockerfile

在 `frontend` 目录下创建 `Dockerfile`：

```dockerfile
# 构建阶段
FROM node:16-alpine as build

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build

# 生产阶段
FROM nginx:alpine

COPY --from=build /app/dist /usr/share/nginx/html

# 复制 Nginx 配置文件
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

在 `frontend` 目录下创建 `nginx.conf`：

```nginx
server {
    listen 80;
    server_name localhost;

    root /usr/share/nginx/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /api {
        proxy_pass http://backend:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### 2.4 创建后端 Dockerfile

在 `backend` 目录下创建 `Dockerfile`：

```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["gunicorn", "-w", "4", "-b", "0.0.0.0:5000", "app:app"]
```

### 2.5 创建 AI 模型 Dockerfile

在 `ai_model` 目录下创建 `Dockerfile`：

```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

# 创建模型目录
RUN mkdir -p models/base_model

COPY . .

EXPOSE 8000

CMD ["python", "inference/main.py"]
```

## 3. 部署应用

### 3.1 构建和启动容器

```bash
# 构建并启动所有服务
docker-compose up -d

# 查看运行状态
docker-compose ps
```

### 3.2 初始化数据库（首次部署）

```bash
# 进入后端容器
docker-compose exec backend bash

# 初始化数据库
python -c "from app import db; db.create_all()"

# 导入初始数据（如果有）
python scripts/import_initial_data.py

# 退出容器
exit
```

## 4. 配置公网访问

### 4.1 配置域名（推荐）

1. 在域名注册商处购买域名
2. 将域名解析到您服务器的公网 IP 地址
3. 等待 DNS 解析生效（通常需要几分钟到几小时）

### 4.2 配置 HTTPS（推荐）

使用 Let's Encrypt 免费 SSL 证书：

```bash
# 安装 certbot
sudo apt install -y certbot python3-certbot-nginx

# 获取证书并自动配置 Nginx
sudo certbot --nginx -d your-domain.com -d www.your-domain.com

# 设置自动续期
sudo systemctl status certbot.timer
```

### 4.3 配置防火墙

```bash
# 安装 UFW（如果尚未安装）
sudo apt install -y ufw

# 允许 SSH 连接（重要！避免被锁定）
sudo ufw allow ssh

# 允许 HTTP 和 HTTPS 流量
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# 启用防火墙
sudo ufw enable

# 检查状态
sudo ufw status
```

## 5. 维护与更新

### 5.1 查看日志

```bash
# 查看所有服务的日志
docker-compose logs

# 查看特定服务的日志
docker-compose logs frontend
docker-compose logs backend
docker-compose logs ai-model

# 实时查看日志
docker-compose logs -f
```

### 5.2 更新应用

```bash
# 拉取最新代码
git pull

# 重新构建并启动容器
docker-compose up -d --build
```

### 5.3 数据库备份

```bash
# 创建备份脚本
cat > backup.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="./backups"
DATETIME=$(date +"%Y%m%d_%H%M%S")

mkdir -p $BACKUP_DIR

# 备份 MySQL 数据库
docker-compose exec -T mysql mysqldump -u zhiyitong_user -pyour_secure_password zhiyitong > $BACKUP_DIR/zhiyitong_$DATETIME.sql

# 压缩备份文件
gzip $BACKUP_DIR/zhiyitong_$DATETIME.sql

# 删除 30 天前的备份
find $BACKUP_DIR -name "zhiyitong_*.sql.gz" -type f -mtime +30 -delete
EOF

# 添加执行权限
chmod +x backup.sh

# 添加到 crontab，每天凌晨 3 点执行
(crontab -l 2>/dev/null; echo "0 3 * * * $(pwd)/backup.sh") | crontab -
```

## 6. 故障排除

### 6.1 容器无法启动

```bash
# 查看容器日志
docker-compose logs [service_name]

# 检查容器状态
docker-compose ps

# 重启特定服务
docker-compose restart [service_name]
```

### 6.2 无法访问应用

1. 检查服务器防火墙设置
2. 确认 Docker 容器正在运行
3. 检查 Nginx 配置
4. 验证域名 DNS 解析是否正确

### 6.3 数据库连接问题

```bash
# 进入 MySQL 容器
docker-compose exec mysql bash

# 登录 MySQL
mysql -u zhiyitong_user -p

# 检查数据库是否存在
SHOW DATABASES;

# 退出
exit
```

## 7. 性能优化

### 7.1 增加 Swap 空间（内存较小的服务器）

```bash
# 创建 4GB 的 swap 文件
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# 设置开机自动启用
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

### 7.2 配置 Nginx 缓存

修改 `frontend/nginx.conf` 文件，添加缓存配置：

```nginx
# 在 server 块内添加
location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
    expires 30d;
    add_header Cache-Control "public, no-transform";
}
```

重新构建前端容器：

```bash
docker-compose up -d --build frontend
```

## 8. 安全建议

1. 定期更新系统和 Docker
2. 使用强密码并定期更换
3. 启用 HTTPS
4. 限制 SSH 访问
5. 定期备份数据
6. 监控服务器资源使用情况
7. 实施 API 限流

## 9. 联系与支持

如有任何部署问题，请联系项目维护团队：

- 邮箱：support@zhiyitong.example.com
- 问题追踪：https://github.com/twilight-l-wxy/health-ai-platform/issues

---

本 Docker 部署指南最后更新于：2024年5月