# Python 版部署包说明

本目录由 `scripts/package-python-vm.sh` 生成。

## 交付物

- `testcase-python-app-20260323.tar.gz`：Python 版完整应用包（主服务 + 文档解析 + 前端静态资源 + prompts）

## 快速启动（Ubuntu/CentOS 通用）

```bash
mkdir -p /opt/testcase-python && cd /opt/testcase-python
tar -xzf testcase-python-app-20260323.tar.gz

# 1) 启动文档解析（5000）
cd 文档解析
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
HOST=0.0.0.0 PORT=5000 python3 web_app.py
```

另开一个终端：

```bash
cd /opt/testcase-python/python_server
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# 可选：从项目根加载 .env
set -a; source /opt/testcase-python/.env 2>/dev/null || true; set +a

# 2) 启动 Python 主服务（8000）
PYTHON_PORT=8000 uvicorn app.main:app --host 0.0.0.0 --port 8000
```

访问：

- `http://<服务器IP>:8000`
- 健康检查：`http://<服务器IP>:8000/api/health`

## 说明

- 主服务默认会代理到文档解析服务 `http://localhost:5000`（可通过 `DOC_PARSER_API_BASE` 修改）
- 生产建议使用 Nginx + HTTPS + systemd，参考 `docs/cloud-deploy.md`
