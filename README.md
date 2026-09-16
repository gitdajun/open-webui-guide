# Open WebUI 详细安装与使用指南

Open WebUI 提供浏览器里的聊天界面，可连接 Ollama、部分 OpenAI 兼容接口等，适合把本地模型当成「私有 ChatGPT」来用。

- 源码：https://github.com/open-webui/open-webui
- 常见访问地址：`http://localhost:3000`（映射端口可变）

---

## 一、你需要先有什么

1. **Docker**（推荐部署方式）或按官方文档用 Python/其它方式安装
2. **至少一个模型后端**，最常见是本机 **Ollama** 已安装并 `ollama list` 能看到模型
3. 浏览器；若远程访问，再考虑反向代理与 HTTPS

---

## 二、Docker 安装（推荐）

### 标准启动

```bash
docker run -d -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:main
```

说明：

| 参数 | 含义 |
|------|------|
| `-p 3000:8080` | 主机 3000 映射容器内 8080 |
| `host.docker.internal` | 让容器访问宿主机上的 Ollama |
| `-v open-webui:...` | 持久化用户、对话、知识库等数据 |
| `--restart always` | 开机或崩溃后自动拉起 |

浏览器打开：`http://localhost:3000`

### 首次进入

1. 注册**第一个用户**（通常会成为管理员）
2. 进入设置，检查 **Ollama** 连接地址
   - 本机直接跑 WebUI 时可能是 `http://127.0.0.1:11434`
   - Docker 访问宿主机 Ollama：`http://host.docker.internal:11434`
3. 在模型列表中应能看到 Ollama 已拉取的模型；没有则检查 Ollama 是否运行、地址是否可达

### Linux 补充

若 `host.docker.internal` 不可用，可改用宿主机 IP，或：

```bash
--add-host=host.docker.internal:host-gateway
```

并确认防火墙放行 11434（仅限信任网络）。

---

## 三、日常使用

### 聊天

- 左上角或模型选择器切换模型
- 支持多会话；注意上下文长度受模型与配置限制

### 知识库 / 文档

1. 在知识库功能中新建集合
2. 上传 PDF、文本等（具体格式以当前版本为准）
3. 对话时引用该知识库，实现「基于你的文档回答」

文档解析与嵌入会消耗时间与磁盘，大文件建议先试小样。

### 联网搜索

需在设置中配置搜索引擎或 API（若版本提供）。未配置时不要依赖「实时联网」能力。

### 多用户

管理员可在后台管理用户、权限。部署在多人环境时务必：

- 强密码
- 不要裸奔公网
- 用 HTTPS 反代（Caddy / Nginx 等）

---

## 四、更新与备份

```bash
# 备份：实质是备份 Docker volume
docker pull ghcr.io/open-webui/open-webui:main
docker stop open-webui
docker rm open-webui
# 再执行与当初相同的 docker run（同一 volume 名则数据还在）
```

重要数据可定期 `docker volume` 备份或导出。升级后若异常，查看容器日志：

```bash
docker logs -f open-webui
```

---

## 五、连接其它后端

除 Ollama 外，很多版本支持 OpenAI 兼容 API（填 Base URL 与 Key）。用于接入云端或其它本地推理服务。Key 不要提交到公开仓库。

---

## 六、常见问题

| 现象 | 处理 |
|------|------|
| 页面打开但无模型 | Ollama 未启动、地址写错、Docker 网络隔离 |
| 502 / 连不上 | 端口映射、容器未启动、`docker ps` 检查 |
| 很慢 | 模型太大、机器在 CPU 推理、磁盘 I/O |
| 忘记管理员密码 | 查官方文档中关于数据目录/重置的说明，避免直接删 volume 除非可接受清空 |
| 外网访问不安全 | 上 HTTPS、鉴权、VPN 或内网穿透加密码 |

---

## 七、合规

请遵守当地法律、模型许可与服务条款。勿用于未授权的数据抓取或违规内容。

## License

本仓库文档 MIT。Open WebUI 本体许可以官方为准。
