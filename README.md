# 🛒 Flipkart Product Recommender - RAG-Based E-commerce Chatbot

An intelligent product recommendation system built with **LangChain**, **RAG (Retrieval-Augmented Generation)**, and **AstraDB** vector store. This chatbot provides personalized product recommendations by analyzing Flipkart product reviews and titles.

![Workflow](assets/Flipkart+product+recommender+Workflow.png)

## 📋 Table of Contents
- [Overview](#overview)
- [System Workflow](#system-workflow)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Project Structure](#project-structure)
- [Setup & Installation](#setup--installation)
- [Deployment Guide](#deployment-guide)
- [Monitoring Setup](#monitoring-setup)
- [API Endpoints](#api-endpoints)
- [Environment Variables](#environment-variables)

---

## 🎯 Overview

This project implements a conversational AI assistant that helps users find products on Flipkart by understanding their queries and matching them with relevant product reviews. The system uses **RAG architecture** to provide context-aware, accurate recommendations based on real customer reviews.

**Key Capabilities:**
- Natural language product search
- Context-aware conversations with memory
- Review-based product recommendations
- Real-time query processing with LLM
- Scalable deployment on Kubernetes
- Prometheus & Grafana monitoring

---

## 🔄 System Workflow

### **High-Level Architecture Flow**

1. **Data Preparation Layer**
   - Raw Flipkart product data (CSV) containing product titles and customer reviews
   - `DataConverter` class processes the CSV and converts it into LangChain `Document` objects
   - Each document contains review text as page content and product title as metadata

2. **Vector Store Ingestion**
   - `DataIngestor` class handles the embedding and storage process
   - Uses **HuggingFace embeddings** (`BAAI/bge-base-en-v1.5` model) to convert text into vectors
   - Stores vectors in **AstraDB** (Cassandra-based vector database) for efficient similarity search
   - Vector collection named `flipkart_database` stores all product embeddings

3. **RAG Chain Construction**
   - `RAGChainBuilder` creates a sophisticated retrieval chain with:
     - **History-aware retriever**: Reformulates queries based on conversation context
     - **Document retriever**: Fetches top-k (k=3) most relevant product reviews
     - **Chat history management**: Maintains conversation context per session
   - Uses **Groq's LLaMA 3.1 8B** model for generating responses

4. **User Interaction Flow**
   - User sends a query through the Flask web interface
   - Query → RAG Chain → Context Retrieval → LLM Processing → Response
   - Chat history is maintained for contextual follow-up questions
   - Responses are based on actual product reviews, ensuring accuracy

5. **Monitoring & Observability**
   - **Prometheus**: Collects metrics (HTTP requests, response times)
   - **Grafana**: Visualizes application performance and usage patterns

---

## ✨ Features

- **🤖 Conversational AI**: Natural language understanding with context retention
- **🔍 Smart Retrieval**: Vector similarity search for relevant product matching
- **💬 Session Management**: Maintains conversation history per user session
- **📊 Monitoring**: Built-in Prometheus metrics and Grafana dashboards
- **🚀 Scalable**: Kubernetes deployment with Docker containerization
- **⚡ Fast Processing**: Groq API for low-latency LLM inference
- **💾 Vector Database**: AstraDB for efficient similarity search at scale

---

## 🛠 Tech Stack

### **Core Technologies**
- **Backend**: Flask (Python web framework)
- **LLM Framework**: LangChain
- **Vector Store**: AstraDB (DataStax)
- **Embeddings**: HuggingFace (`BAAI/bge-base-en-v1.5`)
- **LLM Model**: Groq LLaMA 3.1 8B Instant
- **Data Processing**: Pandas

### **DevOps & Monitoring**
- **Containerization**: Docker
- **Orchestration**: Kubernetes (Minikube)
- **Monitoring**: Prometheus + Grafana
- **Metrics**: prometheus_client

### **Frontend**
- HTML/CSS/JavaScript
- Responsive chat interface

---

## 🏗 Architecture

### **Component Breakdown**

#### **1. Data Ingestion Pipeline (`flipkart/data_ingestion.py`)**
```python
DataIngestor
├── Initializes HuggingFace embeddings
├── Connects to AstraDB vector store
└── Ingests documents (with load_existing option)
```

**Process:**
- Loads existing vector store OR creates new embeddings
- Converts CSV data to document embeddings
- Stores in AstraDB for retrieval

#### **2. RAG Chain Builder (`flipkart/rag_chain.py`)**
```python
RAGChainBuilder
├── History-aware retriever (reformulates queries)
├── Document retriever (k=3 similarity search)
├── LLM chain (Groq LLaMA 3.1)
└── Message history management
```

**Two-Stage Prompting:**
1. **Context Prompt**: Reformulates user query based on chat history
2. **QA Prompt**: Generates answer using retrieved context

#### **3. Flask Application (`app.py`)**
```python
Routes:
├── / → Main chat interface
├── /get → POST endpoint for user queries
└── /metrics → Prometheus metrics endpoint
```

---

## 📁 Project Structure

```
Flipkart-Product-Recommender/
│
├── flipkart/                      # Core application logic
│   ├── config.py                  # Environment configuration
│   ├── data_converter.py          # CSV to Document converter
│   ├── data_ingestion.py          # Vector store ingestion
│   └── rag_chain.py               # RAG chain implementation
│
├── data/                          # Dataset directory
│   └── flipkart_product_review.csv
│
├── templates/                     # HTML templates
│   └── index.html
│
├── static/                        # CSS, JS, images
│
├── assets/                        # Documentation assets
│   └── Flipkart+product+recommender+Workflow.png
│
├── prometheus/                    # Prometheus config
│   ├── prometheus-configmap.yaml
│   └── prometheus-deployment.yaml
│
├── grafana/                       # Grafana config
│   └── grafana-deployment.yaml
│
├── utils/                         # Utility modules
│   ├── logger.py
│   └── custom_exception.py
│
├── app.py                         # Flask application entry point
├── requirements.txt               # Python dependencies
├── Dockerfile                     # Docker image definition
├── flask-deployment.yaml          # Kubernetes deployment config
├── setup.py                       # Package setup
└── README.md                      # Documentation
```

---

## 🚀 Setup & Installation

### **Prerequisites**
- Python 3.8+
- Docker
- Kubernetes (Minikube for local development)
- Google Cloud VM (for production deployment)

### **Local Development**

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/Flipkart-Product-Recommender.git
cd Flipkart-Product-Recommender
```

2. **Create virtual environment**
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. **Install dependencies**
```bash
pip install -r requirements.txt
```

4. **Set up environment variables**

Create a `.env` file in the root directory:
```env
GROQ_API_KEY=your_groq_api_key
ASTRA_DB_APPLICATION_TOKEN=your_astra_token
ASTRA_DB_KEYSPACE=default_keyspace
ASTRA_DB_API_ENDPOINT=your_astra_endpoint
HF_TOKEN=your_huggingface_token
HUGGINGFACEHUB_API_TOKEN=your_huggingface_token
```

5. **Run the application**
```bash
python app.py
```

The application will be available at `http://localhost:5000`

---

## 🌐 Deployment Guide

### **1. Initial Setup**

#### **Create a GCP VM Instance**
- **Name**: `flipkart-recommender`
- **Machine Type**: E2 Standard (16 GB RAM)
- **Boot Disk**: Ubuntu 24.04 LTS (256 GB)
- **Networking**: Enable HTTP/HTTPS traffic

#### **Connect to VM via SSH**
```bash
# Access from GCP Console or use gcloud CLI
gcloud compute ssh <instance-name>
```

---

### **2. Configure VM Instance**

#### **Clone Repository**
```bash
git clone https://github.com/yourusername/Flipkart-Product-Recommender.git
cd Flipkart-Product-Recommender
```

#### **Install Docker**
```bash
# Add Docker's official GPG key
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add repository to Apt sources
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Test Docker
docker run hello-world
```

#### **Run Docker without sudo**
```bash
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
docker run hello-world
```

#### **Enable Docker on boot**
```bash
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

---

### **3. Configure Minikube**

#### **Install Minikube**
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```

#### **Install kubectl**
```bash
sudo snap install kubectl --classic
kubectl version --client
```

#### **Start Minikube**
```bash
minikube start
minikube status
kubectl get nodes
```

---

### **4. Configure Git**

```bash
git config --global user.email "youremail@example.com"
git config --global user.name "yourusername"
```

---

### **5. Build and Deploy Application**

#### **Point Docker to Minikube**
```bash
eval $(minikube docker-env)
```

#### **Build Docker Image**
```bash
docker build -t flask-app:latest .
```

#### **Create Kubernetes Secrets**
```bash
kubectl create secret generic flipkart-secrets \
  --from-literal=GROQ_API_KEY="your_groq_api_key" \
  --from-literal=ASTRA_DB_APPLICATION_TOKEN="your_token" \
  --from-literal=ASTRA_DB_KEYSPACE="default_keyspace" \
  --from-literal=ASTRA_DB_API_ENDPOINT="your_endpoint" \
  --from-literal=HF_TOKEN="your_hf_token" \
  --from-literal=HUGGINGFACEHUB_API_TOKEN="your_hf_token"
```

#### **Deploy to Kubernetes**
```bash
kubectl apply -f flask-deployment.yaml
kubectl get pods
```

#### **Port Forward to Access Application**
```bash
kubectl port-forward svc/flask-service 5000:80 --address 0.0.0.0
```

Access the application at: `http://<EXTERNAL_VM_IP>:5000`

---

## 📊 Monitoring Setup

### **Deploy Prometheus & Grafana**

#### **1. Create Monitoring Namespace**
```bash
kubectl create namespace monitoring
kubectl get ns
```

#### **2. Deploy Prometheus**
```bash
kubectl apply -f prometheus/prometheus-configmap.yaml
kubectl apply -f prometheus/prometheus-deployment.yaml
```

#### **3. Deploy Grafana**
```bash
kubectl apply -f grafana/grafana-deployment.yaml
```

#### **4. Port Forward Services**

**Prometheus (Port 9090):**
```bash
kubectl port-forward --address 0.0.0.0 svc/prometheus-service -n monitoring 9090:9090
```
Access at: `http://<EXTERNAL_VM_IP>:9090`

**Grafana (Port 3000):**
```bash
kubectl port-forward --address 0.0.0.0 svc/grafana-service -n monitoring 3000:3000
```
Access at: `http://<EXTERNAL_VM_IP>:3000`
- **Default credentials**: `admin` / `admin`

---

### **Configure Grafana**

1. Navigate to **Settings → Data Sources → Add Data Source**
2. Select **Prometheus**
3. Set URL: `http://prometheus-service.monitoring.svc.cluster.local:9090`
4. Click **Save & Test** (should show green success message)

#### **Create Dashboards**
- Import pre-built dashboards or create custom visualizations
- Monitor metrics like:
  - HTTP request count (`http_requests_total`)
  - Response times
  - Pod resource usage
  - Error rates

---

## 🔌 API Endpoints

### **1. Home Page**
```
GET /
```
Returns the main chat interface

### **2. Get Response**
```
POST /get
Form Data: { "msg": "user query" }
```
Processes user query and returns chatbot response

**Example Request:**
```bash
curl -X POST http://localhost:5000/get \
  -d "msg=Show me best smartphones under 20000"
```

**Example Response:**
```
Based on customer reviews, here are the top-rated smartphones under ₹20,000:
1. Samsung Galaxy M32 - Great battery life and display
2. Realme 8 Pro - Excellent camera performance
...
```

### **3. Metrics Endpoint**
```
GET /metrics
```
Returns Prometheus metrics for monitoring

---

## 🔐 Environment Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `GROQ_API_KEY` | Groq API key for LLM access | `gsk_...` |
| `ASTRA_DB_APPLICATION_TOKEN` | AstraDB authentication token | `AstraCS:...` |
| `ASTRA_DB_KEYSPACE` | AstraDB keyspace name | `default_keyspace` |
| `ASTRA_DB_API_ENDPOINT` | AstraDB API endpoint URL | `https://...` |
| `HF_TOKEN` | HuggingFace API token | `hf_...` |
| `HUGGINGFACEHUB_API_TOKEN` | HuggingFace Hub token | `hf_...` |

---

## 🤝 How It Works

### **User Query Flow**

1. **User Input**: "Show me laptops with good battery life"
2. **Embedding**: Query converted to vector using HuggingFace embeddings
3. **Retrieval**: AstraDB returns top 3 similar product reviews
4. **Context Building**: Retrieved reviews passed to LLM as context
5. **Response Generation**: Groq LLaMA generates natural language response
6. **History Update**: Conversation stored for context in follow-up queries

### **Example Conversation**

```
User: Show me gaming laptops
Bot: Based on customer reviews, here are highly-rated gaming laptops:
     - ASUS TUF Gaming: Praised for thermal management...
     - HP Pavilion Gaming: Great price-to-performance ratio...

User: What about their battery life?
Bot: [Uses conversation history to understand "their" refers to gaming laptops]
     Regarding battery life of these gaming laptops...
```

---

## 📚 Key Technologies Explained

### **RAG (Retrieval-Augmented Generation)**
Combines information retrieval with language generation:
- **Retrieval**: Fetches relevant documents from vector store
- **Augmentation**: Adds retrieved context to the prompt
- **Generation**: LLM generates answer based on context

### **Vector Embeddings**
Text converted to numerical vectors (768 dimensions):
- Captures semantic meaning
- Enables similarity search
- `BAAI/bge-base-en-v1.5` model used

### **AstraDB**
Cassandra-based vector database:
- Distributed and scalable
- Built-in vector similarity search
- Low-latency queries

### **LangChain**
Framework for building LLM applications:
- Chains multiple components
- Manages conversation history
- Simplifies RAG implementation

---

## 🐛 Troubleshooting

### **Common Issues**

1. **Pod not starting**: Check secrets are created correctly
```bash
kubectl get secrets
kubectl describe secret flipkart-secrets
```

2. **Database connection error**: Verify AstraDB credentials and endpoint

3. **Port forwarding issues**: Ensure ports are not already in use
```bash
sudo lsof -i :5000
```

4. **Docker build fails**: Check Dockerfile and dependencies

---

## 📈 Performance Optimization

- **Caching**: Vector store supports `load_existing=True` to avoid re-embedding
- **Batch Processing**: Process multiple queries efficiently
- **K-value Tuning**: Adjust `k=3` in retriever for balance between accuracy and speed
- **Model Selection**: Groq provides fast inference with LLaMA 3.1

---

## 🔮 Future Enhancements

- [ ] Add user authentication and personalization
- [ ] Implement product image search
- [ ] Multi-modal RAG (text + images)
- [ ] A/B testing for different retrieval strategies
- [ ] Auto-scaling based on traffic
- [ ] Advanced analytics dashboard
- [ ] Support for multiple languages

---

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

## 👥 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📞 Contact

For questions or support, please open an issue in the GitHub repository.

---

## 🙏 Acknowledgments

- **LangChain** for the RAG framework
- **DataStax AstraDB** for vector storage
- **Groq** for fast LLM inference
- **HuggingFace** for embedding models
- **Flipkart** for the product dataset

---

**Built with ❤️ using LangChain, RAG, and Modern MLOps practices**
