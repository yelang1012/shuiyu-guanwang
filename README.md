# 水域官网 — 部署说明

水域 APP 的静态介绍官网，使用 nginx + Docker 部署。

## 快速启动

```bash
# 克隆项目
git clone https://github.com/yelang1012/shuiyu-guanwang.git
cd shuiyu-guanwang

# 启动
docker compose up -d

# 验证
curl http://localhost/
```

访问：http://YOUR_SERVER_IP

---

## APK 上传方法

本地构建好 APK 后，通过 SCP 上传到服务器：

```bash
# 上传命令（在你本地机器执行）
scp app-release.apk root@43.112.79.91:/root/projects/shuiyu-guanwang/downloads/shuiyu-latest.apk
```

上传后下载链接自动生效：`http://43.112.79.91/downloads/shuiyu-latest.apk`

---

## 迁移到新服务器

### 环境要求

| 软件 | 版本要求 | 安装命令（Ubuntu/Debian） |
|------|----------|--------------------------|
| Docker | 20.x+ | `curl -fsSL https://get.docker.com \| sh` |
| Docker Compose | v2+ | 随 Docker 一起安装，或 `apt install docker-compose-plugin` |
| Git | 任意 | `apt install git` |

**注意：** 本项目只需 nginx（纯静态），无需 Go/Node/Python 等语言环境。

### 迁移步骤

```bash
# 1. 在新服务器安装 Docker
curl -fsSL https://get.docker.com | sh
systemctl enable docker && systemctl start docker

# 2. 克隆代码
git clone https://github.com/yelang1012/shuiyu-guanwang.git /opt/shuiyu-guanwang
cd /opt/shuiyu-guanwang

# 3. 重新上传 APK
scp app-release.apk root@NEW_SERVER:/opt/shuiyu-guanwang/downloads/shuiyu-latest.apk

# 4. 启动服务
docker compose up -d

# 5. 验证
curl http://localhost/
```

### 端口说明

| 服务 | 端口 | 说明 |
|------|------|------|
| 官网 | 80 | HTTP，对外访问 |

如需 HTTPS，在 nginx.conf 中添加 SSL 配置，并挂载证书文件即可。

### 绑定域名后的 HTTPS（Let's Encrypt）

```bash
# 安装 certbot
apt install certbot python3-certbot-nginx

# 停止 Docker nginx（临时）
docker compose stop web

# 申请证书
certbot certonly --standalone -d yourdomain.com

# 修改 docker-compose.yml 挂载证书并监听 443，重启即可
docker compose up -d
```

---

## 目录结构

```
shuiyu-guanwang/
├── www/                  # 网站静态文件
│   ├── index.html        # 首页
│   ├── privacy.html      # 隐私政策
│   ├── terms.html        # 用户协议
│   ├── css/style.css     # 样式
│   └── js/main.js        # 脚本
├── downloads/            # APK 下载目录（.apk 不进 Git）
├── nginx.conf            # nginx 配置
├── docker-compose.yml    # Docker Compose
└── README.md
```
