---
title: "Vertica MCP 연동: LLM을 활용한 자연어 데이터 분석 - Vertica Blog"
layout: default
description: "Vertica와 LLM(Claude 등)을 연동하여 자연어 기반 데이터 분석을 수행하는 방법을 알아봅니다. MCP(Model Context Protocol)를 활용한 AI Agent 구축 및 구성 예시를 제공합니다."
keywords: "vertica, mcp, model context protocol, llm, claude, ai agent, natural language query"
canonical_url: "https://jin0326.github.io/vertica_blog/100playbook/100playbook#mcp-integration"
---

<div id="mcp-integration" style="scroll-margin-top: 100px;" itemscope itemtype="https://schema.org/TechArticle">
<meta itemprop="headline" content="{{ page.title | remove: ' - Vertica Blog' }}">
<meta itemprop="url" content="{{ page.canonical_url }}">

<div itemprop="articleBody" markdown="1">
## Vertica MCP 연동

<div class="architecture-section">
  <p class="section-description">
    전통적인 LLM은 학습 데이터에 의존적이며 외부 시스템과의 통합에 한계가 있었습니다. 실제 비즈니스 환경에서 자율적으로 작동하는 AI Agent를 구축하기 위해, Vertica는 <strong>MCP(Model Context Protocol)</strong>를 활용한 혁신적인 데이터 연계를 지원합니다.
    Vertica 26.1 버전부터는 자체적으로 MCP 서버를 지원하여 외부 도구 없이도 LLM과 직접 연동할 수 있습니다.
  </p>

  <div class="feature-box">
    <dl class="feature-dl">
      <dt class="feature-dt">
        <span class="feature-dt__icon">◆</span> MCP(Model Context Protocol)란?
      </dt>
      <dd class="feature-dd">
        LLM이 외부 시스템의 파일, 데이터, API, 응용 프로그램과 안전하고 구조화된 방식으로 통신하도록 돕는 오픈 프로토콜입니다. 이를 통해 클라이언트(Claude, Notion, Slack 등)는 로컬 데이터 소스나 데이터베이스(Vertica)와 직접 상호작용하여 더욱 다양한 데이터를 활용할 수 있습니다.
      </dd>
    </dl>
  </div>

  <!-- 아키텍처 흐름 이미지 추가된 부분 -->
  <div class="image-box-styled" style="margin-bottom: 3.5rem;">
    <img src="/vertica_blog/assets/images/vertica_mcp.png" alt="AI Host to Vertica DB Architecture" >
  </div>

  <h3 class="eon-section-title">Vertica MCP Server 구성 예시</h3>
  
  <div class="syntax-box">
    <strong>1. Vertica MCP Server Configure (Linux)</strong>
    <pre><code># 1. Clone the repository
git clone https://github.com/zaboura/vertica-mcp.git
cd vertica-mcp

# 2. Install uv (if not already installed)
curl -LsSf https://astral.sh/uv/install.sh | sh

# 3. Setup environment and install dependencies
uv sync
source .venv/bin/activate

# 4. Install in development mode
uv pip install -e .

# 5. Configure database connection
cp .env.example .env

# 6. Run the server (SSE 전송 프로토콜 방식 활용)
vertica-mcp --transport sse --port 8000 --bind-host 0.0.0.0</code></pre>
  </div>

  <div class="syntax-box">
    <strong>2. Vertica DB + MCP Connection 설정 (.env 수정)</strong>
    <pre><code># =========================
# Vertica connection (REQUIRED)
# =========================
# Hostname or IP of the Vertica server
VERTICA_HOST=IP주소
# Port (Vertica defaults to 5433)
VERTICA_PORT=5433
# Database name
VERTICA_DATABASE=DB명
# Credentials
VERTICA_USER=dbuser명
VERTICA_PASSWORD=패스워드</code></pre>
  </div>

  <div class="syntax-box">
    <strong>3. Claude Desktop (Windows) MCP Client 설정</strong>
    <pre><code># 1. 사전 패키지 설치 (cmd)
winget install OpenJS.Nodejs

# 2. Claude Desktop Config 수정 
# 경로: %APPDATA%/Claude/claude_desktop_config.json
{
  "mcpServers": {
    "vertica-mcp-http": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "http://IP주소:8000/sse", "--allow-http"
      ]
    }
  }
}</code></pre>
  </div>

  <h3 class="eon-section-title">Vertica + MCP 실행 및 데이터 추출</h3>
  <p class="section-description">
    설정이 완료되면 LLM(Claude Desktop)에서 제공되는 다양한 Tool(예: <code>Get schema tables</code>, <code>Run query safely</code>)을 사용하여 자연어로 직접 Vertica DB의 테이블 구조를 파악하고, 조건에 맞는 복잡한 쿼리를 실행하여 결과 데이터를 추출할 수 있습니다.
  </p>
</div>
</div>
</div>