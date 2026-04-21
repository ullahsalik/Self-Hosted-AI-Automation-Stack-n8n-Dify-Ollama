

# Self-Hosted AI Automation Stack: n8n + Dify + Ollama

This repository provides a production-ready Docker Compose configuration to deploy a private, local AI orchestration stack. It enables you to run LLMs locally, manage RAG (Retrieval-Augmented Generation) pipelines, and automate workflows across 400+ applications without third-party data exposure.



## 🏗 Architecture Overview

* **Model Layer (Ollama):** Local LLM inference engine.
* **App & RAG Layer (Dify):** LLM application development platform and vector database management.
* **Automation Layer (n8n):** Workflow orchestrator connecting the AI stack to external APIs and databases.

## 🚀 Prerequisites

* **OS:** Ubuntu 22.04 LTS or macOS.
* **Hardware:** * Minimum: 16GB RAM, 4-core CPU.
    * Recommended: NVIDIA GPU (8GB+ VRAM) for faster inference.
* **Tools:** Docker Engine 24.0+ and Docker Compose v2.20+.

## 🛠 Deployment Steps

### 1. Clone the Repository
```bash
git clone https://github.com/your-repo/ai-automation-stack.git
cd ai-automation-stack
```

### 2. Configure Environment Variables
Copy the example environment file and update the credentials.
```bash
cp .env.example .env
```
*Note: Ensure `OLLAMA_API_BASE_URL` is set to `http://ollama:11434` for internal Docker networking.*

### 3. Launch the Stack
```bash
docker-compose up -d
```

### 4. Initialize Models
Once the containers are healthy, download your preferred model into Ollama:
```bash
docker exec -it ollama ollama pull llama3
```

## 🔗 Service Access

| Service | Port | Description |
| :--- | :--- | :--- |
| **n8n** | `5678` | Workflow Automation UI |
| **Dify** | `80` | App Builder & RAG Interface |
| **Ollama** | `11434` | Local LLM API |
| **PostgreSQL** | `5432` | Shared Database (Internal) |
| **Redis** | `6379` | Queue/Cache (Internal) |



## 🛡 DevOps & Scaling

### GPU Acceleration (NVIDIA)
To enable GPU support for Ollama, ensure the `nvidia-container-toolkit` is installed on the host and uncomment the `deploy` section in `docker-compose.yaml`:
```yaml
services:
  ollama:
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
```

### Monitoring
The stack includes a health check endpoint for each service. For production environments, integrate these with your existing Prometheus/Grafana stack:
* n8n: `GET /healthz`
* Ollama: `GET /api/tags`

## 🔒 Security Best Practices
* **Network:** Do not expose port `11434` (Ollama) or `6379` (Redis) to the public internet. Use a VPN or SSH tunnel.
* **Secrets:** All sensitive keys (Postgres passwords, n8n encryption keys) should be managed via `.env` or integrated with **HashiCorp Vault**.
* **Volume Backups:** Regularly back up the `./docker_volumes` directory to ensure no loss of workflow data or RAG vector embeddings.

## 📄 License
This project is licensed under the MIT License.
