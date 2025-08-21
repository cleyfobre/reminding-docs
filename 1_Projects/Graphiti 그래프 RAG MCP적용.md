# Graphiti MCP Server

### 목적
claude desktop + graphiti

### Graphiti github repo
https://github.com/getzep/graphiti/blob/main/mcp_server/README.md#prerequisites

### 진행 과정
- 한달간 성능 및 비용 테스트(8월14일 기준 20달러 결제)

### git clone
`git clone https://github.com/getzep/graphiti.git`

### 필요조건
- Ensure you have Python 3.10 or higher installed.
- A running Neo4j database (version 5.26 or later required)
- OpenAI API key for LLM operations

### 필요조건 진행방법
- python은 그냥 깔면 되고
- neo4j docker-compose 파일은 위에 repo를 clone 받으면 있음
- OpenAI API 20달러만 결제해 놓음. 미리 알람 설정해놓을 수도 있음. 나는 예산에 80% 채워지면 에러나도록 수정함.

### 방법
- 공식 문서에 uv 사용, .env 설정 등등 많은데, 다 필요없고...
- mcp를 하고 싶으면 clone 받은 폴더에 mcp_server 폴더로 이동하면 docker-compose.yml 이 있다.
- docker-compose.yml 을 아래와 같이 되어 있을 것.. OpenAI key랑 사용하고 싶은 gpt 모델 넣고 docker-compose up 하면 된다. 자세한 api 가격은 아래 링크 참고한다.
  - https://platform.openai.com/docs/pricing

```yaml
services:
  neo4j:
    image: neo4j:5.26.0
    ports:
      - "7474:7474" # HTTP
      - "7687:7687" # Bolt
    environment:
      - NEO4J_AUTH=${NEO4J_USER:-neo4j}/${NEO4J_PASSWORD:-demodemo}
      - NEO4J_server_memory_heap_initial__size=512m
      - NEO4J_server_memory_heap_max__size=1G
      - NEO4J_server_memory_pagecache_size=512m
    volumes:
      - neo4j_data:/data
      - neo4j_logs:/logs
    healthcheck:
      test: ["CMD", "wget", "-O", "/dev/null", "http://localhost:7474"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s

  graphiti-mcp:
    image: zepai/knowledge-graph-mcp:latest
    build:
      context: .
      dockerfile: Dockerfile
    env_file:
      - path: .env
        required: false # Makes the file optional. Default value is 'true'
    depends_on:
      neo4j:
        condition: service_healthy
    environment:
      - NEO4J_URI=${NEO4J_URI:-bolt://neo4j:7687}
      - NEO4J_USER=${NEO4J_USER:-neo4j}
      - NEO4J_PASSWORD=${NEO4J_PASSWORD:-demodemo}
      - OPENAI_API_KEY={여기에 OpenAi API Key를 넣자}
      - MODEL_NAME=gpt-4.1-mini
      - PATH=/root/.local/bin:${PATH}
      - SEMAPHORE_LIMIT=${SEMAPHORE_LIMIT:-10}
    ports:
      - "8000:8000" # Expose the MCP server via HTTP for SSE transport
    command: ["uv", "run", "graphiti_mcp_server.py", "--transport", "sse"]

volumes:
  neo4j_data:
  neo4j_logs:
```

### claude desktop에 mcp 클라이언트 추가
```json
{
  "mcpServers": {
    "graphiti-memory": {
      // You can choose a different name if you prefer
      "command": "npx", // Or the full path to mcp-remote if npx is not in your PATH
      "args": [
        "mcp-remote",
        "http://localhost:8000/sse" // Ensure this matches your Graphiti server's SSE endpoint
      ]
    }
  }
}
```
