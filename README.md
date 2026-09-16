# Open WebUI 安装与使用指南

本地 Web 聊天界面，可对接 Ollama 等多模型，支持知识库、搜索与多用户。

项目：https://github.com/open-webui/open-webui

## 前置

- 已安装 Docker
- 本机已运行 Ollama（或其他兼容后端）

## Docker 部署

```bash
docker run -d -p 3000:8080 \
  --add-host=host.docker.internal:host-gateway \
  -v open-webui:/app/backend/data \
  --name open-webui \
  --restart always \
  ghcr.io/open-webui/open-webui:main
```

浏览器访问：`http://localhost:3000`  
首次使用创建管理员账号。在设置中确认 Ollama 地址（Docker 下常见为 `http://host.docker.internal:11434`）。

## 更新

```bash
docker pull ghcr.io/open-webui/open-webui:main
docker rm -f open-webui
# 重新执行上面的 docker run（数据在 volume 中保留）
```

## 说明

请遵守当地法律与软件许可。使用风险自负。

## License

本仓库文档 MIT。
