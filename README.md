# bert-sentiment-api

University of California, Berkeley
Master of Information and Data Science (MIDS)
DATA 255 — Machine Learning Systems Engineering
End-to-End Machine Learning API — Sentiment Analysis
This project is a containerized FastAPI sentiment analysis API using a pretrained DistilBERT model fine-tuned on SST-2. It accepts batches of text, returns positive and negative sentiment scores, and uses Redis for response caching. The application was deployed to Amazon EKS with images stored in Amazon ECR, Kustomize for Kubernetes configuration, and Istio for routing. k6 and Grafana were used for load testing and monitoring.

Key functionalities:
- Exposes health and bulk sentiment prediction endpoints
- Validates requests and responses with Pydantic
- Runs DistilBERT inference through Hugging Face Transformers
- Caches API responses with Redis
- Packages the application and model in a Docker image
- Deploys API and Redis workloads to Kubernetes with health probes and autoscaling
- Uses k6 and Grafana to evaluate performance
Project Structure
- mlapi/Dockerfile — multistage Docker build for the FastAPI service
- mlapi/pyproject.toml — Python dependencies managed with Poetry
- mlapi/src/main.py — main FastAPI application and /project route mounting
- mlapi/src/sentiment_predict.py — inference pipeline, Pydantic models, Redis caching, and endpoints
- mlapi/tests/ — API tests
- mlapi/trainer/ — reference model training and prediction scripts
- .k8s/base/ — Kubernetes Deployments and Services
- .k8s/overlays/dev/ — local development configuration
- .k8s/overlays/prod/ — EKS deployment configuration, Istio VirtualService, and autoscaling
- load.js — k6 load-testing script
Endpoints
- GET /project/health — health check; returns {"status": "healthy"}
- POST /project/bulk-predict — accepts {"text": ["I loved it", "I disliked it"]} and returns a predictions array containing sentiment labels and scores for each input
Local Development
Requires Python 3.13+, Poetry, Redis, and the fine-tuned DistilBERT model files in distilbert-base-uncased-finetuned-sst2/ at the repository root. Model weights are excluded from Git.
cd mlapi
poetry install
From the repository root, after starting Redis locally, run:
cd mlapi
poetry run uvicorn src.main:app --host 0.0.0.0 --port 8000
The model must be available at the path expected by mlapi/src/sentiment_predict.py. Depending on your working directory, adjust that path or the launch directory before running.
Deployment & Monitoring
The application was deployed to AWS EKS using Docker, Amazon ECR, Kustomize, and Istio, with Redis as a separate Kubernetes workload. Kubernetes health probes and HorizontalPodAutoscalers supported service availability and scaling. k6 load tests and Grafana dashboards were used to examine latency and workload behavior.

Technologies: Python, FastAPI, Pydantic, PyTorch, Hugging Face Transformers, Redis, Docker, Kubernetes, AWS EKS/ECR, Kustomize, Istio, Poetry, k6, Grafana.

Academic project for UC Berkeley MIDS. The deployed classifier uses an existing fine-tuned DistilBERT model; the project focuses on inference serving and infrastructure.
