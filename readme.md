# quant-paper-daily

这个项目仿照 `robot-paper-daily` 制作，用于抓取 arXiv `q-fin` 分类下的近期论文，调用 LLM 生成中文总结和相关性评分，并输出：

- `README.md`：Markdown 论文日报
- `index.html`：静态网页展示页
- `arxiv_q_fin_papers_final.json`：原始结构化数据

注意：仓库里的 `README.md` 是生成结果，不是项目说明。本文件 `readme.md` 才是说明文档。

## 目录说明

- `paper_daily.py`：抓取论文、提取正文片段、调用 LLM、写入 JSON 和 `README.md`
- `create_index.py`：根据 JSON 渲染 `index.html`
- `template.html`：HTML 模板
- `.github/workflows/paper.yml`：GitHub Actions 定时运行配置
- `requirements.txt`：Python 依赖

## 数据源

- arXiv recent 页面：`https://arxiv.org/list/q-fin/recent?show=100`

## 本地运行

### 1. 安装依赖

```bash
cd /home/hanyu/Codes/quant-paper-daily
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 2. 配置环境变量

```bash
export LLM_API_KEY='你的 API Key'
export LLM_PROMPT='你的提示词'
```

默认会调用：

- Host: `api.chatanywhere.org`
- Endpoint: `/v1/chat/completions`
- Model: `gpt-4o-mini`

### 3. 运行脚本

```bash
cd /home/hanyu/Codes/quant-paper-daily
python paper_daily.py
python create_index.py
```

运行完成后会更新：

- `arxiv_q_fin_papers_final.json`
- `README.md`
- `index.html`

### 4. 本地预览页面

```bash
cd /home/hanyu/Codes/quant-paper-daily
python -m http.server 8000
```

打开：

`http://127.0.0.1:8000/index.html`

## GitHub Actions 自动更新

工作流文件：

- `.github/workflows/paper.yml`

你需要在 GitHub 仓库里配置：

### Secrets

- `LLM_API_KEY`
- `GIT_TOKEN`

### Variables

- `LLM_PROMPT`

然后可以：

- 手动触发 `workflow_dispatch`
- 或等待定时任务自动运行

## 当前限制

- 当前 `MAX_CRAWL_PAGES = 1`，默认只抓取有限页数
- 总结质量依赖你提供的 `LLM_PROMPT`
- 如果上游接口超时或限流，论文会被记录为“大模型总结失败”
